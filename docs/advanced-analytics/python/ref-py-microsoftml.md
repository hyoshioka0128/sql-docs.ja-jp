---
title: microsoft ml Python パッケージ
description: SQL Server machine learning ワークロードに関連する Python 用の Microsoft machine learning アルゴリズムとモデルについて説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 12/04/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: c7e7a25749484ff0db4d133f10862438ae5f8ea1
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715140"
---
# <a name="microsoftml-python-module-in-sql-server"></a>microsoft ml (SQL Server の Python モジュール)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

**Microsoft** の Python35 互換モジュールは、高性能機械学習アルゴリズムを提供します。 トレーニングと変換、スコアリング、テキストと画像の分析、および既存のデータから値を派生させるための機能の抽出を行うための関数が含まれています。

Machine learning Api は、内部機械学習アプリケーション用に Microsoft によって開発されたもので、マルチコア処理と高速データストリーミングを使用したビッグデータの高パフォーマンスをサポートするために、長年にわたって改善されています。 このパッケージは、同様の機能を持つ R バージョンの[Microsoft ml](../r/ref-r-microsoftml.md)に相当する Python として生成されました。 

## <a name="full-reference-documentation"></a>完全なリファレンスドキュメント

Microsoft **ml**ライブラリは複数の Microsoft 製品に配布されていますが、SQL Server または別の製品でライブラリを入手した場合でも、使用方法は同じです。 関数は同じであるため、[個々の microsoft ml 関数のドキュメント](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)は、Microsoft Machine Learning Server の[Python リファレンス](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)の下にある1つの場所にのみ発行されます。 製品固有の動作が存在する場合、関数のヘルプページには相違点が示されます。

## <a name="versions-and-platforms"></a>バージョンとプラットフォーム

Microsoft **ml**モジュールは Python 3.5 をベースとしており、次のいずれかの Microsoft 製品またはダウンロードをインストールした場合にのみ使用できます。

+ [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)
+ [Microsoft Machine Learning Server 9.2.0 以降](https://docs.microsoft.com/machine-learning-server/)
+ [データサイエンスクライアント用の Python クライアントライブラリ](setup-python-client-tools-sql.md)

> [!NOTE]
> 製品の完全リリースバージョンは、SQL Server 2017 以降の Windows のみで使用できます。 **Microsoft ml**の Linux サポートは、 [SQL Server 2019 Preview](../../linux/sql-server-linux-setup-machine-learning.md)の新機能です。

## <a name="package-dependencies"></a>パッケージの依存関係

**Microsoft ml**のアルゴリズムは、次の場合に[revoscalepy](ref-py-revoscalepy.md)に依存します。

+ データソースオブジェクト。 **Microsoft ml**関数によって消費されるデータは、 **revoscalepy** functions を使用して作成されます。
+ リモートコンピューティング (関数の実行をリモート SQL Server インスタンスにシフトする)。 **Revoscalepy**ライブラリには、SQL server のリモート計算コンテキストを作成およびアクティブ化するための関数が用意されています。

ほとんどの場合は、 **microsoft ml**を使用しているときに、パッケージをまとめて読み込みます。

## <a name="functions-by-category"></a>カテゴリ別の関数

このセクションでは、関数をカテゴリ別に一覧表示して、それぞれの使用方法について説明します。 また、[目次](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)を使用して、関数をアルファベット順に検索することもできます。

## <a name="1-training-functions"></a>1-トレーニング関数

| 関数 | 説明 |
|----------|-------------|
|[microsoftml.rx_ensemble](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-ensemble) | モデルのトレーニングをトレーニングします。 |
|[microsoftml.rx_fast_forest](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-fast-forest)  | ランダムフォレスト。 |
|[rx_fast_linear](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-fast-linear) | 線形モデル。 ストキャスティクスの2つの座標のアセントが使用されます。 |
|[rx_fast_trees](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-fast-trees) | ブーストツリー。 |
|[microsoftml.rx_logistic_regression](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-logistic-regression) | ロジスティック回帰。 |
|[microsoftml.rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) | ニューラルネットワーク。 |
|[microsoftml.rx_oneclass_svm](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-oneclass-svm) | 異常検出。 |

<a name="ml-transforms"></a>

## <a name="2-transform-functions"></a>2-変換関数

### <a name="categorical-variable-handling"></a>カテゴリ変数の処理

| 関数 | 説明 |
|----------|-------------|
|[microsoftml.categorical](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/categorical) | テキスト列をカテゴリに変換します。 |
|[microsoftml.categorical_hash](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/categorical-hash) | テキスト列をハッシュし、カテゴリに変換します。 |

### <a name="schema-manipulation"></a>スキーマの操作

| 関数 | 説明 |
|----------|-------------|
|[microsoftml.concat](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/concat) | 複数の列を連結して1つのベクトルにします。 |
|[microsoftml.drop_columns](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/drop-columns) | データセットから列を削除します。 |
|[microsoftml.select_columns](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/select-columns) | データセットの列を保持します。 |


### <a name="variable-selection"></a>選択、変数

| 関数 | 説明 |
|----------|-------------|
|[microsoftml.count_select](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/count-select) |カウントに基づく機能の選択。 |
|[microsoftml.mutualinformation_select](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/mutualinformation-select) | 相互情報に基づく機能の選択。 |


### <a name="text-analytics"></a>テキスト分析

| 関数 | 説明 |
|----------|-------------|
|[microsoftml.featurize_text](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/featurize-text) | テキスト列を数値の特徴に変換します。 |
|[microsoft ml. get _sentiment](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/get-sentiment) | センチメント分析。 |


### <a name="image-analytics"></a>イメージ分析 

| 関数 | 説明 |
|----------|-------------|
|[load_image](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/load-image) | イメージを読み込みます。 |
|[resize_image](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/resize-image) | イメージのサイズを変更します。 |
|[extract_pixels](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/extract-pixels) | イメージからピクセルを抽出します。 |
|[featurize_image](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/featurize-image) | 画像を機能に変換します。 |

### <a name="featurization-functions"></a>特性付け関数

| 関数 | 説明 |
|----------|-------------|
|[rx_featurize](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-featurize) | データソースのデータ変換 |

<a name="ml-scoring"></a>

## <a name="3-scoring-functions"></a>3-スコアリング関数

| 関数 | 説明 |
|----------|-------------|
|[microsoftml.rx_predict](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-predict) | Microsoft machine learning モデルを使用したスコア |

## <a name="how-to-call-microsoftml"></a>Microsoft ml を呼び出す方法

**Microsoft ml**の関数は、ストアドプロシージャにカプセル化された Python コードで呼び出すことができます。 ほとんどの開発者は、ローカルで**microsoft ml**ソリューションをビルドし、完成した Python コードを配置の演習としてストアドプロシージャに移行します。

Python 用の**microsoft ml**パッケージは既定でインストールされますが、 **revoscalepy**とは異なり、SQL Server と共にインストールされた python の実行可能ファイルを使用して python セッションを開始すると、既定では読み込まれません。

最初の手順として、 **microsoft ml**パッケージをインポートし、リモートの計算コンテキスト、関連する接続、またはデータソースオブジェクトを使用する必要がある場合は、 **revoscalepy**をインポートします。 次に、必要な個々の関数を参照します。

```python
from microsoftml.modules.logistic_regression.rx_logistic_regression import rx_logistic_regression
from revoscalepy.functions.RxSummary import rx_summary
from revoscalepy.etl.RxImport import rx_import_datasource
```

## <a name="see-also"></a>関連項目

+ [Python のチュートリアル](../tutorials/sql-server-python-tutorials.md)
+ [チュートリアル: T-sql に Python コードを埋め込む](../tutorials/run-python-using-t-sql.md)
+ [Python リファレンス (Microsoft Machine Learning Server)](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)

