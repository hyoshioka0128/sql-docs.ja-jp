---
title: R と Python のチュートリアルの SQL Server
description: SQL Server Machine Learning Services での R および Python スクリプトの例とチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 03/29/2019
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 5e05a62be604b9d1a3feeaea1ed4f05dc3538493
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715470"
---
# <a name="sql-server-machine-learning-tutorials-in-r-and-python"></a>R と Python での Machine Learning チュートリアルの SQL Server
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、 [SQL Server 2016 R Services](../install/sql-r-services-windows-install.md)または[SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)の機械学習機能をデモンストレーションするチュートリアルとコードサンプルの包括的な一覧を示します。 

+ クイックスタートは、組み込みデータを使用するか、最小限の労力で迅速な探索を行うためのデータを使用しません。
+ チュートリアルでは、より多くのタスク、大規模なデータセット、さらに詳しい説明をより深く掘り下げていきます。
+ サンプルとソリューションは、コードから開始し、概念とレッスンにさかのぼって知識のギャップを埋める開発者を対象としています。

ここでは、R と Python ライブラリを、同じ操作コンテキストで常駐するリレーショナルデータと共に使用する方法、カスタムコードを実行および配置するために SQL Server ストアドプロシージャを使用する方法、および高パフォーマンスデータ用に Microsoft R と Python ライブラリを呼び出す方法について説明します。サイエンスおよび機械学習のタスク。

最初の手順として、Microsoft の R および Python と SQL Server の統合に関する基本概念を確認します。

## <a name="concepts"></a>概念

データベース内分析とは、SQL Server Machine Learning Services をインストールするとき、またはデータベースエンジンへのアドオンとして 2016 R Services (R のみ) を SQL Server するときに SQL Server での R および Python のネイティブサポートを指します。 R と Python の統合には、基本のオープンソースディストリビューションに加え、高性能分析用の Microsoft 固有のライブラリが含まれています。

アーキテクチャのスタンドポイントでは、データベースエンジンの整合性を維持するために、コードがボックス上で外部プロセスとして実行されます。 ただし、すべてのデータアクセスとセキュリティは SQL Server データベースのロールとアクセス許可を使用します。つまり、SQL Server にアクセスできるアプリケーションは、ストアドプロシージャとしてデプロイするときに R および Python スクリプトにアクセスできます。また、トレーニング済みのモデルをシリアル化して SQL に保存することもできます。サーバーデータベース。

SQL Server での R と Python のサポートの主な相違点と、他の Microsoft 製品およびサービスでの同等の言語サポートには、次のようなものがあります。

+ ストアドプロシージャまたはバイナリモデルとしてコードを "パッケージ化" できます。
+ SQL Server プログラムファイルと共にローカルにインストールされた Microsoft R および Python ライブラリを呼び出すコードを記述します。
+ R および Python ソリューションに SQL Server のデータベースセキュリティアーキテクチャを適用します。
+ カスタムソリューションの SQL Server インフラストラクチャと管理サポートを活用します。

## <a name="quickstarts"></a>クイック スタート

ここから開始して、T-sql から R または Python を実行する方法と、SQL 運用環境の R および Python コードを運用化する方法を学習してください。

+ [PythonT-sql を使用して Python を実行する](run-python-using-t-sql.md)
+ [\R\N\R\NR と SQL の Hello World](rtsql-using-r-code-in-transact-sql-quickstart.md)
+ [\R\N\R\N入力と出力の処理](rtsql-working-with-inputs-and-outputs.md)
+ [\R\N\R\Nデータ型とオブジェクトの処理](rtsql-r-and-sql-data-types-and-data-objects.md)
+ [\R\N\R\NR 関数の使用](rtsql-using-r-functions-with-sql-server-data.md)
+ [\R\N\R\N予測モデルを作成する](rtsql-create-a-predictive-model-r.md)
+ [\R\N\R\Nモデルからの予測とプロット](rtsql-predict-and-plot-from-model.md)

## <a name="tutorials"></a>チュートリアル

Microsoft のパッケージと、ローカルからリモートの計算コンテキストへの移行など、その他の特殊な操作について詳しく説明することにより、R、Python、T-sql を使用した最初の経験を基に構築できます。

+ [Python のチュートリアル](sql-server-python-tutorials.md)
+ [R チュートリアル](sql-server-r-tutorials.md)

<a name ="bkmk_samples"></a>

## <a name="samples"></a>サンプル

SQL Server および R Server 開発チームが提供するこれらのサンプルとデモは、実際のアプリケーションで埋め込み分析を使用する方法を示しています。

| リンク | 説明 | 
|------|-------------|
| [R と SQL Server を使用して顧客のクラスタリングを実行する](https://microsoft.github.io/sql-ml-tutorials/R/customerclustering/) | 教師なし learning を使用して、売上データに基づいて顧客をセグメント化します。 この例では、Microsoft R のスケーラブルな rxKmeans アルゴリズムを使用して、クラスターモデルを構築します。 |
| [Python と SQL Server を使用して顧客のクラスタリングを実行する](https://microsoft.github.io/sql-ml-tutorials/python/customerclustering/) | Kmeans アルゴリズムを使用して顧客のクラスタリングを実行する方法について説明します。 この例では、データベース内の Python 言語を使用します。| SQL Server 2017 |
| [R と SQL Server を使用した予測モデルの作成](https://microsoft.github.io/sql-ml-tutorials/R/rentalprediction) | Ski レンタル企業が機械学習を使用して将来のレンタルを予測する方法について説明します。これにより、ビジネスプランやスタッフが将来の需要に対応できるようになります。 この例では、Microsoft アルゴリズムを使用して、ロジスティック回帰モデルとデシジョンツリーモデルを作成します。 | 
| [Python と SQL Server を使用した予測モデルの作成](https://microsoft.github.io/sql-ml-tutorials/python/rentalprediction/) | 将来の需要に対応するために、Python を使用して ski レンタル分析アプリケーションを構築します。 この例では、新しい Python ライブラリ**revoscalepy**を使用して、線形回帰モデルを作成します。 | 

<a name="bkmk_solutions"></a>

## <a name="solution-templates"></a>ソリューションテンプレート

Microsoft データサイエンスチームは、カスタマイズ可能なソリューションテンプレートを提供しています。このテンプレートを使用して、一般的なシナリオに対応するソリューションをすぐに開始できます。 各ソリューションは、特定のタスクまたは業界の問題に合わせて調整されています。 ほとんどのソリューションは、SQL Server、または Azure Machine Learning などのクラウド環境で実行するように設計されています。 その他のソリューションは、Microsoft R Server または Machine Learning Server を使用して、Linux または Spark または Hadoop クラスターで実行できます。

すべてのコードが提供されます。また、SQL Server ストアドプロシージャを使用してスコアリングのためのモデルをトレーニングしてデプロイする方法についても説明しています。

+ [不正行為の検出](https://gallery.cortanaanalytics.com/Tutorial/Online-Fraud-Detection-Template-with-SQL-Server-R-Services-1)
+ [顧客離れ予測](https://gallery.cortanaanalytics.com/Tutorial/Customer-Churn-Prediction-Template-with-SQL-Server-R-Services-1)
+ [予測メンテナンス](https://gallery.cortanaanalytics.com/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1)
+ [病院の医療の長さを予測する](https://gallery.cortanaintelligence.com/Solution/Predicting-Length-of-Stay-in-Hospitals-1)

