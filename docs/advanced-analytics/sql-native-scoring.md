---
title: 予測 T-sql ステートメントを使用したネイティブスコアリング
description: 予測を生成します。この関数では、R または Python で記述された事前トレーニング済みのモデルに対して dta 入力をスコア付けして SQL Server します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 08/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: f84b799fa901f7461f448683cceffe78e1dddfd3
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714951"
---
# <a name="native-scoring-using-the-predict-t-sql-function"></a>予測 T-sql 関数を使用したネイティブスコアリング
[!INCLUDE[appliesto-ss-asdb-xxxx-xxx-md](../includes/appliesto-ss-asdb-xxxx-xxx-md.md)]

ネイティブスコアリングでは、予測値または新しいデータC++入力の*スコア*をほぼリアルタイムで生成するために SQL Server 2017 の[予測 t-sql 関数](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql)とネイティブ拡張機能を使用します。 この方法では、予測と予測のワークロードの処理速度が最速になりますが、プラットフォームとライブラリの要件がありますC++ 。 RevoScaleR と revoscalepy の関数のみが実装されています。

ネイティブスコアリングを使用するには、既にトレーニング済みのモデルが必要です。 SQL Server 2017 の Windows または Linux、または Azure SQL Database では、Transact-sql で PREDICT 関数を呼び出して、入力パラメーターとして指定した新しいデータに対してネイティブスコアリングを呼び出すことができます。 PREDICT 関数は、指定されたデータ入力のスコアを返します。

## <a name="how-native-scoring-works"></a>ネイティブスコアリングのしくみ

ネイティブスコアリングではC++ 、以前に特別なバイナリ形式で保存された、または raw バイトストリームとしてディスクに保存された、既にトレーニング済みのモデルを読み取ることができる Microsoft のネイティブライブラリを使用して、指定した新しいデータ入力のスコアを生成します。 モデルはトレーニング、発行、および保存されるため、R または Python インタープリターを呼び出さなくてもスコアリングに使用できます。 そのため、複数のプロセス相互作用のオーバーヘッドが軽減されるため、エンタープライズ運用環境では、予測パフォーマンスが格段に速くなります。

ネイティブスコアリングを使用するには、予測 T-sql 関数を呼び出し、次の必須の入力を渡します。

+ サポートされているアルゴリズムに基づく互換性のあるモデル。
+ 入力データ。通常は SQL クエリとして定義されます。

関数は、入力データの予測を、パススルーするソースデータの列と共に返します。

## <a name="prerequisites"></a>必須コンポーネント

PREDICT は SQL Server 2017 データベースエンジンのすべてのエディションで使用でき、Windows、SQL Server 2017 (Windows)、SQL Server 2017 (Linux)、Azure SQL Database の SQL Server Machine Learning Services を含め、既定で有効になっています。 R や Python をインストールしたり、追加機能を有効にしたりする必要はありません。

+ モデルは、下記のサポートされている**rx**アルゴリズムのいずれかを使用して事前にトレーニングする必要があります。

+ R の場合は[rxSerialize](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)を、Python の場合は[rx_serialize_model](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-serialize-model)を使用してモデルをシリアル化します。 これらのシリアル化関数は、高速スコア付けをサポートするように最適化されています。

<a name="bkmk_native_supported_algos"></a> 

## <a name="supported-algorithms"></a>サポートされているアルゴリズム

+ revoscalepy モデル

  + [rx_lin_mod](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-lin-mod)
  + [rx_logit](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-logit) 
  + [rx_btrees](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-btrees) 
  + [rx_dtree](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-dtree) 
  + [rx_dforest](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-dforest) 

+ RevoScaleR モデル

  + [rxLinMod](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxlinmod)
  + [rxLogit](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxlogit)
  + [rxBTrees](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxbtrees)
  + [rxDtree](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxdtree)
  + [rxDForest](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxdforest)

Microsoft Ml または microsoft ml のモデルを使用する必要がある場合は、 [sp_rxPredict でリアルタイムスコアリング](real-time-scoring.md)を使用します。

サポートされていないモデルの種類には、次の種類があります。

+ 他の変換を含むモデル
+ RevoScaleR または`rxGlm` revoscalepy `rxNaiveBayes`に同等のアルゴリズムまたはアルゴリズムを使用するモデル
+ PMML モデル
+ 他のオープンソースライブラリまたはサードパーティライブラリを使用して作成されたモデル

## <a name="example-predict-t-sql"></a>例:PREDICT (T-SQL)

この例では、モデルを作成し、T-sql からリアルタイムの予測関数を呼び出します。

### <a name="step-1-prepare-and-save-the-model"></a>手順 1. モデルの準備と保存

次のコードを実行して、サンプルデータベースと必要なテーブルを作成します。

```sql
CREATE DATABASE NativeScoringTest;
GO
USE NativeScoringTest;
GO
DROP TABLE IF EXISTS iris_rx_data;
GO
CREATE TABLE iris_rx_data (
  "Sepal.Length" float not null, "Sepal.Width" float not null
  , "Petal.Length" float not null, "Petal.Width" float not null
  , "Species" varchar(100) null
);
GO
```

次のステートメントを使用して、**虹彩**データセットのデータをデータテーブルに設定します。

```sql
INSERT INTO iris_rx_data ("Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width" , "Species")
EXECUTE sp_execute_external_script
  @language = N'R'
  , @script = N'iris_data <- iris;'
  , @input_data_1 = N''
  , @output_data_1_name = N'iris_data';
GO
```

次に、モデルを格納するテーブルを作成します。

```sql
DROP TABLE IF EXISTS ml_models;
GO
CREATE TABLE ml_models ( model_name nvarchar(100) not null primary key
  , model_version nvarchar(100) not null
  , native_model_object varbinary(max) not null);
GO
```

次のコードでは、**虹彩**データセットに基づいてモデルを作成し、モデル**という名前**のテーブルに保存します。

```sql
DECLARE @model varbinary(max);
EXECUTE sp_execute_external_script
  @language = N'R'
  , @script = N'
    iris.sub <- c(sample(1:50, 25), sample(51:100, 25), sample(101:150, 25))
    iris.dtree <- rxDTree(Species ~ Sepal.Length + Sepal.Width + Petal.Length + Petal.Width, data = iris[iris.sub, ])
    model <- rxSerializeModel(iris.dtree, realtimeScoringOnly = TRUE)
    '
  , @params = N'@model varbinary(max) OUTPUT'
  , @model = @model OUTPUT
  INSERT [dbo].[ml_models]([model_name], [model_version], [native_model_object])
  VALUES('iris.dtree','v1', @model) ;
```

> [!NOTE] 
> RevoScaleR の[rxSerializeModel](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)関数を使用して、モデルを保存してください。 標準の R `serialize`関数では、必要な形式を生成できません。

次のようなステートメントを実行すると、格納されているモデルをバイナリ形式で表示できます。

```sql
SELECT *, datalength(native_model_object)/1024. as model_size_kb
FROM ml_models;
```

### <a name="step-2-run-predict-on-the-model"></a>手順 2. モデルに対して PREDICT を実行する

次の単純な PREDICT ステートメントは、**ネイティブスコアリング**関数を使用してデシジョンツリーモデルから分類を取得します。 指定した属性に基づいて虹彩の数が予測されます。花弁の長さと幅です。

```sql
DECLARE @model varbinary(max) = (
  SELECT native_model_object
  FROM ml_models
  WHERE model_name = 'iris.dtree'
  AND model_version = 'v1');
SELECT d.*, p.*
  FROM PREDICT(MODEL = @model, DATA = dbo.iris_rx_data as d)
  WITH(setosa_Pred float, versicolor_Pred float, virginica_Pred float) as p;
go
```

エラーが発生した場合は、関数 PREDICT の実行中にエラーが発生しました。 モデルが破損しているか無効です。通常は、クエリがモデルを返さなかったことを意味します。 モデル名を正しく入力したかどうか、またはモデルテーブルが空であるかどうかを確認します。

> [!NOTE]
> **予測**によって返される列と値は、モデルの種類によって異なる場合があるため、 **WITH**句を使用して、返されるデータのスキーマを定義する必要があります。

## <a name="next-steps"></a>次の手順

ネイティブスコアリングを含む完全なソリューションについては、SQL Server 開発チームの次のサンプルを参照してください。

+ ML スクリプトをデプロイします。[Python モデルの使用](https://microsoft.github.io/sql-ml-tutorials/python/rentalprediction/step/3.html)
+ ML スクリプトをデプロイします。[R モデルの使用](https://microsoft.github.io/sql-ml-tutorials/R/rentalprediction/step/3.html)