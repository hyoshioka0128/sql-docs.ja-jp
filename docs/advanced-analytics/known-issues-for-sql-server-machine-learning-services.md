---
title: Python および R の既知の問題
ms.prod: sql
ms.technology: machine-learning
ms.date: 08/23/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 8cf0441397c8c3a6d743b08692a6d2bac289a03f
ms.sourcegitcommit: 3b1f873f02af8f4e89facc7b25f8993f535061c9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70176195"
---
# <a name="known-issues-in-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services の既知の問題
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、 [SQL Server Machine Learning Services](what-is-sql-server-machine-learning.md)および[SQL Server 2016 R Services](r/sql-server-r-services.md)のオプションとして提供される機械学習コンポーネントの既知の問題または制限事項について説明します。

## <a name="setup-and-configuration-issues"></a>セットアップと構成に関する問題

プロセスの説明と、初期セットアップと構成に関連する一般的な質問については、「[アップグレードとインストール](r/upgrade-and-installation-faq-sql-server-r-services.md)に関する FAQ」を参照してください。 これには、新しい R または Python コンポーネントのアップグレード、サイドバイサイドインストール、およびインストールに関する情報が含まれています。

### <a name="1-inconsistent-results-in-mkl-computations-due-to-missing-environment-variable"></a>1.環境変数が不足しているため、MKL 計算で一貫性のない結果が返される

**適用対象:** R_SERVER バイナリ9.0、9.1、9.2、9.3。

R_SERVER は Intel Math Kernel Library (MKL) を使用します。 MKL を含む計算では、システムに環境変数がない場合に一貫性のない結果が発生する可能性があります。 

環境変数`'MKL_CBWR'=AUTO`を設定して、R_SERVER で条件付きの数値の再現性を保証します。 詳細については、「[条件付き数値の再現性の概要」 (CNR)](https://software.intel.com/articles/introduction-to-the-conditional-numerical-reproducibility-cnr)を参照してください。

**回避策**

1. コントロールパネルで、[**システムとセキュリティ** >  > ] [システム] [システム**設定** > ] **[環境変数]** をクリックします。

2. 新しいユーザー変数またはシステム変数を作成します。 

  + 変数名を ' MKL_CBWR ' に設定します。
  + ' Variable value ' を ' AUTO ' に設定します。

3. R_SERVER を再起動します。 SQL Server では、SQL Server Launchpad サービスを再起動できます。

> [!NOTE]
> Linux で SQL Server 2019 Preview を実行している場合は、ユーザーのホームディレクトリで bash_profile を編集または作成し`export MKL_CBWR="AUTO"`、行を追加し*ます。* bash コマンド プロンプトで「`source .bash_profile`」と入力して、このファイルを実行します。 R コマンドプロンプトで`Sys.getenv()` 「」と入力して、R_SERVER を再起動します。

### <a name="2-r-script-runtime-error-sql-server-2017-cu5-cu7-regression"></a>2.R スクリプトの実行時エラー (SQL Server 2017 CU5-CU7 回帰)

SQL Server 2017 では、累積的な更新プログラム 5 ~ 7 では、temp ディレクトリのファイルパスにスペースが含まれる**rlauncher .config**ファイルに回帰があります。 この回帰は、CU8 で修正されました。

R スクリプトの実行時に表示されるエラーには、次のメッセージが含まれます。

> *' R ' スクリプトのランタイムと通信できません。' R ' ランタイムの要件を確認してください。*
>
> 外部スクリプトからの STDERR メッセージ: 
>
> *致命的なエラー: ' R_TempDir ' を作成できません*

**回避策**

CU8 が使用可能になったときに適用します。 または、管理者特権のコマンドプロンプトで、uninstall/install を使用して**registerrext.exe**を実行し、 **rlauncher**を再作成することもできます。 

```cmd
<SQLInstancePath>\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRExt.exe /uninstall /sqlbinnpath:<SQLInstanceBinnPath> /userpoolsize:0 /instance:<SQLInstanceName>

<SQLInstancePath>\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRExt.exe /install /sqlbinnpath:<SQLInstanceBinnPath> /userpoolsize:0 /instance:<SQLInstanceName>
```

次の例では、既定のインスタンス "MSSQL14." を使用してコマンドを示します。MSSQLSERVER "C:\Program の SQL Server\"にインストールされました。

```cmd
"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRext.exe" /uninstall /sqlbinnpath:"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\Binn" /userpoolsize:0 /instance:MSSQLSERVER

"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRext.exe" /install /sqlbinnpath:"C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\Binn" /userpoolsize:0 /instance:MSSQLSERVER
```

### <a name="3-unable-to-install-sql-server-machine-learning-features-on-a-domain-controller"></a>3.SQL Server machine learning 機能をドメインコントローラーにインストールできない

SQL Server 2016 R Services または SQL Server Machine Learning Services をドメインコントローラーにインストールしようとすると、次のエラーが発生し、セットアップが失敗します。

> *機能のセットアップ処理中にエラーが発生しました*
> 
> *Id のグループが見つかりません*
> 
> *コンポーネントエラーコード:0x8013150 9*

このエラーが発生するのは、ドメインコントローラーで、machine learning を実行するために必要な20個のローカルアカウントをサービスが作成できないためです。 一般に、ドメインコントローラーに SQL Server をインストールすることはお勧めしません。 詳細については、[サポート情報 2032911](https://support.microsoft.com/help/2032911/you-may-encounter-problems-when-installing-sql-server-on-a-domain-cont)を参照してください。

### <a name="4-install-the-latest-service-release-to-ensure-compatibility-with-microsoft-r-client"></a>4.Microsoft R Client との互換性を確保するために、最新のサービスリリースをインストールします

最新バージョンの Microsoft R Client をインストールし、それを使用してリモートの計算コンテキストで SQL Server で R を実行すると、次のようなエラーが表示されることがあります。

> *お使いのコンピューターでバージョン 1.x. x. x Microsoft R Client 実行しています。これは Microsoft R Server バージョン 8. x.x. と互換性がありません。互換性のあるバージョンをダウンロードしてインストールしてください。*

SQL Server 2016 では、クライアント上の R ライブラリがサーバー上の R ライブラリと完全に一致している必要があります。 この制限は、R Server 9.0.1 より後のリリースでは削除されています。 ただし、このエラーが発生した場合は、クライアントとサーバーで使用されている R ライブラリのバージョンを確認し、必要に応じて、サーバーのバージョンに合わせてクライアントを更新します。

SQL Server R Services と共にインストールされる R のバージョンは、SQL Server サービスリリースがインストールされるたびに更新されます。 常に最新バージョンの R コンポーネントがインストールされていることを確認するには、必ずすべてのサービスパックをインストールしてください。

Microsoft R Client 9.0.0 との互換性を確保するには、この[サポート記事](https://support.microsoft.com/kb/3210262)で説明されている更新プログラムをインストールします。

R パッケージに関する問題を回避するには、[次のセクション](#bkmk_sqlbindr)で説明するように、サービス契約を変更して最新のライフサイクルサポートポリシーを使用するように、サーバーにインストールされている r ライブラリのバージョンをアップグレードすることもできます。 この場合、SQL Server と共にインストールされる R のバージョンは、machine Learning Server (旧称 Microsoft R Server) の更新で使用されるのと同じスケジュールで更新されます。

**適用対象:** R Server バージョン9.0.0 以前の 2016 R Services の SQL Server

### <a name="5-r-components-missing-from-cu3-setup"></a>5.CU3 セットアップに R コンポーネントがありません

SQL Server に含める必要がある R インストールファイルを使用せずにプロビジョニングされた Azure 仮想マシンの数に制限があります。 この問題は、2018-01-05 から2018-01-23 の期間にプロビジョニングされた仮想マシンに適用されます。 この問題は、2018-01-05 から2018-01-23 の期間に SQL Server 2017 の CU3 更新プログラムを適用した場合にも、オンプレミスのインストールに影響を与える可能性があります。

正しいバージョンの R インストールファイルを含むサービスリリースが提供されています。

+ [SQL Server 2017 KB4052987 の累積的な更新プログラムパッケージ 3](https://www.microsoft.com/download/details.aspx?id=56128)。

コンポーネントをインストールして SQL Server 2017 CU3 を修復するには、CU3 をアンインストールし、更新後のバージョンを再インストールする必要があります。

1. R インストーラーを含む、更新された CU3 インストールファイルをダウンロードします。
2. CU3 をアンインストールします。 コントロールパネルで、**更新プログラムのアンインストール** を検索し、SQL Server 2017 (KB4052987) (64 ビット) の修正プログラム 3015 を選択します。 アンインストール手順を続行します。
3. ダウンロード`SQLServer2017-KB4052987-x64.exe`した KB4052987 の更新プログラム () をダブルクリックして、CU3 更新プログラムを再インストールします。 インストール手順に従います。

### <a name="6-unable-to-install-python-components-in-offline-installations-of-sql-server-2017-ctp-20-or-later"></a>6.SQL Server 2017 CTP 2.0 以降のオフラインインストールに Python コンポーネントをインストールできない

SQL Server 2017 のプレリリース版をインターネットにアクセスできないコンピューターにインストールすると、インストーラーによって、ダウンロードした Python コンポーネントの場所の入力を求めるページが表示されない場合があります。 このようなインスタンスでは、Machine Learning Services 機能をインストールできますが、Python コンポーネントはインストールできません。

この問題は、リリースバージョンで修正されています。 また、この制限は R コンポーネントには適用されません。

**適用対象:** Python を使用した2017の SQL Server

### <a name="bkmk_sqlbindr"></a>を使用してクライアントから古いバージョンの SQL Server R Services に接続すると、互換性のないバージョンの警告が発生する[!INCLUDE[ssSQLv14_md](../includes/sssqlv14-md.md)]

SQL Server 2016 のコンピューティングコンテキストで R コードを実行すると、次のエラーが表示される場合があります。

> *Microsoft R Server バージョン 8.0.3 と互換性のない、バージョン 9.0.0 の Microsoft R Client をコンピューター上で実行しています。互換性のあるバージョンをダウンロードしてインストールしてください。*

このメッセージは、次の2つのステートメントのいずれかが true の場合に表示されます。

+ の[!INCLUDE[ssSQLv14_md](../includes/sssqlv14-md.md)]セットアップウィザードを使用して、クライアントコンピューターに R Server (スタンドアロン) をインストールした。
+ [個別の Windows インストーラー](https://docs.microsoft.com/machine-learning-server/install/r-server-install-windows)を使用して Microsoft R Server をインストールしました。

サーバーとクライアントが同じバージョンを使用するようにするには、Microsoft R Server 9.0 以降のリリースでサポートされている_バインディング_を使用して、SQL Server 2016 インスタンスの R コンポーネントをアップグレードする必要があります。 お使いのバージョンの R Services でアップグレードのサポートが利用可能かどうかを確認するには、「 [SqlBindR .exe を使用して r services のインスタンスをアップグレード](install/upgrade-r-and-python.md)する」を参照してください。

**適用対象:** R Server バージョン9.0.0 以前の 2016 R Services の SQL Server

### <a name="7-setup-for-sql-server-2016-service-releases-might-fail-to-install-newer-versions-of-r-components"></a>7.SQL Server 2016 サービス リリースをセットアップすると、新しいバージョンの R コンポーネントのインストールに失敗する場合がある

累積的な更新プログラムをインストールするとき、またはインターネットに接続されていないコンピューターに SQL Server 2016 の Service Pack をインストールすると、セットアップウィザードによって、ダウンロードした CAB ファイルを使用して R コンポーネントを更新するためのプロンプトが表示されない場合があります。 このエラーは、通常、複数のコンポーネントがデータベースエンジンと共にインストールされた場合に発生します。

この回避策として、コマンドラインを使用し、次の例に示す`MRCACHEDIRECTORY`ように引数を指定することで、サービスリリースをインストールできます。この例では、CU1 の更新プログラムをインストールします。

`C:\<path to installation media>\SQLServer2016-KB3164674-x64.exe /Action=Patch /IACCEPTROPENLICENSETERMS /MRCACHEDIRECTORY=<path to CU1 CAB files>`

最新のインストーラーを入手するには、「[インターネットにアクセスせずに machine learning コンポーネントをインストール](install/sql-ml-component-install-without-internet-access.md)する」を参照してください。

**適用対象:** R Server バージョン9.0.0 以前の 2016 R Services の SQL Server

### <a name="8-launchpad-services-fails-to-start-if-the-version-is-different-from-the-r-version"></a>8.バージョンが R バージョンと異なる場合、スタートパッドサービスが起動しない

データベースエンジンとは別に SQL Server R Services をインストールし、ビルドバージョンが異なる場合は、システムイベントログに次のエラーが表示されることがあります。

> *次のエラーが発生したため、SQL Server Launchpad サービスを開始できませんでした:サービスが開始または制御要求に適切なタイミングで応答しませんでした。*

たとえば、リリースバージョンを使用してデータベースエンジンをインストールし、データベースエンジンをアップグレードする修正プログラムを適用してから、リリースバージョンを使用して R Services 機能を追加した場合に、このエラーが発生することがあります。

この問題を回避するには、ファイルマネージャーなどのユーティリティを使用して、sqldk などのバージョンの SQL バイナリと update.exe のバージョンを比較します。 すべてのコンポーネントのバージョン番号は同じである必要があります。 1 つのコンポーネントをアップグレードする場合は、インストールされている他のすべてのコンポーネントに必ず同じアップグレードを適用してください。

インスタンスの`Binn`フォルダーでスタートパッドを検索します。 たとえば、SQL Server 2016 の既定のインストールでは、パスはになり`C:\Program Files\Microsoft SQL Server\MSSQL.13.InstanceNameMSSQL\Binn`ます。 

### <a name="9-remote-compute-contexts-are-blocked-by-a-firewall-in-sql-server-instances-that-are-running-on-azure-virtual-machines"></a>9.リモートコンピューティングコンテキストは、Azure virtual machines で実行されている SQL Server インスタンスのファイアウォールによってブロックされます。

が Azure 仮想マシン[!INCLUDE[ssCurrent](../includes/sscurrent-md.md)]にインストールされている場合は、仮想マシンのワークスペースを使用する必要がある計算コンテキストを使用できないことがあります。 その理由は、既定では、Azure 仮想マシンのファイアウォールに、ローカルの R ユーザーアカウントのネットワークアクセスをブロックする規則が含まれているためです。

回避策として、Azure VM で **[セキュリティが強化された Windows ファイアウォール]** を開き、 **[送信の規則]** を選択して、次の規則を無効にします。**SQL Server インスタンス MSSQLSERVER の R ローカルユーザーアカウントのネットワークアクセスをブロック**します。 また、規則を有効のままにしておくこともできますが、セキュリティを有効にするにはセキュリティプロパティを変更します。

### <a name="10-implied-authentication-in-sqlexpress"></a>10.SQLEXPRESS での暗黙の認証

統合 Windows 認証を使用してリモートデータサイエンスワークステーションから R ジョブを実行すると、SQL Server は*暗黙的な認証*を使用して、スクリプトで必要になる可能性のあるローカルの ODBC 呼び出しを生成します。 ただし、この機能は、SQL Server Express Edition の RTM ビルドでは使用できません。

この問題を解決するために、新しいサービス リリースにアップグレードすることをお勧めします。

アップグレードできない場合は、回避策として、SQL ログインを使用して、埋め込み ODBC 呼び出しを必要とする可能性のあるリモート R ジョブを実行します。

**適用対象:** SQL Server 2016 R Services Express Edition

### <a name="11-performance-limits-when-libraries-used-by-sql-server-are-called-from-other-tools"></a>11.SQL Server によって使用されるライブラリが他のツールから呼び出される場合のパフォーマンスの制限

RGui などの外部アプリケーションから SQL Server 用にインストールされている machine learning ライブラリを呼び出すことができます。 これにより、新しいパッケージをインストールしたり、非常に短いコードサンプルでアドホックテストを実行したりするなど、特定のタスクを実行するための最も便利な方法が得られます。 ただし、SQL Server の外部では、パフォーマンスが制限される可能性があります。 

たとえば、SQL Server の Enterprise Edition を使用している場合でも、外部ツールを使用して R コードを実行すると、R がシングルスレッドモードで実行されます。 SQL Server でパフォーマンスの利点を得るには、SQL Server 接続を開始し、 [sp_execute_external_script](../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)を使用して外部スクリプトランタイムを呼び出します。

一般に、SQL Server によって使用される機械学習ライブラリを外部ツールから呼び出すのは避けてください。 R または Python コードをデバッグする必要がある場合は、通常、SQL Server の外部で行う方が簡単です。 SQL Server にあるのと同じライブラリを入手するには、Microsoft R Client、 [SQL Server 2017 Machine Learning Server (スタンドアロン)](install/sql-machine-learning-standalone-windows-install.md)、または[SQL Server 2016 R Server (スタンドアロン)](install/sql-r-standalone-windows-install.md)をインストールします。

### <a name="12-sql-server-data-tools-does-not-support-permissions-required-by-external-scripts"></a>12.SQL Server Data Tools は、外部スクリプトに必要な権限をサポートしていません。

Visual Studio または SQL Server Data Tools を使用してデータベースプロジェクトをパブリッシュする場合、外部スクリプトの実行に固有の権限を持つプリンシパルがあると、次のようなエラーが表示されることがあります。

> *TSQL モデル:データベースのリバースエンジニアリング中にエラーが検出されました。アクセス許可が認識されず、インポートされませんでした。*

現在、DACPAC モデルでは、R Services または Machine Learning Services によって使用されるアクセス許可 (外部スクリプトの許可など) はサポートされていません。また、外部スクリプトを実行することもできません。 この問題は今後のリリースで修正される予定です。

回避策として、追加の GRANT ステートメントを配置後スクリプトで実行します。

### <a name="13-external-script-execution-is-throttled-due-to-resource-governance-default-values"></a>13.リソースガバナンスの既定値により、外部スクリプトの実行が制限されています

Enterprise Edition では、外部スクリプト プロセスを管理するためにリソース プールを使用できます。 一部の初期リリースビルドでは、R プロセスに割り当てることができる最大メモリは 20% でした。 このため、サーバーで 32 GB の RAM が使用されている場合、R の実行可能ファイル (RTerm .exe と BxlServer .exe) は、1つの要求で最大 6.4 GB を使用できます。

リソースの制限に遭遇した場合は、現在の既定値を確認してください。 20% では不十分な場合は、この値[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]を変更する方法についてのドキュメントを参照してください。

**適用対象:** SQL Server 2016 R Services (Enterprise Edition)

## <a name="r-script-execution-issues"></a>R スクリプトの実行に関する問題

このセクションには、SQL Server での R の実行に固有の既知の問題と、Microsoft によって発行された R ライブラリおよびツール (RevoScaleR を含む) に関連するいくつかの問題が含まれています。

R ソリューションに影響する可能性があるその他の既知の問題については、 [Machine Learning Server](https://docs.microsoft.com/machine-learning-server/resources-known-issues)サイトを参照してください。

### <a name="1-access-denied-warning-when-executing-r-scripts-on-sql-server-in-a-non-default-location"></a>1.既定以外の場所で SQL Server で R スクリプトを実行するとアクセス拒否警告が表示される

SQL Server のインスタンスが、フォルダーの`Program Files`外部など、既定以外の場所にインストールされている場合、パッケージをインストールするスクリプトを実行しようとすると、警告 ACCESS_DENIED が発生します。 例 :

> *`normalizePath(path.expand(path), winslash, mustWork)` : パス [2] = "~ externallibraries/R/8/1":アクセスが拒否されました*

その理由は、R 関数がパスを読み取ろうとし、組み込みの users グループ**SQLRUserGroup**に読み取りアクセス権がない場合に失敗することです。 発生した警告によって現在の R スクリプトの実行がブロックされることはありませんが、ユーザーが他の R スクリプトを実行するたびに警告が繰り返し発生する可能性があります。

SQL Server が既定の場所にインストールされている場合、すべての Windows ユーザーがその`Program Files`フォルダーに対する読み取りアクセス許可を持っているため、このエラーは発生しません。

この問題は、今後のサービスリリースで解決されます。 回避策として、グループ**SQLRUserGroup**に、のすべての`ExternalLibraries`親フォルダーに対する読み取りアクセス権を付与します。

### <a name="2-serialization-error-between-old-and-new-versions-of-revoscaler"></a>2.古いバージョンと新しいバージョンの RevoScaleR 間でのシリアル化エラー

シリアル化された形式を使用しているモデルをリモート SQL Server インスタンスに渡すと、次のエラーが発生する可能性があります。 

> *Memdecompress 中にエラーが発生しています (データ、種類 = 圧縮解除)。 memDecompress 中の内部エラー-3 (2)。*

このエラーは、最新バージョンのシリアル化関数[rxSerializeModel](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)を使用してモデルを保存した場合に発生しますが、モデルを逆シリアル化する SQL Server インスタンスには、SQL SERVER 2017 CU2 以前の古いバージョンの RevoScaleR api が含まれています。

この回避策として、SQL Server 2017 インスタンスを CU3 以降にアップグレードできます。

このエラーは、API バージョンが同じである場合、または古いシリアル化関数を使用して保存されたモデルを、新しいバージョンのシリアル化 API を使用するサーバーに移動する場合は表示されません。

つまり、シリアル化と逆シリアル化の両方の操作に同じバージョンの RevoScaleR を使用します。

### <a name="3-real-time-scoring-does-not-correctly-handle-the-_learningrate_-parameter-in-tree-and-forest-models"></a>3.リアルタイムスコアリングでは、ツリーおよびフォレストモデルの_learningRate_パラメーターが正しく処理されない

デシジョンツリーまたはデシジョンフォレストの方法を使用してモデルを作成し、学習速度を指定した場合、 `sp_rxpredict`または SQL `PREDICT`関数を使用すると、の`rxPredict`使用と比較して一貫性のない結果が表示されることがあります。

この原因は、シリアル化されたモデルを処理する API のエラーであり`learningRate` 、 [rxbtrees](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxbtrees)などのパラメーターに限定されています。

この問題は、今後のサービスリリースで解決されます。

### <a name="4-limitations-on-processor-affinity-for-r-jobs"></a>4.R ジョブのプロセッサ アフィニティに関する制限事項

SQL Server 2016 の初期リリースビルドでは、最初の k グループの Cpu に対してのみプロセッサ関係を設定できます。 たとえば、サーバーが2つの k グループを持つ2ソケットコンピューターの場合、R プロセスでは最初の k グループのプロセッサのみが使用されます。 R スクリプトジョブのリソースガバナンスを構成する場合も、同じ制限が適用されます。

この問題は SQL Server 2016 Service Pack 1 で修正されました。 最新のサービスリリースにアップグレードすることをお勧めします。

**適用対象:** SQL Server 2016 R Services RTM バージョン

### <a name="5-changes-to-column-types-cannot-be-performed-when-reading-data-in-a-sql-server-compute-context"></a>5.SQL Server コンピューティング コンテキストでのデータの読み取り時に列の型を変更できない

コンピューティング コンテキストが SQL Server インスタンスに設定されている場合は、 _colClasses_ 引数 (または他の同様の引数) を使用して、R コードで列のデータ型を変更できません。

たとえば、CRSDepTimeStr 列がまだ整数でない場合、次のステートメントでエラーが発生します。

```R
data <- RxSqlServerData(
  sqlQuery = "SELECT CRSDepTimeStr, ArrDelay  FROM AirlineDemoSmall", 
  connectionString = connectionString, 
  colClasses = c(CRSDepTimeStr = "integer"))
```

回避策として、CAST または CONVERT を使用するように SQL クエリを書き直して、正しいデータ型を使用してデータを R に提示することができます。 一般に、R コード内のデータを変更するのではなく、SQL を使用してデータを操作すると、パフォーマンスが向上します。

**適用対象:** SQL Server 2016 R Services

### <a name="6-limits-on-size-of-serialized-models"></a>6.シリアル化されたモデルのサイズの制限

モデルを SQL Server テーブルに保存する場合は、モデルをシリアル化し、バイナリ形式で保存する必要があります。 理論的には、このメソッドで格納できるモデルの最大サイズは 2 GB です。これは SQL Server の varbinary 列の最大サイズです。

大規模なモデルを使用する必要がある場合は、次の回避策を使用できます。

+ モデルのサイズを小さくするための手順を実行します。 オープンソースの R パッケージの中には、モデルオブジェクトに大量の情報が含まれているものもあります。この情報の多くは、デプロイのために削除できます。 
+ 機能の選択を使用して不要な列を削除します。
+ オープンソースアルゴリズムを使用している場合は、Microsoft Ml または RevoScaleR の対応するアルゴリズムを使用した同様の実装を検討してください。 これらのパッケージは、展開シナリオに合わせて最適化されています。
+ モデルが合理化され、前の手順を使用してサイズが縮小した後、SQL Server に渡す前に、base R の[Memcompress](https://www.rdocumentation.org/packages/base/versions/3.4.1/topics/memCompress)関数を使用してモデルのサイズを小さくすることができるかどうかを確認してください。 このオプションは、モデルが 2 GB の制限に近い場合に最適です。
+ 大規模なモデルでは、SQL Server [FileTable](../relational-databases/blob/filetables-sql-server.md)機能を使用して、varbinary 列を使用するのではなく、モデルを格納できます。

    Filetable を使用するには、ファイアウォールの例外を追加する必要があります。これは、Filetable に格納されたデータが SQL Server の Filestream ファイルシステムドライバーによって管理され、既定のファイアウォール規則によってネットワークファイルへのアクセスがブロックされるためです。 詳細については、「 [FileTable の前提条件を有効にする](../relational-databases/blob/enable-the-prerequisites-for-filetable.md)」を参照してください。

    FileTable を有効にした後、モデルを作成するには、FileTable API を使用して SQL からのパスを取得し、コードからその場所にモデルを書き込みます。 モデルを読み取る必要がある場合は、SQL からのパスを取得し、スクリプトからのパスを使用してモデルを呼び出します。 詳細については、「 [File Input-Output api を使用した Filetable へのアクセス](../relational-databases/blob/access-filetables-with-file-input-output-apis.md)」を参照してください。

### <a name="7-avoid-clearing-workspaces-when-you-execute-r-code-in-a-includessnoversionincludesssnoversion-mdmd-compute-context"></a>7.[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]コンピューティングコンテキストで R コードを実行するときにワークスペースをクリアしない

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]コンピューティングコンテキストで r コードを実行しているときに r コマンドを使用してオブジェクトのワークスペースをクリアした場合、または[sp_execute_external_script](../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)を使用して呼び出された r スクリプトの一部としてワークスペースをクリアした場合は、次のエラーが表示されることがあります:*ワークスペースオブジェクト revoScriptConnection が見つかりません*

`revoScriptConnection` は、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]から呼び出される R セッションに関する情報を含む R ワークスペース内のオブジェクトです。 ただし、R コードにワークスペースをクリアするためのコマンド ( `rm(list=ls()))`など) が含まれている場合は、R ワークスペース内のセッションおよび他のオブジェクトに関するすべての情報もクリアされます。

回避策として、で[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]R を実行しているときに、変数とその他のオブジェクトを区別に消去しないようにしてください。 R コンソールで作業する場合、ワークスペースのクリアは一般的ですが、意図しない結果になる可能性があります。

* 特定の変数を削除するには`remove` 、R 関数を使用します。たとえば、次のように指定します。`remove('name1', 'name2', ...)`
* 削除する変数が複数ある場合は、リストに一時変数の名前を保存し、ガベージ コレクションを定期的に実行します。

### <a name="8-restrictions-on-data-that-can-be-provided-as-input-to-an-r-script"></a>8.R スクリプトの入力として提供できるデータに対する制限事項

次の種類のクエリ結果は、R スクリプトでは使用できません。

- AlwaysEncrypted 列を参照する [!INCLUDE[tsql](../includes/tsql-md.md)] クエリのデータ。
  
- マスクされた列を参照する [!INCLUDE[tsql](../includes/tsql-md.md)] クエリのデータ。
  
     R スクリプトでマスクされたデータを使用する必要がある場合、考えられる次善策として、一時テーブルにデータのコピーを作成し、代わりにそのデータを使用します。

### <a name="9-use-of-strings-as-factors-can-lead-to-performance-degradation"></a>9.文字列を要因として使用すると、パフォーマンスが低下する可能性があります。

文字列型の変数を係数として使用すると、R 操作に使用されるメモリの量を大幅に増やすことができます。 これは、一般的な R に関する既知の問題であり、件名には多くの記事があります。 たとえば、次のような[[要因は、r のファーストクラスの市民ではなく、John Mount、ブロガーで](https://www.r-bloggers.com/factors-are-not-first-class-citizens-in-r/)、stringsAsFactors にあります。承認さ](https://simplystatistics.org/2015/07/24/stringsasfactors-an-unauthorized-biography/)れていない経歴は、Roger を持っています。 

この問題は SQL Server に固有のものではありませんが、SQl Server での R コードの実行のパフォーマンスに大きく影響する可能性があります。 文字列は通常、varchar または nvarchar として格納され、文字列データの列に一意の値が多数含まれている場合、これらを整数に変換し、R によって文字列に戻すプロセスによって、メモリ割り当てエラーが発生する可能性もあります。

他の操作に対して文字列データ型を使用する必要がない場合は、データ準備の一部として文字列値を数値 (整数) データ型にマップすると、パフォーマンスとスケールの観点からメリットが得られます。

この問題とその他のヒントについては、「 [R Services のパフォーマンス-データの最適化](r/r-and-data-optimization-r-services.md)」を参照してください。

### <a name="10-arguments-varstokeep-and-varstodrop-are-not-supported-for-sql-server-data-sources"></a>10.引数*Varstokeep*および*varstokeep*は SQL Server データソースではサポートされていません

RxDataStep 関数を使用してテーブルに結果を書き込む場合、操作の一部として含めるまたは除外する列を指定する便利な方法として、 *Varstokeep*と*varstokeep*を使用する方法があります。 ただし、これらの引数は SQL Server データソースではサポートされていません。

### <a name="11-limited-support-for-sql-data-types-in-sp_execute_external_script"></a>11.Sp\_での SQL データ型の制限さ\_れ\_たサポート外部スクリプトの実行

SQL でサポートされているすべてのデータ型を R で使用できるわけではありません。回避策として、データを sp\_\_\_に渡す前に、サポートされていないデータ型をサポートされるデータ型にキャストすることを検討してください。

詳細については、「 [R ライブラリとデータ型](r/r-libraries-and-data-types.md)」を参照してください。

### <a name="12-possible-string-corruption-using-unicode-strings-in-varchar-columns"></a>12.Varchar 列で unicode 文字列を使用すると、文字列が破損する可能性があります

Varchar 型の列[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]の unicode データを R/Python に渡すと、文字列が破損する可能性があります。 これは、照合順序で[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]のこれらの unicode 文字列のエンコーディングが、R/Python で使用される既定の utf-8 エンコードと一致しない場合があるためです。 

から[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] ASCII 以外の文字列データを R/Python に送信するには、utf-8 エンコード (で[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)]使用可能) を使用するか、同じに対して nvarchar 型を使用します。

### <a name="13-only-one-value-of-type-raw-can-be-returned-from-sp_execute_external_script"></a>13.型`raw`の1つの値のみを返すことができます。`sp_execute_external_script`

R からバイナリデータ型 (R の**生**データ型) が返される場合、値は出力データフレームに送信される必要があります。

**Raw**以外のデータ型では、OUTPUT キーワードを追加することにより、ストアドプロシージャの結果と共にパラメーター値を返すことができます。 詳細については、「[パラメーター](https://docs.microsoft.com/sql/relational-databases/stored-procedures/parameters)」を参照してください。

**Raw**型の値を含む複数の出力セットを使用する場合、1つの回避策として、ストアドプロシージャの複数の呼び出しを実行するか、ODBC [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]を使用して結果セットをに返すことが考えられます。

### <a name="14-loss-of-precision"></a>14.精度の損失

と[!INCLUDE[tsql](../includes/tsql-md.md)] R ではさまざまなデータ型がサポートされるため、変換時に数値データ型の精度が損なわれる可能性があります。

暗黙的なデータ型変換の詳細については、「 [R ライブラリとデータ型](r/r-libraries-and-data-types.md)」を参照してください。

### <a name="15-variable-scoping-error-when-you-use-the-transformfunc-parameter"></a>15.TransformFunc パラメーターを使用する場合の変数のスコープエラー

モデリング中にデータを変換するには、や`rxLogit`など`rxLinmod`の関数で*transformfunc*引数を渡すことができます。 ただし、入れ子になった関数呼び出しでは、呼び出しがローカルの計算コンテキストで正しく動作する場合でも、SQL Server の計算コンテキストでスコープエラーが発生する可能性があります。

> *分析用のサンプルデータセットに変数がありません*

たとえば、ローカルグローバル環境でとという2つ`f`の`g`関数が定義されていると`g`し`f`ます。とはを呼び出します。 を`g`含む分散またはリモート呼び出しでは、 `g`の呼び出しがこのエラーで失敗`f`する可能性があります。これは、と`f` `g`の両方をリモート呼び出しに渡した場合でも、が見つからないためです。

この問題が発生した場合は、 `f` の定義内の、通常 `g`が `g` を呼び出す場所より前の任意の場所に、 `f`の定義を組み込むことで問題を回避できます。

例 :

```R
f <- function(x) { 2*x * 3 }
g <- function(y) {
              a <- 10 * y
               f(a)
}
```

このエラーを回避するには、次のように定義を書き直します。

```R
g <- function(y){
              f <- function(x) { 2*x +3}
              a <- 10 * y
              f(a)
}
```

### <a name="16-data-import-and-manipulation-using-revoscaler"></a>16.RevoScaleR を使用したデータのインポートと操作

**Varchar**型の列がデータベースから読み取られると、空白が切り捨てられます。 これを防ぐには、空白以外の文字で文字列を囲みます。

など`rxDataStep`の関数を使用して、 **varchar**列を含むデータベーステーブルを作成する場合、列の幅は、データのサンプルに基づいて推定されます。 幅が異なる場合は、すべての文字列を共通の長さに埋め込むことが必要になることがあります。

`rxImport` または `rxTextToXdf` の繰り返しの呼び出しを使用して行のインポートと追加を実行し、複数の入力ファイルを 1 つの .xdf ファイルに結合する場合、変換を使用した変数のデータ型の変更はサポートされていません。

### <a name="17-limited-support-for-rxexec"></a>17.RxExec の制限付きサポート

SQL Server 2016 `rxExec`では、RevoScaleR パッケージによって提供される関数は、シングルスレッドモードでのみ使用できます。

### <a name="18-increase-the-maximum-parameter-size-to-support-rxgetvarinfo"></a>18.RxGetVarInfo をサポートするために、パラメーターの最大サイズを大きくします。

非常に大量の変数 (たとえば、4万を超えるデータセット) を使用する場合は、R `max-ppsize`を起動してなど`rxGetVarInfo`の関数を使用するときにフラグを設定します。 `max-ppsize` フラグは、ポインター保護スタックの最大サイズを指定します。

R コンソール (たとえば、RGui .exe や Rgui .exe) を使用している場合は、次のように入力して、 _max ppsize_の値を50万に設定できます。

```R
R --max-ppsize=500000
```

### <a name="19-issues-with-the-rxdtree-function"></a>19.RxDTree 関数に関する問題

`rxDTree` 関数では、式内変換は現在サポートされていません。 特に、係数を即座に作成するための `F()` 構文の使用はサポートされていません。 ただし、数値データは自動的にビン分割されます。

順序付けされた係数は、 `rxDTree`を除くすべての RevoScaleR 分析関数の係数と同様に処理されます。

### <a name="20-datatable-as-an-outputdataset-in-r"></a>20.Data. テーブルを OutputDataSet として R に

を`data.table` R `OutputDataSet` in として使用することは、SQL Server 2017 の累積的な更新プログラム 13 (CU13) およびそれ以前のバージョンではサポートされていません。 次のメッセージが表示されることがあります。

```
Msg 39004, Level 16, State 20, Line 2
A 'R' script error occurred during execution of 
'sp_execute_external_script' with HRESULT 0x80004004.
Msg 39019, Level 16, State 2, Line 2
An external script error occurred: 
Error in alloc.col(newx) : 
  Internal error: length of names (0) is not length of dt (11)
Calls: data.frame ... as.data.frame -> as.data.frame.data.table -> copy -> alloc.col

Error in execution.  Check the output for more information.
Error in eval(expr, envir, enclos) : 
  Error in execution.  Check the output for more information.
Calls: source -> withVisible -> eval -> eval -> .Call
Execution halted
```

`data.table`R の`OutputDataSet`は、SQL Server 2017 の累積的な更新プログラム 14 (CU14) 以降でサポートされています。

## <a name="python-script-execution-issues"></a>Python スクリプトの実行に関する問題

このセクションには、SQL Server での Python の実行に固有の既知の問題と、Microsoft によって発行された Python パッケージに関連する問題 ( [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)や microsoft [ml](https://docs.microsoft.com/r-server/python-reference/microsoftml/microsoftml-package)など) が含まれています。

### <a name="1-call-to-pretrained-model-fails-if-path-to-model-is-too-long"></a>1.モデルへのパスが長すぎると、事前トレーニング済みモデルへの呼び出しが失敗する

SQL Server 2017 の初期リリースで事前トレーニング済みのモデルをインストールした場合、Python が読み取るには、トレーニング済みのモデルファイルへの完全なパスが長すぎる可能性があります。 この制限は、後のサービスリリースで修正されています。

考えられる回避策がいくつかあります。 

+ 事前トレーニング済みのモデルをインストールするときに、カスタムの場所を選択します。
+ 可能な場合は、C:\SQL\MSSQL14. などの短いパスを使用して、カスタムインストールパスに SQL Server インスタンスをインストールします。Ms.
+ Windows ユーティリティ[Fsutil](https://technet.microsoft.com/library/cc788097(v=ws.11).aspx)を使用して、モデルファイルを短いパスにマップするハードリンクを作成します。
+ 最新のサービスリリースに更新します。

### <a name="2-error-when-saving-serialized-model-to-sql-server"></a>2.シリアル化されたモデルを SQL Server に保存するときにエラーが発生します

リモート SQL Server インスタンスにモデルを渡し、 `rx_unserialize` [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)の関数を使用してバイナリモデルを読み取ると、次のエラーが表示されることがあります。 

> *NameError: 名前 ' rx_unserialize_model ' は定義されていません*

このエラーは、最新バージョンのシリアル化関数を使用してモデルを保存したが、モデルを逆シリアル化する SQL Server インスタンスがシリアル化 API を認識しない場合に発生します。

この問題を解決するには、SQL Server 2017 インスタンスを CU3 以降にアップグレードします。

### <a name="3-failure-to-initialize-a-varbinary-variable-causes-an-error-in-bxlserver"></a>3.Varbinary 変数を初期化できないと、BxlServer でエラーが発生します。

を使用して`sp_execute_external_script`SQL Server で Python コードを実行し、そのコードに varbinary (max)、varchar (max)、または類似の型の出力変数がある場合は、変数を初期化するか、スクリプトの一部として設定する必要があります。 それ以外の場合は、データ交換コンポーネント BxlServer によってエラーが発生し、動作が停止します。

この制限は、今後のサービスリリースで修正される予定です。 回避策として、変数が Python スクリプト内で初期化されていることを確認します。 次の例のように、任意の有効な値を使用できます。

```sql
declare @b varbinary(max);
exec sp_execute_external_script
  @language = N'Python'
  , @script = N'b = 0x0'
  , @params = N'@b varbinary(max) OUTPUT'
  , @b = @b OUTPUT;
go
```

```sql
declare @b varchar(30);
exec sp_execute_external_script
  @language = N'Python'
  , @script = N' b = ""  '
  , @params = N'@b varchar(30) OUTPUT'
  , @b = @b OUTPUT;
go
```

### <a name="4-telemetry-warning-on-successful-execution-of-python-code"></a>4.Python コードが正常に実行された場合のテレメトリに関する警告

SQL Server 2017 CU2 以降では、Python コードが正常に実行された場合でも、次のメッセージが表示されることがあります。

> *外部スクリプトからの STDERR メッセージ:* 
>  *~ PYTHON_SERVICES\lib\site-packages\revoscalepy\utils\RxTelemetryLogger*
> *SyntaxWarning: telemetry_state がグローバル宣言の前に使用さ*れています

この問題は SQL Server 2017 累積的な更新プログラム 3 (CU3) で修正されました。 

### <a name="5-numeric-decimal-and-money-data-types-not-supported"></a>5。Numeric、decimal、money の各データ型はサポートされていません

SQL Server 2017 累積更新プログラム 12 (CU12) 以降では、との Python `sp_execute_external_script`を使用する場合、結果セットでの数値、decimal、money のデータ型はサポートされません。 次のメッセージが表示される可能性があります。

> *コード39004、SQL の状態:S1000] ' Python ' スクリプトエラーが発生しました。これは HRESULT 0x80004004 で実行されます。*

> *コード39019、SQL の状態:S1000] 外部スクリプトエラーが発生しました:*
> 
> *SqlSatelliteCall エラー:出力スキーマでサポートされていない型です。サポートされている型: bit、smallint、int、datetime、smallmoney、real、および float。char、varchar は部分的にサポートされています。*

これは SQL Server 2017 累積更新プログラム 14 (CU14) で修正されました。

### <a name="6-bad-interpreter-error-when-installing-python-packages-with-pip-on-linux"></a>6。Linux で pip を使用して Python パッケージをインストールするときに無効なインタープリターエラーが発生する 

SQL Server 2019 で、 **pip**を使用しようとした場合。 以下に例を示します。

```bash
/opt/mssql/mlservices/runtime/python/bin/pip -h
```

次のエラーが表示されます。

> *bash:/opt/mssql/mlservices/runtime/python/bin/pip:/opt/microsoft/mlserver/9.4.7/bin/python/python: 無効なインタープリター:ファイルまたはディレクトリがありません*

**回避策**

[Python パッケージ機関 (PyPA)](https://www.pypa.io)から**pip**をインストールします。

```bash
wget 'https://bootstrap.pypa.io/get-pip.py' 
/opt/mssql/mlservices/bin/python/python ./get-pip.py 
```

**推奨**

「 [Sqlmlutils を使用して Python パッケージをインストール](package-management/install-additional-python-packages-on-sql-server.md)する」を参照してください。

**適用対象:** Linux 上の SQL Server 2019

## <a name="revolution-r-enterprise-and-microsoft-r-open"></a>Revolution R Enterprise と Microsoft R Open

このセクションでは、変革分析によって提供される R 接続、開発、およびパフォーマンスツールに固有の問題を示します。 これらのツールは、以前のプレリリースバージョンの[!INCLUDE[ssCurrent](../includes/sscurrent-md.md)]で提供されていました。

一般に、これらの以前のバージョンをアンインストールし、SQL Server または Microsoft R Server の最新バージョンをインストールすることをお勧めします。

### <a name="1-revolution-r-enterprise-is-not-supported"></a>1.変革 R Enterprise はサポートされていません

すべてのバージョンの[!INCLUDE[rsql_productname_md](../includes/rsql-productname-md.md)]と共に、革命 R Enterprise をサイドバイサイドでインストールすることはサポートされていません。

革命 R Enterprise の既存のライセンスがある場合は、インスタンスと、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]インスタンスへの接続に使用するワークステーションの両方から別のコンピューターに配置する必要があります。

の[!INCLUDE[rsql_productname](../includes/rsql-productname-md.md)]プレリリース版には、革命分析によって作成された Windows 用の R 開発環境が含まれています。 このツールは提供されなくなったため、サポートされていません。

と[!INCLUDE[rsql_productname](../includes/rsql-productname-md.md)]の互換性を確保するために、代わりに Microsoft R Client をインストールすることをお勧めします。 [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/)と[Visual Studio Code](https://code.visualstudio.com/)は Microsoft R ソリューションもサポートしています。

### <a name="2-compatibility-issues-with-sqlite-odbc-driver-and-revoscaler"></a>2.SQLite ODBC ドライバーと RevoScaleR に関する互換性の問題

SQLite ODBC ドライバーのリビジョン0.92 は RevoScaleR と互換性がありません。 リビジョン 0.88-0.91 と0.93 以降には互換性があることがわかっています。

## <a name="next-steps"></a>次の手順

[SQL Server での機械学習のトラブルシューティング](machine-learning-troubleshooting-faq.md)
