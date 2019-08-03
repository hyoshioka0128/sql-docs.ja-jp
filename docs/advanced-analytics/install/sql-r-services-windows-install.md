---
title: SQL Server 2016 R Services (In-Database) をインストールする
description: Windows 上の SQL Server 2016 R Services のデータベースエンジンに、R プログラミング言語のサポートを追加します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 05/03/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: =sql-server-2016||=sqlallproducts-allversions
ms.openlocfilehash: 61dd49191e85d9fd4685904ae01b72d754d43318
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715808"
---
# <a name="install-sql-server-2016-r-services"></a>SQL Server 2016 R Services のインストール
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

この記事では、 **SQL Server 2016 R Services**をインストールして構成する方法について説明します。 SQL Server 2016 を使用している場合は、SQL Server で R コードの実行を有効にするために、この機能をインストールします。

SQL Server 2017 では、Python の追加を反映する[Machine Learning Services](../r/r-server-standalone.md)で R 統合が提供されています。 R の統合が必要で、SQL Server 2017 のインストールメディアがある場合は、「 [SQL Server Machine Learning Services をインストール](sql-machine-learning-services-windows-install.md)して機能を追加する」を参照してください。 

<a name="bkmk_prereqs"> </a> 

## <a name="pre-install-checklist"></a>インストール前のチェックリスト

+ データベースエンジンのインスタンスが必要です。 R だけをインストールすることはできませんが、既存のインスタンスに段階的に追加することはできます。

+ ビジネス継続性のために、 [Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)は R Services でサポートされています。 各ノードで R Services をインストールし、パッケージを構成する必要があります。

+ フェールオーバークラスターに R Services をインストールしないでください。 R プロセスを分離するために使用されるセキュリティメカニズムは、Windows Server フェールオーバークラスター環境と互換性がありません。

+ R Services をドメインコントローラーにインストールしないでください。 セットアップの R Services の部分は失敗します。

+ **共有機能** > の**R Server (スタンドアロン)** は、データベース内のインスタンスを実行している同じコンピューターにインストールしないでください。 

  SQL Server インスタンスはオープンソースの R および Anaconda ディストリビューションの独自のコピーを使用するため、他のバージョンの R および Python とのサイドバイサイドインストールが可能です。 ただし、SQL Server 外の SQL Server コンピューターで R および Python を使用するコードを実行すると、さまざまな問題が発生する可能性があります。
    
  + SQL Server でを実行しているときとは別のライブラリと実行可能ファイルを使用し、異なる結果を得ることができます。
  + 外部ライブラリで実行されている R および Python スクリプトは、SQL Server で管理できないため、リソースの競合が発生します。
  
以前のバージョンの変革分析開発環境または RevoScaleR パッケージを使用した場合、または SQL Server 2016 のプレリリースバージョンをインストールした場合は、アンインストールする必要があります。 古いバージョンと新しいバージョンの RevoScaleR およびその他の独自のパッケージの実行はサポートされていません。 以前のバージョンの削除については、「 [SQL Server Machine Learning Services のアップグレードとインストールに関する FAQ](../r/upgrade-and-installation-faq-sql-server-r-services.md)」を参照してください。

> [!IMPORTANT]
> セットアップが完了したら、この記事で説明されている追加の構成後の手順を完了してください。 これらの手順には、外部スクリプトを使用する SQL Server の有効化、およびユーザーの代わりに R ジョブを実行するために SQL Server に必要なアカウントの追加が含まれます。 通常、構成を変更するには、インスタンスを再起動するか、スタートパッドサービスを再起動する必要があります。

## <a name="get-the-installation-media"></a>インストール メディアを入手する

[!INCLUDE[GetInstallationMedia](../../includes/getssmedia.md)]

<a name="bkmk_ga_instalpatch"></a>

 ### <a name="install-patch-requirement"></a>パッチのインストール要件 

SQL Server の前提条件としてインストールされる特定のバージョンの Microsoft VC++ 2013 ランタイム バイナリに問題が見つかりました。 VC ランタイム バイナリに対するこの更新プログラムをインストールしないと、特定のシナリオにおいて、SQL Server で安定性の問題が発生する可能性があります。 SQL Server をインストールする前に、「[SQL Server リリース ノート](../../sql-server/sql-server-2016-release-notes.md#bkmk_ga_instalpatch)」にある手順に従って、ご使用のコンピューターに VC ランタイム バイナリのパッチが必要かどうかを確認してください。  

<a name="bkmk2016top"></a>

## <a name="run-setup"></a>セットアップの実行

ローカル インストールの場合は、セットアップを管理者として実行する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をリモート共有からインストールする場合は、そのリモート共有に対する読み取り権限と実行権限を持つドメイン アカウントを使用する必要があります。

1. SQL Server 2016 のセットアップウィザードを開始します。

2. **インストール** タブで、**新規 SQL Server スタンドアロンインストールを選択するか、既存のインストールに機能を追加**します。
    
   ![R Services (データベース内) のインストール](media/2016-setup-installation-rsvcs.png "R Services を使用してデータベースエンジンのインストールを開始")する
   
3. [**機能の選択**] ページで、次のオプションを選択します。

   - [**データベースエンジン Services**] を選択します。 Machine learning を使用する各インスタンスには、データベースエンジンが必要です。
   - [ **R Services (データベース内)** ] を選択します。 では、データベース内で R を使用するためのサポートがインストールされます。
    
     ![R Services 機能の選択](media/2016setup-rsvcs-features.png "データベース内の R Services の機能を選択する")

    > [!IMPORTANT]
    > R Server と R Services を同時にインストールしないでください。 通常は、R Server (スタンドアロン) をインストールして、データ科学者または開発者が R ソリューションを SQL Server に接続してデプロイするために使用する環境を作成します。 そのため、両方を同一のコンピューターにインストールする必要はありません。

4.  [ **Microsoft R Open のインストールに同意する**] ページで、[**同意**する] をクリックします。
  
    Microsoft r Open をダウンロードするには、この使用許諾契約書が必要です。これには、Microsoft R 開発チームの拡張 R パッケージと接続プロバイダーと共に、オープンソースの R 基本パッケージとツールの配布が含まれます。
  
5. 使用許諾契約書に同意した後、インストーラーが準備されている間、簡単に一時停止します。 ボタンが使用可能になったら、[**次へ**] をクリックします。

6. [**インストールの準備完了**] ページで、次の項目が含まれていることを確認し、[**インストール**] を選択します。

   + データベース エンジン サービス
   + R Services (データベース内)

    構成ファイルが格納されているパス`..\Setup Bootstrap\Log`の下にあるフォルダーの場所をメモしておきます。 セットアップが完了したら、概要ファイルにインストールされているコンポーネントを確認できます。

7. セットアップが完了したら、コンピューターを再起動するように指示された場合は、ここで実行します。 セットアップが完了した時点で、インストール ウィザードによるメッセージを確認することが重要です。 詳細については、「 [SQL Server セットアップ ログ ファイルの表示と読み取り](https://docs.microsoft.com/sql/database-engine/install-windows/view-and-read-sql-server-setup-log-files)」を参照してください。

## <a name="set-environment-variables"></a>環境変数の設定

R 機能の統合のみの場合、 **MKL_CBWR**環境変数を設定し[て](https://software.intel.com/articles/introduction-to-the-conditional-numerical-reproducibility-cnr)、Intel MATH Kernel Library (MKL) 計算からの一貫した出力を確保する必要があります。

1. コントロールパネルで、[**システムとセキュリティ** >  **] [システム]**  >  [システム**設定** > ] [**環境変数**] をクリックします。

2. 新しいユーザー変数またはシステム変数を作成します。 

  + 変数名をに設定します`MKL_CBWR`
  + 変数の値をに設定します。`AUTO`

この手順では、サーバーを再起動する必要があります。 スクリプトの実行を有効にする場合は、すべての構成作業が完了するまで再起動を停止できます。

<a name="bkmk_enableFeature"></a>

##  <a name="enable-script-execution"></a>スクリプトの実行を有効にする

1. [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]を開きます。 

    > [!TIP]
    > 適切なバージョンは、次のページからダウンロードしてインストールできます。[SQL Server Management Studio (SSMS) をダウンロードしてください](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。
    > 
    > また、 [Azure Data Studio](../../azure-data-studio/what-is.md)のプレビューリリースを試すこともできます。これにより、SQL Server に対する管理タスクとクエリがサポートされます。
  
2. Machine Learning Services をインストールしたインスタンスに接続し、[**新しいクエリ**] をクリックしてクエリウィンドウを開き、次のコマンドを実行します。

   ```sql
   sp_configure
   ```
    プロパティ `external scripts enabled` の値は、この時点では **0** であることが必要です。 これは、機能が既定で無効になっているためです。 この機能は、R または Python スクリプトを実行する前に、管理者が明示的に有効にする必要があります。
     
3. 外部スクリプト機能を有効にするには、次のステートメントを実行します。
  
    ```sql
    EXEC sp_configure  'external scripts enabled', 1
    RECONFIGURE WITH OVERRIDE
    ```
  
## <a name="restart-the-service"></a>サービスを再起動します。

インストールが完了したら、次のスクリプトの実行を有効にする前に、データベースエンジンを再起動します。

サービスを再起動すると、関連[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)]するサービスも自動的に再起動されます。

サービスを再起動するには、SSMS でインスタンスの右クリック**restart**コマンドを使用するか、コントロールパネルの [**サービス**] パネルを使用するか、または[SQL Server 構成マネージャー](../../relational-databases/sql-server-configuration-manager.md)を使用します。

## <a name="verify-installation"></a>インストールの確認

[カスタムレポート](../r/monitor-r-services-using-custom-reports-in-management-studio.md)を使用して、インスタンスのインストール状態を確認します。

外部スクリプトの起動に使用されるすべてのコンポーネントが実行されていることを確認するには、次の手順に従います。

1. SQL Server Management Studio で、新しいクエリウィンドウを開き、次のコマンドを実行します。
    
    ```sql
    EXEC sp_configure  'external scripts enabled'
    ```

    この時点で、**run_value** が 1 に設定されている必要があります。

2. [**サービス**] パネルまたは SQL Server 構成マネージャーを開き、 **SQL Server Launchpad サービス**が実行されていることを確認します。 R または Python がインストールされているデータベースエンジンインスタンスごとに1つのサービスを用意する必要があります。 サービスの詳細については、「[機能拡張フレームワーク](../concepts/extensibility-framework.md)」を参照してください。

7. スタートパッドが実行されている場合は、単純な R を実行して、外部スクリプトランタイムが SQL Server と通信できることを確認できます。 

    で[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]新しい**クエリ**ウィンドウを開き、次のようなスクリプトを実行します。
    
    ```sql
    EXEC sp_execute_external_script  @language =N'R',
    @script=N'
    OutputDataSet <- InputDataSet;
    ',
    @input_data_1 =N'SELECT 1 AS hello'
    WITH RESULT SETS (([hello] int not null));
    GO
    ```

    スクリプトの実行には、最初に外部スクリプトランタイムが読み込まれるときに少し時間がかかります。 結果は次のようになります。

    | hello |
    |----|
    | 1|

<a name="apply-cu"></a>

## <a name="apply-updates"></a>更新プログラムの適用

最新の累積的な更新プログラムをデータベースエンジンと機械学習コンポーネントの両方に適用することをお勧めします。

インターネットに接続されているデバイスでは、通常、累積更新プログラムは Windows Update によって適用されますが、次の手順を使用して、管理された更新プログラムを適用することもできます。 データベースエンジンの更新プログラムを適用すると、同じインスタンスにインストールした R ライブラリの累積更新プログラムがによって取得されます。 

切断されたサーバーでは、追加の手順が必要です。 詳細については、「[インターネットにアクセスできないコンピューターにインストールする > 累積的な更新プログラムを適用](sql-ml-component-install-without-internet-access.md#apply-cu)する」を参照してください。

1. 既にインストールされているベースラインインスタンスを使用して開始する:SQL Server 2016 初期リリース、SQL Server 2016 SP 1、または SQL Server 2016 SP 2。

2. 累積更新プログラムの一覧にアクセスします。[SQL Server 2016 の更新プログラム](https://sqlserverupdates.com/sql-server-2016-updates/)

3. 最新の累積的な更新プログラムを選択します。 実行可能ファイルは自動的にダウンロードおよび抽出されます。

4. セットアップを実行します。 ライセンス条項に同意し、[機能の選択] ページで、累積的な更新プログラムが適用されている機能を確認します。 R Services を含む、現在のインスタンスにインストールされているすべての機能が表示されます。 すべての機能を更新するために必要な CAB ファイルがダウンロードされます。

5. ウィザードを続行し、R ディストリビューションのライセンス条項に同意します。 

<a name="bkmk_FollowUp"></a> 

## <a name="additional-configuration"></a>その他の構成

外部スクリプトの検証手順が成功した場合は、SQL Server Management Studio、Visual Studio Code、または T-sql ステートメントをサーバーに送信できるその他のクライアントから Python コマンドを実行できます。

コマンドの実行中にエラーが発生した場合は、このセクションの追加の構成手順を確認してください。 場合によっては、サービスまたはデータベースに対して適切な構成を追加する必要があります。

インスタンスレベルでは、追加の構成には次のものが含まれます。

* [SQL Server Machine Learning Services のファイアウォール構成](../../advanced-analytics/security/firewall-configuration.md)
* [追加のネットワークプロトコルを有効にする](../../database-engine/configure-windows/enable-or-disable-a-server-network-protocol.md)
* [リモート接続を有効にする](../../database-engine/configure-windows/configure-the-remote-access-server-configuration-option.md)
* ディスク[クォータを管理](https://docs.microsoft.com/windows/desktop/fileio/managing-disk-quotas)して、ディスク領域を消費するタスクを実行する外部スクリプトを回避する

<a name="bkmk_configureAccounts"></a>
<a name="bkmk_AllowLogon"></a>

データベースでは、次の構成の更新が必要になる場合があります。

* [SQL Server するためのアクセス許可をユーザーに付与する Machine Learning Services](../../advanced-analytics/security/user-permission.md)
* [データベース ユーザーとしての SQLRUserGroup の追加](../../advanced-analytics/security/create-a-login-for-sqlrusergroup.md)

> [!NOTE]
> 一覧に表示されているすべての変更が必要であるとは限りません。必要になる場合もあります。 要件は、SQL Server をインストールしたセキュリティスキーマと、ユーザーがデータベースに接続して外部スクリプトを実行することを期待する方法によって異なります。 その他のトラブルシューティングのヒントについては、次を参照してください。[アップグレードとインストールに関してよく寄せられる質問](../r/upgrade-and-installation-faq-sql-server-r-services.md)

## <a name="suggested-optimizations"></a>推奨される最適化

すべての作業が完了したので、機械学習をサポートするようにサーバーを最適化したり、事前トレーニング済みのモデルをインストールしたりすることもできます。

### <a name="add-more-worker-accounts"></a>ワーカーアカウントを追加する

R を頻繁に使用する可能性がある場合、または多数のユーザーが同時にスクリプトを実行することが予想される場合は、スタートパッドサービスに割り当てられているワーカーアカウントの数を増やすことができます。 詳細については、「 [SQL Server Machine Learning Services のユーザーアカウントプールを変更](../administration/modify-user-account-pool.md)する」を参照してください。

<a name="bkmk_optimize"></a>

### <a name="optimize-the-server-for-external-script-execution"></a>外部スクリプト実行用にサーバーを最適化する

セットアップの[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]既定の設定は、データベースエンジンによってサポートされるさまざまなサービス (抽出、変換、読み込み (ETL) プロセス、レポート、監査、およびなど) について、サーバーのバランスを最適化することを目的としています。データを使用[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]するアプリケーション。 そのため、既定の設定では、特にメモリを集中的に使用する操作で、機械学習用のリソースが制限または調整されることがあります。

Machine learning ジョブの優先順位とリソースを適切に設定するには、SQL Server Resource Governor を使用して外部リソースプールを構成することをお勧めします。 また、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]データベースエンジンに割り当てられているメモリの量を変更したり、 [!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)]サービスで実行されるアカウントの数を増やしたりすることもできます。

- 外部リソースを管理するためのリソースプールを構成するには、「[外部リソースプールの作成](../../t-sql/statements/create-external-resource-pool-transact-sql.md)」を参照してください。
  
- データベース用に予約されているメモリの量を変更するには、「[サーバーメモリ構成オプション](../../database-engine/configure-windows/server-memory-server-configuration-options.md)」を参照してください。
  
- で[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)]開始できる R アカウントの数を変更するには、「 [machine learning のユーザーアカウントプールを変更](../administration/modify-user-account-pool.md)する」を参照してください。

Standard Edition を使用していて、Resource Governor がない場合は、動的管理ビュー (Dmv) と拡張イベントを使用して、Windows イベント監視を使用して、R で使用されるサーバーリソースを管理することができます。詳細については、「 [R Services の監視と管理](../r/managing-and-monitoring-r-solutions.md)」を参照してください。

### <a name="install-additional-r-packages"></a>追加の R パッケージをインストールする

SQL Server 用に作成する R ソリューションでは、基本的な R 関数、SQL Server と共にインストールされた独自のパッケージの関数、および SQL Server によってインストールされたオープンソース R のバージョンと互換性のあるサードパーティ製の R パッケージを呼び出すことができます。

SQL Server で使用するパッケージは、インスタンスによって使用される既定のライブラリにインストールする必要があります。 コンピューターに R が別途インストールされている場合、またはユーザーライブラリにパッケージをインストールした場合は、これらのパッケージを T-sql から使用することはできません。

R パッケージのインストールと管理のプロセスは SQL Server 2016 と SQL Server 2017 で異なります。 SQL Server 2016 では、データベース管理者は、ユーザーが必要とする R パッケージをインストールする必要があります。 SQL Server 2017 では、データベースレベルでパッケージを共有するようにユーザーグループを設定したり、ユーザーが独自のパッケージをインストールできるようにデータベースロールを構成したりすることができます。 詳細については、「 [Install New R packages](../r/install-additional-r-packages-on-sql-server.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

R 開発者は、いくつかの簡単な例を使って開始し、SQL Server での R の動作の基本を学習できます。 次の手順については、次のリンクを参照してください。

+ [チュートリアル: T-sql で R を実行する](../tutorials/rtsql-using-r-code-in-transact-sql-quickstart.md)
+ [チュートリアル: R 開発者向けのデータベース内分析](../tutorials/sqldev-in-database-r-for-sql-developers.md)

実際のシナリオに基づく機械学習の例については、「 [machine learning のチュートリアル](../tutorials/machine-learning-services-tutorials.md)」を参照してください。