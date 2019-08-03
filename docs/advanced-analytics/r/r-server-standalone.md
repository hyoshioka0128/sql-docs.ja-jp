---
title: スタンドアロンの R Server または Machine Learning Server インストール
description: 概要 SQL Server セットアップでのスタンドアロン R Server と Machine Learning Server の概要
ms.prod: sql
ms.technology: machine-learning
ms.date: 12/18/2018
ms.topic: overview
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||=sqlallproducts-allversions'
ms.openlocfilehash: d4843aaf14a944e7beef73ef1330985666007498
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715111"
---
# <a name="r-server-standalone-and-machine-learning-server-standalone-in-sql-server"></a>SQL Server の R Server (スタンドアロン) と Machine Learning Server (スタンドアロン)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server では、スタンドアロンの R サーバーまたは SQL Server とは別に実行される Machine Learning Server のインストールがサポートされます。 SQL Server のバージョンに応じて、スタンドアロンサーバーにはオープンソースの R と、場合によっては Python の基盤があり、Microsoft の高パフォーマンスのライブラリを使用して、大規模な統計分析や予測分析を追加します。 ライブラリは、R または Python でスクリプト化された機械学習タスクも有効にします。 

SQL Server 2016 では、この機能は**r Server (スタンドアロン)** と呼ばれ、r のみです。 SQL Server 2017 では**Machine Learning Server (スタンドアロン)** と呼ばれ、R と Python の両方が含まれています。  

> [!Note]
> SQL Server セットアップによってインストールされたスタンドアロンサーバーは、SQL 以外のバージョンの[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)と機能的に同等であり、リモート実行、運用化、web など、同じユーザーシナリオをサポートします。サービスと、R および Python ライブラリの完全なコレクション。

## <a name="components"></a>コンポーネント

SQL Server 2016 は R のみです。 SQL Server 2017 では、R と Python がサポートされています。 次の表では、各バージョンの機能について説明します。

| コンポーネント | 説明 |
|-----------|-------------|
| R パッケージ | [**RevoScaleR**](ref-r-revoscaler.md)は、データ操作、変換、視覚化、および分析を行うための機能を備えたスケーラブルな R のプライマリライブラリです。  <br/>[**Microsoft ml**](ref-r-microsoftml.md)は、機械学習アルゴリズムを追加して、テキスト分析、画像分析、およびセンチメント分析のためのカスタムモデルを作成します。 <br/>[**sqlRUtils**](ref-r-sqlrutils.md)には、r スクリプトを t-sql ストアドプロシージャに配置し、ストアドプロシージャをデータベースに登録し、r 開発環境からストアドプロシージャを実行するためのヘルパー関数が用意されています。<br/>[**mrsdeploy**](operationalization-with-mrsdeploy.md)では、web サービスのデプロイが提供されます (SQL Server 2017 のみ)。 <br/>[**Olapr**](ref-r-olapr.md)は、R で MDX クエリを指定するためのものです。|
| Microsoft R Open (MRO) | [**Mro**](https://mran.microsoft.com/open)は、Microsoft の R のオープンソースディストリビューションです。パッケージとインタープリターが含まれています。 セットアップでバンドルされているバージョンの MRO を常に使用してください。 |
| R ツール | R コンソールのウィンドウとコマンドプロンプトは、R ディストリビューションの標準ツールです。 このような場所は、Server\140\R_SERVER\bin\x64. にあります。 |
| R のサンプルとスクリプト |  オープンソースの R パッケージと RevoScaleR パッケージには、事前にインストールされたデータを使用してスクリプトを作成して実行するための組み込みデータセットが含まれています。 これらのデータは、「140」および「\library\RevoScaleR.」で確認してください。 |
| Python パッケージ | [**revoscalepy**](../python/ref-py-revoscalepy.md)は、データ操作、変換、視覚化、および分析を行うための機能を備えたスケーラブルな Python のプライマリライブラリです。 <br/>[**microsoft ml**](../python/ref-py-microsoftml.md)は、機械学習アルゴリズムを追加して、テキスト分析、画像分析、およびセンチメント分析のためのカスタムモデルを作成します。  |
| Python ツール | 組み込みの Python コマンドラインツールは、アドホックテストやタスクに役立ちます。 このツールは、「140」を参照してください。 |
| Anaconda | Anaconda は、Python と必須パッケージのオープンソースディストリビューションです。 |
| Python のサンプルとスクリプト | R と同様に、Python には組み込みのデータセットとスクリプトが含まれています。 Revoscalepy のデータを Server\140\PYTHON_SERVER\lib\site-packages\revoscalepy\data\sample-data. に検索する |
| R と Python の事前トレーニング済みのモデル | 事前トレーニング済みのモデルは、特定のユースケースに対して作成され、Microsoft のデータサイエンスエンジニアリングチームによって管理されます。 事前トレーニング済みのモデルを使用すると、指定した新しいデータ入力を使用して、テキスト内で肯定的なセンチメントをスコア付けしたり、画像の特徴を検出したりできます。 事前トレーニング済みのモデルはスタンドアロンサーバーでサポートされており、使用できますが SQL Server セットアップを使用してインストールすることはできません。 詳細については、「 [SQL Server に事前トレーニング済みの機械学習モデルをインストール](../install/sql-pretrained-models-install.md)する」を参照してください。 |

## <a name="using-a-standalone-server"></a>スタンドアロンサーバーを使用する

R と Python の開発者は、通常、オープンソースの R および Python のメモリと処理の制約を超えて移動するスタンドアロンサーバーを選択します。 スタンドアロンサーバーで実行されている R および Python ライブラリは、大量のデータを複数のコアで読み込んで処理し、結果を1つの統合された出力に集計できます。 高パフォーマンスの関数は、スケールとユーティリティの両方に対して設計されています。予測分析、統計モデリング、データの視覚化、およびによって設計およびサポートされている商用サーバー製品の最先端の機械学習アルゴリズムを提供します。エクスプローラー.

SQL Server から切り離された独立したサーバーとして、R および Python 環境は、SQL Server ではなく、スタンドアロンサーバーに用意されている基盤のオペレーティングシステムと標準ツールを使用して構成、セキュリティ保護、およびアクセスします。 SQL Server リレーショナルデータの組み込みサポートはありません。 SQL Server データを使用する場合は、任意のクライアントの場合と同様に、データソースオブジェクトと接続を作成できます。

SQL Server の補完として、スタンドアロンサーバーは、ローカルとリモートの両方のコンピューティングが必要な場合に強力な開発環境としても役立ちます。 スタンドアロンサーバー上の R および Python パッケージは、データベースエンジンのインストールに用意されているものと同じであり、コードの移植性と[コンピューティングコンテキストの切り替え](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-compute-context)が可能です。

## <a name="how-to-get-started"></a>開始する方法

セットアップを開始し、お気に入りの開発ツールにバイナリをアタッチして、最初のスクリプトを記述します。

### <a name="step-1-install-the-software"></a>手順 1:ソフトウェアのインストール

次のいずれかのバージョンをインストールします。

+ [SQL Server 2017 Machine Learning Server (スタンドアロン)](../install/sql-machine-learning-standalone-windows-install.md)
+ [SQL Server 2016 R Server (スタンドアロン)-R のみ](../install/sql-r-standalone-windows-install.md)

### <a name="step-2-configure-a-development-tool"></a>手順 2:開発ツールの構成

スタンドアロンサーバーでは、同じコンピューターにインストールされている開発を使用してローカルで作業するのが一般的です。

+ [R ツールの設定](set-up-a-data-science-client.md)
+ [Python ツールの設定](../python/setup-python-client-tools-sql.md)

### <a name="step-3-write-your-first-script"></a>手順 3:最初のスクリプトを作成する

RevoScaleR、revoscalepy、機械学習アルゴリズムの機能を使用して、R または Python スクリプトを記述します。
  
  + [25 の関数で R と RevoScaleR を探索し](https://docs.microsoft.com/machine-learning-server/r/tutorial-r-to-revoscaler)ます。基本的な R コマンドから始めて、R ソリューションへの高パフォーマンスとスケーリングを提供する RevoScaleR の再頒布可能分析関数に進みます。 最も一般的な R モデリング パッケージ (K-平均法クラスタリング、デシジョン ツリー、デシジョン フォレストなど) の並列化可能なバージョンの多くと、データ操作のツールを含みます。

  + [クイック スタート:Microsoft ml Python パッケージ](https://docs.microsoft.com/machine-learning-server/python/quickstart-binary-classification-with-microsoftml)を使用したバイナリ分類の例を次に示します。Microsoft ml の関数と既知の型の形式のがんデータセットを使用して、二項分類モデルを作成します。

タスクに最適な言語を選択します。 R は、SQL を使用して実装するのが困難な統計計算に最適です。 データに対するセットベースの操作では、の機能[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]を活用して最高のパフォーマンスを実現します。 列を非常に高速に計算するには、メモリ内データベースエンジンを使用します。

### <a name="step-4-operationalize-your-solution"></a>手順 4:ソリューションの運用化

スタンドアロンサーバーでは、SQL 以外の[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)の運用[化機能を](https://docs.microsoft.com//machine-learning-server/what-is-operationalization)使用できます。 運用化のためのスタンドアロンサーバーを構成できます。これにより、コードを web サービスとしてデプロイしてホストし、診断を実行し、web サービスの機能をテストすることができます。

### <a name="step-5-maintain-your-server"></a>手順 5:サーバーの保守

SQL Server は、累積更新プログラムを定期的にリリースします。 累積的な更新プログラムを適用すると、既存のインストールにセキュリティ機能と機能強化が追加されます。 

新機能または変更された機能の説明については、 [CAB のダウンロード](../install/sql-ml-cab-downloads.md)に関する記事と、 [SQL Server 2016 の累積的な更新](https://support.microsoft.com/help/3177312/sql-server-2016-build-versions)プログラムと[SQL Server 2017 の累積的な更新プログラム](https://support.microsoft.com/help/4047329)の web ページを参照してください。 

既存のインスタンスに更新プログラムを適用する方法の詳細については、「インストールの指示に従って[更新プログラムを適用](../install/sql-machine-learning-standalone-windows-install.md#apply-cu)する」を参照してください。

## <a name="see-also"></a>関連項目

 [R Server (スタンドアロン) または Machine Learning Server (スタンドアロン) のインストール](../install/sql-machine-learning-standalone-windows-install.md)

