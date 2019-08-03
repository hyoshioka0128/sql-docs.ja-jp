---
title: SQL Server Machine Learning Services の概要 (R、Python)
description: SQL Server の Machine Learning Services 機能の概要。ここでは、Python と R を、データサイエンスと統計モデリング、機械学習モデル、予測分析、データ可視化などのためのリレーショナルデータと統合できます。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/24/2019
ms.topic: overview
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 4ab4cd7c93cfd1a98a819a849e643d590450cd28
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714668"
---
# <a name="sql-server-machine-learning-services-r-python"></a>SQL Server Machine Learning Services (R、Python)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

Machine Learning Services は SQL Server の機能であり、データベース内の R および Python スクリプトの実行に使用されます。 この機能には、高パフォーマンスの予測分析と機械学習を行うための[Microsoft R と Python のパッケージ](#components)が含まれています。 リレーショナルデータは、ストアドプロシージャ、R および Python ステートメントを含む T-sql スクリプト、T-sql を含む R および Python コードを使用して、R および Python スクリプトで使用できます。

Azure SQL Database では、 [Machine Learning Services (R)](https://docs.microsoft.com/azure/sql-database/sql-database-machine-learning-services-overview)は現在パブリックプレビューの段階にあります。

## <a name="bring-compute-power-to-the-data"></a>データのコンピューティング能力を活用する

Machine Learning Services の主な価値の提案は、高度な分析を大規模に提供し、データがどこにあるかを計算して処理する機能であり、データを何度もプルする必要がなくなるという、エンタープライズ R および Python パッケージの機能です。ネットワーク。 これには、次のような利点があります。

+ データのセキュリティ。 R と Python の実行をデータソースの近くに置くことで、無駄や安全でないデータ移動を回避できます。
+ 速さ。 データベースは、セットベースの操作用に最適化されています。 インメモリテーブルなどのデータベースの最近のイノベーションによって、集計と集計が可能になり、データサイエンスを完全に補完することができます。
+ デプロイと統合の容易さ。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]は、他の多くのデータ管理タスクおよびアプリケーションにおける操作の中心となるポイントです。 データベースまたはレポートウェアハウスに格納されているデータを使用することにより、機械学習ソリューションで使用されるデータを確実に最新の状態に保つことができます。 
+ クラウドとオンプレミスの間の効率性。 R または Python セッションでデータを処理するのではなく、および Azure Data Factory を[!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)]含むエンタープライズデータパイプラインを利用できます。 Power BI や [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] を使用して、結果や分析を簡単にレポートできます。

さまざまなデータ処理や分析タスクで SQL と R を正しく組み合わて使用することで、データ サイエンティストと開発者の両方の生産性がさらに向上します。

<a name="components"></a>

## <a name="components"></a>コンポーネント

SQL Server では、R と Python がサポートされています。 次の表で、コンポーネントについて説明します。

| コンポーネント | 説明 |
|-----------|-------------|
| SQL Server Launchpad サービス | 外部 R および Python ランタイムとデータベースエンジンインスタンス間の通信を管理するサービス。 |
| R パッケージ | [**RevoScaleR**](r/ref-r-revoscaler.md)はスケーラブルな R のプライマリライブラリです。このライブラリの関数は、最も広く使用されています。 これらのライブラリには、データの変換と操作、統計の概要作成、視覚化、モデリングと分析のさまざまな形式があります。 また、これらのライブラリの関数は、並列処理のために使用可能なコア間にワークロードを自動的に分散します。また、計算エンジンによって調整および管理されるデータのチャンクを操作できます。  <br/>[**Microsoft ml (R)** ](r/ref-r-microsoftml.md)は、機械学習アルゴリズムを追加して、テキスト分析、画像分析、およびセンチメント分析のためのカスタムモデルを作成します。 <br/>[**sqlRUtils**](r/ref-r-sqlrutils.md)には、r スクリプトを t-sql ストアドプロシージャに配置し、ストアドプロシージャをデータベースに登録し、r 開発環境からストアドプロシージャを実行するためのヘルパー関数が用意されています。<br/>[**Olapr**](r/ref-r-olapr.md)は、R スクリプトで MDX クエリを構築または実行するためのものです。|
| Microsoft R Open (MRO) | [**Mro**](https://mran.microsoft.com/open)は、Microsoft の R のオープンソースディストリビューションです。パッケージとインタープリターが含まれています。 セットアップによってインストールされたバージョンの MRO を常に使用します。 |
| R ツール | R コンソールのウィンドウとコマンドプロンプトは、R ディストリビューションの標準ツールです。  |
| R のサンプルとスクリプト |  オープンソースの R パッケージと RevoScaleR パッケージには、事前にインストールされたデータを使用してスクリプトを作成して実行するための組み込みデータセットが含まれています。 |
| Python パッケージ | [**revoscalepy**](python/ref-py-revoscalepy.md)は、データ操作、変換、視覚化、および分析を行うための機能を備えたスケーラブルな Python のプライマリライブラリです。 <br/>[**microsoft ml (Python)** ](python/ref-py-microsoftml.md)は、機械学習アルゴリズムを追加して、テキスト分析、画像分析、およびセンチメント分析のためのカスタムモデルを作成します。  |
| Python ツール | 組み込みの Python コマンドラインツールは、アドホックテストやタスクに役立ちます。  |
| Anaconda | Anaconda は、Python と必須パッケージのオープンソースディストリビューションです。 |
| Python のサンプルとスクリプト | R と同様に、Python には組み込みのデータセットとスクリプトが含まれています。  |
| R と Python の事前トレーニング済みのモデル | 事前トレーニング済みのモデルは、特定のユースケースに対して作成され、Microsoft のデータサイエンスエンジニアリングチームによって管理されます。 事前トレーニング済みのモデルを使用すると、指定した新しいデータ入力を使用して、テキスト内で肯定的なセンチメントをスコア付けしたり、画像の特徴を検出したりできます。 モデルは Machine Learning Services で実行されますが、SQL Server セットアップを使用してインストールすることはできません。 詳細については、「 [SQL Server で事前トレーニング済みの機械学習モデルをインストール](install/sql-pretrained-models-install.md)する」を参照してください。 |

## <a name="using-sql-mls"></a>SQL MLS の使用

多くの場合、開発者とアナリストは、ローカル SQL Server インスタンス上でコードを実行しています。 Machine Learning Services を追加し、外部スクリプトの実行を有効にすることにより、R と Python のコードを、ストアドプロシージャ内のラッピングスクリプト、SQL Server テーブルに格納する、または T-sql 関数と R 関数または Python 関数を組み合わせることによって、SQL Server 感覚様相で実行できるようになります。クエリ内。

スクリプトの実行は、データセキュリティモデルの境界内にあります。リレーショナルデータベースに対する権限は、スクリプト内でのデータアクセスの基礎となります。 R または Python スクリプトを実行するユーザーは、SQL クエリでそのユーザーがアクセスできなかったデータを使用できないようにする必要があります。 標準のデータベースの読み取りと書き込みのアクセス許可に加え、外部スクリプトを実行するための追加のアクセス許可が必要です。 リレーショナルデータ用に記述したモデルとコードは、ストアドプロシージャにラップされるか、バイナリ形式にシリアル化されてテーブルに格納されるか、生のバイトストリームをファイルにシリアル化した場合はディスクから読み込まれます。

データベース内分析の最も一般的な方法は、 [sp_execute_external_script](../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)を使用して、R または Python スクリプトを入力パラメーターとして渡すことです。

従来のクライアントとサーバー間の対話は、もう1つの方法です。 IDE がインストールされている任意のクライアントワークステーションから、 [Microsoft R Client](https://docs.microsoft.com/machine-learning-server/r-client/what-is-microsoft-r-client)または[Python ライブラリ](https://docs.microsoft.com/machine-learning-server/install/python-libraries-interpreter)をインストールしてから、リモート SQL Server に対してデータおよび操作に対して実行 (*リモート計算コンテキスト*と呼ばれる) をプッシュするコードを記述することができます。 

また、[スタンドアロンサーバー](r/r-server-standalone.md)と Developer edition を使用している場合は、同じライブラリおよびインタープリターを使用してクライアントワークステーションでソリューションをビルドし、SQL Server Machine Learning Services (データベース内) に運用コードをデプロイすることができます。 

## <a name="how-to-get-started"></a>開始する方法

### <a name="step-1-install-the-software"></a>手順 1:ソフトウェアのインストール

+ [SQL Server Machine Learning Services (データベース内)](install/sql-machine-learning-services-windows-install.md)
 
### <a name="step-2-configure-a-development-tool"></a>手順 2:開発ツールの構成

データ科学者は、通常、独自のノート pc または開発ワークステーションで R または Python を使用し、データを探索し、適切な予測モデルが実現されるまで予測モデルを構築および調整します。 SQL Server でのデータベース内分析を使用する場合、このプロセスを変更する必要はありません。 インストールが完了したら、SQL Server で R または Python コードをローカルおよびリモートで実行できます。

![rsql_keyscenario2](r/media/rsql-keyscenario2.png) 

+ **好みの IDE を使用**します。 R と Python ライブラリを任意の開発ツールにリンクすることができます。 詳細については、「 [R ツールのセットアップ](r/set-up-a-data-science-client.md)」および「 [Python ツールの設定](python/setup-python-client-tools-sql.md)」を参照してください。  

+ **リモートまたはローカルで作業**します。 データ科学者は、SQL Server に接続し、通常どおり、ローカル分析のためにデータをクライアントに取り込むことができます。 ただし、より適切な解決策は、 **RevoScaleR** api または**revoscalepy** api を使用して SQL Server コンピューターに計算をプッシュすることです。これにより、コストのかかる安全でないデータ移動を回避できます。

+ **SQL Server ストアドプロシージャに R または Python スクリプトを埋め込み**ます。 コードが完全に最適化されたら、不要なデータ移動を回避し、データ処理タスクを最適化するために、ストアドプロシージャにラップします。

### <a name="step-3-write-your-first-script"></a>手順 3:最初のスクリプトを作成する

T-sql スクリプト内から R または Python 関数を呼び出す:

+ [\R\N\R\NR を使用したデータベース内分析の学習](tutorials/sqldev-in-database-r-for-sql-developers.md)
+ [\R\N\R\NR を使用したエンドツーエンドチュートリアル](tutorials/walkthrough-data-science-end-to-end-walkthrough.md)
+ [PythonT-sql を使用して Python を実行する](tutorials/run-python-using-t-sql.md)
+ [PythonPython を使用したデータベース内分析の学習](tutorials/sqldev-in-database-python-for-sql-developers.md)

タスクに最適な言語を選択します。 R は、SQL を使用して実装するのが困難な統計計算に最適です。 データに対するセットベースの操作の場合は、SQL Server の機能を活用して最大のパフォーマンスを実現します。 列を非常に高速に計算するには、メモリ内データベースエンジンを使用します。

### <a name="step-4-optimize-your-solution"></a>手順 4:ソリューションを最適化する

企業データに対してモデルを拡張する準備が整ったら、データサイエンスは DBA または SQL 開発者と協力して、次のようなプロセスを最適化します。

+ 機能エンジニアリング
+ データインジェストとデータ変換
+ ポイントの計算

従来、R を使用するデータ科学者は、特に大規模なデータセットを使用する場合に、パフォーマンスとスケールの両方に問題がありました。 これは、共通のランタイム実装がシングルスレッドであり、ローカルコンピューター上の使用可能なメモリに収まるデータセットだけを格納できるためです。 SQL Server Machine Learning Services との統合により、パフォーマンス向上のための複数の機能が提供され、データが増えます。

+ **RevoScaleR**:この R パッケージには、並列処理とスケールを提供するために再設計された、最も一般的ないくつかの R 関数の実装が含まれています。 このパッケージには、SQL Server コンピューターに計算をプッシュすることによってパフォーマンスとスケールをさらに向上させる機能も含まれています。通常、これにはメモリと計算能力が非常に大きくなります。

+ **revoscalepy**。 この Python ライブラリは、リモート計算コンテキストなど、RevoScaleR の最も一般的な関数、および分散処理をサポートする多くのアルゴリズムを実装しています。

パフォーマンスの詳細については、この[パフォーマンスのケーススタディ](r/performance-case-study-r-services.md)と[R とデータの最適化](r/r-and-data-optimization-r-services.md)に関する説明を参照してください。

### <a name="step-5-deploy-and-consume"></a>手順 5:展開と使用

スクリプトまたはモデルを運用環境で使用する準備が整ったら、データベース開発者は、保存されている R または Python コードをアプリケーションから呼び出すことができるように、ストアドプロシージャにコードまたはモデルを埋め込むことができます。 SQL Server から R コードを格納して実行すると、多くの利点があります。便利な SQL Server インターフェイスを使用でき、すべての計算がデータベース内で行われるため、不要なデータ移動を回避できます。

![rsql_keyscenario1](r/media/rsql-keyscenario1.png)

+ **セキュリティで保護された拡張**。 SQL Server は、データベースエンジンのセキュリティを維持し、R と Python のセッションを分離する新しい拡張機能アーキテクチャを使用します。 また、スクリプトを実行できるユーザーを制御したり、コードからアクセスできるデータベースを指定したりすることもできます。 大量の計算によってサーバー全体のパフォーマンスが低下するのを防ぐために、ランタイムに割り当てられるリソースの量を制御できます。

+ **スケジュールと監査**。 外部スクリプトジョブを SQL Server で実行すると、データ科学者によって使用されるデータを制御および監査できます。 また、他の T-sql ジョブまたはストアドプロシージャをスケジュールするのと同じように、外部の R または Python スクリプトを含むジョブをスケジュールし、ワークフローを作成することもできます。

SQL Server のリソース管理機能とセキュリティ機能を活用するために、展開プロセスには次のタスクが含まれる場合があります。

+ ストアドプロシージャで最適に実行できる関数にコードを変換する
+ セキュリティを設定し、特定のタスクで使用されるパッケージをロックダウンする
+ リソースガバナンスを有効にする (Enterprise edition が必要)

詳細については、「 [r のリソースガバナンス](r/resource-governance-for-r-services.md)」および「 [SQL Server の r Package Management](r/install-additional-r-packages-on-sql-server.md)」を参照してください。

## <a name="portability-and-related-products"></a>移植性と関連製品

カスタム R と Python コードの移植性は、パッケージの配布と、複数の製品に組み込まれているインタープリターによって解決されます。 SQL Server に同梱されているのと同じパッケージは、他のいくつかの Microsoft 製品およびサービス ( [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/)と呼ばれる SQL 以外のバージョンを含む) でも使用できます。 

R および Python インタープリターを含む無料のクライアントは、 [Microsoft R Client](https://docs.microsoft.com/machine-learning-server/r-client/what-is-microsoft-r-client)と[python ライブラリ](https://docs.microsoft.com/machine-learning-server/install/python-libraries-interpreter)です。

Azure では、Microsoft の R および Python のパッケージとインタープリターは、Azure Machine Learning、および[HDInsight](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview)や azure [virtual Machines](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-azure-vm-on-linux)などの azure サービスでも利用できます。 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)には、複数のベンダーからのツールと、Microsoft のライブラリやインタープリターを備えた、完全に装備された開発ワークステーションが含まれています。

## <a name="see-also"></a>関連項目

[SQL Server Machine Learning Services のインストール](install/sql-machine-learning-services-windows-install.md)
