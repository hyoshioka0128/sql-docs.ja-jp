---
title: レッスン 3 R と T-sql を使用したモデルのトレーニングと保存
description: SQL Server ストアドプロシージャと T-sql 関数を使用して R モデルをトレーニング、シリアル化、および保存する方法を示すチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/16/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: f23f4c350855b71a3633587bb3c092988fe89fef
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715334"
---
# <a name="lesson-3-train-and-save-a-model-using-t-sql"></a>レッスン 3: T-sql を使用したモデルのトレーニングと保存
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、SQL Server で R を使用する方法に関する SQL 開発者向けのチュートリアルの一部です。

このレッスンでは、R を使用して機械学習モデルをトレーニングする方法について説明します。前のレッスンで作成したデータ機能を使用してモデルをトレーニングし、トレーニング済みのモデルを[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]テーブルに保存します。 この場合、R パッケージはと共[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]に既にインストールされているので、SQL からすべてを実行できます。

## <a name="create-the-stored-procedure"></a>ストアドプロシージャを作成する

T-sql から R を呼び出すときは、システムストアドプロシージャ[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)を使用します。 ただし、モデルの再トレーニングなど、頻繁に繰り返すプロセスでは、別のストアドプロシージャで sp_execute_exernal_script への呼び出しをカプセル化する方が簡単です。

1. で[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]、新しい**クエリ**ウィンドウを開きます。

2. 次のステートメントを実行して、ストアドプロシージャ**RxTrainLogitModel**を作成します。 このストアドプロシージャは、入力データを定義し、RevoScaleR の**rxLogit**を使用してロジスティック回帰モデルを作成します。

    ```sql
    CREATE PROCEDURE [dbo].[RxTrainLogitModel] (@trained_model varbinary(max) OUTPUT)
    
    AS
    BEGIN
      DECLARE @inquery nvarchar(max) = N'
        select tipped, fare_amount, passenger_count,trip_time_in_secs,trip_distance,
        pickup_datetime, dropoff_datetime,
        dbo.fnCalculateDistance(pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as direct_distance
        from nyctaxi_sample
        tablesample (70 percent) repeatable (98052)
    '
    
      EXEC sp_execute_external_script @language = N'R',
                                      @script = N'
    ## Create model
    logitObj <- rxLogit(tipped ~ passenger_count + trip_distance + trip_time_in_secs + direct_distance, data = InputDataSet)
    summary(logitObj)
    
    ## Serialize model 
    trained_model <- as.raw(serialize(logitObj, NULL));
    ',
      @input_data_1 = @inquery,
      @params = N'@trained_model varbinary(max) OUTPUT',
      @trained_model = @trained_model OUTPUT; 
    END
    GO
    ```

    - モデルをテストするために一部のデータが残っていることを確認するために、トレーニング目的で、データの 70% がタクシーデータテーブルからランダムに選択されます。

    - SELECT クエリによって、カスタムのスカラー関数 *fnCalculateDistance* が使用され、乗車位置と降車位置直線距離が計算されます。 クエリの結果は、 `InputDataset`既定の R 入力変数であるに格納されます。
  
    - R スクリプトは、ロジスティック回帰モデルを作成するために、に[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]含まれている拡張 R 関数の1つである rxLogit 関数を呼び出します。
  
        二項変数 _tipped_ が *ラベル* または結果列として使用され、モデルは、  _passenger_count_、 _trip_distance_、 _trip_time_in_secs_、および _direct_distance_の機能列を使用して調整されます。
  
    - R 変数`logitObj`に保存されたトレーニング済みのモデルはシリアル化され、出力パラメーターとして返されます。

## <a name="train-and-deploy-the-r-model-using-the-stored-procedure"></a>ストアドプロシージャを使用した R モデルのトレーニングとデプロイ

ストアドプロシージャには既に入力データの定義が含まれているので、入力クエリを指定する必要はありません。

1. R モデルをトレーニングしてデプロイするには、ストアドプロシージャを呼び出してデータベーステーブル_nyc_taxi_models_に挿入します。これにより、将来の予測に使用できるようになります。

    ```sql
    DECLARE @model VARBINARY(MAX);
    EXEC RxTrainLogitModel @model OUTPUT;
    INSERT INTO nyc_taxi_models (name, model) VALUES('RxTrainLogit_model', @model);
    ```

2. 次のメッセージのよう[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]に、R の**stdout**ストリームにパイプされるメッセージについては、の **[メッセージ]** ウィンドウを見てください。 

    "外部スクリプトからの STDOUT メッセージ:読み取られる行:1193025、処理された行数の合計:1193025、合計チャンク時間:0.093 秒 "

    また、個々の関数`rxLogit`に固有のメッセージが表示される場合もあります。これは、モデル作成の一環として生成された変数とテストメトリックを表示するものです。

3.  ステートメントが完了したら、 *nyc_taxi_models*テーブルを開きます。 データの処理とモデルの調整には時間がかかることがあります。

    1つの新しい行が追加されていることがわかります。これには、列_モデル_のシリアル化されたモデルと、列_名_に**RxTrainLogit_model**モデル名が含まれています。

    ```sql
    model                        name
    ---------------------------- ------------------
    0x580A00000002000302020....  RxTrainLogit_model
    ```

次の手順では、トレーニング済みのモデルを使用して予測を生成します。

## <a name="next-lesson"></a>次のレッスン

[レッスン 4:ストアドプロシージャで R モデルを使用して潜在的な結果を予測する](../tutorials/sqldev-operationalize-the-model.md)

## <a name="previous-lesson"></a>前のレッスン

[レッスン 2:R と T-sql 関数を使用したデータ機能の作成](..//tutorials/sqldev-create-data-features-using-t-sql.md)

