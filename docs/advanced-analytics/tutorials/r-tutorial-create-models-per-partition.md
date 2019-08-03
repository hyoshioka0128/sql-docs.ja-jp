---
title: R でのパーティションベースのモデルの作成、トレーニング、およびスコア付けに関するチュートリアル
description: SQL Server machine learning のパーティションベースのモデリング機能を使用するときに、動的に作成されるパーティション分割されたデータをモデル化、トレーニング、および使用する方法について説明します。
ms.custom: sqlseattle
ms.prod: sql
ms.technology: machine-learning
ms.date: 03/27/2019
ms.topic: tutorial
ms.author: davidph
author: dphansen
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 04393e7a43ef240fb8a48de49352b183d79a9208
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714738"
---
# <a name="tutorial-create-partition-based-models-in-r-on-sql-server"></a>チュートリアル:SQL Server の R でパーティションベースのモデルを作成する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server 2019 では、パーティションベースのモデリングは、パーティション分割されたデータに対してモデルを作成およびトレーニングする機能です。 地理的地域、日付と時刻、年齢、性別など、特定の分類法に自然に分割されている層化データの場合、データセット全体に対してスクリプトを実行できます。また、そのまま残るパーティションをモデル化、トレーニング、およびスコア付けすることもできます。すべての操作を行います。 

パーティションベースのモデリングは、 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)の2つの新しいパラメーターによって有効になります。

+ **input_data_1_partition_by_columns**。パーティション分割する列を指定します。
+ **input_data_1_order_by_columns**並べ替える列を指定します。 

このチュートリアルでは、従来の NYC タクシーサンプルデータと R スクリプトを使用したパーティションベースのモデリングについて説明します。 パーティション列は支払い方法です。

> [!div class="checklist"]
> * パーティションは支払いの種類 (5) に基づいています。
> * 各パーティションでモデルを作成およびトレーニングし、データベースにオブジェクトを格納します。
> * 各パーティションモデルのチップの結果の確率を予測します。その目的のために予約されているサンプルデータを使用します。

## <a name="prerequisites"></a>必須コンポーネント
 
このチュートリアルを完了するには、次のものが必要です。

+ 十分なシステムリソース。 データセットは大規模で、トレーニング操作はリソースを大量に消費します。 可能であれば、少なくとも 8 GB の RAM を搭載したシステムを使用してください。 または、小さいデータセットを使用して、リソースの制約を回避することもできます。 データセットを減らすための手順はインラインです。 

+ [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)など、t-sql クエリを実行するためのツールです。

+ [NYCTaxi_Sample。](https://sqlmldoccontent.blob.core.windows.net/sqlml/NYCTaxi_Sample.bak)これを[ダウンロードし](demo-data-nyctaxi-in-sql.md)て、ローカルデータベースエンジンインスタンスに復元することができます。 ファイルサイズは約 90 MB です。

+ Machine Learning Services と R の統合を使用して、SQL Server 2019 のデータベースエンジンインスタンスをプレビューします。

クエリツールで t-sql **`SELECT @@Version`** クエリとしてを実行してバージョンを確認します。 出力は、"Microsoft SQL Server 2019 (CTP 2.4)-15.0" になります。

データベースエンジンインスタンスと共に現在インストールされているすべての R パッケージの適切な形式の一覧を返すことにより、R パッケージの可用性を確認します。

```sql
EXECUTE sp_execute_external_script
  @language=N'R',
  @script = N'str(OutputDataSet);
  packagematrix <- installed.packages();
  Name <- packagematrix[,1];
  Version <- packagematrix[,3];
  OutputDataSet <- data.frame(Name, Version);',
  @input_data_1 = N''
WITH RESULT SETS ((PackageName nvarchar(250), PackageVersion nvarchar(max) ))
```

## <a name="connect-to-the-database"></a>データベースに接続する

Management Studio を起動し、データベースエンジンインスタンスに接続します。 オブジェクトエクスプローラーで、 [NYCTaxi_Sample データベース](demo-data-nyctaxi-in-sql.md)が存在することを確認します。 

## <a name="create-calculatedistance"></a>CalculateDistance の作成

デモデータベースには距離を計算するためのスカラー関数が用意されていますが、ストアドプロシージャはテーブル値関数によってより適切に動作します。 次のスクリプトを実行して、後で[トレーニング手順](#training-step)で使用する**CalculateDistance**関数を作成します。

関数が作成されたことを確認するには、オブジェクトエクスプローラーの**NYCTaxi_Sample**データベースで \Programmability\Functions\Table-valued 関数を確認します。

```sql
USE NYCTaxi_sample
GO

SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE FUNCTION [dbo].[CalculateDistance] (
    @Lat1 FLOAT
    ,@Long1 FLOAT
    ,@Lat2 FLOAT
    ,@Long2 FLOAT
    )
    -- User-defined function calculates the direct distance between two geographical coordinates.
RETURNS TABLE
AS
RETURN

SELECT COALESCE(3958.75 * ATAN(SQRT(1 - POWER(t.distance, 2)) / nullif(t.distance, 0)), 0) AS direct_distance
FROM (
    VALUES (CAST((SIN(@Lat1 / 57.2958) * SIN(@Lat2 / 57.2958)) + (COS(@Lat1 / 57.2958) * COS(@Lat2 / 57.2958) * COS((@Long2 / 57.2958) - (@Long1 / 57.2958))) AS DECIMAL(28, 10)))
    ) AS t(distance)
GO
 ```

## <a name="define-a-procedure-for-creating-and-training-per-partition-models"></a>パーティションごとのモデルを作成およびトレーニングする手順を定義する

このチュートリアルでは、ストアドプロシージャで R スクリプトをラップします。 この手順では、R を使用して入力データセットを作成し、ヒントの結果を予測する分類モデルを作成して、データベースにモデルを格納するストアドプロシージャを作成します。

このスクリプトで使用されるパラメーター入力の中で、 **input_data_1_partition_by_columns**と**input_data_1_order_by_columns**が表示されます。 これらのパラメーターは、パーティション分割されたモデリングを実行するメカニズムであることを思い出してください。 パラメーターは[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)に入力として渡され、外部スクリプトがパーティションごとに1回実行されているパーティションを処理します。 

このストアドプロシージャでは、完了までの時間を短縮するために[並列処理を使用](#parallel)します。

このスクリプトを実行すると、オブジェクトエクスプローラーの**NYCTaxi_Sample**データベースの \Programmability\Stored プロシージャに**train_rxLogIt_per_partition**が表示されます。 また、モデルの格納に使用される新しいテーブルである**nyctaxi_models**も表示されます。

```sql
USE NYCTaxi_Sample
GO

CREATE
    OR

ALTER PROCEDURE [dbo].[train_rxLogIt_per_partition] (@input_query NVARCHAR(max))
AS
BEGIN
    DECLARE @start DATETIME2 = SYSDATETIME()
        ,@model_generation_duration FLOAT
        ,@model VARBINARY(max)
        ,@instance_name NVARCHAR(100) = @@SERVERNAME
        ,@database_name NVARCHAR(128) = db_name();

    EXEC sp_execute_external_script @language = N'R'
        ,@script = 
        N'
    
    # Make sure InputDataSet is not empty. In parallel mode, if one thread gets zero data, an error occurs
    if (nrow(InputDataSet) > 0) {
    # Define the connection string
    connStr <- paste("Driver=SQL Server;Server=", instance_name, ";Database=", database_name, ";Trusted_Connection=true;", sep="");
    
    # build classification model to predict a tip outcome
    duration <- system.time(logitObj <- rxLogit(tipped ~ passenger_count + trip_distance + trip_time_in_secs + direct_distance, data = InputDataSet))[3];

    # First, serialize a model to and put it into a database table
    modelbin <- as.raw(serialize(logitObj, NULL));

    # Create the data source. To reduce data size, add rowsPerRead=500000 to cut the dataset by half.
    ds <- RxOdbcData(table="ml_models", connectionString=connStr);

    # Store the model in the database
    model_name <- paste0("nyctaxi.", InputDataSet[1,]$payment_type);
    
    rxWriteObject(ds, model_name, modelbin, version = "v1",
    keyName = "model_name", valueName = "model_object", versionName = "model_version", overwrite = TRUE, serialize = FALSE);
    } 
    
    '
        ,@input_data_1 = @input_query
        ,@input_data_1_partition_by_columns = N'payment_type'
        ,@input_data_1_order_by_columns = N'passenger_count'
        ,@parallel = 1
        ,@params = N'@instance_name nvarchar(100), @database_name nvarchar(128)'
        ,@instance_name = @instance_name
        ,@database_name = @database_name
    WITH RESULT SETS NONE
END;
GO
```

<a name="parallel"></a>

### <a name="parallel-execution"></a>並列実行

[Sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)入力には、並列処理を有効にするために使用される **@parallel= 1**が含まれていることに注意してください。 以前のリリースとは異なり、SQL Server 2019 では、  **@parallel= 1**に設定すると、クエリオプティマイザーに対してより強力なヒントが提供されるため、並列実行ではより多くの結果が得られます。

既定では、256行を超えるテーブルでは、クエリオプティマイザーは **@parallel= 1**で動作する傾向がありますが、このスクリプトで示すように **@parallel= 1**を設定して明示的に処理できます。

> [!Tip]
> トレーニング作業では、Microsoft rx 以外の **@parallel** アルゴリズムを使用している場合でも、任意のトレーニングスクリプトと共にを使用できます。 通常、SQL Server のトレーニングシナリオでは、RevoScaleR アルゴリズム (rx プレフィックスを持つ) のみが並列処理を提供します。 ただし、新しいパラメーターを使用すると、その機能を使用して特に設計されていないオープンソースの R 関数を含む関数を呼び出すスクリプトを並列化できます。 これは、パーティションが特定のスレッドに関係しているため、スクリプト内で呼び出されるすべての操作は、指定されたスレッドでパーティション単位で実行されるためです。

<a name="training-step"></a>

## <a name="run-the-procedure-and-train-the-model"></a>プロシージャを実行してモデルをトレーニングする

このセクションでは、スクリプトによって、前の手順で作成および保存したモデルをトレーニングします。 次の例は、モデルをトレーニングするための2つの方法を示しています。データセット全体を使用するか、部分的なデータを使用します。 

この手順には時間がかかることが予想されます。 トレーニングはかなりの時間がかかり、完了までに数分かかります。 システムリソース (特にメモリ) が負荷に対して十分でない場合は、データのサブセットを使用します。 2番目の例では、という構文を使用します。

```sql
--Example 1: train on entire dataset
EXEC train_rxLogIt_per_partition N'
SELECT payment_type, tipped, passenger_count, trip_time_in_secs, trip_distance, d.direct_distance
  FROM dbo.nyctaxi_sample CROSS APPLY [CalculateDistance](pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as d
';
GO
```

```sql
--Example 2: Train on 20 percent of the dataset to expedite processing.
EXEC train_rxLogIt_per_partition N'
  SELECT tipped, payment_type, passenger_count, trip_time_in_secs, trip_distance, d.direct_distance
  FROM dbo.nyctaxi_sample TABLESAMPLE (20 PERCENT) REPEATABLE (98074)
  CROSS APPLY [CalculateDistance](pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as d
';
GO
```

> [!NOTE]
> 他のワークロードを実行している場合`OPTION(MAXDOP 2)`は、クエリ処理を2コアのみに制限する場合は、SELECT ステートメントにを追加できます。

## <a name="check-results"></a>結果の確認

モデルテーブルの結果は5つの異なるモデルで、5つの支払いの種類でセグメント化された5つのパーティションに基づいている必要があります。 モデルは、 **ml_models**データソースに含まれています。

```sql
SELECT *
FROM ml_models
```
 
## <a name="define-a-procedure-for-predicting-outcomes"></a>結果を予測するためのプロシージャを定義する

同じパラメーターを使用してスコアリングを行うことができます。 次のサンプルには、現在処理中のパーティションに対して正しいモデルを使用してスコア付けする R スクリプトが含まれています。

前と同様に、R コードをラップするストアドプロシージャを作成します。

```sql
USE NYCTaxi_Sample
GO

-- Stored procedure that scores per partition. 
-- Depending on the partition being processed, a model specific to that partition will be used
CREATE
    OR

ALTER PROCEDURE [dbo].[predict_per_partition]
AS
BEGIN
    DECLARE @predict_duration FLOAT
        ,@instance_name NVARCHAR(100) = @@SERVERNAME
        ,@database_name NVARCHAR(128) = db_name()
        ,@input_query NVARCHAR(max);

    SET @input_query = 'SELECT tipped, passenger_count, trip_time_in_secs, trip_distance, d.direct_distance, payment_type
                          FROM dbo.nyctaxi_sample TABLESAMPLE (1 PERCENT) REPEATABLE (98074)
                          CROSS APPLY [CalculateDistance](pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as d'

    EXEC sp_execute_external_script @language = N'R'
        ,@script = 
        N'
    
    if (nrow(InputDataSet) > 0) {

    #Get the partition that is currently being processed
    current_partition <- InputDataSet[1,]$payment_type;

    #Create the SQL query to select the right model
    query_getModel <- paste0("select model_object from ml_models where model_name = ", "''", "nyctaxi.",InputDataSet[1,]$payment_type,"''", ";")
    

    # Define the connection string
    connStr <- paste("Driver=SQL Server;Server=", instance_name, ";Database=", database_name, ";Trusted_Connection=true;", sep="");
        
    #Define data source to use for getting the model
    ds <- RxOdbcData(sqlQuery = query_getModel, connectionString = connStr)

    # Load the model
    modelbin <- rxReadObject(ds, deserialize = FALSE)
    # unserialize model
    logitObj <- unserialize(modelbin);

    # predict tipped or not based on model
    predictions <- rxPredict(logitObj, data = InputDataSet, overwrite = TRUE, type = "response", writeModelVars = TRUE
        , extraVarsToWrite = c("payment_type"));        
    OutputDataSet <- predictions
    
    } else {
        OutputDataSet <- data.frame(integer(), InputDataSet[,]);        
    }
    '
        ,@input_data_1 = @input_query
        ,@parallel = 1
        ,@input_data_1_partition_by_columns = N'payment_type'
        ,@params = N'@instance_name nvarchar(100), @database_name nvarchar(128)'
        ,@instance_name = @instance_name
        ,@database_name = @database_name
    WITH RESULT SETS((
                tipped_Pred INT
                ,payment_type VARCHAR(5)
                ,tipped INT
                ,passenger_count INT
                ,trip_distance FLOAT
                ,trip_time_in_secs INT
                ,direct_distance FLOAT
                ));
END;
GO
```

## <a name="create-a-table-to-store-predictions"></a>予測を格納するテーブルを作成する

```sql
CREATE TABLE prediction_results (
    tipped_Pred INT
    ,payment_type VARCHAR(5)
    ,tipped INT
    ,passenger_count INT
    ,trip_distance FLOAT
    ,trip_time_in_secs INT
    ,direct_distance FLOAT
    );

TRUNCATE TABLE prediction_results
GO
```

## <a name="run-the-procedure-and-save-predictions"></a>プロシージャを実行して予測を保存する

```sql
INSERT INTO prediction_results (
    tipped_Pred
    ,payment_type
    ,tipped
    ,passenger_count
    ,trip_distance
    ,trip_time_in_secs
    ,direct_distance
    )
EXECUTE [predict_per_partition]
GO
```

## <a name="view-predictions"></a>予測の表示

予測が格納されるので、単純なクエリを実行して結果セットを返すことができます。

```sql
SELECT *
FROM prediction_results;
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)を使用して、パーティション分割されたデータに対する操作を反復処理します。 ストアドプロシージャでの外部スクリプトの呼び出しと RevoScaleR 関数の使用の詳細については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [R と SQL Server のチュートリアル](walkthrough-data-science-end-to-end-walkthrough.md)

<!--
## Old intro

**(Not for production workloads)**

One of the more common approaches for executing R or Python code on SQL data is providing script as an input parameter to the [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) stored procedure. In this CTP release, SQL Server 2019 adds new parameters to `sp_execute_external_script` to process partitions with the external script executing once for every partition:

| Parameter | Usage |
|-----------|-------|
| **input_data_1_partition_by_columns** | Specifies which columns to partition by. |
| **input_data_1_order_by_columns** | Specifies which columns to order by.  |

Partitions are an organizational mechanism for stratified data that naturally segments into a given classification scheme. Common examples include partitioning by geographic region, by date and time, by age or gender, and so forth. Given the existence of partitioned data, you might want to execute script over the entire data set, with the ability to model, train, and score partitions that remain intact over all these operations. Calling `sp_execute_external_script` with the new parameters allows you to do just that.

You can run this operation in parallel by combining `partition_by` with `@parallel`. If the input query can be parallelized, set `@parallel=1` as part of your arguments to `sp_execute_external_script`. By default, the query optimizer operates under `@parallel=1` on tables having more than 256 rows.

When the scenario is training, one advantage is that any arbitrary training script, even those using non-Microsoft-rx algorithms, can be parallelized by also using the @parallel parameter. Typically, you would have to use RevoScaleR algorithms (with the rx prefix) to obtain parallelism in training scenarios in SQL Server. But with the new parameter, you can parallelize a script that calls functions not specifically engineered with that capability.
-->
