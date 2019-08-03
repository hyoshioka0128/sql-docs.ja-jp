---
title: インターネットにアクセスせずに R 言語と Python コンポーネントをインストールする
description: ネットワークファイアウォールの内側にある分離された SQL Server インスタンスで、オフラインまたは切断された Machine Learning R と Python のセットアップ。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: ddeea99addae3229575ca581f344332587e85981
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715825"
---
# <a name="install-sql-server-machine-learning-r-and-python-on-computers-with-no-internet-access"></a>インターネットにアクセスできないコンピューターに SQL Server machine learning R と Python をインストールする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

既定では、インストーラーは Microsoft ダウンロードサイトに接続して、SQL Server での機械学習のために必要なコンポーネントと更新されたコンポーネントを取得します。 ファイアウォールの制約によって、インストーラーがこれらのサイトに到達できない場合は、インターネットに接続されたデバイスを使用してファイルをダウンロードし、オフラインのサーバーにファイルを転送してからセットアップを実行できます。

データベース内分析は、データベースエンジンインスタンスに加えて、SQL Server のバージョンに応じて、R および Python 統合用の追加コンポーネントで構成されます。 

+ SQL Server 2017 以降には、R と Python が含まれています 
+ SQL Server 2016 は R のみです。

分離サーバーでは、機械学習と R/Python 言語固有の機能が、CAB ファイルを介して追加されます。 

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
## <a name="sql-server-2017-offline-install"></a>SQL Server 2017 オフラインインストール

分離されたサーバーに SQL Server Machine Learning Services (R および Python) をインストールするには、まず、SQL Server の最初のリリースと、R と Python のサポートに対応する CAB ファイルをダウンロードします。 最新の累積的な更新プログラムを使用するようにサーバーをすぐに更新する予定がある場合でも、最初に最初のリリースをインストールする必要があります。

> [!Note]
> SQL Server 2017 には、サービスパックがありません。 これは、最初のリリースを唯一のベースラインとして使用し、累積更新プログラムのみを提供する SQL Server の最初のリリースです。 

### <a name="1---download-2017-cabs"></a>1-2017 Cab をダウンロードする

インターネットに接続されているコンピューターで、最初のリリースの R および Python の機能を提供する CAB ファイルと SQL Server 2017 のインストールメディアをダウンロードします。 

リリース  |ダウンロード リンク  |
---------|---------------|
Microsoft R Open     |[SRO_3.3.3.24_1033.cab](https://go.microsoft.com/fwlink/?LinkId=851496)|
Microsoft R Server      |[SRS_9.2.0.24_1033.cab](https://go.microsoft.com/fwlink/?LinkId=851507)|
Microsoft Python Open     |[SPO_9.2.0.24_1033.cab](https://go.microsoft.com/fwlink/?LinkId=851502) |
Microsoft Python サーバー    |[SPS_9.2.0.24_1033.cab](https://go.microsoft.com/fwlink/?LinkId=851508) |

###  <a name="2---get-sql-server-2017-installation-media"></a>2-SQL Server 2017 インストールメディアを入手する

1. インターネットに接続されているコンピューターで、 [SQL Server 2017 セットアッププログラム](https://www.microsoft.com/sql-server/sql-server-downloads)をダウンロードします。 

2. セットアップ をダブルクリックし、**ダウンロードメディア** インストールの種類を選択します。 このオプションを使用すると、セットアッププログラムによって、インストールメディアを含む .iso (.cab) ファイルが作成されます。

   ![ダウンロードメディアのインストールの種類を選択する](media/offline-download-tile.png "メディアのダウンロード")

::: moniker-end

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"

## <a name="sql-server-2016-offline-install"></a>SQL Server 2016 オフラインインストール

SQL Server 2016 のデータベース内分析は R のみです。製品パッケージ用の CAB ファイルが2つ、オープンソース R が Microsoft によって配布されています。 次のいずれかのリリースをインストールして開始します。RTM、SP 1、SP 2。 基本インストールが実施されたら、次の手順として累積的な更新プログラムを適用できます。

インターネットに接続しているコンピューターで、SQL Server 2016 でデータベース内分析をインストールするためにセットアップで使用される CAB ファイルをダウンロードします。 

### <a name="1---download-2016-cabs"></a>1-2016 Cab をダウンロードする

リリース  | Microsoft R Open | Microsoft R Server |
---------|-----------------|---------------------|
**SQL Server 2016 RTM**     | [SRO_3.2.2.803_1033.cab](https://go.microsoft.com/fwlink/?LinkId=761266) |[SRS_8.0.3.0_1033.cab](https://go.microsoft.com/fwlink/?LinkId=735051) |
**SQL Server 2016 SP 1**     | [SRO_3.2.2.15000_1033.cab](https://go.microsoft.com/fwlink/?LinkId=824879) |[SRS_8.0.3.15000_1033.cab](https://go.microsoft.com/fwlink/?LinkId=824881) | 
**SQL Server 2016 SP 2**  |[SRO_3.2.2.16000_1033.cab](https://go.microsoft.com/fwlink/?LinkId=866039) |[SRS_8.0.3.17000_1033.cab](https://go.microsoft.com/fwlink/?LinkId=850317) |

### <a name="2---get-sql-server-2016-installation-media"></a>2-SQL Server 2016 インストールメディアを入手する

ターゲットコンピューターに最初のインストールとして、SQL Server 2016 RTM、SP 1、または SP 2 をインストールすることができます。 これらのバージョンはいずれも、累積的な更新プログラムを受け入れることができます。

インストールメディアを含む .iso ファイルを取得する方法の1つとして、 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)を使用します。 サインインし、 **[ダウンロード]** リンクを使用して、インストールする SQL Server 2016 リリースを見つけます。 ダウンロードは .iso ファイルの形式で実行され、オフラインインストールの対象コンピューターにコピーできます。

::: moniker-end

## <a name="transfer-files"></a>ファイルの転送

SQL Server インストールメディア (.iso または .cab) とデータベース内分析 CAB ファイルをターゲットコンピューターにコピーします。 CAB ファイルとインストールメディアファイルを、セットアップユーザーの% TEMP * フォルダーなど、ターゲットコンピューターの同じフォルダーに配置します。

Python CAB ファイルには% TEMP% フォルダーが必要です。 R の場合は、% TEMP% を使用するか、myrcachedirectory パラメーターを CAB パスに設定できます。

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
次のスクリーンショットは、CAB ファイルと ISO ファイルの SQL Server を示しています。 

![転送されるファイルの一覧](media/offline-file-list.png "ファイルの一覧")
::: moniker-end

## <a name="run-setup"></a>セットアップの実行

インターネットに接続されていないコンピューターで SQL Server セットアップを実行すると、セットアップによって**オフラインインストール**ページがウィザードに追加され、前の手順でコピーした CAB ファイルの場所を指定できるようになります。

1. インストールを開始するには、.iso または .cab ファイルをダブルクリックして、インストールメディアにアクセスします。 Setup.exe**ファイルが**表示されます。

2. **Setup.exe**を右クリックし、[管理者として実行] をクリックします。

3. セットアップウィザードで、オープンソースの R または Python コンポーネントのライセンスページが表示されたら、[**同意**する] をクリックします。 ライセンス条項に同意すると、次の手順に進むことができます。

4. **[オフラインインストール]** ページが表示されたら、 **[インストールパス]** で、前の手順でコピーした CAB ファイルが格納されているフォルダーを指定します。

   ![Cab フォルダーを選択するためのウィザードページ](media/screenshot-sql-offline-cab-page.png "CAB フォルダー")

5. 画面の指示に従って、インストールを完了します。

<a name="apply-cu"></a>

## <a name="apply-cumulative-updates"></a>累積的な更新プログラムの適用

最新の累積的な更新プログラムをデータベースエンジンと機械学習コンポーネントの両方に適用することをお勧めします。 累積的な更新プログラムは、セットアッププログラムを使用してインストールされます。 

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
1. ベースラインインスタンスを使用して開始します。 SQL Server の最初のリリースの既存のインストールにのみ、累積的な更新プログラムを適用できます。

2. インターネットに接続されているデバイスで、使用しているバージョンの SQL Server の累積的な更新プログラムの一覧にアクセスします。

  + [SQL Server 2017 の更新プログラム](https://sqlserverupdates.com/sql-server-2017-updates/)
::: moniker-end

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
1. ベースラインインスタンスを使用して開始します。 累積的な更新プログラムは、SQL Server 2016 の初期リリース、SQL Server 2016 SP 1、または SQL Server 2016 SP 2 の既存のインストールにのみ適用できます。

2. インターネットに接続されているデバイスで、使用しているバージョンの SQL Server の累積的な更新プログラムの一覧にアクセスします。

  + [SQL Server 2016 の更新プログラム](https://sqlserverupdates.com/sql-server-2016-updates/)
::: moniker-end

3. 最新の累積的な更新プログラムを選択して、実行可能ファイルをダウンロードします。

4. R および Python 用の対応する CAB ファイルを取得します。 ダウンロードリンクについては、 [SQL Server データベース内分析インスタンスの累積的な更新プログラムの CAB ダウンロード](sql-ml-cab-downloads.md)に関するページを参照してください。

5. すべてのファイル、実行可能ファイル、および CAB ファイルを、オフラインコンピューター上の同じフォルダーに転送します。

6. セットアップを実行します。 ライセンス条項に同意し、[機能の選択] ページで、累積的な更新プログラムが適用されている機能を確認します。 機械学習機能を含む、現在のインスタンスにインストールされているすべての機能が表示されます。

    ![機能ツリーから機能を選択する](media/cumulative-update-feature-selection.png "機能の一覧")

5. ウィザードを続行し、R および Python ディストリビューションのライセンス条項に同意します。 インストール中に、更新された CAB ファイルを含むフォルダーの場所を選択するように求められます。

## <a name="set-environment-variables"></a>環境変数の設定

R 機能の統合のみの場合、 **MKL_CBWR**環境変数を設定し[て](https://software.intel.com/articles/introduction-to-the-conditional-numerical-reproducibility-cnr)、Intel MATH Kernel Library (MKL) 計算からの一貫した出力を確保する必要があります。

1. コントロールパネルで、[**システムとセキュリティ** >  > ] [システム] [システム**設定** > ] **[環境変数]** をクリックします。

2. 新しいユーザー変数またはシステム変数を作成します。 

  + 変数名をに設定します`MKL_CBWR`
  + 変数の値をに設定します。`AUTO`

この手順では、サーバーを再起動する必要があります。 スクリプトの実行を有効にする場合は、すべての構成作業が完了するまで再起動を停止できます。

## <a name="post-install-configuration"></a>インストール後の構成

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
インストールが完了したら、サービスを再起動し、スクリプトの実行を有効にするようにサーバーを構成します。

+ [外部スクリプトの実行を有効にする](sql-machine-learning-services-windows-install.md#bkmk_enableFeature)

SQL Server Machine Learning Services の初期オフラインインストールには、オンラインインストールと同じ構成が必要です。

+ [インストールの確認](sql-machine-learning-services-windows-install.md#verify-installation)
+ [必要に応じて追加の構成](sql-machine-learning-services-windows-install.md#additional-configuration)
::: moniker-end

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
インストールが完了したら、サービスを再起動し、スクリプトの実行を有効にするようにサーバーを構成します。

+ [外部スクリプトの実行を有効にする](sql-r-services-windows-install.md#bkmk_enableFeature)

SQL Server R Services の初期オフラインインストールには、オンラインインストールと同じ構成が必要です。

+ [インストールの確認](sql-r-services-windows-install.md#verify-installation)
+ [必要に応じて追加の構成](sql-r-services-windows-install.md#bkmk_FollowUp)
::: moniker-end

## <a name="next-steps"></a>次の手順

インスタンスのインストール状態を確認し、一般的な問題を解決するには、「 [SQL Server のカスタムレポート](../r/monitor-r-services-using-custom-reports-in-management-studio.md)」を参照してください。

不明なメッセージやログエントリについては、「[アップグレードとインストールに関してよく寄せられる質問-Machine Learning Services](../r/upgrade-and-installation-faq-sql-server-r-services.md)」を参照してください。
