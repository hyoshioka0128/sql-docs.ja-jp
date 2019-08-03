---
title: Microsoft Ml R 関数ライブラリ
description: SQL Server 2016 R Services での Microsoft Ml 関数ライブラリの概要と、R を使用した Machine Learning Services SQL Server について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: af9e85586a2aad69a87072caa820fff4026d1feb
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715664"
---
# <a name="microsoftml-r-library-in-sql-server"></a>Microsoft Ml (SQL Server の R ライブラリ)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

Microsoft**は、** 高性能な機械学習アルゴリズムを提供する Microsoft の R 関数ライブラリです。 トレーニングと変換、スコアリング、テキストと画像の分析、および既存のデータから値を派生させるための機能の抽出を行うための関数が含まれています。

Machine learning Api は、内部機械学習アプリケーション用に Microsoft によって開発されたもので、マルチコア処理と高速データストリーミングを使用したビッグデータの高パフォーマンスをサポートするために、長年にわたって改善されています。 また、Microsoft Ml には、テキストと画像処理のための変換も多数含まれています。

## <a name="full-reference-documentation"></a>完全なリファレンスドキュメント

Microsoft **ml**ライブラリは複数の Microsoft 製品に配布されていますが、SQL Server または別の製品でライブラリを入手した場合でも、使用方法は同じです。 関数は同じであるため、[個々の RevoScaleR 関数のドキュメント](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)は、Microsoft Machine Learning Server の[R リファレンス](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)の下にある1つの場所にのみ発行されます。 製品固有の動作が存在する場合、関数のヘルプページには相違点が示されます。

## <a name="versions-and-platforms"></a>バージョンとプラットフォーム

Microsoft **ml**ライブラリは R 3.4.3 に基づいており、次のいずれかの Microsoft 製品またはダウンロードをインストールした場合にのみ利用できます。

+ [SQL Server 2016 R Services](../install/sql-r-services-windows-install.md)
+ [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)
+ [Microsoft Machine Learning Server 9.2.0 以降](https://docs.microsoft.com/machine-learning-server/)
+ [Microsoft R クライアント](set-up-a-data-science-client.md)

> [!NOTE]
> 製品の完全リリースバージョンは、SQL Server 2017 以降の Windows のみで使用できます。 **Microsoft ml**の Linux サポートは、 [SQL Server 2019 Preview](../../linux/sql-server-linux-setup-machine-learning.md)の新機能です。

## <a name="package-dependencies"></a>パッケージの依存関係

**Microsoft ml**のアルゴリズムは、次の場合に[RevoScaleR](ref-r-revoscaler.md)に依存します。

+ データソースオブジェクト。 **Microsoft ml**関数によって消費されるデータは、 **RevoScaleR** functions を使用して作成されます。
+ リモートコンピューティング (関数の実行をリモート SQL Server インスタンスにシフトする)。 **RevoScaleR**ライブラリには、SQL server のリモート計算コンテキストを作成およびアクティブ化するための関数が用意されています。

ほとんどの場合は、 **Microsoft ml**を使用しているときに、パッケージをまとめて読み込みます。

## <a name="functions-by-category"></a>カテゴリ別の関数

このセクションでは、関数をカテゴリ別に一覧表示して、それぞれの使用方法について説明します。 また、[目次](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)を使用して、関数をアルファベット順に検索することもできます。

<a name="ml-algorithms"></a>

## <a name="1-machine-learning-algorithms"></a>1-機械学習アルゴリズム

| 関数名 | 説明 |
|---------------|-------------|
|[rxFastTrees](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfasttrees) | FastRank の実装。これは、マートの勾配ブーストアルゴリズムの効率的な実装です。  |
|[rxFastForest](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfastforest) | [RxFastTrees](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfasttrees)を使用した、ランダムフォレストと分位点回帰フォレストの実装。  |
|[rxLogisticRegression](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/logisticregression) | L-BFGS を使用したロジスティック回帰。  |
|[rxOneClassSvm](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxoneclasssvm) | 1つのクラスがベクターマシンをサポートします。  
|[rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet) | バイナリ、多クラス、回帰ニューラル net。  |
|[rxFastLinear](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfastlinear) | ストキャスティクスは、線形二項分類と回帰に対して、デュアル座標のアセントの最適化を行います。 |
|[rxEnsemble](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxensemble) | さまざまな種類のモデルをトレーニングすることで、1つのモデルから得られるよりも多くの予測パフォーマンスを得ることができます。|

<a name="ml-transforms"></a>

## <a name="2-transformation-functions"></a>2-変換関数

| 関数名 | 説明 |
|---------------|-------------|
|[concat](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/concat) | 複数の列から1つのベクトル値列を作成する変換です。  |
|[categorical](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/categorical) | 辞書でカテゴリ変換を使用してインジケーターベクターを作成します。  |
|[categoricalHash](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/categoricalhash) | カテゴリ値をハッシュしてインジケーター配列に変換します。 |
|[featurizeText](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/featurizetext) | 指定されたテキストのコーパスから n グラムと呼ばれる連続する単語のシーケンスの数のバッグを生成します。 言語の検出、トークン化、ストップワードの削除、テキストの正規化、特徴の生成を提供します。  |
|[getSentiment](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/getsentiment) | 自然言語のテキストをスコア付けし、テキストのセンチメントが正である確率を含む列を作成します。|
|[ngram](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/ngram) | カウントベースとハッシュベースの特徴抽出の引数を定義できます。|
|[selectColumns](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/selectcolumns) | 再トレーニングする列のセットを選択し、他の列をすべて削除します。 |
|[selectFeatures](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/selectfeatures) | 指定されたモードを使用して、指定された変数から特徴を選択します。|
|[loadImage](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/loadimage) | 画像データを読み込みます。|
|[resizeImage](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/resizeimage) | 指定されたサイズ変更方法を使用して、指定されたディメンションにイメージのサイズを変更します。|
|[extractPixels](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/extractpixels) | イメージからピクセル値を抽出します。|
|[featurizeImage](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/featurizeimage) | 事前トレーニング済みのディープニューラルネットワークモデルを使用してイメージを画像します。|


## <a name="3-scoring-and-training-functions"></a>3-スコアリングとトレーニングの機能

| 関数名 | 説明 |
|---------------|-------------|
|[rxPredict.mlModel](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxpredict) | SQL Server から、ストアドプロシージャを使用して、または R コードから実行して、リアルタイムのスコアリングを可能にすることで、より高速な予測パフォーマンスを実現することで、スコアリングライブラリを実行します。|
|[rxFeaturize](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfeaturize) | 入力データセットのデータを出力データセットに変換します。|
|[mlModel](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/mlmodel) | Microsoft R Machine Learning モデルの概要について説明します。|


## <a name="4-loss-functions-for-classification-and-regression"></a>分類と回帰の4つの損失関数

| 関数名 | 説明 |
|---------------|-------------|
|[expLoss](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/loss) | 指数分類損失関数の仕様。 | 
|[logLoss](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/loss) | ログ分類損失機能の仕様。  |
|[hingeLoss](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/loss) | ヒンジ分類損失機能の仕様。 | 
|[smoothHingeLoss](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/loss) | Smooth ヒンジ分類損失機能の仕様。  |
| [poissonLoss](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/loss) | ポワソン回帰損失関数の仕様。 | 
|[squaredLoss](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/loss) | 二乗回帰損失関数の仕様。   |   

## <a name="5-feature-selection-functions"></a>5機能の選択関数

| 関数名 | 説明 |
|---------------|-------------|
|[minCount](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/mincount) | カウントモードでの機能の選択の仕様。 |
|[mutualInformation](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/mutualinformation) | 相互情報モードでの機能の選択の仕様。 |

## <a name="6-ensemble-modeling-functions"></a>6-モデリング関数

| 関数名 | 説明 |
|---------------|-------------|
|[fastTrees](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/fasttrees) | [RxEnsemble](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxensemble)を使用して高速ツリーモデルをトレーニングするための関数名と引数を含むリストを作成します。|
|[fastForest](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfastforest) | [RxEnsemble](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxensemble)を使用して高速フォレストモデルをトレーニングするための関数名と引数を含むリストを作成します。|
|[fastLinear](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/fastlinear) | [RxEnsemble](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxensemble)を使用して高速線形モデルをトレーニングするための関数名と引数を含むリストを作成します。|
|[logisticRegression](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/logisticregression) | [RxEnsemble](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxensemble)を使用してロジスティック回帰モデルをトレーニングするための関数名と引数を含むリストを作成します。|
|[oneClassSvm](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/oneclasssvm) | [RxEnsemble](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxensemble)で OneClassSvm モデルをトレーニングするための関数名と引数を含むリストを作成します。|

## <a name="7-neural-networking-functions"></a>7-ニューラルネットワーク機能

| 関数名 | 説明 |
|---------------|-------------|
|[オプティマイザー](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/optimizer) | [RxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet) machine learning アルゴリズムの最適化アルゴリズムを指定します。|


## <a name="8-package-state-functions"></a>8-パッケージ状態関数

| 関数名 | 説明 |
|---------------|-------------|
|[rxHashEnv](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxHashEnv) | パッケージ全体の状態を格納するために使用される環境オブジェクト。 |


## <a name="how-to-use-microsoftml"></a>Microsoft Ml の使用方法

**Microsoft ml**の関数は、ストアドプロシージャにカプセル化された R コードで呼び出すことができます。 ほとんどの開発者は、 **microsoft の ml**ソリューションをローカルでビルドし、完成した R コードを配置の演習としてストアドプロシージャに移行します。

R 用の**Microsoft ml**パッケージは、SQL Server 2017 ではすぐに使用できます。 また、インスタンスの R コンポーネントをアップグレードすると、SQL Server 2016 でも使用できます。[バインディングを使用して SQL Server のインスタンスをアップグレードする](../install/upgrade-r-and-python.md)

既定では、パッケージは読み込まれません。 最初の手順として、 **Microsoft ml**パッケージを読み込み、リモートの計算コンテキストまたは関連する接続またはデータソースオブジェクトを使用する必要がある場合は、 **RevoScaleR**を読み込みます。 次に、必要な個々の関数を参照します。

```R
library(microsoftml);
library(RevoScaleR);
logisticRegression(args);
```

## <a name="see-also"></a>関連項目

+ [R チュートリアル](../tutorials/sql-server-r-tutorials.md)
+ [コンピューティングコンテキストの使用方法](../tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)
+ [SQL 開発者向け R:モデルのトレーニングと運用化](../tutorials/sqldev-in-database-r-for-sql-developers.md)
+ [GitHub の Microsoft 製品サンプル](https://github.com/Microsoft/SQL-Server-R-Services-Samples)
+ [R リファレンス (Microsoft Machine Learning Server)](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) 