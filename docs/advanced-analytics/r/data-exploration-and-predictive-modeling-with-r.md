---
title: R を使用したデータ探索と予測モデリング
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 3ffd82aeb1aa099b3f9f21368eacbf603032fd6e
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715726"
---
# <a name="data-exploration-and-predictive-modeling-with-r-in-sql-server"></a>SQL Server での R を使用したデータ探索と予測モデリング
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server との統合によって可能なデータサイエンスプロセスの機能強化について説明します。

適用対象SQL Server 2016 R Services、SQL Server 2017 Machine 教師 Services

## <a name="the-data-science-process"></a>データサイエンスプロセス

多くの場合、データ サイエンティストは R を使用してデータを探索し、予測モデルを構築します。 通常、適切な予測モデルが構築されるまで、試行錯誤が繰り返されます。 経験豊富なデータ サイエンティストであれば、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースに接続し、RODBC パッケージを使用してローカル ワークステーションにデータをフェッチし、データを探索して標準的な R パッケージを使用して予測モデルを構築する場合があります。

しかし、このアプローチには多くの欠点があり、これは企業内での R の採用が大きくなってきました。 

+ データの移動は、速度が遅い、非効率である、または安全でない可能性があります。
+ R 自体にはパフォーマンスとスケールの制限があります

これらの欠点は、大量のデータを移動して分析する必要がある場合や、コンピューターで使用可能なメモリに適さないデータセットを使用する場合に、より明確になります。

に含まれている新しいスケーラブルな[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]パッケージと R 関数は、これらの課題の多くを克服するのに役立ちます。 

## <a name="whats-different-about-revoscaler"></a>RevoScaleR の違いは何ですか。

**RevoScaleR** パッケージには、並列処理とスケール機能を提供するために再設計された、最も一般的ないくつかの R 関数の実装が含まれます。 詳細については、「 [RevoScaleR を使用した分散コンピューティング](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing)」を参照してください。

また、RevoScaleR パッケージでは *実行コンテキスト*の変更もサポートされます。 つまり、ソリューション全体または 1 つの関数のみの場合、ローカル ワークステーションではなく、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスをホストするコンピューターのリソースを使用して計算を行う必要があることを示すことができます。 これを行う利点は複数あります。不要なデータ移動を回避し、サーバー コンピューター上のより多くの計算リソースを活用することができます。

## <a name="r-environment-and-packages"></a>R の環境とパッケージ

[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)] でサポートされている R 環境は、ランタイム、オープン ソース言語、複数のパッケージでサポートされ拡張されたグラフィック エンジンで構成されます。 この言語では、パッケージを使用して実装されるさまざまな拡張機能を使用できます。  

### <a name="using-other-r-packages"></a>他の R パッケージの使用

Microsoft Machine Learning に含まれる独自の R ライブラリに加えて、ソリューションでは次のようなほとんどすべての R パッケージを使用できます。

+ パブリック リポジトリの汎用 R パッケージ。 データ サイエンティストが使用できる 6,000 個を超えるパッケージをホストする、CRAN などのパブリック リポジトリから最も一般的なオープン ソース R パッケージを取得することができます。
  
  Windows プラットフォームの場合、R パッケージは zip ファイルとして提供され、GPL ライセンス下でダウンロードしてインストールできます。  
  
  [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]で使用するためにサードパーティ製のパッケージをインストールする方法については、「[Install Additional R Packages on SQL Server](../../advanced-analytics/r/install-additional-r-packages-on-sql-server.md) (SQL Server に追加の R パッケージをインストールする)」をご覧ください。  
  
+ [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]で提供される追加のパッケージとライブラリ。   
  
     **RevoScaleR** パッケージには、高パフォーマンスのビッグ データ分析、一般的なデータ サイエンス タスクをサポートする強化バージョンの関数、Naive Bayes 用に最適的化された学習者、線形回帰、タイム シリーズ モデル、ニューラル ネットワーク、高度な算術ライブラリなどが含まれています。  
  
     **RevoPemaR** パッケージでは、R で独自の並列外部メモリ アルゴリズムを開発できます。  
  
     これらのパッケージとその使用方法の詳細については、「 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler)と、 [RevoPemaR の](https://docs.microsoft.com/machine-learning-server/r/how-to-developer-pemar)概要」を参照してください。 

+ Microsoft **ml**には、高度に最適化された機械学習アルゴリズムと Microsoft データサイエンスチームからのデータ変換のコレクションが含まれています。 多くのアルゴリズムは Azure Machine Learning でも使用されています。 詳細については、 [SQL Server の「Microsoft ml](ref-r-microsoftml.md)」を参照してください。

### <a name="r-development-tools"></a>R 開発ツール

R ソリューションを開発するときは、必ず Microsoft R Client をダウンロードしてください。 この無料ダウンロードには、リモートの計算コンテキストとスケーラブルな alorithms をサポートするために必要なライブラリが含まれています。

+ **[!INCLUDE[rsql_rro-noversion](../../includes/rsql-rro-noversion-md.md)]:** R ランタイムのディストリビューションと、標準的な R 操作のパフォーマンスを向上させる Intel math kernel library などの一連のパッケージ。  
  
+ **RevoScaleR**の[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスに計算をプッシュできる R パッケージ。 [!INCLUDE[rsql_rre-noversion](../../includes/rsql-rre-noversion-md.md)] 。 パフォーマンスとスケーラビリティを高めるために再設計された一連の共通の R 関数も含まれています。 これらの強化された関数は、 **rx** プレフィックスで識別できます。 さまざまなソースの強化されたデータ プロバイダーも含まれており、これらの関数には **Rx** のプレフィックスが付いています。

[!INCLUDE[rsql_rtvs](../../includes/rsql-rtvs-md.md)]や rstudio など、R をサポートする任意の Windows ベースのコードエディターを使用できます。 [!INCLUDE[rsql_rro-noversion](../../includes/rsql-rro-noversion-md.md)] をダウンロードすれば、RGui.exe などの R の一般的なコマンド ライン ツールも取得できます。

## <a name="use-new-data-sources-and-compute-contexts"></a>新しいデータソースと計算コンテキストを使用する

RevoScaleR パッケージを使用してに[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]接続する場合は、R コードで使用する次の関数を探します。

+ **RxSqlServerData** は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]に含まれている新しいスケーラブル パッケージと R 関数を使用して克服できます。
  
     R コードでこの関数を使用して、 *データ ソース*を定義します。 データ ソース オブジェクトではデータがあるテーブルとサーバーを指定し、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]に対するデータの読み取りおよび書き込みタスクを管理します。
  
-   **RxInSqlServer** 関数を使用して、*計算コンテキスト*を指定できます。  つまり、R コードを実行する必要がある場所 (ローカル ワークステーションや [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスをホストするコンピューターなど) を示すことができます。  詳細については、「 [RevoScaleR Functions](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)」を参照してください。
  
     計算コンテキストを設定した場合、影響を受けるのはリモートの実行コンテキストをサポートする計算のみです。これは、R 演算が RevoScaleR パッケージと、関連する関数で提供されることを意味します。 一般に、CRAN の標準パッケージに基づく R ソリューションはリモートの計算コンテキストでは実行できませんが、T-SQL で開始した場合は [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] コンピューター上で実行できます。 ただし、`rxExec` 関数を使用して R 関数を個別に呼び出し、[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] でリモートで実行できます。

データソースと実行コンテキストを作成して操作する方法の例については、次のチュートリアルを参照してください。

+ [データ サイエンスの詳細情報](../../advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)  
+  [Microsoft R を使用したデータ分析](https://docs.microsoft.com/machine-learning-server/r/how-to-introduction)

## <a name="deploy-r-code-to-production"></a>R コードを運用環境にデプロイする

データ サイエンスの重要な部分は、自分の分析を他のユーザーに提供したり、予測モデルを使用してビジネスの結果またはプロセスを改善することです。 [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]では、R スクリプトまたはモデルの準備ができている場合、運用環境に簡単に移行することができます。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で実行するコードの移行方法の詳細については、「[Operationalizing Your R Code (R コードの運用)](../../advanced-analytics/r/operationalizing-your-r-code.md)」をご覧ください。

通常、デプロイ プロセスは、運用環境では不要なコードを削除するためのスクリプトのクリーンアップから始まります。 計算をデータの近くに移動すると、R ですべてを実行するよりも効率的にデータを移動、要約、または表示する方法が見つかる可能性があります。 データ科学者に問い合わせて、パフォーマンスを向上させる方法について調べることをお勧めします。特に、SQL の方が効果的なデータクレンジングや特徴エンジニアリングを行う場合は、データベース開発者に相談することをお勧めします。 モデルの構築またはスコアリングのワークフローが失敗していないことと、入力データが適切な形式で使用できることを確認するために、ETL プロセスの変更が必要な場合があります。

## <a name="see-also"></a>参照

[Base R 関数と RevoScaleR 関数の比較](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-compared-to-base-r)

[SQL Server の RevoScaleR ライブラリ](ref-r-revoscaler.md)
