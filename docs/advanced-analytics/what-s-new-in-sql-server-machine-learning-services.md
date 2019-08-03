---
title: 新機能
description: SQL Server 2016 R Services、R Server、SQL Server Machine Learning Services の各リリースの新機能に関するお知らせ。
ms.date: 07/31/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.custom: sqlseattle
ms.prod: sql
ms.technology: machine-learning
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 9d63aac9c91919a2b4e3296f29e939c8cd09ad76
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715302"
---
# <a name="whats-new-in-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services の新機能

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

機械学習機能は、データプラットフォーム、高度な分析、データサイエンスの間の統合を継続的に拡張、拡張、強化するために、各リリースの SQL Server に追加されます。 

::: moniker range=">=sql-server-ver15||>=sql-server-linux-ver15||=sqlallproducts-allversions"
## <a name="new-in-sql-server-2019-preview"></a>SQL Server 2019 preview の新版

このリリースでは、R および Python 機械学習操作のための上位の要求された機能が SQL Server に追加されています。 このリリースのすべての機能の詳細については、 [SQL Server 2019 の新](../sql-server/what-s-new-in-sql-server-ver15.md)機能」および[SQL Server 2019 のリリースノート](../sql-server/sql-server-ver15-release-notes.md)を参照してください。

> [!NOTE]
> SQL Server 2019 の Java の新機能に関するドキュメントについては、「 [SQL Server 言語拡張機能の新](https://docs.microsoft.com/sql/language-extensions/language-extensions-whats-new)機能」を参照してください。

| リリース | 機能の更新 |
|---------|----------------|
| CTP 3.2 | 変更はありません。 |
| CTP 3.1 | 変更はありません。 |
| CTP 3.0 | 変更はありません。 |
| CTP 2.5 | 変更はありません。 |
| CTP 2.4 | Linux では、R と Python 用に[CREATE EXTERNAL LIBRARY (transact-sql) が](../t-sql/statements/create-external-library-transact-sql.md)サポートされています。 |
| CTP 2.3 | Windows の場合のみ、 [CREATE EXTERNAL library (transact-sql)](../t-sql/statements/create-external-library-transact-sql.md)ステートメントを使用して、外部ライブラリ内の Python コードにアクセスできます。 |
| CTP 2.2 | 変更はありません。 |
| CTP 2.1 | 変更はありません。 |
| CTP 2.0 | Linux プラットフォームでの R および Python machine learning のサポート。 [Linux でのインストール SQL Server Machine Learning Services](../linux/sql-server-linux-setup-machine-learning.md)の概要については、こちらをご覧ください。 |
|  | [Sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)には、パーティション分割されたデータから複数のモデルを簡単に生成できる2つの新しいパラメーターが導入されています。 このチュートリアルの詳細については、「 [R でパーティションベースのモデルを作成する](tutorials/r-tutorial-create-models-per-partition.md)」を参照してください。 |
|   | すべてのノードで SQL Server Launchpad サービスが開始されていることを前提として、フェールオーバークラスターのサポートが Windows および Linux でサポートされるようになりました。 詳細については、「 [SQL Server フェールオーバークラスターのインストール](../sql-server/failover-clusters/install/sql-server-failover-cluster-installation.md)」を参照してください。 |

::: moniker-end

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
## <a name="new-in-sql-server-2017"></a>SQL Server 2017 の新方法

このリリース[では、Python サポートと業界をリードする機械学習アルゴリズム](https://cloudblogs.microsoft.com/sqlserver/2017/04/19/python-in-sql-server-2017-enhanced-in-database-machine-learning/)が追加されています。 新しいスコープを反映するように名前が変更されました。 SQL Server 2017 では、Python と R の両方の言語サポートを使用して[SQL Server Machine Learning Services (データベース内)](what-is-sql-server-machine-learning.md)の導入を示しています。 

機能の発表については、「 [SQL Server 2017 の新](../sql-server/what-s-new-in-sql-server-2017.md)機能」を参照してください。

### <a name="r-enhancements"></a>R の機能強化

SQL Server Machine Learning Services の R コンポーネントは SQL Server 2016 R Services の次世代で、base R、RevoScaler、およびその他のパッケージの更新バージョンが含まれています。

R の新機能には、[**パッケージ管理**](r/install-additional-r-packages-on-sql-server.md)が含まれます。次の点が重要です。 

+ データベースロールは、Dba がパッケージを管理したり、パッケージのインストールにアクセス許可を割り当てたりするのに役立ちます。
+ [外部ライブラリを作成する](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)と、dba は使い慣れた t-sql 言語でパッケージを管理できます。
+ [RevoScaleR](r/use-revoscaler-to-manage-r-packages.md) functions は、ユーザーが所有するパッケージをインストール、削除、または一覧表示するのに役立ちます。 詳細については、 [SQL Server で RevoScaleR functions を使用して R パッケージを検索またはインストールする方法](r/use-revoscaler-to-manage-r-packages.md)に関する説明を参照してください。

### <a name="r-libraries"></a>R ライブラリ

| [パッケージ] | 説明 |
|---------|-------------|
| [**MicrosoftML**](r/ref-r-microsoftml.md) | このリリースでは、既定の R インストールに Microsoft Ml が組み込まれており、前の SQL Server 2016 R Services で必要なアップグレード手順が不要になっています。 Microsoft Ml は、リモートコンピューティングコンテキストでスケーリングまたは実行できる、最先端の機械学習アルゴリズムとデータ変換を提供します。 アルゴリズムには、カスタマイズ可能なディープニューラルネットワーク、高速デシジョンツリーとデシジョンフォレスト、線形回帰、およびロジスティック回帰が含まれます。  |

### <a name="python-integration-for-in-database-analytics"></a>データベース内分析のための Python 統合

Python は、さまざまな機械学習タスクに柔軟で強力な機能を提供する言語です。 Python 用オープンソースライブラリには、カスタマイズ可能なニューラルネットワーク用の複数のプラットフォームに加え、自然言語処理用の一般的なライブラリが含まれています。 

Python はデータベースエンジンと統合されているため、分析をデータの近くに保持し、データ移動に伴うコストとセキュリティ上のリスクを排除できます。 Visual Studio などのツールを使用して、Python に基づいた機械学習ソリューションをデプロイできます。 実稼働アプリケーションでは、SQL Server データアクセスメソッドを使用して、Python 3.5 ランタイムから予測、モデル、またはビジュアルを取得できます。

T-sql と Python の統合は、 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)システムストアドプロシージャでサポートされています。 このストアドプロシージャを使用すると、任意の Python コードを呼び出すことができます。 コードは、セキュリティで保護されたデュアルアーキテクチャで動作します。これにより、単純なストアドプロシージャを使用して、アプリケーションから呼び出すことができる、Python モデルとスクリプトのエンタープライズレベルのデプロイが可能になります。 SQL から Python プロセスおよび MPI リング並列処理のデータをストリーミングすることで、パフォーマンスがさらに向上します。

T-sql [PREDICT](../t-sql/queries/predict-transact-sql.md)関数を使用すると、以前に必要なバイナリ形式で保存した事前トレーニング済みのモデルに対して[ネイティブスコアリング](sql-native-scoring.md)を実行できます。

### <a name="python-libraries"></a>Python ライブラリ

| [パッケージ] | 説明 |
|---------|-------------|
|[**revoscalepy**](python/ref-py-revoscalepy.md)| Python-RevoScaleR に相当します。 線形およびロジスティック回帰、デシジョンツリー、ブーストツリー、ランダムフォレストの Python モデルを作成し、すべての並列化を可能にし、リモートの計算コンテキストで実行することができます。 このパッケージは、複数のデータソースとリモートの計算コンテキストの使用をサポートしています。 データ科学者や開発者は、リモート SQL Server で Python コードを実行して、データを移動したり、データを移動せずにモデルを構築したりできます。 |
|[**microsoftml**](python/ref-py-microsoftml.md) |Python-Microsoft Ml R パッケージに相当します。 |

### <a name="pre-trained-models"></a>トレーニング済みモデル

[**事前トレーニング済みのモデル**](install/sql-pretrained-models-install.md)は、Python と R の両方で使用できます。これらのモデルを使用して、イメージの認識と肯定的な負のセンチメント分析を行うことで、独自のデータに対する予測を生成できます。 

### <a name="standalone-server-as-a-shared-feature-in-sql-server-setup"></a>SQL Server セットアップの共有機能としてのスタンドアロンサーバー

また、このリリースでは、完全に独立したデータサイエンスサーバーである[SQL Server Machine Learning Server (スタンドアロン)](r/r-server-standalone.md)も追加され、R および Python での統計分析と予測分析がサポートされます。 R Services と同様に、このサーバーは SQL Server 2016 R Server (スタンドアロン) の次のバージョンです。 スタンドアロンサーバーを使用すると、SQL Server に依存せずに R または Python ソリューションを配布し、スケーリングすることができます。
::: moniker-end

::: moniker range=">=sql-server-2016||=sqlallproducts-allversions"
## <a name="new-in-sql-server-2016"></a>SQL Server 2016 の新方法

このリリースでは、データベースエンジンインスタンス内の常駐データに対して R スクリプトを処理するためのデータベース内分析エンジンである**SQL Server 2016 R Services**を通じて SQL Server に機械学習機能が導入されました。

また、Windows server に R Server をインストールする方法として、 **SQL Server 2016 r server (スタンドアロン)** がリリースされました。 最初に、SQL Server セットアップでは、R Server for Windows をインストールする唯一の方法が提供されていました。 今後のリリースでは、Windows 上の R Server を必要とする開発者やデータ科学者は、別のスタンドアロンインストーラーを使用して同じ目的を達成できました。 SQL Server のスタンドアロンサーバーは、 [Windows の Microsoft R Server](https://docs.microsoft.com/machine-learning-server/install/r-server-install-windows)スタンドアロンサーバー製品と機能的に同等です。

機能の発表については、「 [SQL Server 2016 の新](../sql-server/what-s-new-in-sql-server-2016.md)機能」を参照してください。

| リリース |機能の更新 |
|---------|----------------|
| CU の追加 | [**リアルタイムスコアリング**](real-time-scoring.md)では、ネイティブC++ライブラリを使用して、最適化されたバイナリ形式で格納されたモデルを読み取り、R ランタイムを呼び出すことなく予測を生成します。 これにより、スコアリング操作が大幅に高速化します。 リアルタイムのスコアリングでは、ストアドプロシージャを実行したり、R コードからリアルタイムのスコアリングを実行したりすることができます。 インスタンスがの[!INCLUDE[rsql-platform-md](../includes/rsql-platform-md.md)]最新リリースにアップグレードされている場合、SQL Server 2016 ではリアルタイムのスコアリングも利用できます。 |
| 最初のリリース | [**データベース内分析のための R 統合**](r/sql-server-r-services.md)。 <br/><br/> R は、T-sql で R 関数を呼び出すためのパッケージです。また、その逆も同様です。 RevoScaleR functions では、データをコンポーネントパーツに分割し、分散処理を調整および管理し、結果を集計することによって、大規模な R 分析を実現しています。 SQL Server 2016 R Services (データベース内) では、RevoScaleR エンジンはデータベースエンジンインスタンス、brining データ、および分析を同じ処理コンテキストにまとめて統合されています。 <br/><br/>[Sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)を介した T-sql と R の統合。 このストアドプロシージャを使用すると、任意の R コードを呼び出すことができます。 このセキュリティで保護されたインフラストラクチャにより、単純なストアドプロシージャを使用してアプリケーションから呼び出すことができる、Rn モデルおよびスクリプトのエンタープライズレベルの展開が可能になります。 SQL から R のプロセスおよび MPI リングの並列処理のデータをストリーミングすることで、パフォーマンスがさらに向上します。 <br/><br/>T-sql [PREDICT](../t-sql/queries/predict-transact-sql.md)関数を使用すると、以前に必要なバイナリ形式で保存した事前トレーニング済みのモデルに対して[ネイティブスコアリング](sql-native-scoring.md)を実行できます。|

::: moniker-end

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
## <a name="linux-support"></a>Linux のサポート

SQL Server 2019 では、データベースエンジンインスタンスを使用して machine learning パッケージをインストールすると、R と Python の Linux サポートが追加されます。 詳細については、「 [Install SQL Server Machine Learning Services On Linux](../linux/sql-server-linux-setup-machine-learning.md)」を参照してください。

Linux では、SQL Server 2017 には R または Python の統合はありませんが、linux で[ネイティブスコアリング](sql-native-scoring.md)を使用することができます。これは、linux で実行される t-sql [PREDICT](../t-sql/queries/predict-transact-sql.md)を通じて使用できるためです。 ネイティブスコアリングは、を呼び出さずに、または R ランタイムを必要とすることなく、事前トレーニング済みモデルの高パフォーマンススコア付けを可能にします。
::: moniker-end

<a name="azure-sql-database-roadmap"></a>

## <a name="machine-learning-services-in-azure-sql-database"></a>Azure SQL Database の Machine Learning Services

Azure SQL Database の Machine Learning Services はパブリックプレビューの段階にあります。 詳細については、「 [Azure SQL Database Machine Learning Services (プレビュー)](https://docs.microsoft.com/azure/sql-database/sql-database-machine-learning-services-overview)」を参照してください。

## <a name="next-steps"></a>次の手順

+ [SQL Server Machine Learning Services のインストール (データベース内)](install/sql-machine-learning-services-windows-install.md)
+ [Machine learning のチュートリアルとサンプル](tutorials/machine-learning-services-tutorials.md)
