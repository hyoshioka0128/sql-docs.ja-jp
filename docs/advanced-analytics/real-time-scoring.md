---
title: Sp_rxPredict ストアドプロシージャを使用したリアルタイムスコアリング
description: Sp_rxPredict を使用して予測を生成し、SQL Server で R で記述された事前トレーニング済みモデルに対してデータ入力をスコア付けします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/26/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 26701ac6e538d195a5a85ad66af9578848889d23
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715645"
---
# <a name="real-time-scoring-with-sprxpredict-in-sql-server-machine-learning"></a>SQL Server machine learning での sp_rxPredict を使用したリアルタイムのスコアリング
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

リアルタイムスコアリングでは、 [sp_rxPredict](https://docs.microsoft.com//sql/relational-databases/system-stored-procedures/sp-rxpredict-transact-sql)システムストアドプロシージャと CLR 拡張機能を使用して、高パフォーマンスの予測や予測ワークロードのスコアを SQL Server します。 リアルタイムのスコアリングは言語に依存せず、R や Python の実行時間に依存せずに実行されます。 モデルが Microsoft functions を使用して作成およびトレーニングされた後、SQL Server でバイナリ形式にシリアル化された場合は、リアルタイムスコアリングを使用して、R または Python アドオンのない SQL Server インスタンスの新しいデータ入力に対して予測される結果を生成できます。ら.

## <a name="how-real-time-scoring-works"></a>リアルタイムスコアリングのしくみ

リアルタイムスコアリングは、RevoScaleR または[rxLinMod (RevoScaleR)](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod)[RxNeuralNet (microsoft ml)](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)などの microsoft ml 関数に基づく特定のモデルの種類でサポートされています。 また、ネイティブC++ライブラリを使用して、特別なバイナリ形式で格納された機械学習モデルに提供されるユーザー入力に基づいてスコアを生成します。

トレーニング済みのモデルは、外部言語ランタイムを呼び出すことなくスコアリングに使用できるため、複数のプロセスのオーバーヘッドが削減されます。 これにより、運用スコアリングシナリオでの予測パフォーマンスが格段に速くなります。 データが SQL Server から出ることはないため、R と SQL の間でデータを変換しなくても、結果を生成して新しいテーブルに挿入することができます。

リアルタイムのスコアリングは、複数の手順から成るプロセスです。

1. スコア付けを行うストアドプロシージャは、データベースごとに有効にする必要があります。
2. 事前トレーニング済みのモデルをバイナリ形式で読み込みます。
3. モデルへの入力として、スコア付けする新しい入力データ (表形式または単一行) を指定します。
4. スコアを生成するには、 [sp_rxPredict](https://docs.microsoft.com//sql/relational-databases/system-stored-procedures/sp-rxpredict-transact-sql)ストアドプロシージャを呼び出します。

## <a name="prerequisites"></a>前提条件

+ [SQL SERVER CLR 統合を有効に](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/introduction-to-sql-server-clr-integration)します。

+ [リアルタイムスコアリングを有効に](#bkmk_enableRtScoring)します。

+ サポートされている**rx**アルゴリズムのいずれかを使用して、事前にモデルをトレーニングする必要があります。 R では、 `sp_rxPredict` [RevoScaleR および microsoft ml でサポートされているアルゴリズム](#bkmk_rt_supported_algos)を使用したリアルタイムのスコアリングが可能です。 Python については、「 [revoscalepy および microsoft ml でサポートされるアルゴリズム](#bkmk_py_supported_algos)」を参照してください。

+ R の場合は[rxSerialize](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)を、Python の場合は[rx_serialize_model](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-serialize-model)を使用してモデルをシリアル化します。 これらのシリアル化関数は、高速スコア付けをサポートするように最適化されています。

+ モデルを呼び出し元のデータベースエンジンインスタンスに保存します。 このインスタンスには、R または Python のランタイム拡張機能を含める必要はありません。

> [!Note]
> 現在、リアルタイムスコアリングは、少数の行から数百の行に至るまで、小さいデータセットに対する迅速な予測に最適化されています。 ビッグデータセットでは、 [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict)の使用が高速になる場合があります。

<a name="bkmk_py_supported_algos"></a>

## <a name="supported-algorithms"></a>サポートされているアルゴリズム

### <a name="python-algorithms-using-real-time-scoring"></a>リアルタイムスコアリングを使用する Python アルゴリズム

+ revoscalepy モデル

  + [rx_lin_mod](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-lin-mod) \*
  + [rx_logit](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-logit) \*
  + [rx_btrees](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-btrees) \*
  + [rx_dtree](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-dtree) \*
  + [rx_dforest](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-dforest) \*
  
  で\*マークされたモデルは、PREDICT 関数を使用したネイティブスコアリングもサポートします。

+ microsoft ml モデル

  + [rx_fast_trees](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-fast-trees)
  + [rx_fast_forest](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-fast-forest)
  + [rx_logistic_regression](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-logistic-regression)
  + [rx_oneclass_svm](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-oneclass-svm)
  + [rx_neural_net](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network)
  + [rx_fast_linear](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-fast-linear)

+ Microsoft ml によって提供される変換

  + [featurize_text](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/featurize-text)
  + [concat](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/concat)
  + [categorical](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/categorical)
  + [categorical_hash](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/categorical-hash)


<a name="bkmk_rt_supported_algos"></a>

### <a name="r-algorithms-using-real-time-scoring"></a>リアルタイムスコアリングを使用した R アルゴリズム

+ RevoScaleR モデル

  + [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) \*
  + [rxLogit](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlogit) \*
  + [rxBTrees](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxbtrees) \*
  + [rxDtree](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdtree) \*
  + [rxdForest](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdforest) \*
  
  で\*マークされたモデルは、PREDICT 関数を使用したネイティブスコアリングもサポートします。

+ Microsoft Ml モデル

  + [rxFastTrees](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfasttrees)
  + [rxFastForest](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfastforest)
  + [rxLogisticRegression](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxlogisticregression)
  + [rxOneClassSvm](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxoneclasssvm)
  + [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)
  + [rxFastLinear](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfastlinear)

+ Microsoft Ml によって提供される変換

  + [featurizeText](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfasttrees)
  + [concat](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/concat)
  + [categorical](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/categorical)
  + [categoricalHash](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/categoricalHash)
  + [selectFeatures](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/selectFeatures)

### <a name="unsupported-model-types"></a>サポートされていないモデルの種類

リアルタイムスコアリングはインタープリターを使用しません。そのため、インタープリターを必要とする可能性がある機能は、スコアリングの手順ではサポートされていません。  たとえば、次のようなものがあります。

  + アルゴリズム`rxGlm`または`rxNaiveBayes`アルゴリズムを使用するモデルはサポートされていません。

  + 変換関数または変換を含む数式を使用するモデル ( <code>A ~ log(B)</code>など) は、リアルタイムスコアリングではサポートされていません。 この型のモデルを使用するには、データをリアルタイムスコアリングに渡す前に、入力データに対して変換を実行することをお勧めします。


## <a name="example-sprxpredict"></a>例: sp_rxPredict

ここでは、**リアルタイム**予測を設定するために必要な手順について説明し、t-sql から関数を呼び出す方法の例を示します。

<a name ="bkmk_enableRtScoring"></a> 

### <a name="step-1-enable-the-real-time-scoring-procedure"></a>手順 1. リアルタイムスコアリングプロシージャを有効にする

スコアリングに使用するデータベースごとに、この機能を有効にする必要があります。 サーバー管理者は、RevoScaleR パッケージに含まれているコマンドラインユーティリティ (RegisterRExt) を実行する必要があります。

> [!NOTE]
> リアルタイムスコアリングを機能させるには、インスタンスで SQL CLR 機能を有効にする必要があります。また、データベースは信頼できるものとしてマークされている必要があります。 スクリプトを実行すると、これらのアクションが実行されます。 ただし、これを行う前に、セキュリティへの追加の影響について検討してください。

1. 管理者特権でのコマンドプロンプトを開き、RegisterRExt が配置されているフォルダーに移動します。 次のパスは、既定のインストールで使用できます。
    
    `<SQLInstancePath>\R_SERVICES\library\RevoScaleR\rxLibs\x64\`

2. インスタンスの名前と、拡張ストアドプロシージャを有効にするターゲットデータベースを置き換えて、次のコマンドを実行します。

    `RegisterRExt.exe /installRts [/instance:name] /database:databasename`

    たとえば、拡張ストアドプロシージャを既定のインスタンスの CLRPredict データベースに追加するには、次のように入力します。

    `RegisterRExt.exe /installRts /database:CLRPRedict`

    データベースが既定のインスタンス上にある場合、インスタンス名は省略可能です。 名前付きインスタンスを使用している場合は、インスタンス名を指定する必要があります。

3. RegisterRExt は、次のオブジェクトを作成します。

    + 信頼されたアセンブリ
    + ストアドプロシージャ`sp_rxPredict`
    + 新しいデータベースロール`rxpredict_users`。 データベース管理者は、このロールを使用して、リアルタイムスコアリング機能を使用するユーザーに権限を付与できます。

4. 新しいロールに実行`sp_rxPredict`する必要があるユーザーを追加します。

> [!NOTE]
> 
> SQL Server 2017 では、CLR 統合に関する問題を回避するために、追加のセキュリティ対策が実施されています。 これらのメジャーでは、このストアドプロシージャの使用に関する追加の制限が課されます。 

### <a name="step-2-prepare-and-save-the-model"></a>手順 2. モデルの準備と保存

Sp\_rxPredict で必要とされるバイナリ形式は、PREDICT 関数を使用するために必要な形式と同じです。 したがって、R コードでは、 [rxSerializeModel](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)への呼び出しを含め、次の例`realtimeScoringOnly = TRUE`のようにを指定してください。

```R
model <- rxSerializeModel(model.name, realtimeScoringOnly = TRUE)
```

### <a name="step-3-call-sprxpredict"></a>手順 3. Sp_rxPredict の呼び出し

他のストアド\_プロシージャと同様に、sp rxPredict を呼び出します。 現在のリリースでは、ストアドプロシージャは2つのパラメーター  _\@_ (モデルのバイナリ形式でのモデル) と _\@_ 、スコアリングで使用するデータの inputdata (有効な SQL クエリとして定義されています) のみを使用します。

バイナリ形式は PREDICT 関数で使用されるものと同じであるため、前の例のモデルとデータテーブルを使用できます。

```sql
DECLARE @irismodel varbinary(max)
SELECT @irismodel = [native_model_object] from [ml_models]
WHERE model_name = 'iris.dtree' 
AND model_version = 'v1''

EXEC sp_rxPredict
@model = @irismodel,
@inputData = N'SELECT * FROM iris_rx_data'
```

> [!NOTE]
> 
> スコアリングの入力データ\_にモデルの要件に一致する列が含まれていない場合、sp rxPredict への呼び出しは失敗します。 現時点では、次の .NET データ型のみがサポートされています。 double、float、short、ushort、long、ulong、および string。
> 
> そのため、リアルタイムスコアリングに使用する前に、入力データでサポートされていない型を除外することが必要になる場合があります。
> 
> 対応する SQL 型の詳細については、「 [sql-Clr 型のマッピング](/dotnet/framework/data/adonet/sql/linq/sql-clr-type-mapping)」または「 [clr パラメーターデータのマッピング](https://docs.microsoft.com/sql/relational-databases/clr-integration-database-objects-types-net-framework/mapping-clr-parameter-data)」を参照してください。

## <a name="disable-real-time-scoring"></a>リアルタイムスコアリングを無効にする

リアルタイムスコアリング機能を無効にするには、管理者特権でのコマンドプロンプトを開き、次のコマンドを実行します。`RegisterRExt.exe /uninstallrts /database:<database_name> [/instance:name]`

## <a name="next-steps"></a>次のステップ

SQL Server でのスコアリングの背景については、 [SQL Server machine learning で予測を生成する方法](r/how-to-do-realtime-scoring.md)に関するページを参照してください。
