---
title: RevoScaleR R 関数ライブラリ
description: SQL Server 2016 R Services での RevoScaleR 関数ライブラリの概要と SQL Server R を使用した Machine Learning Services。
ms.prod: sql
ms.technology: machine-learning
ms.date: 12/04/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: b5dcd2f14d1a1d8e23a62be299b1ff6f41814041
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715060"
---
# <a name="revoscaler-r-library-in-sql-server"></a>RevoScaleR (SQL Server の R ライブラリ)

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

**RevoScaleR**は、Microsoft の高性能データサイエンス機能のライブラリです。 関数は、データのインポート、データ変換、概要作成、視覚化、および分析をサポートします。

基本の R 関数とは異なり、RevoScaleR 操作は非常に大規模なデータセット、並列、および分散ファイルシステムに対して実行できます。 関数は、チャンクを使用してメモリに収まりきらないデータセットや、操作の完了時に reassembling の結果を操作できます。

RevoScaleR 関数は、簡単に識別できるように、 **rx**または**rx**プレフィックスで示されます。

RevoScaleR は、分散データサイエンスのプラットフォームとして機能します。 たとえば、RevoScaleR のコンピューティングコンテキストと変換を、 [Microsoft ml](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package)の最先端アルゴリズムと共に使用できます。 [RxExec](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxexec)を使用して、基本の R 関数を並列で実行することもできます。

## <a name="full-reference-documentation"></a>完全なリファレンスドキュメント

**RevoScaleR**ライブラリは複数の Microsoft 製品に配布されていますが、SQL Server または別の製品でライブラリを入手したとしても、使用方法は同じです。 関数は同じであるため、[個々の RevoScaleR 関数のドキュメント](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)は、Microsoft Machine Learning Server の[R リファレンス](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)の下にある1つの場所にのみ発行されます。 製品固有の動作が存在する場合、関数のヘルプページには相違点が示されます。

## <a name="versions-and-platforms"></a>バージョンとプラットフォーム

**RevoScaleR**ライブラリは R 3.4.3 に基づいており、次のいずれかの Microsoft 製品またはダウンロードをインストールした場合にのみ使用できます。

+ [SQL Server 2016 R Services](../install/sql-r-services-windows-install.md)
+ [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)
+ [Microsoft Machine Learning Server 9.2.0 以降](https://docs.microsoft.com/machine-learning-server/)
+ [Microsoft R クライアント](set-up-a-data-science-client.md)

> [!NOTE]
> 製品の完全リリースバージョンは、SQL Server 2017 以降の Windows のみで使用できます。 Linux support for **RevoScaleR**は、 [SQL Server 2019 Preview](../../linux/sql-server-linux-setup-machine-learning.md)の新機能です。

## <a name="functions-by-category"></a>カテゴリ別の関数

このセクションでは、関数をカテゴリ別に一覧表示して、それぞれの使用方法について説明します。 また、[目次](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)を使用して、関数をアルファベット順に検索することもできます。

## <a name="1-data-source-and-compute"></a>1-データソースとコンピューティング

**RevoScaleR**には、データソースを作成し、計算を実行する場所の場所または*計算コンテキスト*を設定するための関数が含まれています。 データ ソース オブジェクトは、接続文字列と一緒に必要な一連のデータを指定するコンテナーです。テーブル、ビュー、またはクエリとして定義されます。 ストアド プロシージャの呼び出しはサポートされていません。 SQL Server シナリオに関連する関数を次の表に示します。

SQL Server と R は、場合によっては異なるデータ型を使用します。 SQL データ型と R データ型の間のマッピングの一覧については、「 [r TO sql データ型](r-libraries-and-data-types.md)」を参照してください。

| 関数| 説明|
| ------- | ---------- |
| [RxInSqlServer](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinsqlserver) |  計算コンテキストオブジェクト SQL Server を作成して、リモートインスタンスに計算をプッシュします。 いくつかの**RevoScaleR**関数は、引数として計算コンテキストを受け取ります。 |
|[rxGetComputeContext / rxSetComputeContext](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsetcomputecontext) | アクティブなコンピューティングコンテキストを取得または設定します。 |
| [RxSqlServerData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqlserverdata) | SQL Server クエリまたはテーブルに基づいてデータオブジェクトを作成します。 |
| [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) | ODBC 接続に基づいてデータソースを作成します。 |
| [RxXdfData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxxdfdata) | ローカル XDF ファイルに基づいてデータソースを作成します。 XDF ファイルは、メモリ内データをディスクにオフロードするためによく使用されます。 XDF ファイルは、1つのバッチでデータベースから転送できるよりも多くのデータを操作する場合や、メモリに収まりきらないデータを追加する場合に役立ちます。 たとえば、データベースからローカルワークステーションに大量のデータを定期的に移動する場合は、R 操作ごとにデータベースに対して繰り返しクエリを実行するのではなく、XDF ファイルをキャッシュの一種として使用して、データをローカルに保存し、R ワークスペースで操作することができます。|

> [!TIP]
> データソースまたはコンピューティングコンテキストの概念を初めて使用する場合は、Microsoft Machine Learning Server のドキュメントで[分散コンピューティング](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing)から始めることをお勧めします。

### <a name="perform-ddl-statements"></a>DDL ステートメントを実行する

インスタンスとデータベースに対して必要な権限を持っている場合は、R から DDL ステートメントを実行できます。 次の関数は、ODBC 呼び出しを使用して DDL ステートメントを実行したり、データベーススキーマを取得したりします。

| 関数| 説明|
| ------- | ---------- |
| [rxSqlServerTableExists と rxSqlServerDropTable](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqlserverdroptable) | [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]テーブルを削除するか、データベーステーブルまたはオブジェクトが存在するかどうかを確認します。 |
| [rxExecuteSQLDDL](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxexecutesqlddl) | データベースオブジェクトを定義または操作するデータ定義言語 (DDL) コマンドを実行します。 この関数はデータを返すことができず、オブジェクトスキーマまたはメタデータを取得または変更するためにのみ使用されます。|

## <a name="2-data-manipulation-etl"></a>2-データ操作 (ETL)

データソースオブジェクトを作成した後は、オブジェクトを使用して、そのオブジェクトへのデータの読み込み、データの変換、または指定された変換先への新しいデータの書き込みを行うことができます。 ソースのデータのサイズによりますが、データ ソースの一部としてバッチ サイズを定義し、データをチャンク単位で動かすことができます。

| 関数 | 説明 |
|----------|-------------|
| [rxOpen-メソッド](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxopen-methods) | データソースが使用可能かどうかを確認し、データソースを開いたり閉じたり、ソースからデータを読み取り、ターゲットにデータを書き込んだり、データソースを閉じたりします。|
| [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) | データソースからファイルストレージまたはデータフレームにデータを移動します。|
| [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) | データソース間でデータを移動するときにデータを変換します。|

<a name="graphing-functions"></a>

## <a name="3-graphing-functions"></a>3-関数のグラフ化

| 関数名 | 説明 |
|---------------|-------------|
|[rxHistogram](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxhistogram)  |データからヒストグラムを作成します。 | 
|[rxLinePlot](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlineplot) |データから折れ線プロットを作成します。 | 
|[rxLorenz](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlorenz)  |プロットできるプロットできるローレンツ curve を計算します。 | 
|[rxRocCurve](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxroc)  |実際のデータと予測されるデータから ROC 曲線を計算してプロットします。 | 

<a name="statistics-functions"></a>

## <a name="4-descriptive-statistics"></a>4-説明の統計

| 関数名 | 説明 |
|---------------|-------------|
|[rxQuantile](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxquantile)<sup>*</sup> |並べ替えを行わずに、xdf ファイルとデータフレームのおおよその分位点を計算します。 | 
|[rxSummary](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsummary)<sup>*</sup> |グループごとの計算を含む、データの基本的な統計概要。 グループ計算による xdf ファイルへの書き込みはサポートされていません。 | 
|[rxCrossTabs](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxcrosstabs)<sup>*</sup> |数式に基づくデータのクロス集計。 | 
|[rxCube](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxcube)<sup>*</sup> |キューブの結果を返す効率的な表現のために設計された代替の数式ベースのクロス集計。 Xdf ファイルへの出力の書き込みはサポートされていません。 | 
|[rxMarginals](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxmarginals)  |クロス集計の概要。 | 
|[as.xtabs](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/as.xtabs)  |クロス集計の結果を xtabs オブジェクトに変換します。 | 
|[rxChiSquaredTest](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxchisquaredtest)  |Xtabs オブジェクトでカイ2乗検定を実行します。 小さなデータセットで使用され、データをチャンクしません。 | 
|[rxFisherTest](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxchisquaredtest)  |Xtabs オブジェクトに対してフィッシャーの正確なテストを実行します。 小さなデータセットで使用され、データをチャンクしません。 | 
|[rxKendallCor](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxchisquaredtest)  |Xtabs オブジェクトを使用して、Kendall のタウ順位相関係数を計算します。 | 
|[rxPairwiseCrossTab](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpairwisecrosstab)  |Xtabs オブジェクトの行と列のペアの組み合わせに関数を適用します。 | 
|[rxRiskRatio](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxriskratio)  |2×2の xtabs オブジェクトの相対的なリスクを計算します。 | 
|[rxOddsRatio](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxriskratio)  |2×2の xtabs オブジェクトの確率比を計算します。 | 

<sup>*</sup>このカテゴリで最もよく使われる関数を示します。

<a name="prediction-functions"></a>

## <a name="5-prediction-functions"></a>5つの予測関数

| 関数名 | 説明 |
|---------------|-------------|
|[rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) <sup>*</sup> |線形モデルをデータに適合させる。 | 
|[rxLogit](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlogit) <sup>*</sup> |ロジスティック回帰モデルをデータに適合させる。 | 
|[rxGlm](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxglm) <sup>*</sup> |一般化された線形モデルをデータに適合させる。 | 
|[rxCovCor](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxcovcor) <sup>*</sup> |変数のセットについて、二乗/クロス積行列の共変性、相関、または合計を計算します。 | 
|[rxDTree](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdtree) <sup>*</sup> |分類または回帰ツリーをデータに適合させる。 | 
|[Rxbtrees](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxbtrees)<sup>*</sup> |ストキャスティクス勾配ブーストアルゴリズムを使用して、分類または回帰デシジョンフォレストをデータに適合させる。 | 
|[rxDForest](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdforest)<sup>*</sup> |分類または回帰の意思決定フォレストをデータに適合させる。 | 
|[rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxPredict) <sup>*</sup> |適合モデルの予測を計算します。 出力は XDF データソースである必要があります。 | 
|[rxKmeans](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxkmeans) <sup>*</sup> |K を意味するクラスタリングを実行します。 | 
|[rxNaiveBayes](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxnaivebayes) <sup>*</sup> |Naive Bayes 分類を実行します。 | 
|[rxCov](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxcovcor) |一連の変数の共分散マトリックスを計算します。 | 
|[rxCor](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxcovcor)  |一連の変数の相関関係マトリックスを計算します。 | 
|[rxSSCP](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxcovcor)  |一連の変数の二乗/クロス積マトリックスの合計を計算します。 | 
|[rxRoc](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxroc)  |バイナリ分類器システムの実際の値と予測値を使用して、受信側の動作特性 (ROC) を計算します。 | 

<sup>*</sup>このカテゴリで最もよく使われる関数を示します。


## <a name="how-to-work-with-revoscaler"></a>RevoScaleR を使用する方法

**RevoScaleR**の関数は、ストアドプロシージャにカプセル化された R コードで呼び出すことができます。 ほとんどの開発者はローカルで**RevoScaleR**ソリューションをビルドし、デプロイの演習として、完成した R コードをストアドプロシージャに移行します。

ローカルで実行する場合は、通常、コマンドラインまたは R 開発環境から R スクリプトを実行し、 **RevoScaleR**関数のいずれかを使用して SQL Server 計算コンテキストを指定します。 リモートコンピューティングコンテキストは、コード全体または個々の関数に使用できます。 たとえば、最新のデータを使用してデータの移動を回避するために、モデルのトレーニングをサーバーにオフロードすることができます。

R スクリプトをストアドプロシージャ[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)内にカプセル化する準備ができたら、明確に定義された入力と出力を持つ単一の関数としてコードを書き直すことをお勧めします。 

## <a name="see-also"></a>関連項目

+ [R チュートリアル](../tutorials/sql-server-r-tutorials.md)
+ [コンピューティングコンテキストの使用方法](../tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)
+ [SQL 開発者向け R:モデルのトレーニングと運用化](../tutorials/sqldev-in-database-r-for-sql-developers.md)
+ [GitHub の Microsoft 製品サンプル](https://github.com/Microsoft/SQL-Server-R-Services-Samples)
+ [R リファレンス (Microsoft Machine Learning Server)](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) 
