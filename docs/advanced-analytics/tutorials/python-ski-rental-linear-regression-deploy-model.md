---
title: Python のチュートリアル:モデルの配置 (線形回帰)
description: このチュートリアルでは、SQL Server Machine Learning Services で Python と線形回帰を使用して、ski のレンタル数を予測します。 Machine Learning Services を使用して、Python で開発した線形回帰モデルを SQL Server データベースにデプロイします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/03/2019
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 08f5d19af93ab180c660a264d5aaabc538d527a5
ms.sourcegitcommit: ecb19d0be87c38a283014dbc330adc2f1819a697
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242520"
---
# <a name="python-tutorial-deploy-a-linear-regression-model-to-sql-server-machine-learning-services"></a>Python のチュートリアル:SQL Server Machine Learning Services に線形回帰モデルをデプロイする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この4部構成のチュートリアルシリーズの第4部では、Machine Learning Services を使用して、Python で開発された線形回帰モデルを SQL Server データベースにデプロイします。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * 機械学習モデルを生成するストアドプロシージャを作成する
> * データベーステーブルにモデルを格納する
> * モデルを使用して予測を行うストアドプロシージャを作成する
> * 新しいデータでモデルを実行する

[パート 1](python-ski-rental-linear-regression.md)では、サンプルデータベースを復元する方法を学習しました。

[パート 2](python-ski-rental-linear-regression-prepare-data.md)では、SQL Server から python データフレームにデータを読み込み、python でデータを準備する方法を学習しました。

[パート 3](python-ski-rental-linear-regression-train-model.md)では、Python で線形回帰機械学習モデルをトレーニングする方法について学習しました。

## <a name="prerequisites"></a>前提条件

* このチュートリアルの第4部では、[パート 1](python-ski-rental-linear-regression.md)とその前提条件を完了していることを前提としています。

## <a name="create-a-stored-procedure-that-generates-the-model"></a>モデルを生成するストアドプロシージャを作成する

ここで、開発した Python スクリプトを使用し、scikit-learn から LinearRegression を使用して線形回帰モデルをトレーニングおよび生成するストアドプロシージャ**generate_rental_rx_model**を作成します。

Azure Data Studio で次の T-sql ステートメントを実行し、モデルをトレーニングするストアドプロシージャを作成します。

```sql
-- Stored procedure that trains and generates a Python model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_py_model;
go
CREATE PROCEDURE generate_rental_py_model (@trained_model varbinary(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script
      @language = N'Python'
    , @script = N'
from sklearn.linear_model import LinearRegression
import pickle

df = rental_train_data

# Get all the columns from the dataframe.
columns = df.columns.tolist()

# Store the variable well be predicting on.
target = "RentalCount"

# Initialize the model class.
lin_model = LinearRegression()

# Fit the model to the training data.
lin_model.fit(df[columns], df[target])

# Before saving the model to the DB table, convert it to a binary object
trained_model = pickle.dumps(lin_model)'

, @input_data_1 = N'select "RentalCount", "Year", "Month", "Day", "WeekDay", "Snow", "Holiday" from dbo.rental_data where Year < 2015'
, @input_data_1_name = N'rental_train_data'
, @params = N'@trained_model varbinary(max) OUTPUT'
, @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>データベーステーブルにモデルを格納する

TutorialDB データベースにテーブルを作成し、そのテーブルにモデルを保存します。

1. Azure Data Studio で次の T-sql ステートメントを実行し、モデルの格納に使用される**rental_py_models**という名前のテーブルを作成します。

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS dbo.rental_py_models;
    GO
    CREATE TABLE dbo.rental_py_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY,
        model VARBINARY(MAX) NOT NULL
    );
    GO
    ```

1. モデル名**linear_model**を使用して、モデルをバイナリオブジェクトとしてテーブルに保存します。

    ```sql
    DECLARE @model VARBINARY(MAX);
    EXEC generate_rental_py_model @model OUTPUT;
    
    INSERT INTO rental_py_models (model_name, model) VALUES('linear_model', @model);
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>予測を行うストアドプロシージャを作成する

1. トレーニング済みのモデルと新しいデータのセットを使用して予測を行うストアドプロシージャ**py_predict_rentalcount**を作成します。 次の T-sql を Azure Data Studio で実行します。

    ```sql
    DROP PROCEDURE IF EXISTS py_predict_rentalcount;
    GO
    CREATE PROCEDURE py_predict_rentalcount (@model varchar(100))
    AS
    BEGIN
        DECLARE @py_model varbinary(max) = (select model from rental_py_models where model_name = @model);
    
        EXEC sp_execute_external_script
                    @language = N'Python',
                    @script = N'
    
    # Import the scikit-learn function to compute error.
    from sklearn.metrics import mean_squared_error
    import pickle
    import pandas as pd
    
    rental_model = pickle.loads(py_model)
    
    df = rental_score_data
    
    # Get all the columns from the dataframe.
    columns = df.columns.tolist()
    
    # Variable you will be predicting on.
    target = "RentalCount"
    
    # Generate the predictions for the test set.
    lin_predictions = rental_model.predict(df[columns])
    print(lin_predictions)
    
    # Compute error between the test predictions and the actual values.
    lin_mse = mean_squared_error(lin_predictions, df[target])
    #print(lin_mse)
    
    predictions_df = pd.DataFrame(lin_predictions)
    
    OutputDataSet = pd.concat([predictions_df, df["RentalCount"], df["Month"], df["Day"], df["WeekDay"], df["Snow"], df["Holiday"], df["Year"]], axis=1)
    '
    , @input_data_1 = N'Select "RentalCount", "Year" ,"Month", "Day", "WeekDay", "Snow", "Holiday"  from rental_data where Year = 2015'
    , @input_data_1_name = N'rental_score_data'
    , @params = N'@py_model varbinary(max)'
    , @py_model = @py_model
    with result sets (("RentalCount_Predicted" float, "RentalCount" float, "Month" float,"Day" float,"WeekDay" float,"Snow" float,"Holiday" float, "Year" float));
    
    END;
    GO
    ```

1. 予測を格納するテーブルを作成します。

    ```sql
    DROP TABLE IF EXISTS [dbo].[py_rental_predictions];
    GO

    CREATE TABLE [dbo].[py_rental_predictions](
     [RentalCount_Predicted] [int] NULL,
     [RentalCount_Actual] [int] NULL,
     [Month] [int] NULL,
     [Day] [int] NULL,
     [WeekDay] [int] NULL,
     [Snow] [int] NULL,
     [Holiday] [int] NULL,
     [Year] [int] NULL
    ) ON [PRIMARY]
    GO
    ```

1. このストアドプロシージャを実行してレンタルカウントを予測する

    ```sql
    --Insert the results of the predictions for test set into a table
    INSERT INTO py_rental_predictions
    EXEC py_predict_rentalcount 'linear_model';

    -- Select contents of the table
    SELECT * FROM py_rental_predictions;
    ```

これで、SQL Server Machine Learning Services にモデルを作成、トレーニング、および配置することができました。 その後、ストアドプロシージャでそのモデルを使用して、新しいデータに基づいて値を予測します。

## <a name="next-steps"></a>次の手順

このチュートリアルシリーズの第4部では、次の手順を完了しました。

* 機械学習モデルを生成するストアドプロシージャを作成する
* データベーステーブルにモデルを格納する
* モデルを使用して予測を行うストアドプロシージャを作成する
* 新しいデータでモデルを実行する

SQL Server Machine Learning Services での Python の使用の詳細については、次のページを参照してください。

+ [SQL Server Machine Learning Services の Python チュートリアル](sql-server-python-tutorials.md)