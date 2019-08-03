---
title: レッスン 4 R モデルを使用して潜在的な結果を予測する
description: T-sql 関数を使用して SQL Server ストアドプロシージャに埋め込まれた R スクリプトを運用化する方法を示すチュートリアル
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/16/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: abacf3c384430417dbf0630f2f8dcd8adff68259
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714736"
---
# <a name="lesson-4-run-predictions-using-r-embedded-in-a-stored-procedure"></a>レッスン 4:ストアドプロシージャに埋め込まれた R を使用して予測を実行する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、SQL Server で R を使用する方法に関する SQL 開発者向けのチュートリアルの一部です。

このステップでは、新しい観測にモデルを使用して潜在的な結果を予測する方法について学習します。 モデルは、他のアプリケーションから直接呼び出すことができるストアドプロシージャにラップされます。 このチュートリアルでは、スコア付けを実行するいくつかの方法を示します。

- **バッチスコアリングモード**:ストアドプロシージャへの入力として SELECT クエリを使用します。 このストアド プロシージャは、入力ケースに対応する観察のテーブルを返します。

- **個々のスコア付けモード**:個別のパラメーター値のセットを入力として渡します。  このストアド プロシージャは、1 つの行または値を返します。

まず、一般的なスコア付け方法を見てみましょう。

## <a name="basic-scoring"></a>基本スコア付け

ストアドプロシージャ**RxPredict**は、ストアドプロシージャで RevoScaleR RxPredict 呼び出しをラップするための基本構文を示しています。

```sql
CREATE PROCEDURE [dbo].[RxPredict] (@model varchar(250), @inquery nvarchar(max))
AS 
BEGIN 

DECLARE @lmodel2 varbinary(max) = (SELECT model FROM nyc_taxi_models WHERE name = @model);  
EXEC sp_execute_external_script @language = N'R',
  @script = N' 
    mod <- unserialize(as.raw(model)); 
    print(summary(mod)) 
    OutputDataSet<-rxPredict(modelObject = mod, data = InputDataSet, outData = NULL, predVarNames = "Score", type = "response", writeModelVars = FALSE, overwrite = TRUE); 
    str(OutputDataSet) 
    print(OutputDataSet) 
    ', 
  @input_data_1 = @inquery, 
  @params = N'@model varbinary(max)',
  @model = @lmodel2 
  WITH RESULT SETS ((Score float));
END
GO
```

+ SELECT ステートメントは、シリアル化されたモデルをデータベースから取得し、r を使用`mod`してさらに処理するために r 変数にモデルを格納します。

+ スコアリングの新しいケースは、ストアドプロシージャの[!INCLUDE[tsql](../../includes/tsql-md.md)]最初のパラメーター `@inquery`であるで指定されたクエリから取得されます。 クエリ データが読み取られると、行が既定のデータ フレーム `InputDataSet`に保存されます。 このデータフレームは、スコアを生成する[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)の[rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict)関数に渡されます。
  
    `OutputDataSet<-rxPredict(modelObject = mod, data = InputDataSet, outData = NULL, predVarNames = "Score", type = "response", writeModelVars = FALSE, overwrite = TRUE);`
  
    data.frame には 1 つの行を含めることができるため、一括または 1 回のスコア付けに同じコードを利用できます。
  
+ `rxPredict`関数によって返される値は、ドライバーが任意の量のチップを取得する確率を表す**float**です。

## <a name="batch-scoring-a-list-of-predictions"></a>バッチスコアリング (予測の一覧)

より一般的なシナリオは、バッチモードで複数の観測の予測を生成することです。 この手順では、バッチスコアリングのしくみを見てみましょう。

1.  最初に、使用する入力データの小さなセットを取得します。 このクエリは、予測に必要な乗客数とその他の機能を利用し、乗車の "上位 10" 一覧を作成します。
  
    ```sql
    SELECT TOP 10 a.passenger_count AS passenger_count, a.trip_time_in_secs AS trip_time_in_secs, a.trip_distance AS trip_distance, a.dropoff_datetime AS dropoff_datetime, dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude,dropoff_longitude) AS direct_distance
    
    FROM (SELECT medallion, hack_license, pickup_datetime, passenger_count,trip_time_in_secs,trip_distance, dropoff_datetime, pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude FROM nyctaxi_sample)a

    LEFT OUTER JOIN

    (SELECT medallion, hack_license, pickup_datetime FROM nyctaxi_sample TABLESAMPLE (70 percent) REPEATABLE (98052)    )b

    ON a.medallion=b.medallion AND a.hack_license=b.hack_license 
    AND a.pickup_datetime=b.pickup_datetime
    WHERE b.medallion IS NULL
    ```

    **サンプルの結果**
    
    ```sql
    passenger_count   trip_time_in_secs    trip_distance  dropoff_datetime   direct_distance
    1  283 0.7 2013-03-27 14:54:50.000   0.5427964547
    1  289 0.7 2013-02-24 12:55:29.000   0.3797099614
    1  214 0.7 2013-06-26 13:28:10.000   0.6970098661
    ```

2. で[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] **RxPredictBatchOutput**というストアドプロシージャを作成します。

    ```sql
    CREATE PROCEDURE [dbo].[RxPredictBatchOutput] (@model varchar(250), @inquery nvarchar(max))
    AS
    BEGIN
    DECLARE @lmodel2 varbinary(max) = (SELECT model FROM nyc_taxi_models WHERE name = @model);
    EXEC sp_execute_external_script 
      @language = N'R',
      @script = N'
        mod <- unserialize(as.raw(model));
        print(summary(mod))
        OutputDataSet<-rxPredict(modelObject = mod, data = InputDataSet, outData = NULL, predVarNames = "Score", type = "response", writeModelVars = FALSE, overwrite = TRUE);
        str(OutputDataSet)
        print(OutputDataSet)
      ',
      @input_data_1 = @inquery,
      @params = N'@model varbinary(max)',
      @model = @lmodel2
      WITH RESULT SETS ((Score float));
    END
    ```

3.  変数にクエリテキストを指定し、それをパラメーターとしてストアドプロシージャに渡します。

    ```sql
    -- Define the input data
    DECLARE @query_string nvarchar(max)
    SET @query_string='SELECT TOP 10 a.passenger_count as passenger_count, a.trip_time_in_secs AS trip_time_in_secs, a.trip_distance AS trip_distance, a.dropoff_datetime AS dropoff_datetime, dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude,dropoff_longitude) AS direct_distance FROM  (SELECT medallion, hack_license, pickup_datetime, passenger_count,trip_time_in_secs,trip_distance, dropoff_datetime, pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude FROM nyctaxi_sample  )a   LEFT OUTER JOIN (SELECT medallion, hack_license, pickup_datetime FROM nyctaxi_sample TABLESAMPLE (70 percent) REPEATABLE (98052))b ON a.medallion=b.medallion AND a.hack_license=b.hack_license AND a.pickup_datetime=b.pickup_datetime WHERE b.medallion is null'
    
    -- Call the stored procedure for scoring and pass the input data
    EXEC [dbo].[RxPredictBatchOutput] @model = 'RxTrainLogit_model', @inquery = @query_string;
    ```
  
このストアドプロシージャは、上位10回の各トリップの予測を表す一連の値を返します。 ただし、上位のトリップは、比較的短いトリップ距離を持つ1つの乗客の旅行でもあり、その場合、ドライバーがヒントを得られる可能性はほとんどありません。
  

> [!TIP]
> 
> "Yes-tip" と "no tip" の結果のみを返すのではなく、予測の確率スコアを返した後、WHERE 句を_スコア_列の値に適用して、スコアを "チップになる可能性がある" または "ヒント" のように分類することもできます。0.5 や0.7 などのしきい値。 この手順はストアド プロシージャに含まれていませんが、導入は簡単です。

## <a name="single-row-scoring-of-multiple-inputs"></a>複数の入力の単一行スコアリング

場合によっては、複数の入力値を渡し、それらの値に基づいて1つの予測を取得する必要があります。 たとえば、Excel ワークシート、web アプリケーション、または Reporting Services レポートを設定して、ストアドプロシージャを呼び出し、それらのアプリケーションからユーザーが入力または選択した入力を提供することができます。

このセクションでは、乗客数、旅行距離など、複数の入力を受け取るストアドプロシージャを使用して単一の予測を作成する方法について説明します。 ストアドプロシージャは、以前に格納された R モデルに基づいてスコアを作成します。
  
外部アプリケーションからストアドプロシージャを呼び出す場合は、データが R モデルの要件と一致していることを確認してください。 場合によっては、入力データを R データ型にキャストまたは変換できなければなりません。あるいは、データ型やデータ長を検証する必要があります。 

1. ストアドプロシージャ**RxPredictSingleRow**を作成します。
  
    ```sql
    CREATE PROCEDURE [dbo].[RxPredictSingleRow] @model varchar(50), @passenger_count int = 0, @trip_distance float = 0, @trip_time_in_secs int = 0, @pickup_latitude float = 0, @pickup_longitude float = 0, @dropoff_latitude float = 0, @dropoff_longitude float = 0
    AS
    BEGIN
    DECLARE @inquery nvarchar(max) = N'SELECT * FROM [dbo].[fnEngineerFeatures](@passenger_count, @trip_distance, @trip_time_in_secs,  @pickup_latitude, @pickup_longitude, @dropoff_latitude, @dropoff_longitude)';
    DECLARE @lmodel2 varbinary(max) = (SELECT model FROM nyc_taxi_models WHERE name = @model);
    EXEC sp_execute_external_script  
      @language = N'R',
      @script = N'  
        mod <- unserialize(as.raw(model));  
        print(summary(mod));  
        OutputDataSet<-rxPredict(modelObject = mod, data = InputDataSet, outData = NULL, predVarNames = "Score", type = "response", writeModelVars = FALSE, overwrite = TRUE);  
        str(OutputDataSet);
        print(OutputDataSet); 
        ',  
      @input_data_1 = @inquery,  
      @params = N'@model varbinary(max),@passenger_count int,@trip_distance float,@trip_time_in_secs int ,  @pickup_latitude float ,@pickup_longitude float ,@dropoff_latitude float ,@dropoff_longitude float', @model = @lmodel2, @passenger_count =@passenger_count, @trip_distance=@trip_distance, @trip_time_in_secs=@trip_time_in_secs, @pickup_latitude=@pickup_latitude, @pickup_longitude=@pickup_longitude, @dropoff_latitude=@dropoff_latitude, @dropoff_longitude=@dropoff_longitude  
      WITH RESULT SETS ((Score float));  
    END
    ```

2. 手動で値を指定し、お試しください。
  
    新しい**クエリ**ウィンドウを開き、ストアドプロシージャを呼び出して、各パラメーターの値を指定します。 パラメーターは、モデルで使用される特徴列を表し、必須です。

    ```sql
    EXEC [dbo].[RxPredictSingleRow] @model = 'RxTrainLogit_model',
    @passenger_count = 1,
    @trip_distance = 2.5,
    @trip_time_in_secs = 631,
    @pickup_latitude = 40.763958,
    @pickup_longitude = -73.973373,
    @dropoff_latitude =  40.782139,
    @dropoff_longitude = -73.977303
    ```

    または、[ストアドプロシージャのパラメーター](https://docs.microsoft.com/sql/relational-databases/stored-procedures/specify-parameters)に対してサポートされている短い形式を使用します。
  
    ```sql
    EXEC [dbo].[RxPredictSingleRow] 'RxTrainLogit_model', 1, 2.5, 631, 40.763958,-73.973373, 40.782139,-73.977303
    ```

3. この結果は、これらの上位10回のトリップで、ヒントを取得する確率が低い (ゼロ) ことを示しています。これは、すべてが比較的短い距離での単一の乗客のトリップであるためです。

## <a name="conclusions"></a>まとめ

これでチュートリアルは終わりです。 これで、ストアドプロシージャに R コードを埋め込む方法を学習できました。次は、これらのプラクティスを拡張して独自のモデルを構築することができます。 [!INCLUDE[tsql](../../includes/tsql-md.md)] との統合により、R モデルを展開して予測することと、企業データ ワークフローの一部としてモデルを組み込み、維持することが簡単になります。

## <a name="previous-lesson"></a>前のレッスン

[レッスン 3:T-sql を使用した R モデルのトレーニングと保存](sqldev-train-and-save-a-model-using-t-sql.md)
