---
title: Machine learning のデータ収集のトラブルシューティング
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: c7566d9b25b15a334e48380daca6cb81e92f6a2b
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715229"
---
# <a name="troubleshoot-data-collection-for-machine-learning"></a>Machine learning のデータ収集のトラブルシューティング

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、お客様の問題を解決するときに使用する必要があるデータ収集方法について説明します。または、Microsoft カスタマーサポートにお問い合わせください。

## <a name="sql-server-version-and-edition"></a>SQL Server のバージョンとエディション

SQL Server 2016 R Services は、統合 R サポートを含めるための SQL Server の最初のリリースです。 SQL Server 2016 Service Pack 1 (SP1) には、外部スクリプトを実行する機能など、いくつかの重要な機能強化が含まれています。 SQL Server 2016 を使用している場合は、SP1 以降をインストールすることを検討してください。

SQL Server 2017 以降には、Python 言語が統合されています。 以前のリリースでは、Python 機能の統合を取得できません。

エディションとバージョンの取得の詳細については、この記事を参照してください。 [SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/783.sql-server-versions.aspx#Service_Pack_editions)の各バージョンのビルド番号が一覧表示されます。

使用している SQL Server のエディションによっては、一部の機械学習機能が使用できない、または制限されている場合があります。 次の記事では、Enterprise、Developer、Standard、および Express の各エディションの機械学習機能の一覧を示します。

* [SQL Server の各エディションとサポートされている機能](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-2016)
* [SQL Server のエディション別の R および Python の機能](r/differences-in-r-features-between-editions-of-sql-server.md)

## <a name="r-language-and-tool-versions"></a>R 言語とツールのバージョン

一般に、R Services 機能または Machine Learning Services 機能を選択したときにインストールされる Microsoft R のバージョンは、SQL Server のビルド番号によって決まります。 SQL Server をアップグレードまたは修正する場合は、その R コンポーネントもアップグレードまたは修正する必要があります。

R コンポーネントダウンロードのリリースとリンクの一覧については、「[インターネットにアクセスせずに machine learning コンポーネントをインストール](https://docs.microsoft.com/sql/advanced-analytics/r/installing-ml-components-without-internet-access)する」を参照してください。 インターネットにアクセスできるコンピューターでは、必要なバージョンの R が自動的に識別され、インストールされます。

バインドと呼ばれるプロセスでは、R Server コンポーネントを SQL Server データベースエンジンとは別にアップグレードすることができます。 そのため、SQL Server で R コードを実行するときに使用する R のバージョンは、インストールされている SQL Server のバージョンと、サーバーを最新の R バージョンのどちらに移行したかによって異なる場合があります。

### <a name="determine-the-r-version"></a>R バージョンを確認する

R バージョンを確認する最も簡単な方法は、次のようなステートメントを実行してランタイムプロパティを取得することです。

```sql
exec sp_execute_external_script
       @language = N'R'
       , @script = N'
# Transform R version properties to data.frame
OutputDataSet <- data.frame(
  property_name = c("R.version", "Revo.version"),
  property_value = c(R.Version()$version.string, Revo.version$version.string),
  stringsAsFactors = FALSE)
# Retrieve properties like R.home, libPath & default packages
OutputDataSet <- rbind(OutputDataSet, data.frame(
  property_name = c("R.home", "libPaths", "defaultPackages"),
  property_value = c(R.home(), .libPaths(), paste(getOption("defaultPackages"), collapse=", ")),
  stringsAsFactors = FALSE)
)
'
WITH RESULT SETS ((PropertyName nvarchar(100), PropertyValue nvarchar(4000)));

```

> [!TIP]
> R Services が動作していない場合は、RGui から R スクリプトの部分のみを実行してみてください。

最後の手段として、サーバー上のファイルを開いて、インストールされているバージョンを確認できます。 これを行うには、rlauncher .config ファイルを見つけて、R ランタイムの場所と現在の作業ディレクトリを取得します。 プロパティを誤って変更しないように、ファイルのコピーを作成して開くことをお勧めします。

* SQL Server 2016
  
  `C:\Program Files\Microsoft SQL Server\MSSQL13.<instance_name\MSSQL\Binn\rlauncher.config`

* SQL Server 2017
  
  `C:\Program Files\Microsoft SQL Server\MSSQL14.<instance_name>\MSSQL\Binn\rlauncher.config`

R バージョンと RevoScaleR バージョンを取得するには、R コマンドプロンプトを開くか、インスタンスに関連付けられている RGui を開きます。

* SQL Server 2016
  
  `C:\Program Files\Microsoft SQL Server\MSSQL13.<instancename>\R_SERVICES\bin\x64\RGui.exe`

* SQL Server 2017
  
  `C:\Program Files\Microsoft SQL Server\MSSQL14.<instance_name>\R_SERVICES\bin\x64\RGui.exe`

R コンソールには、スタートアップ時にバージョン情報が表示されます。 たとえば、次のバージョンは、SQL Server 2017 の既定の構成を表しています。

    *Microsoft R Open 3.3.3*

    *The enhanced R distribution from Microsoft*

    *Microsoft packages Copyright (C) 2017 Microsoft*

    *Loading Microsoft R Server packages, version 9.1.0.*

## <a name="python-versions"></a>Python のバージョン

Python のバージョンを取得するには、いくつかの方法があります。 最も簡単な方法は、Management Studio またはその他の SQL クエリツールからこのステートメントを実行することです。

```sql
-- Get Python runtime properties:
exec sp_execute_external_script
       @language = N'Python'
       , @script = N'
import sys
import pkg_resources
OutputDataSet = pandas.DataFrame(
                    {"property_name": ["Python.home", "Python.version", "Revo.version", "libpaths"],
                    "property_value": [sys.executable[:-10], sys.version, pkg_resources.get_distribution("revoscalepy").version, str(sys.path)]}
)
'
with WITH RESULT SETS (SQL keywords) ((PropertyName nvarchar(100), PropertyValue nvarchar(4000)));
```

Machine Learning Services が実行されていない場合は、python ランチャー. .config ファイルを参照して、インストールされている Python のバージョンを確認できます。 プロパティを誤って変更しないように、ファイルのコピーを作成して開くことをお勧めします。

1. SQL Server 2017 のみ:`C:\Program Files\Microsoft SQL Server\MSSQL14.<instance_name>\MSSQL\Log\ExtensibilityLog\pythonlauncher.config`
2. **Python ホーム**の値を取得します。
3. 現在の作業ディレクトリの値を取得します。

> [!NOTE]
> SQL Server 2017 に Python と R の両方をインストールした場合、作業ディレクトリとワーカーアカウントのプールは、R 言語と Python 言語で共有されます。

## <a name="are-multiple-instances-of-r-or-python-installed"></a>R または Python の複数のインスタンスがインストールされていますか。

R ライブラリの複数のコピーがコンピューターにインストールされているかどうかを確認します。 この重複は、次の場合に発生する可能性があります。

* セットアップ中に、R Services (データベース内) と R Server (スタンドアロン) の両方を選択します。
* SQL Server に加えて Microsoft R Client をインストールします。
* R Tools for Visual Studio、R Studio、Microsoft R Client、または別の R IDE を使用して、別の R ライブラリセットがインストールされました。
* コンピューターは SQL Server の複数のインスタンスをホストし、複数のインスタンスが機械学習を使用します。

Python にも同じ条件が適用されます。

複数のライブラリまたはランタイムがインストールされていることがわかった場合は、SQL Server インスタンスで使用されている Python または R ランタイムに関連付けられているエラーだけを取得してください。

## <a name="origin-of-errors"></a>エラーの発生元

R コードを実行しようとしたときに表示されるエラーは、次のいずれかのソースから取得できます。

* ストアドプロシージャ sp_execute_external_script を含む SQL Server データベースエンジン
* SQL Server 信頼されたスタートパッド
* 拡張機能フレームワークのその他のコンポーネント (R、Python ランチャー、およびサテライトプロセスを含む)
* Microsoft Open Database Connectivity などのプロバイダー (ODBC)
* R 言語

サービスを初めて使用する場合、どのメッセージがどのサービスから送信されたかを判断するのは困難な場合があります。 正確なメッセージテキストだけでなく、メッセージが表示されたコンテキストもキャプチャすることをお勧めします。 Machine learning コードを実行するために使用しているクライアントソフトウェアを確認します。

* Management Studio を使用していますか? 外部アプリケーションですか。
* リモートクライアントで R コードを実行しているか、ストアドプロシージャ内で直接実行されていますか?

## <a name="sql-server-log-files"></a>ログファイルの SQL Server

最新の SQL Server エラーログを取得します。 エラーログの完全なセットは、次の既定のログディレクトリのファイルで構成されています。

* SQL Server 2016
  
  `C:\Program Files\Microsoft SQL Server\MSSQL13.SQL2016\MSSQL\Log\ExtensibilityLog`

* SQL Server 2017
  
  `C:\Program Files\Microsoft SQL Server\MSSQL14.SQL2016\MSSQL\Log\ExtensibilityLog`

> [!NOTE]
> 正確なフォルダー名は、インスタンス名によって異なります。

## <a name="errors-returned-by-spexecuteexternalscript"></a>Sp_execute_external_script によって返されるエラー

Sp_execute_external_script コマンドを実行したときに返されたエラーの完全なテキスト (存在する場合) を取得します。

R または Python の問題を考慮から削除するには、このスクリプトを実行します。このスクリプトは、R または Python ランタイムを起動し、データを前後に渡します。

**R の場合**

```sql
exec sp_execute_external_script @language =N'R',  
@script=N'OutputDataSet<-InputDataSet',  
@input_data_1 =N'select 1 as hello'  
with result sets (([hello] int not null));  
go
```

**Python の場合**

```sql
exec sp_execute_external_script @language =N'Python',  
@script=N'OutputDataSet= InputDataSet',  
@input_data_1 =N'select 1 as hello'  
with result sets (([hello] int not null));  
go
```

## <a name="errors-generated-by-the-extensibility-framework"></a>機能拡張フレームワークによって生成されたエラー

SQL Server では、外部スクリプト言語ランタイム用に個別のログが生成されます。 これらのエラーは Python または R 言語では生成されません。 これらは、言語固有のランチャーとそのサテライトプロセスを含め、SQL Server の機能拡張コンポーネントから生成されます。

これらのログは、次の既定の場所から取得できます。

* SQL Server 2016
  
  `C:\Program Files\Microsoft SQL Server\MSSQL13.<instance_name>\MSSQL\Log\ExtensibilityLog`

* SQL Server 2017
  
  `C:\Program Files\Microsoft SQL Server\MSSQL14.<instance_name>\MSSQL\Log\ExtensibilityLog`

> [!NOTE]
> 正確なフォルダー名は、インスタンス名によって異なります。 構成によっては、フォルダーが別のドライブにある可能性があります。

たとえば、次のログメッセージは、機能拡張フレームワークに関連しています。

* *ユーザー MSSQLSERVER01 の LogonUser に失敗しました*
  
  これは、外部スクリプトを実行するワーカーアカウントがインスタンスにアクセスできないことを示している可能性があります。

* *InitializePhysicalUsersPool に失敗しました*
  
  このメッセージは、セキュリティ設定が原因で、外部スクリプトを実行するために必要なワーカーアカウントのプールが作成されないことを意味します。

* *セキュリティコンテキストマネージャーを初期化できませんでした*

* *サテライトセッションマネージャーを初期化できませんでした*

## <a name="system-events"></a>システム イベント

1. Windows イベントビューアーを開き、**システムイベント**ログで、"*スタートパッド*" という文字列を含むメッセージを検索します。
2. ExtLaunchErrorlog ファイルを開き、「 *ErrorCode*」という文字列を探します。 ErrorCode に関連付けられているメッセージを確認します。

たとえば、次のメッセージは、SQL Server 機能拡張フレームワークに関連する一般的なシステムエラーです。

* *次のエラーが発生したため、SQL Server Launchpad (MSSQLSERVER) サービスを開始できませんでした:<text>*

* *サービスが開始または制御要求に適切なタイミングで応答しませんでした。*

* *SQL Server Launchpad (MSSQLSERVER) サービスが接続するのを待機している間にタイムアウトになりました (12万ミリ秒)。*

## <a name="dump-files"></a>ダンプファイル

デバッグに関する知識がある場合は、ダンプファイルを使用して、スタートパッドでのエラーを分析できます。

1. SQL Server のセットアップブートストラップログが格納されているフォルダーを見つけます。 たとえば、SQL Server 2016 では、既定のパスは C:\Program Server\130\Setup ある summary.txt ファイルになります。
2. 機能拡張に固有のブートストラップログサブフォルダーを開きます。
3. サポート要求を送信する必要がある場合は、このフォルダーの内容全体を zip 形式のファイルに追加します。 たとえば、C:\Program Server\130\Setup Bootstrap\Log\LOG\ExtensibilityLog. のようになります。
  
システムによっては正確な場所が異なる場合があり、C ドライブ以外のドライブに配置されている可能性があります。 Machine learning がインストールされているインスタンスのログを取得してください。

## <a name="configuration-settings"></a>構成設定

このセクションでは、R または Python スクリプトを実行するときにエラーの原因となる可能性がある追加のコンポーネントまたはプロバイダーを示します。

### <a name="what-network-protocols-are-available"></a>使用可能なネットワークプロトコル

Machine Learning Services では、拡張性コンポーネント間の内部通信、および外部 R または Python クライアントとの通信に、次のネットワークプロトコルが必要です。

* 名前付きパイプ
* TCP/IP

SQL Server 構成マネージャーを開いて、プロトコルがインストールされているかどうかを確認し、インストールされている場合は、有効になっているかどうかを判断します。

### <a name="security-configuration-and-permissions"></a>セキュリティの構成とアクセス許可

ワーカーアカウントの場合:

1. コントロールパネル で **ユーザーとグループ** を開き、外部スクリプトジョブを実行するために使用するグループを探します。 既定では、グループは**SQLRUserGroup**です。
2. グループが存在し、少なくとも1つのワーカーアカウントが含まれていることを確認してください。
3. SQL Server Management Studio で、R または Python ジョブを実行するインスタンスを選択し、 **[セキュリティ]** を選択して、SQLRUserGroup のログオンがあるかどうかを確認します。
4. ユーザーグループのアクセス許可を確認します。

個々のユーザーアカウントの場合:

1. インスタンスが混合モード認証、SQL ログインのみ、または Windows 認証のみをサポートするかどうかを判断します。 この設定は、R または Python のコード要件に影響します。
2. R コードを実行する必要があるユーザーごとに、R からオブジェクトが書き込まれる、データにアクセスする、またはオブジェクトが作成される各データベースに必要な権限レベルを決定します。
3. スクリプトの実行を有効にするには、必要に応じて、ロールを作成するか、次のロールにユーザーを追加します。

   - *Db_owner*以外:外部スクリプトの実行を必須にします。
   - *db_datawriter*:R または Python から結果を書き込むことができます。
   - *db_ddladmin*:新しいオブジェクトを作成します。
   - *db_datareader*:R または Python コードによって使用されるデータを読み取る。
4. SQL Server 2016 をインストールしたときに、既定のスタートアップアカウントを変更したかどうかに注意してください。
5. ユーザーが新しい R パッケージをインストールする必要がある場合、または他のユーザーによってインストールされた R パッケージを使用する必要がある場合は、インスタンスでパッケージ管理を有効にしてから、追加のアクセス許可を割り当てる必要があります。 詳細については、「 [R パッケージの管理を有効または無効](r/r-package-how-to-enable-or-disable.md)にする」を参照してください。

### <a name="what-folders-are-subject-to-locking-by-antivirus-software"></a>ウイルス対策ソフトウェアによってロックされるフォルダー

ウイルス対策ソフトウェアを使用すると、フォルダーをロックできます。これにより、機械学習機能のセットアップとスクリプト実行の両方が妨げられます。 SQL Server ツリー内のフォルダーがウイルススキャンの対象になるかどうかを判断します。

ただし、1つのインスタンスに複数のサービスまたは機能がインストールされている場合、そのインスタンスで使用されている可能性のあるすべてのフォルダーを列挙することが困難になることがあります。 たとえば、新しい機能が追加されたときに、新しいフォルダーを識別して除外する必要があります。

また、実行時に新しいフォルダーを動的に作成する機能もあります。 たとえば、インメモリ OLTP テーブル、ストアドプロシージャ、および関数はすべて、実行時に新しいディレクトリを作成します。 これらのフォルダー名は、多くの場合 Guid を含み、予測することはできません。 SQL Server の信頼されたスタートパッドは、R および Python スクリプトジョブ用の新しい作業ディレクトリを作成します。

SQL Server プロセスとその機能に必要なすべてのフォルダーを除外することはできない可能性があるため、SQL Server インスタンスディレクトリツリー全体を除外することをお勧めします。

### <a name="is-the-firewall-open-for-sql-server-does-the-instance-support-remote-connections"></a>ファイアウォールは SQL Server 用に開いていますか。 インスタンスはリモート接続をサポートしていますか。

1. SQL Server がリモート接続をサポートするかどうかを判断するには、「[リモートサーバー接続の構成](../database-engine/configure-windows/view-or-configure-remote-server-connection-options-sql-server.md)」を参照してください。

2. SQL Server に対してファイアウォール規則が作成されているかどうかを確認します。 セキュリティ上の理由から、既定のインストールでは、リモート R または Python クライアントがインスタンスに接続できないことがあります。 詳細については、「 [SQL Server への接続に関するトラブルシューティング](../database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine.md)」を参照してください。

## <a name="see-also"></a>関連項目

[SQL Server での機械学習のトラブルシューティング](machine-learning-troubleshooting-faq.md)
