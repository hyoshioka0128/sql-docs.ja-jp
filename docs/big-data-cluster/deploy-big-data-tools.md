---
title: ビッグ データ ツールをインストールする
titleSuffix: SQL Server big data clusters
description: (プレビュー) で使用するツール[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]をインストールする方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: afd036dfe33ab538d3d845e73f45e804052a9903
ms.sourcegitcommit: ecb19d0be87c38a283014dbc330adc2f1819a697
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70238700"
---
# <a name="install-sql-server-2019-big-data-tools"></a>SQL Server 2019 のビッグ データ ツールをインストールする

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

この記事では、(プレビュー) を作成、管理、および使用[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]するためにインストールする必要があるクライアントツールについて説明します。 次のセクションでは、ツールおよびインストール手順へのリンクの一覧を示します。 ビッグ データ クラスターを配置する前に、必須とマークされているツールを Windows または Linux で構成します。

[!INCLUDE [Limited public preview note](../includes/big-data-cluster-preview-note.md)]

## <a name="big-data-cluster-tools"></a>ビッグ データ クラスターのツール

次の表では、一般的なビッグ データ クラスター ツールとそのインストール方法を示します。

| ツール | 必須 | 説明 | インストール |
|---|---|---|---|
| **python** | はい | Python は、動的なセマンティクスを備えた、インタープリター方式でオブジェクト指向の高水準プログラミング言語です。 SQL Server 用のビッグ データ クラスターの多くの部分で、Python が使われています。 | [Python のインストール](#python)|
| **azdata** | はい | ビッグ データ クラスターをインストールして管理するためのコマンドライン ツールです。 | [インストール](deploy-install-azdata.md) |
| **kubectl**<sup>1</sup> | はい | 基になる Kuberentes クラスターを監視するためのコマンドライン ツールです ([詳細情報](https://kubernetes.io/docs/tasks/tools/install-kubectl/))。 | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-native-package-management) |
| **Azure Data Studio-SQL Server 2019 リリース候補 (RC)** | はい | SQL Server のクエリを実行するためのクロスプラットフォームグラフィックツール。 | [インストール](#download-and-install-azure-data-studio-sql-server-2019-release-candidate-rc) |
| **SQL Server 2019 拡張機能** | はい | ビッグ データ クラスターへの接続をサポートする Azure Data Studio の拡張機能です。 データ仮想化ウィザードも提供されています。 | [インストール](../azure-data-studio/sql-server-2019-extension.md) |
| **Azure CLI**<sup>2</sup> | AKS の場合 | Azure のサービスを管理するための最新のコマンドライン インターフェイスです。 AKS ビッグ データ クラスターの展開で使われます ([詳細情報](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest))。 | [インストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) |
| **mssql-cli** | 省略可 | SQL Server のクエリを実行するための最新のコマンドライン インターフェイスです ([詳細情報](https://github.com/dbcli/mssql-cli/blob/master/README.rst))。 | [Windows](https://github.com/dbcli/mssql-cli/blob/master/doc/installation/windows.md) \| [Linux](https://github.com/dbcli/mssql-cli/blob/master/doc/installation/linux.md) |
| **sqlcmd** | 一部のスクリプトの場合 | SQL Server のクエリを実行するための従来のコマンドライン ツールです ([詳細情報](https://docs.microsoft.com/sql/tools/sqlcmd-utility?view=sql-server-ver15))。 | [Windows](https://www.microsoft.com/download/details.aspx?id=36433) \| [Linux](../linux/sql-server-linux-setup-tools.md) |
| **curl** <sup>3</sup> | 一部のスクリプトの場合 | URL でデータを転送するためのコマンドライン ツールです。 | [Windows](https://curl.haxx.se/windows/) \| Linux: curl パッケージのインストール |

<sup>1</sup> kubectl バージョン1.13 以降を使用する必要があります。 また、kubectl のバージョンは、Kubernetes クラスターより 1 つ上または下のマイナー バージョンにする必要があります。 特定のバージョンの Kubectl クライアントをインストールする場合は、「[curl を使用して kubectl バイナリをインストールする](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl)」を参照してください (windows 10 では、Windows PowerShell ではなく cmd.exe を使って、curl を実行してください)。

> [!TIP]
> Azure Kubernetes Service (AKS) に以前に展開されたクラスターで kubectl を使うには、次の Azure CLI コマンドを使ってクラスター コンテキストを設定する必要があります。
>
>    ```azurecli
>    az aks get-credentials --name <aks_cluster_name> --resource-group <azure_resource_group_name>
>    ```

<sup>2</sup> Azure CLI バージョン 2.0.4 以降を使う必要があります。 バージョンを確認する必要がある場合は、`az --version` を実行します。

<sup>3</sup> Windows 10 の場合は、cmd プロンプトから実行するときは、**curl** は既に PATH にあります。 他のバージョンの Windows では、リンクを使って **curl** をダウンロードし、PATH に配置します。

## <a name="which-tools-are-required"></a>必要なツール

上の表では、ビッグ データ クラスターで使われる一般的なツールがすべて示されています。 どのツールが必要かは、実際のシナリオによって異なります。 ただし、一般に、クラスターの管理、接続、クエリを行うには、次のツールが最も重要です。

- **azdata**
- **kubectl**
- **Azure Data Studio**
- **SQL Server 2019 拡張機能**

残りのツールは、特定のシナリオでのみ必要です。 **Azure CLI** を使って、AKS の展開に関連付けられている Azure サービスを管理できます。 **mssql-cli** は必須ではありませんが便利なツールです。これを使うと、クラスター内の SQL Server マスター インスタンスに接続し、コマンド ラインからクエリを実行できます。 そして、GitHub スクリプトでサンプル データをインストールする場合は、**sqlcmd** と **curl** が必要です。

### <a id="python"></a> Python をオフラインでインストールする

1. インターネットにアクセスできるコンピューターで、Python が含まれる次のいずれかの圧縮ファイルをダウンロードします。

   | オペレーティング システム | ダウンロード |
   |---|---|
   | Windows | [https://go.microsoft.com/fwlink/?linkid=2074021](https://go.microsoft.com/fwlink/?linkid=2074021) |
   | Linux   | [https://go.microsoft.com/fwlink/?linkid=2065975](https://go.microsoft.com/fwlink/?linkid=2065975) |
   | OSX     | [https://go.microsoft.com/fwlink/?linkid=2065976](https://go.microsoft.com/fwlink/?linkid=2065976) |

1. 圧縮されたファイルをターゲット コンピューターにコピーし、任意のフォルダーに抽出します。

1. Windows の場合にのみ、そのフォルダーから `installLocalPythonPackages.bat` を実行して、パラメーターと同じフォルダーへの完全なパスを渡します。

   ```PowerShell
   installLocalPythonPackages.bat "C:\python-3.6.6-win-x64-0.0.1-offline\0.0.1"
   ```

## <a name="download-and-install-azure-data-studio-sql-server-2019-release-candidate-rc"></a>Azure Data Studio SQL Server 2019 リリース候補 (RC) のダウンロードとインストール

Azure Data Studio SQL Server 2019 RC には、SQL Server 2019 RC 専用の機能が用意されています。

Azure Data Studio の通常の運用リリースでは、「Azure Data Studio の[ダウンロードとインストール](../azure-data-studio/download.md)」の手順に従います。

|プラットフォーム|ダウンロード|リリース日| バージョン |
|:---|:---|:---|:---|
|Windows|[ユーザー インストーラー (推奨)](https://go.microsoft.com/fwlink/?linkid=2102435)<br>[システム インストーラー](https://go.microsoft.com/fwlink/?linkid=2102523)<br>[.zip](https://go.microsoft.com/fwlink/?linkid=2102524)|2019年8月27日 |RC 1.11.0-insider|
|macOS|[.zip](https://go.microsoft.com/fwlink/?linkid=2102436)|2019年8月27日 |RC 1.11.0-insider|
|Linux|[.deb](https://go.microsoft.com/fwlink/?linkid=2102526)<br>[.rpm](https://go.microsoft.com/fwlink/?linkid=2102437)<br>[.tar.gz](https://go.microsoft.com/fwlink/?linkid=2102525)|2019年8月27日 |RC 1.11.0-insider|

最新のリリースに関する詳細は、次の [リリース ノート](../big-data-cluster/release-notes-big-data-cluster.md) を参照してください。

### <a name="get-azure-data-studio-for-windows"></a>Azure Data Studio for Windows を取得する

[!INCLUDE[name-sos](../includes/name-sos-short.md)] のこのリリースには、標準の Windows インストーラーのエクスペリエンスと、.zip ファイルが含まれています。

管理者特権を必要としないため、*ユーザー インストーラー*をお勧めします。これにより、インストールとアップグレードの両方が簡略化されます。 場所はユーザーの Local AppData (LOCALAPPDATA) フォルダーの下にあるため、ユーザー インストーラーに管理者特権は必要ありません。 また、ユーザーインストーラーでは、より滑らかなバックグラウンドの更新エクスペリエンスも提供されます。 詳細については、[Windows 用のユーザーのセットアップ](https://code.visualstudio.com/updates/v1_26#_user-setup-for-windows)に関するセクションを参照してください。

**ユーザー インストーラー** (推奨)

1. [Windows 用の [!INCLUDE[name-sos](../includes/name-sos-short.md)] *ユーザー* インストーラー](https://go.microsoft.com/fwlink/?linkid=2102435)をダウンロードして実行します。
2. [!INCLUDE[name-sos-short](../includes/name-sos-short.md)]のアプリを開始します。

**システム インストーラー**

1. [Windows 用の [!INCLUDE[name-sos](../includes/name-sos-short.md)] *システム* インストーラー](https://go.microsoft.com/fwlink/?linkid=2102523)をダウンロードして実行します。
2. [!INCLUDE[name-sos-short](../includes/name-sos-short.md)]のアプリを開始します。

**zip ファイル**

1. [[!INCLUDE[name-sos](../includes/name-sos-short.md)]for Windows .zip](https://go.microsoft.com/fwlink/?linkid=2102524) をダウンロードします。
2. ダウンロードしたファイルを参照し、展開します。
3. `\azuredatastudio-windows\azuredatastudio.exe`を実行します。

### <a name="get-azure-data-studio-for-macos"></a>Azure Data Studio for macOS を取得する

1. [ [!INCLUDE[name-sos](../includes/name-sos-short.md)] for macOS](https://go.microsoft.com/fwlink/?linkid=2102436) をダウンロードします。
2. zip のコンテンツを展開するには、ダブルクリックします。
3. [!INCLUDE[name-sos](../includes/name-sos-short.md)] を*スタート パッド*で使用できるようにするには、*Azure Data Studio.app* を *[アプリケーション]* フォルダーにドラッグします。

### <a name="get-azure-data-studio-for-linux"></a>Linux 用の Azure Data Studio を取得する

1. インストーラーのいずれか、または tar.gz アーカイブを使用することで、Linux 用の [!INCLUDE[name-sos](../includes/name-sos-short.md)] をダウンロードします。
    - [.deb](https://go.microsoft.com/fwlink/?linkid=2102526)
    - [.rpm](https://go.microsoft.com/fwlink/?linkid=2102437)
    - [.tar.gz](https://go.microsoft.com/fwlink/?linkid=2102525)
1. ファイルを展開し、[!INCLUDE[name-sos](../includes/name-sos-short.md)] を起動するため、新しいターミナル ウィンドウを開いて次のコマンドを入力します。

   **Debian のインストール:**
   ```bash
   cd ~
   sudo dpkg -i ./Downloads/azuredatastudio-linux-<version string>.deb

   azuredatastudio
   ```

   **rpm のインストール:**
   ```bash
   cd ~
   yum install ./Downloads/azuredatastudio-linux-<version string>.rpm

   azuredatastudio
   ```

   **tar.gz のインストール:**
   ```bash 
   cd ~ 
   cp ~/Downloads/azuredatastudio-linux-<version string>.tar.gz ~ 
   tar -xvf ~/azuredatastudio-linux-<version string>.tar.gz 
   echo 'export PATH="$PATH:~/azuredatastudio-linux-x64"' >> ~/.bashrc
   source ~/.bashrc 
   azuredatastudio 
   ``` 

   > [!NOTE]
   > Debian、Redhat、および Ubuntu で、依存関係が見つからないことがあります。 これらの依存関係をインストールするために、Linux のバージョンに応じて、次のコマンドを使用してください。
   

   **Debian:** 
   ```bash
   sudo apt-get install libunwind8
   ```

   **Redhat:** 
   ```bash
   yum install libXScrnSaver
   ```

   **Ubuntu:** 
   ```bash
   sudo apt-get install libxss1

   sudo apt-get install libgconf-2-4

   sudo apt-get install libunwind8
   ```

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

[!INCLUDE[name-sos](../includes/name-sos-short.md)] は Windows、macOS、そして Linux で動作し、次のプラットフォームでサポートされています。

#### <a name="windows"></a>Windows

- Windows 10 (64 ビット)
- Windows 8.1 (64 ビット)
- Windows 8 (64 ビット)
- Windows 7 (SP1) (64 ビット) - [KB2533623](https://www.microsoft.com/download/details.aspx?id=26767) が必要です
- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2 (64 ビット)
- Windows Server 2012 (64 ビット)
- Windows Server 2008 R2 (64 ビット)

#### <a name="macos"></a>macOS

- macOS 10.13 High Sierra
- macOS 10.12 Sierra

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.3
- SUSE Linux Enterprise Server v12 SP2
- Ubuntu 16.04

## <a name="next-steps"></a>次の手順

ツールを構成した後、クラウドまたはオンプレミスの Kubernetes に SQL Server 2019 ビッグ データ クラスターを配置します。 詳しくは、展開に関する次の記事をご覧ください。

- [クイックスタート: Azure Kubernetes Service (AKS) に SQL Server ビッグ データ クラスターを展開する](quickstart-big-data-cluster-deploy.md)
- [Kubernetes にデプロイ[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]する方法](deployment-guidance.md)

ビッグデータクラスターの詳細について[は[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]](big-data-cluster-overview.md)、「」を参照してください。
