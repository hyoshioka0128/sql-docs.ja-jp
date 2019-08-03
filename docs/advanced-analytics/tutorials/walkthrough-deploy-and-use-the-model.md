---
title: SQL Server での予測用の R モデルのデプロイ
description: データベース内分析のために SQL Server に R モデルをデプロイする方法を示すチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/26/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: aba6990fbed5b24d63d4ab5c16e192718aeff305
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714681"
---
# <a name="deploy-the-r-model-and-use-it-in-sql-server-walkthrough"></a>R モデルをデプロイして SQL Server で使用する (チュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンでは、ストアドプロシージャからトレーニング済みのモデルを呼び出すことによって、運用環境で R モデルを配置する方法について説明します。 ストアドプロシージャは[!INCLUDE[tsql](../../includes/tsql-md.md)] C#、R またはをサポートする任意のアプリケーションプログラミング言語 (、Java、Python など) から呼び出すことができ、モデルを使用して新しい観測の予測を行うことができます。

この記事では、スコア付けでモデルを使用する最も一般的な2つの方法について説明します。

> [!div class="checklist"]
> * **バッチスコアリングモード**で複数の予測を生成する
> * **個別のスコアリングモード**では、一度に1つずつ予測が生成されます。

## <a name="batch-scoring"></a>バッチスコアリング

複数の予測を生成するストアドプロシージャ*Predictヒント Batchmode*を作成し、SQL クエリまたはテーブルを入力として渡します。 結果のテーブルが返されます。テーブルに直接挿入したり、ファイルに書き込んだりすることができます。

- SQL クエリとして一連の入力データを取得する
- 前のレッスンで保存したトレーニング済みのロジスティック回帰モデルを呼び出す
- ドライバーがゼロ以外のヒントを取得する確率を予測します。

1. Management Studio で、新しいクエリウィンドウを開き、次の T-sql スクリプトを実行して、Predictthe Batchmode ストアドプロシージャを作成します。
  
    ```sql
    USE [NYCTaxi_Sample]
    GO

    SET ANSI_NULLS ON
    GO
    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'PredictTipBatchMode')
    DROP PROCEDURE v
    GO

    CREATE PROCEDURE [dbo].[PredictTipBatchMode] @input nvarchar(max)
    AS
    BEGIN
      DECLARE @lmodel2 varbinary(max) = (SELECT TOP 1 model  FROM nyc_taxi_models);
      EXEC sp_execute_external_script @language = N'R',
         @script = N'
           mod <- unserialize(as.raw(model));
           print(summary(mod))
           OutputDataSet<-rxPredict(modelObject = mod,
             data = InputDataSet,
             outData = NULL,
             predVarNames = "Score", type = "response",
             writeModelVars = FALSE, overwrite = TRUE);
           str(OutputDataSet)
           print(OutputDataSet)',
      @input_data_1 = @input,
      @params = N'@model varbinary(max)',
      @model = @lmodel2
      WITH RESULT SETS ((Score float));
    END
    ```

    + SQL テーブルからストアドモデルを呼び出すには、SELECT ステートメントを使用します。 モデルはテーブルから**varbinary (max)** データとして取得され、SQL 変数 _\@lmodel2_に格納されて、パラメーター *mod*としてシステムストアドプロシージャ[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)に渡されます。

    + スコアリングの入力として使用されるデータは、sql クエリとして定義され、sql 変数 _\@入力_に文字列として格納されます。 データベースからデータが取得されると、 *Inputdataset*と呼ばれるデータフレームに格納されます。これは、 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)プロシージャに対する入力データの既定の名前にすぎません。必要に応じて、  *_\@input_data_1_name_* パラメーターを使用して別の変数名を定義できます。

    + スコアを生成するために、ストアドプロシージャは**RevoScaleR**ライブラリから rxPredict 関数を呼び出します。

    + 戻り値の*スコア*は、モデルの場合、そのドライバーがヒントを取得する確率です。 必要に応じて、戻り値を "ヒント" グループと "チップなし" グループに分類するために、戻り値に何らかのフィルターを適用することもできます。  たとえば、0.5 未満の確率は、チップが低いことを意味します。
  
2.  バッチモードでストアドプロシージャを呼び出すには、ストアドプロシージャへの入力として必要なクエリを定義します。 次に示すのは、SSMS で実行して動作することを確認できる SQL クエリです。

    ```sql
    SELECT TOP 10
      a.passenger_count AS passenger_count,
      a.trip_time_in_secs AS trip_time_in_secs,
      a.trip_distance AS trip_distance,
      a.dropoff_datetime AS dropoff_datetime,
      dbo.fnCalculateDistance( pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS direct_distance
      FROM 
        (SELECT medallion, hack_license, pickup_datetime, passenger_count,trip_time_in_secs,trip_distance, dropoff_datetime, pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude 
        FROM nyctaxi_sample)a 
      LEFT OUTER JOIN
      ( SELECT medallion, hack_license, pickup_datetime
      FROM nyctaxi_sample  tablesample (1 percent) repeatable (98052)  )b
      ON a.medallion=b.medallion
      AND a.hack_license=b.hack_license
      AND a.pickup_datetime=b.pickup_datetime
      WHERE b.medallion is null
    ```

3. 次の R コードを使用して、SQL クエリから入力文字列を作成します。

    ```R
    input <- "N'SELECT TOP 10 a.passenger_count AS passenger_count, a.trip_time_in_secs AS trip_time_in_secs, a.trip_distance AS trip_distance, a.dropoff_datetime AS dropoff_datetime, dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS direct_distance FROM (SELECT medallion, hack_license, pickup_datetime, passenger_count,trip_time_in_secs,trip_distance, dropoff_datetime, pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude FROM nyctaxi_sample)a LEFT OUTER JOIN ( SELECT medallion, hack_license, pickup_datetime FROM nyctaxi_sample  tablesample (1 percent) repeatable (98052)  )b ON a.medallion=b.medallion AND a.hack_license=b.hack_license AND  a.pickup_datetime=b.pickup_datetime WHERE b.medallion is null'";
    q <- paste("EXEC PredictTipBatchMode @input = ", input, sep="");
    ```

4. R からストアドプロシージャを実行するには、 **RODBC**パッケージの**sqlquery**メソッドを呼び出して、前に`conn`定義した SQL 接続を使用します。

    ```R
    sqlQuery (conn, q);
    ```

    ODBC エラーが発生した場合は、構文エラーを確認し、適切な数の引用符があるかどうかを確認します。 
    
    権限エラーが発生した場合は、ログインにストアドプロシージャを実行する権限があることを確認してください。

## <a name="single-row-scoring"></a>単一行のスコアリング

個別のスコアリングモードでは、一度に1つずつ予測が生成され、個別の値のセットが入力としてストアドプロシージャに渡されます。 値は、モデルで予測を作成するために使用されるモデルの特徴と、確率値などの別の結果を生成するために使用されます。 その後、その値をアプリケーションまたはユーザーに返すことができます。

1行単位で予測するためにモデルを呼び出す場合は、個々のケースの特徴を表す値のセットを渡します。 その後、ストアドプロシージャは1つの予測または確率を返します。 

ストアドプロシージャ*PredictTipSingleMode*は、この方法を示しています。 特徴の値 (乗客数や旅行距離など) を表す複数のパラメーターを入力として受け取り、格納されている R モデルを使用してこれらの特徴をスコア付けし、チップの確率を出力します。

1. ストアドプロシージャを作成するには、次の Transact-sql ステートメントを実行します。

    ```sql
    USE [NYCTaxi_Sample]
    GO

    SET ANSI_NULLS ON
    GO
    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'PredictTipSingleMode')
    DROP PROCEDURE v
    GO

    CREATE PROCEDURE [dbo].[PredictTipSingleMode] @passenger_count int = 0,
    @trip_distance float = 0,
    @trip_time_in_secs int = 0,
    @pickup_latitude float = 0,
    @pickup_longitude float = 0,
    @dropoff_latitude float = 0,
    @dropoff_longitude float = 0
    AS
    BEGIN
      DECLARE @inquery nvarchar(max) = N'
        SELECT * FROM [dbo].[fnEngineerFeatures](@passenger_count, @trip_distance, @trip_time_in_secs, @pickup_latitude, @pickup_longitude, @dropoff_latitude, @dropoff_longitude)'
      DECLARE @lmodel2 varbinary(max) = (SELECT TOP 1 model FROM nyc_taxi_models);

      EXEC sp_execute_external_script @language = N'R',  @script = N'
            mod <- unserialize(as.raw(model));
            print(summary(mod))
            OutputDataSet<-rxPredict(
              modelObject = mod,
              data = InputDataSet,
              outData = NULL,
              predVarNames = "Score",
              type = "response",
              writeModelVars = FALSE,
              overwrite = TRUE);
            str(OutputDataSet)
            print(OutputDataSet)
            ',
      @input_data_1 = @inquery,
      @params = N'
      -- passthrough columns
      @model varbinary(max) ,
      @passenger_count int ,
      @trip_distance float ,
      @trip_time_in_secs int ,
      @pickup_latitude float ,
      @pickup_longitude float ,
      @dropoff_latitude float ,
      @dropoff_longitude float',
      -- mapped variables
      @model = @lmodel2 ,
      @passenger_count =@passenger_count ,
      @trip_distance=@trip_distance ,
      @trip_time_in_secs=@trip_time_in_secs ,
      @pickup_latitude=@pickup_latitude ,
      @pickup_longitude=@pickup_longitude ,
      @dropoff_latitude=@dropoff_latitude ,
      @dropoff_longitude=@dropoff_longitude
      WITH RESULT SETS ((Score float));
    END
    ```

2. SQL Server Management Studio では、 **EXEC**プロシージャ ( [!INCLUDE[tsql](../../includes/tsql-md.md)]または**EXECUTE**) を使用してストアドプロシージャを呼び出し、必要な入力を渡すことができます。 たとえば、Management Studio で次のステートメントを実行してみます。

    ```sql
    EXEC [dbo].[PredictTipSingleMode] 1, 2.5, 631, 40.763958,-73.973373, 40.782139,-73.977303
    ```

    ここで渡される値は、各変数 _\_乗客の数_、 _trip_distance_、_旅行\_時間\_(秒単位\__ )、_ピックアップ\_緯度_、_集荷\_経度_、 _降車\_緯度_、および_降車\_経度_。

3. この同じ呼び出しを R コードから実行するには、次のようなストアドプロシージャの呼び出し全体を含む R 変数を定義するだけです。

    ```R
    q2 = "EXEC PredictTipSingleMode 1, 2.5, 631, 40.763958,-73.973373, 40.782139,-73.977303 ";
    ```

    ここで渡される値は、それぞれ、_乗客\_数_、 _\_旅行距離_、 _\_旅行時間\_(秒単位\__ )、_ピックアップ\_緯度_、_集配\_経度_、_降車\_緯度_、および_降車\_経度_。

4. ( `sqlQuery` **RODBC**パッケージから) を呼び出し、ストアドプロシージャ呼び出しを含む文字列変数と共に接続文字列を渡します。

    ```R
    # predict with stored procedure in single mode
    sqlQuery (conn, q2);
    ```

    >[!TIP]
    > R Tools for Visual Studio (RTVS) は、SQL Server と R の両方との優れた統合を提供します。SQL Server 接続での RODBC の使用例については、こちらの記事を参照してください。[SQL Server と R の使用](https://docs.microsoft.com/visualstudio/rtvs/sql-server)

## <a name="next-steps"></a>次の手順

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]データを操作し、トレーニング済みの R モデルをに[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]永続化する方法を学習したので、このデータセットに基づいて新しいモデルを作成するのは比較的簡単です。 たとえば、次のような追加のモデルを作成することができます。

+ チップの金額を予測する回帰モデル
+ チップがビッグ、中、または小であるかどうかを予測する多クラス分類モデル

また、次のような追加のサンプルとリソースを調べることもできます。

+ [データ サイエンスのシナリオとソリューション テンプレート](data-science-scenarios-and-solution-templates.md)
+ [高度な分析 (データベース内)](sqldev-in-database-r-for-sql-developers.md)
+ [Machine Learning Server ハウツーガイド](https://docs.microsoft.com/machine-learning-server/r/how-to-introduction)
+ [その他のリソースの Machine Learning Server](https://docs.microsoft.com//machine-learning-server/resources-more)
