---
title: R および SQL Server 関数を使用したデータ機能の作成
description: データベース内分析用の SQL Server 関数を使用してデータ機能を作成する方法を示すチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/26/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: f12c20a54c0811e392eaa85684d7fac1a209c396
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714696"
---
# <a name="create-data-features-using-r-and-sql-server-walkthrough"></a>R と SQL Server を使用したデータ機能の作成 (チュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

データ エンジニアリングは、機械学習の重要な部分です。 データを予測モデリングに使用するには、多くの場合、変換が必要です。 必要な機能がデータにない場合、既存の値からデータをエンジニアリングできます。

このモデリング タスクでは、乗車位置と降車位置の未加工の緯度値と経度値を使用するのではなく、2 つの位置間の距離 (マイル単位) が必要です。 この機能を作成するには、 [haversine 式](https://en.wikipedia.org/wiki/Haversine_formula)を使用して、2つの点の間の直線距離を直接計算します。

この手順では、データから機能を作成するための2つの異なる方法について説明します。

> [!div class="checklist"]
> * カスタム R 関数の使用
> * でのカスタム T-sql 関数の使用[!INCLUDE[tsql](../../includes/tsql-md.md)]

目標は、元の列と[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]新しい数値の特徴である*direct_distance*を含む新しいデータセットを作成することです。

## <a name="prerequisites"></a>必須コンポーネント

この手順では、このチュートリアルの前の手順に基づいて、R セッションが継続的に実行されることを前提としています。 ここでは、これらの手順で作成した接続文字列とデータソースオブジェクトを使用します。 スクリプトの実行には、次のツールとパッケージが使用されます。

+ R コマンドを実行するための rgui .exe
+ T-sql の実行 Management Studio

## <a name="featurization-using-r"></a>R を使用した特性付け

R 言語は統計ライブラリが豊富なことで知られていますが、カスタム データの変換の作成も必要になることがあります。

まず、R ユーザーが使い慣れた方法を使用して、ノート pc にデータを取得してから、カスタム R 関数*Computedist*を実行します。この関数は、緯度と経度の値で指定された2点間の直線距離を計算します。

1. 前に作成したデータソースオブジェクトは上位の1000行のみを取得することに注意してください。 では、すべてのデータを取得するクエリを定義してみましょう。

    ```R
    bigQuery <- "SELECT tipped, fare_amount, passenger_count,trip_time_in_secs,trip_distance, pickup_datetime, dropoff_datetime,  pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude FROM nyctaxi_sample";
    ```

2. クエリを使用して、新しいデータソースオブジェクトを作成します。

    ```R
    featureDataSource <- RxSqlServerData(sqlQuery = bigQuery,colClasses = c(pickup_longitude = "numeric", pickup_latitude = "numeric", dropoff_longitude = "numeric", dropoff_latitude = "numeric", passenger_count  = "numeric", trip_distance  = "numeric", trip_time_in_secs  = "numeric", direct_distance  = "numeric"), connectionString = connStr);
    ```

    - [RxSqlServerData](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxsqlserverdata)は、 _sqlquery_パラメーターの引数として指定された有効な SELECT クエリ、または_table_パラメーターとして指定されたテーブルオブジェクトの名前を使用して、クエリを実行することができます。
    
    - テーブルからデータをサンプリングする場合は、 _Sqlquery_パラメーターを使用して、t-sql TABLESAMPLE 句を使用してサンプリングパラメーターを定義し、 _ROWBUFFERING_引数を FALSE に設定する必要があります。

3. 次のコードを実行して、カスタム R 関数を作成します。 ComputeDist は、緯度と経度の値の2つのペアを受け取り、両者の間の直線距離を計算して、距離をマイル単位で返します。

    ```R
    env <- new.env();
    env$ComputeDist <- function(pickup_long, pickup_lat, dropoff_long, dropoff_lat){
      R <- 6371/1.609344 #radius in mile
      delta_lat <- dropoff_lat - pickup_lat
      delta_long <- dropoff_long - pickup_long
      degrees_to_radians = pi/180.0
      a1 <- sin(delta_lat/2*degrees_to_radians)
      a2 <- as.numeric(a1)^2
      a3 <- cos(pickup_lat*degrees_to_radians)
      a4 <- cos(dropoff_lat*degrees_to_radians)
      a5 <- sin(delta_long/2*degrees_to_radians)
      a6 <- as.numeric(a5)^2
      a <- a2+a3*a4*a6
      c <- 2*atan2(sqrt(a),sqrt(1-a))
      d <- R*c
      return (d)
    }
    ```
  
    + 1 行目で新しい環境を定義します。 R では、環境を使用して、パッケージなどの名前空間をカプセル化できます。 `search()` 関数を使用すると、ワークスペース内の環境を確認できます。 特定の環境内にあるオブジェクトを確認するには、「 `ls(<envname>)`」と入力します。
    + `$env.ComputeDist` から始まる行には、ヘイバーサイン式を定義するコードが含まれています。この式で、球面上にある 2 点間の *大圏距離* を計算します。

4. 関数を定義したら、それをデータに適用して新しい特徴列*direct_distance*を作成します。 ただし、変換を実行する前に、コンピューティングコンテキストをローカルに変更します。

    ```R
    rxSetComputeContext("local");
    ```

5. [RxDataStep](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxdatastep)関数を呼び出して特徴エンジニアリングデータを取得し、 `env$ComputeDist`関数をメモリ内のデータに適用します。

    ```R
    start.time <- proc.time();
  
    changed_ds <- rxDataStep(inData = featureDataSource,
    transforms = list(direct_distance=ComputeDist(pickup_longitude,pickup_latitude, dropoff_longitude, dropoff_latitude),
    tipped = "tipped", fare_amount = "fare_amount", passenger_count = "passenger_count",
    trip_time_in_secs = "trip_time_in_secs",  trip_distance="trip_distance",
    pickup_datetime = "pickup_datetime",  dropoff_datetime = "dropoff_datetime"),
    transformEnvir = env,
    rowsPerRead=500,
    reportProgress = 3);
  
    used.time <- proc.time() - start.time;
    print(paste("It takes CPU Time=", round(used.time[1]+used.time[2],2)," seconds, Elapsed Time=", round(used.time[3],2), " seconds to generate features.", sep=""));
    ```

    + RxDataStep 関数は、データを適切に変更するためのさまざまなメソッドをサポートしています。 詳細については、次の記事を参照してください。[Microsft R でデータを変換およびサブセット化する方法](https://docs.microsoft.com/r-server/r/how-to-revoscaler-data-transform)
    
    しかし、rxDataStep について注目すべき点がいくつかあります。 
    
    他のデータソースでは、 *Varstokeep*および*varstokeep*引数を使用できますが、これらは SQL Server データソースではサポートされていません。 したがって、この例では、_変換_引数を使用して、パススルー列と変換された列の両方を指定しています。 また、SQL Server の計算コンテキストで実行する場合、 _Indata_引数は SQL Server データソースのみを受け取ることができます。

    上記のコードでは、より大きなデータセットに対して実行すると警告メッセージが生成される場合もあります。 行の数が、作成される列の数が設定値 (既定値は 300万) を超えた場合、rxDataStep は警告を返し、返されたデータフレーム内の行の数は切り捨てられます。 警告を削除するには、rxDataStep 関数の_maxRowsByCols_引数を変更します。 ただし、 _maxRowsByCols_が大きすぎる場合は、データフレームをメモリに読み込むときに問題が発生する可能性があります。

7. 必要に応じて、 [rxGetVarInfo](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxgetvarinfo)を呼び出して、変換されたデータソースのスキーマを検査できます。

    ```R
    rxGetVarInfo(data = changed_ds);
    ```

## <a name="featurization-using-transact-sql"></a>Transact-SQL を使用した特性付け

この演習では、カスタム R 関数の代わりに、SQL 関数を使用して同じタスクを実行する方法について説明します。 

[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)または別のクエリエディターに切り替えて、t-sql スクリプトを実行します。

1. *FnCalculateDistance*という名前の SQL 関数を使用します。 この関数は、NYCTaxi_Sample データベースに既に存在している必要があります。 オブジェクトエクスプローラーで、次のパスを移動して関数が存在することを確認します。データベース > NYCTaxi_Sample > プログラミング > 関数 > スカラー値関数 > dbo. fnCalculateDistance です。

    関数が存在しない場合は、SQL Server Management Studio を使用して、NYCTaxi_Sample データベースで関数を生成します。

    ```sql
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)
    -- User-defined function calculates the direct distance between two geographical coordinates.
    RETURNS decimal(28, 10)
    AS
    BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
    END
    ```

2. Management Studio の新しいクエリウィンドウで、がサポート[!INCLUDE[tsql](../../includes/tsql-md.md)] [!INCLUDE[tsql](../../includes/tsql-md.md)]する任意のアプリケーションから次のステートメントを実行して、関数の動作を確認します。

    ```sql
    USE nyctaxi_sample
    GO

    SELECT tipped, fare_amount, passenger_count,trip_time_in_secs,trip_distance, pickup_datetime, dropoff_datetime,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) as direct_distance, pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude 
    FROM nyctaxi_sample
    ```
3. 新しいテーブルに値を直接挿入する (最初に作成する必要があります) には、テーブル名を指定する**into**句を追加します。

    ```sql
    USE nyctaxi_sample
    GO

    SELECT tipped, fare_amount, passenger_count, trip_time_in_secs, trip_distance, pickup_datetime, dropoff_datetime,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) as direct_distance, pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude
    INTO NewFeatureTable
    FROM nyctaxi_sample
    ```

4. R コードから SQL 関数を呼び出すこともできます。 Rgui に戻り、SQL 特性付けクエリを R 変数に保存します。

    ```R
    featureEngineeringQuery = "SELECT tipped, fare_amount, passenger_count,
        trip_time_in_secs,trip_distance, pickup_datetime, dropoff_datetime,
        dbo.fnCalculateDistance(pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as direct_distance,
        pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude
        FROM nyctaxi_sample
        tablesample (1 percent) repeatable (98052)"
    ```
  
    > [!TIP]
    > このクエリは、このチュートリアルを高速化するために、データの小さなサンプルを取得するように変更されています。 すべてのデータを取得する場合は、TABLESAMPLE 句を削除できます。ただし、環境によっては、完全なデータセットを R に読み込むことができず、エラーが発生する可能性があります。
  
5. 次のコード行を使用して R 環境から [!INCLUDE[tsql](../../includes/tsql-md.md)] 関数を呼び出し、*featureEngineeringQuery* に定義されているデータに適用します。
  
    ```R
    featureDataSource = RxSqlServerData(sqlQuery = featureEngineeringQuery,
      colClasses = c(pickup_longitude = "numeric", pickup_latitude = "numeric",
        dropoff_longitude = "numeric", dropoff_latitude = "numeric",
        passenger_count  = "numeric", trip_distance  = "numeric",
        trip_time_in_secs  = "numeric", direct_distance  = "numeric"),
      connectionString = connStr)
    ```
  
6.  新しい機能が作成されたので、 **rxGetVarsInfo**を呼び出して、機能テーブル内のデータの概要を作成します。
  
    ```R
    rxGetVarInfo(data = featureDataSource)
    ```

    **結果**

    ```R
    Var 1: tipped, Type: integer
    Var 2: fare_amount, Type: numeric
    Var 3: passenger_count, Type: numeric
    Var 4: trip_time_in_secs, Type: numeric
    Var 5: trip_distance, Type: numeric
    Var 6: pickup_datetime, Type: character
    Var 7: dropoff_datetime, Type: character
    Var 8: direct_distance, Type: numeric
    Var 9: pickup_latitude, Type: numeric
    Var 10: pickup_longitude, Type: numeric
    Var 11: dropoff_latitude, Type: numeric
    Var 12: dropoff_longitude, Type: numeric
    ```

    > [!NOTE]
    > 場合によっては、次のようなエラーが表示されることがあります。*オブジェクト ' fnCalculateDistance ' で実行アクセス許可が拒否されました*その場合は、使用しているログインが、インスタンスだけではなく、スクリプトを実行し、データベースでオブジェクトを作成する権限を持っていることを確認してください。
    > オブジェクトのスキーマ (fnCalculateDistance) を確認します。 オブジェクトがデータベース所有者によって作成されており、ログインがロール db_datareader に属している場合は、スクリプトを実行するための明示的なアクセス許可をログインに付与する必要があります。

## <a name="comparing-r-functions-and-sql-functions"></a>R 関数と SQL 関数の比較

このコードを使用して、R コードを記述することを忘れないでください。

```R
start.time <- proc.time()
<your code here>
used.time <- proc.time() - start.time
print(paste("It takes CPU Time=", round(used.time[1]+used.time[2],2)," seconds, Elapsed Time=", round(used.time[3],2), " seconds to generate features.", sep=""))
```

これを SQL カスタム関数の例と共に使用して、SQL 関数を呼び出すときにデータ変換にかかる時間を確認することができます。 また、rxSetComputeContext を使用してコンピューティングコンテキストを切り替え、タイミングを比較してみてください。

ネットワークの速度やハードウェアの構成によっては、時間が大幅に異なる場合があります。 テストした構成では、 [!INCLUDE[tsql](../../includes/tsql-md.md)]関数のアプローチはカスタム R 関数を使用するよりも高速でした。 そのため、以降の手順[!INCLUDE[tsql](../../includes/tsql-md.md)]では、これらの計算に関数を使用します。

> [!TIP]
> 多くの場合、を使用[!INCLUDE[tsql](../../includes/tsql-md.md)]する特徴エンジニアリングは R よりも高速です。たとえば、T-sql には、ロール移動平均や*n*タイルなどの一般的なデータサイエンス計算に適用できる高速ウィンドウ関数と順位付け関数が含まれています。 データとタスクに基づいて、最も効率的な方法を選択してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [R モデルを構築して SQL に保存する](walkthrough-build-and-save-the-model.md)

