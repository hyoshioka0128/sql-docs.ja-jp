---
title: Python モデルを使用して潜在的な結果を予測する
description: T-sql 関数を使用し SQL Server ストアドプロシージャに埋め込まれた PYthon スクリプトを運用化する方法を示すチュートリアル
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/02/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: be80892db818bafdb45da974a064a0c5cf1fdc3f
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715349"
---
# <a name="run-predictions-using-python-embedded-in-a-stored-procedure"></a>ストアドプロシージャに埋め込まれた Python を使用した予測の実行
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、 [SQL 開発者向けのデータベース内 Python analytics](sqldev-in-database-python-for-sql-developers.md)のチュートリアルの一部です。 

この手順では、前の手順でトレーニングして保存したモデルを*運用化*する方法を学習します。

このシナリオでは、運用化とは、スコア付けのためにモデルを運用環境にデプロイすることを意味します。 SQL Server との統合により、Python コードをストアドプロシージャに埋め込むことができるため、これは非常に簡単になります。 新しい入力に基づいてモデルから予測を取得するには、アプリケーションからストアドプロシージャを呼び出し、新しいデータを渡します。

このレッスンでは、1つの Python モデルに基づいて予測を作成する2つの方法を示します。バッチスコアリングと、行ごとのスコア付けです。

- **バッチスコアリング:** 入力データの複数の行を指定するには、SELECT クエリを引数としてストアドプロシージャに渡します。 結果は、入力したケースに対応する観測のテーブルになります。
- **個々のスコア付け:** 個別のパラメーター値のセットを入力として渡します。  このストアド プロシージャは、1 つの行または値を返します。

スコアリングに必要なすべての Python コードは、ストアドプロシージャの一部として提供されています。

## <a name="batch-scoring"></a>バッチスコアリング

最初の2つのストアドプロシージャは、ストアドプロシージャで Python 予測呼び出しをラップするための基本的な構文を示しています。 どちらのストアドプロシージャでも、入力としてデータのテーブルが必要です。

- 使用するモデルの正確な名前は、ストアドプロシージャへの入力パラメーターとして提供されます。 ストアドプロシージャは、ストアドプロシージャの SELECT ステートメントを使用`nyc_taxi_models`して、データベーステーブルからシリアル化されたモデルを読み込みます。
- シリアル化されたモデルは python 変数`mod`に格納され、python を使用してさらに処理を行うことができます。
- スコア付けが必要な新しいケースは、「」で[!INCLUDE[tsql](../../includes/tsql-md.md)] `@input_data_1`指定されたクエリから取得されます。 クエリ データが読み取られると、行が既定のデータ フレーム `InputDataSet`に保存されます。
- どちらのストアドプロシージャも、 `sklearn`の関数を使用して精度メトリック ([曲線] の下の領域) を計算します。 # C などの精度メトリックは、ターゲットラベル (_キャッチ_された列) も指定する場合にのみ生成できます。 予測にはターゲットラベル (変数`y`) は必要ありませんが、精度メトリックの計算では、

    したがって、スコア付けするデータのターゲットラベルがない場合は、ストアドプロシージャを変更して、c の計算を削除し、特徴 (ストアドプロシージャの変数`X` ) からチップの確率のみを返すことができます。

### <a name="predicttipscikitpy"></a>PredictTipSciKitPy

次の T-sql ステートメントを実行して、ストアドプロシージャを作成します。 このストアドプロシージャでは、そのパッケージに固有の関数が使用されるため、scikit-learn パッケージに基づくモデルが必要です。

+ 入力を含むデータフレームは、ロジスティック回帰`predict_proba` `mod`モデルの関数に渡されます。 関数 (`probArray = mod.predict_proba(X)`) は、チップ (任意の金額) が指定される確率を表す float を返します。 `predict_proba`

```sql
DROP PROCEDURE IF EXISTS PredictTipSciKitPy;
GO

CREATE PROCEDURE [dbo].[PredictTipSciKitPy] (@model varchar(50), @inquery nvarchar(max))
AS
BEGIN
DECLARE @lmodel2 varbinary(max) = (select model from nyc_taxi_models where name = @model);
EXEC sp_execute_external_script
  @language = N'Python',
  @script = N'
import pickle;
import numpy;
from sklearn import metrics

mod = pickle.loads(lmodel2)
X = InputDataSet[["passenger_count", "trip_distance", "trip_time_in_secs", "direct_distance"]]
y = numpy.ravel(InputDataSet[["tipped"]])

probArray = mod.predict_proba(X)
probList = []
for i in range(len(probArray)):
  probList.append((probArray[i])[1])

probArray = numpy.asarray(probList)
fpr, tpr, thresholds = metrics.roc_curve(y, probArray)
aucResult = metrics.auc(fpr, tpr)
print ("AUC on testing data is: " + str(aucResult))

OutputDataSet = pandas.DataFrame(data = probList, columns = ["predictions"])
',  
  @input_data_1 = @inquery,
  @input_data_1_name = N'InputDataSet',
  @params = N'@lmodel2 varbinary(max)',
  @lmodel2 = @lmodel2
WITH RESULT SETS ((Score float));
END
GO
```

### <a name="predicttiprxpy"></a>PredictTipRxPy

このストアドプロシージャは同じ入力を使用し、前のストアドプロシージャと同じ種類のスコアを作成しますが、SQL Server machine learning で提供される**revoscalepy**パッケージの関数を使用します。

```sql
DROP PROCEDURE IF EXISTS PredictTipRxPy;
GO

CREATE PROCEDURE [dbo].[PredictTipRxPy] (@model varchar(50), @inquery nvarchar(max))
AS
BEGIN
DECLARE @lmodel2 varbinary(max) = (select model from nyc_taxi_models where name = @model);
EXEC sp_execute_external_script 
  @language = N'Python',
  @script = N'
import pickle;
import numpy;
from sklearn import metrics
from revoscalepy.functions.RxPredict import rx_predict;

mod = pickle.loads(lmodel2)
X = InputDataSet[["passenger_count", "trip_distance", "trip_time_in_secs", "direct_distance"]]
y = numpy.ravel(InputDataSet[["tipped"]])

probArray = rx_predict(mod, X)
probList = probArray["tipped_Pred"].values 

probArray = numpy.asarray(probList)
fpr, tpr, thresholds = metrics.roc_curve(y, probArray)
aucResult = metrics.auc(fpr, tpr)
print ("AUC on testing data is: " + str(aucResult))

OutputDataSet = pandas.DataFrame(data = probList, columns = ["predictions"])
',  
  @input_data_1 = @inquery,
  @input_data_1_name = N'InputDataSet',
  @params = N'@lmodel2 varbinary(max)',
  @lmodel2 = @lmodel2
WITH RESULT SETS ((Score float));
END
GO
```

## <a name="run-batch-scoring-using-a-select-query"></a>SELECT クエリを使用したバッチスコアリングの実行

ストアドプロシージャ**PredictTipSciKitPy**と**PredictTipRxPy**には、次の2つの入力パラメーターが必要です。 

- スコアリングのためにデータを取得するクエリ
- トレーニング済みのモデルの名前

これらの引数をストアドプロシージャに渡すことで、特定のモデルを選択したり、スコアリングに使用するデータを変更したりできます。

1. スコアリングに**scikit-learn**モデルを使用するには、ストアドプロシージャ**PredictTipSciKitPy**を呼び出して、モデル名とクエリ文字列を入力として渡します。

    ```sql
    DECLARE @query_string nvarchar(max) -- Specify input query
      SET @query_string='
      select tipped, fare_amount, passenger_count, trip_time_in_secs, trip_distance,
      dbo.fnCalculateDistance(pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as direct_distance
      from nyctaxi_sample_testing'
    EXEC [dbo].[PredictTipSciKitPy] 'SciKit_model', @query_string;
    ```

    このストアドプロシージャは、入力クエリの一部として渡された各トリップの予測確率を返します。 
    
    クエリの実行に SSMS (SQL Server Management Studio) を使用している場合、確率は**結果**ペインにテーブルとして表示されます。 **メッセージ** ウィンドウには、精度のメトリック (曲線 の下にある 領域) が出力され、0.56 の値が表示されます。

2. スコアリングに**revoscalepy**モデルを使用するには、ストアドプロシージャ**PredictTipRxPy**を呼び出して、モデル名とクエリ文字列を入力として渡します。

    ```sql
    DECLARE @query_string nvarchar(max) -- Specify input query
      SET @query_string='
      select tipped, fare_amount, passenger_count, trip_time_in_secs, trip_distance,
      dbo.fnCalculateDistance(pickup_latitude, pickup_longitude,  dropoff_latitude, dropoff_longitude) as direct_distance
      from nyctaxi_sample_testing'
    EXEC [dbo].[PredictTipRxPy] 'revoscalepy_model', @query_string;
    ```

## <a name="single-row-scoring"></a>単一行のスコアリング

バッチスコアリングではなく、1つのケースを渡し、アプリケーションから値を取得し、それらの値に基づいて1つの結果を返すことが必要になる場合があります。 たとえば、Excel ワークシート、web アプリケーション、またはレポートを設定して、ストアドプロシージャを呼び出し、ユーザーが入力または選択した入力に渡すことができます。

このセクションでは、次の2つのストアドプロシージャを呼び出して単一の予測を作成する方法について説明します。

+ [PredictTipSingleModeSciKitPy](#predicttipsinglemodescikitpy)は、scikit-learn モデルを使用して単一行スコアリングを目的として設計されています。
+ [PredictTipSingleModeRxPy](#predicttipsinglemoderxpy)は、revoscalepy モデルを使用して単一行スコアリングを目的として設計されています。
+ まだモデルをトレーニングしていない場合は、[手順 5](sqldev-py5-train-and-save-a-model-using-t-sql.md). に戻ります。

どちらのモデルも、乗客数、旅行距離などの一連の単一値を入力として受け取ります。 テーブル値関数`fnEngineerFeatures`は、緯度と経度の値を入力から新しい特徴である直行距離に変換するために使用されます。 [レッスン 4](sqldev-py4-create-data-features-using-t-sql.md)には、このテーブル値関数の説明が含まれています。

どちらのストアドプロシージャも、Python モデルに基づいてスコアを作成します。

> [!NOTE]
> 
> 外部アプリケーションからストアドプロシージャを呼び出すときに、Python モデルに必要なすべての入力機能を提供することが重要です。 エラーを回避するには、データ型とデータ長を検証するだけでなく、入力データを Python データ型にキャストまたは変換することが必要になる場合があります。

### <a name="predicttipsinglemodescikitpy"></a>PredictTipSingleModeSciKitPy

**Scikit-learn**モデルを使用してスコアリングを実行するストアドプロシージャのコードを確認します。

```sql
DROP PROCEDURE IF EXISTS PredictTipSingleModeSciKitPy;
GO

CREATE PROCEDURE [dbo].[PredictTipSingleModeSciKitPy] (@model varchar(50), @passenger_count int = 0,
  @trip_distance float = 0,
  @trip_time_in_secs int = 0,
  @pickup_latitude float = 0,
  @pickup_longitude float = 0,
  @dropoff_latitude float = 0,
  @dropoff_longitude float = 0)
AS
BEGIN
  DECLARE @inquery nvarchar(max) = N'
  SELECT * FROM [dbo].[fnEngineerFeatures]( 
    @passenger_count,
    @trip_distance,
    @trip_time_in_secs,
    @pickup_latitude,
    @pickup_longitude,
    @dropoff_latitude,
    @dropoff_longitude)
    '
DECLARE @lmodel2 varbinary(max) = (select model from nyc_taxi_models where name = @model);
EXEC sp_execute_external_script 
  @language = N'Python',
  @script = N'
import pickle;
import numpy;

# Load model and unserialize
mod = pickle.loads(model)

# Get features for scoring from input data
X = InputDataSet[["passenger_count", "trip_distance", "trip_time_in_secs", "direct_distance"]]

# Score data to get tip prediction probability as a list (of float)
probList = []
probList.append((mod.predict_proba(X)[0])[1])

# Create output data frame
OutputDataSet = pandas.DataFrame(data = probList, columns = ["predictions"])
',
  @input_data_1 = @inquery,
  @params = N'@model varbinary(max),@passenger_count int,@trip_distance float,
    @trip_time_in_secs int ,
    @pickup_latitude float ,
    @pickup_longitude float ,
    @dropoff_latitude float ,
    @dropoff_longitude float',
    @model = @lmodel2,
    @passenger_count =@passenger_count ,
    @trip_distance=@trip_distance,
    @trip_time_in_secs=@trip_time_in_secs,
    @pickup_latitude=@pickup_latitude,
    @pickup_longitude=@pickup_longitude,
    @dropoff_latitude=@dropoff_latitude,
    @dropoff_longitude=@dropoff_longitude
WITH RESULT SETS ((Score float));
END
GO
```

### <a name="predicttipsinglemoderxpy"></a>PredictTipSingleModeRxPy

次のストアドプロシージャは、 **revoscalepy**モデルを使用してスコアリングを実行します。

```sql
DROP PROCEDURE IF EXISTS PredictTipSingleModeRxPy;
GO

CREATE PROCEDURE [dbo].[PredictTipSingleModeRxPy] (@model varchar(50), @passenger_count int = 0,
  @trip_distance float = 0,
  @trip_time_in_secs int = 0,
  @pickup_latitude float = 0,
  @pickup_longitude float = 0,
  @dropoff_latitude float = 0,
  @dropoff_longitude float = 0)
AS
BEGIN
DECLARE @inquery nvarchar(max) = N'
  SELECT * FROM [dbo].[fnEngineerFeatures]( 
    @passenger_count,
    @trip_distance,
    @trip_time_in_secs,
    @pickup_latitude,
    @pickup_longitude,
    @dropoff_latitude,
    @dropoff_longitude)
  '
DECLARE @lmodel2 varbinary(max) = (select model from nyc_taxi_models where name = @model);
EXEC sp_execute_external_script 
  @language = N'Python',
  @script = N'
import pickle;
import numpy;
from revoscalepy.functions.RxPredict import rx_predict;

# Load model and unserialize
mod = pickle.loads(model)

# Get features for scoring from input data
X = InputDataSet[["passenger_count", "trip_distance", "trip_time_in_secs", "direct_distance"]]

# Score data to get tip prediction probability as a list (of float)

probArray = rx_predict(mod, X)

probList = []
probList = probArray["tipped_Pred"].values

# Create output data frame
OutputDataSet = pandas.DataFrame(data = probList, columns = ["predictions"])
',
  @input_data_1 = @inquery,
  @params = N'@model varbinary(max),@passenger_count int,@trip_distance float,
    @trip_time_in_secs int ,
    @pickup_latitude float ,
    @pickup_longitude float ,
    @dropoff_latitude float ,
    @dropoff_longitude float',
    @model = @lmodel2,
    @passenger_count =@passenger_count ,
    @trip_distance=@trip_distance,
    @trip_time_in_secs=@trip_time_in_secs,
    @pickup_latitude=@pickup_latitude,
    @pickup_longitude=@pickup_longitude,
    @dropoff_latitude=@dropoff_latitude,
    @dropoff_longitude=@dropoff_longitude
WITH RESULT SETS ((Score float));
END
GO
```

### <a name="generate-scores-from-models"></a>モデルからスコアを生成する

ストアドプロシージャを作成した後は、どちらのモデルに基づいても簡単にスコアを生成できます。 新しい**クエリ**ウィンドウを開き、各機能列のパラメーターを入力するか貼り付けます。 これらの特徴列の7つの必須値は、次の順序で指定します。
    
+ *passenger_count*
+ *trip_distance* v*trip_time_in_secs*
+ *pickup_latitude*
+ *pickup_longitude*
+ *dropoff_latitude*
+ *dropoff_longitude*

1. **Revoscalepy**モデルを使用して予測を生成するには、次のステートメントを実行します。
  
    ```sql
    EXEC [dbo].[PredictTipSingleModeRxPy] 'revoscalepy_model', 1, 2.5, 631, 40.763958,-73.973373, 40.782139,-73.977303
    ```

2. **Scikit-learn**モデルを使用してスコアを生成するには、次のステートメントを実行します。

    ```sql
    EXEC [dbo].[PredictTipSingleModeSciKitPy] 'SciKit_model', 1, 2.5, 631, 40.763958,-73.973373, 40.782139,-73.977303
    ```

両方の手順からの出力は、指定されたパラメーターまたは特徴を使用してタクシー旅行に支払われるチップの確率です。

## <a name="conclusions"></a>まとめ

このチュートリアルでは、ストアドプロシージャに埋め込まれた Python コードを使用する方法について学習しました。 と[!INCLUDE[tsql](../../includes/tsql-md.md)]の統合により、予測のための Python モデルのデプロイが非常に簡単になり、エンタープライズデータワークフローの一部としてモデルの再トレーニングを組み込むことができます。

## <a name="previous-step"></a>前の手順

[Python モデルのトレーニングと保存](sqldev-py5-train-and-save-a-model-using-t-sql.md)

## <a name="see-also"></a>関連項目

[SQL Server の Python 拡張機能](../concepts/extension-python.md)
