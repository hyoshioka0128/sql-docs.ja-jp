---
title: T-sql を使用した Python モデルのトレーニングと保存
description: SQL Server で Transact-sql を使用してモデルをトレーニングし、保存する方法を示す Python チュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/01/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 85115d55f10ebafde4fe2da9c0311425c5e870dc
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715340"
---
# <a name="train-and-save-a-python-model-using-t-sql"></a>T-sql を使用した Python モデルのトレーニングと保存
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、 [SQL 開発者向けのデータベース内 Python analytics](sqldev-in-database-python-for-sql-developers.md)のチュートリアルの一部です。 

この手順では、Python パッケージ**scikit-learn**と**revoscalepy**を使用して機械学習モデルをトレーニングする方法について説明します。 これらの Python ライブラリは、SQL Server Machine Learning Services と共に既にインストールされています。

モジュールを読み込み、必要な関数を呼び出して、SQL Server ストアドプロシージャを使用してモデルを作成およびトレーニングします。 このモデルには、前のレッスンで設計したデータ機能が必要です。 最後に、トレーニング済みのモデルを[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]テーブルに保存します。
 

## <a name="split-the-sample-data-into-training-and-testing-sets"></a>サンプルデータをトレーニングセットとテストセットに分割する

1. **PyTrainTestSplit**というストアドプロシージャを作成し、nyctaxi_sample テーブルのデータを nyctaxi_sample_training と nyctaxi_sample_testing の2つの部分に分割します。 

    このストアドプロシージャは既に作成されていますが、次のコードを実行して作成することもできます。

    ```sql
    DROP PROCEDURE IF EXISTS PyTrainTestSplit;
    GO

    CREATE PROCEDURE [dbo].[PyTrainTestSplit] (@pct int)
    AS
    
    DROP TABLE IF EXISTS dbo.nyctaxi_sample_training
    SELECT * into nyctaxi_sample_training FROM nyctaxi_sample WHERE (ABS(CAST(BINARY_CHECKSUM(medallion,hack_license)  as int)) % 100) < @pct
    
    DROP TABLE IF EXISTS dbo.nyctaxi_sample_testing
    SELECT * into nyctaxi_sample_testing FROM nyctaxi_sample
    WHERE (ABS(CAST(BINARY_CHECKSUM(medallion,hack_license)  as int)) % 100) > @pct
    GO
    ```

2. カスタム分割を使用してデータを分割するには、ストアドプロシージャを実行し、トレーニングセットに割り当てられたデータの割合を表す整数を入力します。 たとえば、次のステートメントでは、60% のデータがトレーニングセットに割り当てられます。

    ```sql
    EXEC PyTrainTestSplit 60
    GO
    ```

## <a name="build-a-logistic-regression-model"></a>ロジスティック回帰モデルを構築する

データの準備が完了したら、それを使用してモデルをトレーニングできます。 これを行うには、トレーニングデータテーブルの入力として、いくつかの Python コードを実行するストアドプロシージャを呼び出します。 このチュートリアルでは、2つのモデル (二項分類モデル) を作成します。

+ ストアドプロシージャ**PyTrainScikit**は、 **scikit-learn**パッケージを使用して tip 予測モデルを作成します。
+ ストアドプロシージャ**TrainTipPredictionModelRxPy**は、 **revoscalepy**パッケージを使用して tip 予測モデルを作成します。

各ストアドプロシージャは、指定した入力データを使用して、ロジスティック回帰モデルを作成およびトレーニングします。 すべての Python コードは、システムストアドプロシージャ[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)にラップされます。

新しいデータでモデルを再トレーニングしやすくするために、別のストアドプロシージャで sp_execute_exernal_script への呼び出しをラップし、新しいトレーニングデータをパラメーターとして渡します。 このセクションでは、そのプロセスについて説明します。

### <a name="pytrainscikit"></a>PyTrainScikit

1.  で[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]、新しい**クエリ**ウィンドウを開き、次のステートメントを実行してストアドプロシージャ**PyTrainScikit**を作成します。  ストアドプロシージャには入力データの定義が含まれているので、入力クエリを指定する必要はありません。

    ```sql
    DROP PROCEDURE IF EXISTS PyTrainScikit;
    GO

    CREATE PROCEDURE [dbo].[PyTrainScikit] (@trained_model varbinary(max) OUTPUT)
    AS
    BEGIN
    EXEC sp_execute_external_script
      @language = N'Python',
      @script = N'
    import numpy
    import pickle
    from sklearn.linear_model import LogisticRegression
    
    ##Create SciKit-Learn logistic regression model
    X = InputDataSet[["passenger_count", "trip_distance", "trip_time_in_secs", "direct_distance"]]
    y = numpy.ravel(InputDataSet[["tipped"]])
    
    SKLalgo = LogisticRegression()
    logitObj = SKLalgo.fit(X, y)
    
    ##Serialize model
    trained_model = pickle.dumps(logitObj)
    ',
    @input_data_1 = N'
    select tipped, fare_amount, passenger_count, trip_time_in_secs, trip_distance, 
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as direct_distance
    from nyctaxi_sample_training
    ',
    @input_data_1_name = N'InputDataSet',
    @params = N'@trained_model varbinary(max) OUTPUT',
    @trained_model = @trained_model OUTPUT;
    ;
    END;
    GO
    ```

2. 次の SQL ステートメントを実行して、トレーニング済みのモデル\_をテーブル nyc taxi_models に挿入します。

    ```sql
    DECLARE @model VARBINARY(MAX);
    EXEC PyTrainScikit @model OUTPUT;
    INSERT INTO nyc_taxi_models (name, model) VALUES('SciKit_model', @model);
    ```

    データの処理とモデルの調整には、数分かかることがあります。 Python の**stdout**ストリームにパイプされるメッセージは、の[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] **[メッセージ]** ウィンドウに表示されます。 例 :

    *外部スクリプトからの STDOUT メッセージ:* 
  *C:\Program ・ SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib\site-packages\revoscalepy*

3. *Nyc\_taxi_models*テーブルを開きます。 _model_列にシリアル化されたモデルを含む新しい行が 1 つ追加されます。

    *SciKit_model* *0x800363736B6C6561726E2E6C696E6561.* ...

### <a name="traintippredictionmodelrxpy"></a>TrainTipPredictionModelRxPy

このストアドプロシージャは新しい**revoscalepy**パッケージを使用します。これは Python 用の新しいパッケージです。 これには、R 言語の**RevoScaleR**パッケージ用に提供されているものと同様のオブジェクト、変換、およびアルゴリズムが含まれています。 

**Revoscalepy**を使用すると、リモートの計算コンテキストの作成、コンピューティングコンテキスト間でのデータの移動、データの変換、およびロジスティックや線形回帰、デシジョンツリーなどの一般的なアルゴリズムを使用した予測モデルのトレーニングを行うことができます。 詳細については、「 [revoscalepy module in SQL Server](../python/ref-py-revoscalepy.md) and [revoscalepy 関数リファレンス](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)」を参照してください。

1. で[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]、新しい**クエリ**ウィンドウを開き、次のステートメントを実行してストアドプロシージャ_TrainTipPredictionModelRxPy_を作成します。  ストアドプロシージャには既に入力データの定義が含まれているので、入力クエリを指定する必要はありません。

    ```sql
    DROP PROCEDURE IF EXISTS TrainTipPredictionModelRxPy;
    GO

    CREATE PROCEDURE [dbo].[TrainTipPredictionModelRxPy] (@trained_model varbinary(max) OUTPUT)
    AS
    BEGIN
    EXEC sp_execute_external_script 
      @language = N'Python',
      @script = N'
    import numpy
    import pickle
    from revoscalepy.functions.RxLogit import rx_logit
    
    ## Create a logistic regression model using rx_logit function from revoscalepy package
    logitObj = rx_logit("tipped ~ passenger_count + trip_distance + trip_time_in_secs + direct_distance", data = InputDataSet);
    
    ## Serialize model
    trained_model = pickle.dumps(logitObj)
    ',
    @input_data_1 = N'
    select tipped, fare_amount, passenger_count, trip_time_in_secs, trip_distance, 
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as direct_distance
    from nyctaxi_sample_training
    ',
    @input_data_1_name = N'InputDataSet',
    @params = N'@trained_model varbinary(max) OUTPUT',
    @trained_model = @trained_model OUTPUT;
    ;
    END;
    GO
    ```

    このストアドプロシージャは、モデルのトレーニングの一環として次の手順を実行します。

    - SELECT クエリはカスタムスカラー関数_fnCalculateDistance_を適用して、取得場所と削除場所の間の直接距離を計算します。 クエリの結果は、 `InputDataset`既定の Python 入力変数であるに格納されます。
    - バイナリ_変数は_*ラベル*または結果列として使用され、モデルは_passenger_count_、 _trip_distance_、 _trip_time_in_secs_、 _direct_distance_という特徴列を使用して適合します。
    - トレーニング済みのモデルはシリアル化され、Python `logitObj`変数に格納されます。 T-sql キーワードの出力を追加することにより、ストアドプロシージャの出力として変数を追加できます。 次の手順では、その変数を使用して、モデルのバイナリコードをデータベーステーブル_nyc_taxi_models_に挿入します。 このメカニズムにより、モデルの格納と再利用が容易になります。

2. 次のようにストアドプロシージャを実行して、トレーニング済みの**revoscalepy**モデルを*nyc_taxi_models*テーブルに挿入します。

    ```sql
    DECLARE @model VARBINARY(MAX);
    EXEC TrainTipPredictionModelRxPy @model OUTPUT;
    INSERT INTO nyc_taxi_models (name, model) VALUES('revoscalepy_model', @model);
    ```

    データの処理とモデルの調整には時間がかかることがあります。 Python の**stdout**ストリームにパイプされるメッセージは、の[!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] **[メッセージ]** ウィンドウに表示されます。 例 :

    *外部スクリプトからの STDOUT メッセージ:* 
  *C:\Program ・ SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib\site-packages\revoscalepy*

3. テーブル *nyc_taxi_models*を開きます。 _model_列にシリアル化されたモデルを含む新しい行が 1 つ追加されます。

    *revoscalepy_model* *0x8003637265766F7363616c....*

次の手順では、トレーニング済みのモデルを使用して予測を作成します。

## <a name="next-step"></a>次の手順

[ストアドプロシージャに埋め込まれた Python を使用した予測の実行](sqldev-py6-operationalize-the-model.md)

## <a name="previous-step"></a>前の手順

[T-SQL を使用してデータ機能を作成する](sqldev-py5-train-and-save-a-model-using-t-sql.md)
