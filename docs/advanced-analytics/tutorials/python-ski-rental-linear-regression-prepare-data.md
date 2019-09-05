---
title: Python のチュートリアル:データの準備 (線形回帰)
description: このチュートリアルでは、SQL Server Machine Learning Services で Python と線形回帰を使用して、ski のレンタル数を予測します。 Python を使用して SQL Server データベースからデータを準備します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/03/2019
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: c6c4d5fb4ffc5049f7e1325267b7623dc195e9d8
ms.sourcegitcommit: ecb19d0be87c38a283014dbc330adc2f1819a697
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242500"
---
# <a name="python-tutorial-prepare-data-to-train-a-linear-regression-model-in-sql-server-machine-learning-services"></a>Python のチュートリアル:SQL Server Machine Learning Services で線形回帰モデルをトレーニングするためのデータを準備する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この4部構成のチュートリアルシリーズの第2部では、Python を使用して SQL Server データベースからデータを準備します。 このシリーズの後半で、このデータを使用して、SQL Server Machine Learning Services を含む Python で線形回帰モデルをトレーニングし、デプロイします。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * SQL Server データベースから **pandas** のデータフレームにデータを読み込む
> * いくつかの列を削除して Python でデータを準備する

[パート 1](python-ski-rental-linear-regression.md)では、サンプルデータベースを復元する方法を学習しました。

[パート 3](python-ski-rental-linear-regression-train-model.md)では、Python で線形回帰機械学習モデルをトレーニングする方法について説明します。

[パート 4](python-ski-rental-linear-regression-deploy-model.md)では、モデルを SQL Server に格納する方法について学習した後、パート2と3で開発した Python スクリプトからストアドプロシージャを作成します。 ストアドプロシージャは、新しいデータに基づいて予測を行うために SQL Server で実行されます。

## <a name="prerequisites"></a>前提条件

* このチュートリアルのパート2では、[パート 1](python-ski-rental-linear-regression.md)とその前提条件を完了していることを前提としています。

## <a name="explore-and-prepare-the-data"></a>データの探索と準備

Python でデータを使用するには、SQL Server データベースから、pandas のデータフレームにデータを読み込みます。

Azure Data Studio で新しい Python notebook を作成し、次のスクリプトを実行します。 を`<SQL Server>`独自の SQL Server 名に置き換えます。

次の Python スクリプトでは、データベースの**rental_data**テーブルから、pandas のデータフレーム**df**にデータセットをインポートします。

```python
import pandas as pd
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error
from revoscalepy import RxComputeContext, RxInSqlServer, RxSqlServerData
from revoscalepy import rx_import

# Connection string to your SQL Server instance
conn_str = 'Driver=SQL Server;Server=<SQL Server>;Database=TutorialDB;Trusted_Connection=True;'

# Define the columns you will import
 column_info = {
         "Year" : { "type" : "integer" },
         "Month" : { "type" : "integer" },
         "Day" : { "type" : "integer" },
         "RentalCount" : { "type" : "integer" },
         "WeekDay" : {
             "type" : "factor",
             "levels" : ["1", "2", "3", "4", "5", "6", "7"]
         },
         "Holiday" : {
             "type" : "factor",
             "levels" : ["1", "0"]
         },
         "Snow" : {
             "type" : "factor",
             "levels" : ["1", "0"]
         }
     }

# Get the data from the SQL Server table
data_source = RxSqlServerData(table="dbo.rental_data",
                               connection_string=conn_str, column_info=column_info)
computeContext = RxInSqlServer(
     connection_string = conn_str,
     num_tasks = 1,
     auto_cleanup = False
)

RxInSqlServer(connection_string=conn_str, num_tasks=1, auto_cleanup=False)

# import data source and convert to pandas dataframe
df = pd.DataFrame(rx_import(input_data = data_source))
print("Data frame:", df)

# Get all the columns from the dataframe.
columns = df.columns.tolist()

# Filter the columns to remove ones we don't want to use in the training
columns = [c for c in columns if c not in ["Year"]]
```

次のような結果が表示されます。

```results
Rows Processed: 453
Data frame:      Day  Holiday  Month  RentalCount  Snow  WeekDay  Year
0     20        1      1          445     2        2  2014
1     13        2      2           40     2        5  2014
2     10        2      3          456     2        1  2013
3     31        2      3           38     2        2  2014
4     24        2      4           23     2        5  2014
5     11        2      2           42     2        4  2015
6     28        2      4          310     2        1  2013
...
[453 rows x 7 columns]
```

## <a name="next-steps"></a>次の手順

このチュートリアルシリーズの第2部では、次の手順を完了しました。

* SQL Server データベースから **pandas** のデータフレームにデータを読み込む
* いくつかの列を削除して Python でデータを準備する

TutorialDB データベースのデータを使用する機械学習モデルをトレーニングするには、このチュートリアルシリーズの第3部に従います。

> [!div class="nextstepaction"]
> [Python のチュートリアル:線形回帰モデルをトレーニングする](python-ski-rental-linear-regression-train-model.md)