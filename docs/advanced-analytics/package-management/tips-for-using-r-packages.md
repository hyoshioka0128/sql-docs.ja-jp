---
title: R パッケージを使用するためのヒント
description: R または SQL Server を初めて使用するユーザーのために SQL Server で R パッケージを使用する際に役立つヒントについて説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 08/06/2019
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 5e283ab478a6d65243e9962fd5c26f5f91d87c15
ms.sourcegitcommit: 00350f6ffb73c2c0d99beeded61c5b9baa63d171
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196345"
---
# <a name="tips-for-using-r-packages"></a>R パッケージを使用するためのヒント

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server で R パッケージを使用する際に役立つヒントについて説明します。 これらのヒントは、R に精通していない Dba と、SQL Server インスタンスでのパッケージアクセスに慣れていない経験豊富な R 開発者を対象としています。

## <a name="if-youre-new-to-r"></a>R を初めて使用する場合

管理者が最初に R パッケージをインストールするときに、R パッケージ管理についていくつかの基本事項を理解しておくと、作業の開始に役立ちます。

### <a name="package-dependencies"></a>パッケージの依存関係

R パッケージは、他の複数のパッケージに頻繁に依存しており、その一部は、インスタンスで使用される既定の R ライブラリでは使用できない可能性があります。 パッケージには、既にインストールされているものとは異なるバージョンの依存パッケージが必要になる場合があります。 パッケージの依存関係は、パッケージに埋め込まれている説明ファイルに記載されていますが、不完全な場合もあります。 [Igraph](https://igraph.org/r/)というパッケージを使用して、依存関係グラフを完全に明確にすることができます。

複数のパッケージをインストールする必要がある場合、または組織内のすべてのユーザーが正しいパッケージの種類とバージョンを取得できるようにする場合は、 [miniCRAN](https://mran.microsoft.com/package/miniCRAN)パッケージを使用して完全な依存関係チェーンを分析することをお勧めします。 minicRAN は、複数のユーザーまたはコンピューター間で共有できるローカルリポジトリを作成します。 詳細については、「 [miniCRAN を使用してローカルパッケージリポジトリを作成する](create-a-local-package-repository-using-minicran.md)」を参照してください。

### <a name="package-sources-versions-and-formats"></a>パッケージのソース、バージョン、および形式

[Cran](https://cran.r-project.org/)や[Bioconductor](https://www.bioconductor.org/)など、R パッケージには複数のソースがあります。 R 言語の公式サイト (<https://www.r-project.org/>) では、これらのリソースの多くが一覧表示されます。 Microsoft では、オープンソース R ([mran](https://mran.microsoft.com/open)) およびその他のパッケージの配布に [mran](https://mran.microsoft.com/) を提供しています。 多くのパッケージは、開発者がソースコードを入手できる GitHub に発行されます。

::: moniker range=">=sql-server-2016||=sqlallproducts-allversions"
R パッケージは、複数のコンピューティングプラットフォームで実行されます。 インストールするバージョンが Windows バイナリであることを確認してください。
::: moniker-end

::: moniker range=">=sql-server-linux-ver15||=sqlallproducts-allversions"
R パッケージは、複数のコンピューティングプラットフォームで実行されます。 インストールするバージョンが Linux バイナリであることを確認してください。
::: moniker-end

### <a name="know-which-library-youre-installing-to-and-which-packages-are-already-installed"></a>インストール先のライブラリ、およびインストール済みのパッケージを確認します。

コンピューター上の r 環境を以前に変更したことがある場合は、何かをインストール`.libPath`する前に、r 環境変数が1つのパスのみを使用するようにしてください。

このパスは、インスタンスの R_SERVICES フォルダーを指している必要があります。 既にインストールされているパッケージを確認する方法など、詳細については、「 [Get R package information](../package-management/r-package-information.md)」を参照してください。

## <a name="if-youre-new-to-sql-server"></a>初めて SQL Server

SQL Server で実行されているコードを操作する R 開発者は、サーバーを保護するセキュリティポリシーによって、R 環境を制御する機能が制限されます。 次のヒントでは、一般的な状況について説明し、この環境での作業に関する推奨事項を示します。

### <a name="r-user-libraries-not-supported-on-sql-server"></a>R ユーザーライブラリ: SQL Server ではサポートされていません

新しい R パッケージをインストールする必要がある r 開発者は、既定のライブラリを使用できないとき、または開発者がコンピューターの管理者でない場合に、プライベートのユーザーライブラリを使用して、パッケージをインストールすることに慣れています。 たとえば、一般的な r 開発環境では、ユーザーはパッケージの場所を r 環境変数`libPath`に追加するか、完全なパッケージパスを参照します。次に例を示します。

```R
library("c:/Users/<username>/R/win-library/packagename")
```

これは、SQL Server で R ソリューションを実行する場合は機能しません。 R パッケージは、インスタンスに関連付けられている特定の既定のライブラリにインストールする必要があるためです。 パッケージを既定のライブラリで利用できない場合、パッケージを呼び出そうとすると、次のエラーが表示されます。

*ライブラリ (xxx) にエラーがあります: ' package name ' という名前のパッケージはありません*

SQL Server で R パッケージをインストールする方法については、「 [SQL Server Machine Learning Services または SQL Server R Services での新しい r パッケージのインストール](install-additional-r-packages-on-sql-server.md)」を参照してください。

### <a name="how-to-avoid-package-not-found-errors"></a>"パッケージが見つかりません" エラーを回避する方法

次のガイドラインを使用すると、"パッケージが見つかりません" エラーを回避できます。

+ ユーザーライブラリへの依存関係を排除します。

    カスタムユーザーライブラリに必要な R パッケージをインストールするのは、不適切な開発手法です。 このため、ライブラリの場所にアクセスできない別のユーザーによってソリューションが実行されていると、エラーが発生する可能性があります。

    また、パッケージが既定のライブラリにインストールされている場合、r コードで別のバージョンを指定した場合でも、R ランタイムは既定のライブラリからパッケージを読み込みます。

+ コードを共有環境で実行できることを確認します。

+ ソリューションの一部としてパッケージをインストールしないようにします。 パッケージをインストールするアクセス許可がない場合、コードは失敗します。 パッケージをインストールするアクセス許可を持っている場合でも、実行する他のコードとは別に行う必要があります。

+ インストールされていないパッケージへの呼び出しがないよう、コードを確認します。

+ R パッケージまたは R ライブラリのパスへの直接参照を削除するようにコードを更新します。

+ インスタンスに関連付けられているパッケージライブラリを把握します。 詳細については、「 [Get R package information](../package-management/r-package-information.md)」を参照してください。

## <a name="see-also"></a>関連項目

+ [新しい R パッケージのインストール](install-additional-r-packages-on-sql-server.md)
+ [新しい Python パッケージのインストール](../python/install-additional-python-packages-on-sql-server.md)
+ [チュートリアル、サンプル、ソリューション](../tutorials/machine-learning-services-tutorials.md)