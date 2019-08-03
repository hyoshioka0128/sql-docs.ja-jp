---
title: 既定の R および Python パッケージライブラリ
description: R Services、R Server、Machine Learning Services (データベース内)、および Machine Learning Server (スタンドアロン) 用の SQL Server によってインストールされる r および Python パッケージ
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: e49b843b0b32969bd440177cf445916487ad2670
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715203"
---
# <a name="default-r-and-python-packages-in-sql-server"></a>SQL Server の既定の R および Python パッケージ
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server と共にインストールされる R および Python パッケージと、パッケージライブラリを検索する場所の一覧を示します。  

## <a name="r-package-list-for-sql-server"></a>SQL Server の R パッケージの一覧

R パッケージは、セットアップ時に R 機能を選択したときに[SQL Server 2016 r Services](../install/sql-r-services-windows-install.md)と[SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)と共にインストールされます。 

|パッケージ         | 2016 | 2017 | 説明 |
|----------------|--------------|--------------|-------------|
| [RevoScaleR](https://docs.microsoft.com/r-server/r-reference/revoscaler/revoscaler)  | 8.0.3 | 9.2 | リモートの計算コンテキスト、ストリーミング、データのインポートと変換、モデリング、視覚化、および分析のための rx 関数の並列実行に使用されます。 |
| [sqlrutils](https://docs.microsoft.com/machine-learning-server/r-reference/sqlrutils/sqlrutils) | 8.0.3 | 9.2 |ストアドプロシージャに R スクリプトを含めるために使用されます。 |
| [MicrosoftML](https://docs.microsoft.com/r-server/r-reference/microsoftml/microsoftml-package)| n.a. | 9.2 | R に機械学習アルゴリズムを追加します。 | 
| [olapR](https://docs.microsoft.com/machine-learning-server/r-reference/olapr/olapr) | n.a.  | 9.2 | R で MDX ステートメントを記述するために使用されます。 |

SQL Server Machine Learning Services では、既定で Microsoft Ml と olapR を利用できます。 SQL Server 2016 R Services インスタンスでは、[コンポーネントをアップグレード](../install/upgrade-r-and-python.md)することで、これらのパッケージを追加できます。 コンポーネントのアップグレードでは、パッケージの新しいバージョンも取得されます (たとえば、新しいバージョンの RevoScaleR には、SQL Server でのパッケージ管理用の関数が含まれています)。

## <a name="python-package-list-for-sql-server"></a>SQL Server の Python パッケージ一覧

Python パッケージは、 [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)をインストールし、python 機能を選択した場合に SQL Server 2017 でのみ使用できます。

| パッケージ         | 2017    |  説明 |
| -----------------|-------------|------------|
| [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) | 9.2 | リモートの計算コンテキスト、ストリーミング、データのインポートと変換、モデリング、視覚化、および分析のための rx 関数の並列実行に使用されます。 |
| [microsoftml](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) | 9.2 | Python で機械学習アルゴリズムを追加します。 |

## <a name="open-source-r-in-your-installation"></a>インストールでのオープンソース R

R サポートにはオープンソースが含まれているので、base R 関数を呼び出して、追加のオープンソースおよびサードパーティパッケージをインストールできます。 R 言語サポートには、**ベース**、**統計**、**ユーティリティ**などのコア機能が含まれています。 R の基本インストールには、多くのサンプルデータセットと、 **Rgui** (軽量対話型エディター) や**Rgui** (R コマンドプロンプト) などの標準の r ツールも含まれています。 

インストールに含まれるオープンソース R のディストリビューションは、 [Microsoft R open (MRO)](https://mran.microsoft.com/open)です。 MRO は、 [Intel Math Kernel Library](https://en.wikipedia.org/wiki/Math_Kernel_Library)などの追加のオープンソースパッケージを含めることによって、base R に値を追加します。

次の表は、SQL Server セットアップを使用して MRO で提供される R のバージョンをまとめたものです。

|リリース             | R バージョン       |
|--------------------|-----------------|
| [SQL Server 2016 R Services](../install/sql-r-services-windows-install.md) | 3.2.2   | 
| [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md) | 3.3.3 |

新しいバージョンの web で SQL Server セットアップによってインストールされた R のバージョンを手動で上書きすることは避けてください。 Microsoft R パッケージは、R の特定のバージョンに基づいています。インストールを変更すると、それが不安定になる可能性があります。

## <a name="open-source-python-in-your-installation"></a>インストールにおけるオープンソースの Python

SQL Server 2017 は Python コンポーネントを追加します。 [Python 言語] オプションを選択すると、Anaconda 4.2 distribution がインストールされます。 Python コードライブラリに加えて、標準インストールには、サンプルデータ、単体テスト、およびサンプルスクリプトが含まれています。 

SQL Server 2017 Machine Learning は、R と Python の両方をサポートするための最初のリリースです。

|リリース             | Anaconda のバージョン| Microsoft パッケージ    |
|--------------------|-----------------|-----------------------|
| SQL Server Machine Learning サービス  | 4.2 over Python 3.5 | revoscalepy、microsoft ml |

新しいバージョンの web で SQL Server セットアップによってインストールされた Python のバージョンを手動で上書きすることは避けてください。 Microsoft Python パッケージは、Anaconda の特定のバージョンに基づいています。 インストールを変更すると、それが不安定になる可能性があります。

## <a name="component-upgrades"></a>コンポーネントのアップグレード

最初のインストール後、R および Python パッケージはサービスパックと累積更新プログラムによって更新されますが、完全バージョンのアップグレードは、最新のライフサイクルサポートポリシーに*バインド*することによってのみ可能です。 バインドによってサービスモデルが変更されます。 既定では、最初のインストール後に、サービスパックと累積更新プログラムによって R パッケージが更新されます。 コア R コンポーネントの追加パッケージと完全バージョンアップグレードは、製品のアップグレード (SQL Server 2016 から SQL Server 2017) を通じて、または R サポートを Microsoft Machine Learning Server にバインドすることによってのみ可能です。 詳細については、「 [SQL Server での R および Python コンポーネントのアップグレード](../install/upgrade-r-and-python.md)」を参照してください。

## <a name="package-library-location"></a>パッケージライブラリの場所

SQL Server を使用して machine learning をインストールすると、インストールする言語ごとに1つのパッケージライブラリがインスタンスレベルで作成されます。 Windows では、インスタンスライブラリは SQL Server に登録されているセキュリティで保護されたフォルダーです。

SQL Server でデータベース内で実行されるすべてのスクリプトまたはコードは、インスタンスライブラリから関数を読み込む必要があります。 SQL Server は、他のライブラリにインストールされているパッケージにアクセスできません。 これは、リモートクライアントにも当てはまります。 リモートクライアントからサーバーに接続する場合、サーバーコンピューティングコンテキストで実行するすべての R または Python コードでは、インスタンスライブラリにインストールされているパッケージのみを使用できます。

サーバー資産を保護するために、既定のインスタンスライブラリは、コンピューターの管理者のみが変更できます。 コンピューターの所有者でない場合は、このライブラリにパッケージをインストールするために、管理者からアクセス許可を取得する必要がある場合があります。 

#### <a name="file-path-for-in-database-engine-instances"></a>データベース内エンジンインスタンスのファイルパス

次の表は、バージョンとデータベースエンジンのインスタンスの組み合わせに関する R と Python のファイルの場所を示しています。 MSSQL13.MSSQLSERVER は SQL Server 2016 を示し、は R のみです。 MSSQL14. は SQL Server 2017 を示し、R および Python フォルダーを持っています。 

ファイルパスには、インスタンス名も含まれます。 SQL Server は、[データベースエンジンインスタンス](../../database-engine/configure-windows/database-engine-instances-sql-server.md)を既定のインスタンス (MSSQLSERVER) として、またはユーザー定義の名前付きインスタンスとしてインストールします。 SQL Server が名前付きインスタンスとしてインストールされている場合は、のよう`MSSQL13.<instance_name>`に名前が追加されます。

|バージョンと言語  | 既定のパス|
|----------------------|------------|
| SQL Server 2016 |C:\Program Server\MSSQL13. SQLMSSQLSERVER\R_SERVICES\library|
| R を使用した SQL Server 2017|C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library |
| Python を使用した2017の SQL Server |C:\Program Server\MSSQL14. SQLMSSQLSERVER\PYTHON_SERVICES\Lib\site-packages |


#### <a name="file-path-for-standalone-server-installations"></a>スタンドアロンサーバーインストールのファイルパス

次の表は、SQL Server 2016 R Server (スタンドアロン) または SQL Server 2017 Machine Learning Server (スタンドアロン) サーバーがインストールされている場合の、バイナリの既定のパスを示しています。 

|バージョン| インストール|既定のパス|
|-------|-------------|------------|
| SQL Server 2016|R Server (スタンドアロン)| C:\Program 130@ SERVER @ _s|
|SQL Server 2017|R を使用した Machine Learning Server |C:\Program 140@ SERVER @ _s|
|SQL Server 2017|Machine Learning Server、Python |C:\Program 140SQL (_s) \ python SERVER|

> [!NOTE]
> サブフォルダーの名前とファイルが類似している他のフォルダーがある場合は、Microsoft R Server または[Machine Learning Server](https://docs.microsoft.com/machine-learning-server/)のスタンドアロンインストールを使用している可能性があります。 これらのサーバー製品には、異なるインストーラーとパスがあります (つまり、C:\Program Files\Microsoft\R Server\r _s または C:\Program Files\Microsoft\ML SERVER\R _s)。 詳細については、「 [windows 用の Machine Learning Server のインストール](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-install)」または「 [Windows 用 R Server 9.1 のインストール](https://docs.microsoft.com/machine-learning-server/install/r-server-install-windows)」を参照してください。

## <a name="next-steps"></a>次のステップ

+ [パッケージ情報の取得](installed-package-information.md)
+ [新しい R パッケージのインストール](../r/install-additional-r-packages-on-sql-server.md)
+ [新しい Python パッケージのインストール](../python/install-additional-python-packages-on-sql-server.md)
+ [リモートの R パッケージ管理を有効にする](../r/r-package-how-to-enable-or-disable.md)
+ [R パッケージ管理の RevoScaleR 関数](../r/use-revoscaler-to-manage-r-packages.md)
+ [R パッケージの同期](../r/package-install-uninstall-and-sync.md)
+ [ローカルの R パッケージ リポジトリの miniCRAN](../r/create-a-local-package-repository-using-minicran.md)
