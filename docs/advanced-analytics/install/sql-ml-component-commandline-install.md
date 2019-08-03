---
title: R および Python コンポーネントのコマンドプロンプトインストール
description: SQL Server コマンドラインセットアップを実行して、R 言語と Python の統合を SQL Server データベースエンジンインスタンスに追加します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 3e1e74c9d14c93cf44a7da5db4795a1524d238be
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715272"
---
# <a name="install-sql-server-machine-learning-r-and-python-components-from-the-command-line"></a>コマンドラインから machine learning R および Python コンポーネントをインストール SQL Server
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

この記事では、コマンドラインから SQL Server machine learning コンポーネントをインストールする手順について説明します。

+ [データベース内の新しいインスタンス](#indb)
+ [既存のデータベースエンジンインスタンスに追加する](#add-existing)
+ [サイレントインストール](#silent)
+ [新しいスタンドアロンサーバー](#shared-feature)

サイレント、基本、または完全な操作は、セットアップのユーザーインターフェイスを使用して指定できます。 この記事では、R および Python machine learning コンポーネントに固有のパラメーターについて説明する、[コマンドプロンプトからのインストール SQL Server](../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)を補足します。

## <a name="pre-install-checklist"></a>インストール前のチェックリスト

+ 管理者特権のコマンドプロンプトからコマンドを実行します。 

+ データベースのインストールには、データベースエンジンのインスタンスが必要です。 R または Python の機能だけをインストールすることはできませんが、[既存のインスタンスに段階的に追加](#add-existing)することはできます。 データベースエンジンを使用せずに R と Python のみが必要な場合は、[スタンドアロンサーバー](#shared-feature)をインストールします。

+ フェールオーバークラスターにをインストールしないでください。 R および Python プロセスを分離するために使用されるセキュリティメカニズムは、Windows Server フェールオーバークラスター環境と互換性がありません。

+ をドメインコントローラーにインストールしないでください。 セットアップの Machine Learning Services の部分は失敗します。

+ スタンドアロンインスタンスとデータベース内インスタンスを同じコンピューターにインストールすることは避けてください。 スタンドアロンサーバーは同じリソースに対して競合し、両方のインストールのパフォーマンスを低下させることになります。


## <a name="command-line-arguments"></a>コマンドライン引数

機能の引数は、ライセンス条項の契約と同様に必須です。 

コマンド プロンプトを使用してインストールする場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、/Q パラメーターを使用した非表示モード、または /QS パラメーターを使用した簡易非表示モードがサポートされます。 /QS スイッチでは、進捗状況のみが表示され、入力はできません。また、該当する場合でもエラー メッセージは表示されません。 /QS パラメーターは、/Action=install を指定した場合にのみサポートされます。

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
| 引数 | 説明 |
|-----------|-------------|
| /FEATURES = AdvancedAnalytics | データベース内のバージョンをインストールします。SQL Server R Services (データベース内)。  |
| /FEATURES = SQL_SHARED_MR | スタンドアロンバージョンの R 機能をインストールします。R Server (スタンドアロン) を SQL Server します。 スタンドアロンサーバーは、データベースエンジンインスタンスにバインドされていない "共有機能" です。|
| /IACCEPTROPENLICENSETERMS  | オープンソース R コンポーネントを使用するためのライセンス条項に同意したことを示します。 |
| /IACCEPTPYTHONLICENSETERMS | Python コンポーネントを使用するためのライセンス条項に同意したことを示します。 |
| /IACCEPTSQLSERVERLICENSETERMS | SQL Server を使用するためのライセンス条項に同意したことを示します。|
| /MRCACHEDIRECTORY | オフラインセットアップでは、R コンポーネントの CAB ファイルを含むフォルダーを設定します。 |
::: moniker-end

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
| 引数 | 説明 |
|-----------|-------------|
| /FEATURES = AdvancedAnalytics | データベース内のバージョンをインストールします。SQL Server Machine Learning Services (データベース内)。  |
| /FEATURES = SQL_INST_MR | これをアドバンス分析と組み合わせます。 Microsoft R Open および独自の R パッケージを含む (データベース内) R 機能をインストールします。 |
| /FEATURES = SQL_INST_MPY | これをアドバンス分析と組み合わせます。 (データベース内) Python 機能 (Anaconda や独自の Python パッケージなど) をインストールします。 |
| /FEATURES = SQL_SHARED_MR | スタンドアロンバージョンの R 機能をインストールします。SQL Server Machine Learning Server (スタンドアロン) です。 スタンドアロンサーバーは、データベースエンジンインスタンスにバインドされていない "共有機能" です。|
| /FEATURES = SQL_SHARED_MPY | では、スタンドアロンバージョンの Python 機能がインストールされます。SQL Server Machine Learning Server (スタンドアロン) です。 スタンドアロンサーバーは、データベースエンジンインスタンスにバインドされていない "共有機能" です。|
| /IACCEPTROPENLICENSETERMS  | オープンソース R コンポーネントを使用するためのライセンス条項に同意したことを示します。 |
| /IACCEPTPYTHONLICENSETERMS | Python コンポーネントを使用するためのライセンス条項に同意したことを示します。 |
| /IACCEPTSQLSERVERLICENSETERMS | SQL Server を使用するためのライセンス条項に同意したことを示します。|
| /MRCACHEDIRECTORY | オフラインセットアップでは、R コンポーネントの CAB ファイルを含むフォルダーを設定します。 |
| /MPYCACHEDIRECTORY | 将来使用するために予約されています。 インターネットに接続されていないコンピューターにインストールする Python コンポーネントの CAB ファイルを格納するには、% TEMP% を使用します。 |
::: moniker-end

## <a name="indb"></a>データベース内インスタンスのインストール

データベース内分析は、データベースエンジンインスタンスで使用できます。これは、のインストールに詳細**分析**機能を追加するために必要です。 高度な分析を使用してデータベースエンジンインスタンスをインストールすることも、[既存のインスタンスに追加](#add-existing)することもできます。 

対話型のスクリーンプロンプトを表示せずに進行状況に関する情報を表示するには、/qs 引数を使用します。

> [!IMPORTANT]
> インストール後、2つの追加の構成手順が残ります。 統合は、これらのタスクが実行されるまで完了しません。 手順については[、「インストール後のタスク](#post-install)」を参照してください。

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
### <a name="sql-server-machine-learning-services-database-engine-advanced-analytics-with-python-and-r"></a>SQL Server Machine Learning Services: データベースエンジン、Python および R を使用した高度な分析

データベースエンジンインスタンスの同時インストールの場合は、インスタンス名と管理者 (Windows) ログインを指定します。 コアおよび言語コンポーネントをインストールするための機能だけでなく、すべてのライセンス条項に同意することもできます。

```cmd
Setup.exe /qs /ACTION=Install /FEATURES=SQLEngine,ADVANCEDANALYTICS,SQL_INST_MR,SQL_INST_MPY
/INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS="<Windows-username>" 
/IACCEPTSQLSERVERLICENSETERMS /IACCEPTROPENLICENSETERMS /IACCEPTPYTHONLICENSETERMS
```

これは同じコマンドですが、混合認証を使用してデータベースエンジンにログイン SQL Server します。

```cmd
Setup.exe /q /ACTION=Install /FEATURES=SQLEngine,ADVANCEDANALYTICS,SQL_INST_MR,SQL_INST_MPY
/INSTANCENAME=MSSQLSERVER /SECURITYMODE=SQL /SAPWD="%password%" /SQLSYSADMINACCOUNTS="<sql-username>" 
/IACCEPTSQLSERVERLICENSETERMS /IACCEPTROPENLICENSETERMS /IACCEPTPYTHONLICENSETERMS
```

この例は Python のみであり、機能を省略して1つの言語を追加できることを示しています。

```cmd  
Setup.exe /qs /ACTION=Install /FEATURES=SQLEngine,ADVANCEDANALYTICS,SQL_INST_MPY 
/INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS="<username>" 
/IACCEPTSQLSERVERLICENSETERMS  /IACCEPTPYTHONLICENSETERMS
```
::: moniker-end

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
### <a name="sql-server-r-services-database-engine-and-advanced-analytics-with-r"></a>SQL Server R Services: R を使用したデータベースエンジンと高度な分析

データベースエンジンインスタンスの同時インストールの場合は、インスタンス名と管理者 (Windows) ログインを指定します。 コアおよび言語コンポーネントをインストールするための機能だけでなく、すべてのライセンス条項に同意することもできます。

```cmd  
Setup.exe /qs /ACTION=Install /FEATURES=SQLEngine,ADVANCEDANALYTICS,SQL_INST_MR
/INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS="<Windows-username>" 
/IACCEPTSQLSERVERLICENSETERMS /IACCEPTROPENLICENSETERMS 
```
::: moniker-end

## <a name="post-install"></a>インストール後の構成 (必須)

データベース内インストールにのみ適用されます。

セットアップが完了すると、R と Python、Microsoft R と Python のパッケージ、Microsoft R Open、Anaconda、tools、samples、およびディストリビューションの一部であるスクリプトが含まれたデータベースエンジンインスタンスが作成されます。 

インストールを完了するには、次の2つのタスクが必要です。


::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
1. データベースエンジンサービスを再起動します。

1. Machine Learning Services の SQL Server:機能を使用する前に、外部スクリプトを有効にしてください。 次の手順として[SQL Server Machine Learning Services (データベース内) のインストール](sql-machine-learning-services-windows-install.md)に関する説明に従ってください。 
::: moniker-end

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
1. データベースエンジンサービスを再起動します。

1. SQL Server R Services:機能を使用する前に、外部スクリプトを有効にしてください。 次の手順に従って[SQL Server R Services (データベース内) をインストール](sql-r-services-windows-install.md)する手順に従います。 
::: moniker-end

## <a name="add-existing"></a>既存のデータベースエンジンインスタンスに高度な分析を追加する

データベース内の高度な分析を既存のデータベースエンジンインスタンスに追加する場合は、インスタンス名を指定します。 たとえば、以前に SQL Server 2017 データベースエンジンと Python をインストールした場合、このコマンドを使用して R を追加できます。

```cmd  
Setup.exe /qs /ACTION=Install /FEATURES=SQL_INST_MR /INSTANCENAME=MSSQLSERVER 
/IACCEPTSQLSERVERLICENSETERMS  /IACCEPTROPENLICENSETERMS
```

## <a name="silent"></a>サイレントインストール

サイレントインストールでは、.cab ファイルの場所のチェックが抑制されます。 このため、.cab ファイルを展開する場所を指定する必要があります。 Python の場合、CAB ファイルは% TEMP * に配置する必要があります。 R の場合、このの一時ディレクトリを使用してフォルダーパスを設定できます。
 
```cmd  
Setup.exe /q /ACTION=Install /FEATURES=SQLEngine,ADVANCEDANALYTICS,SQL_INST_MR,SQL_INST_MPY 
/INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS="<username>" 
/IACCEPTSQLSERVERLICENSETERMS /IACCEPTROPENLICENSETERMS /IACCEPTPYTHONLICENSETERMS 
/MRCACHEDIRECTORY=%temp% 
```

## <a name="shared-feature"></a>スタンドアロンサーバーのインストール

スタンドアロンサーバーは、データベースエンジンインスタンスにバインドされていない "共有機能" です。 次の例は、スタンドアロンサーバーをインストールするための有効な構文を示しています。

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
SQL Server Machine Learning Server は、スタンドアロンサーバー上で Python と R をサポートしています。

```cmd
Setup.exe /q /ACTION=Install /FEATURES=SQL_SHARED_MR,SQL_SHARED_MPY  
/IACCEPTROPENLICENSETERMS /IACCEPTPYTHONLICENSETERMS /IACCEPTSQLSERVERLICENSETERMS
```
::: moniker-end
::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
R Server の SQL Server は R のみです。

```cmd
Setup.exe /q /ACTION=Install /FEATURES=SQL_SHARED_MR 
/IACCEPTROPENLICENSETERMS /IACCEPTSQLSERVERLICENSETERMS
```
::: moniker-end

セットアップが完了すると、サーバー、Microsoft パッケージ、R と Python のオープンソースディストリビューション、ツール、サンプル、およびディストリビューションの一部であるスクリプトが作成されます。 

R コンソールウィンドウを開くには、に`\Program files\Microsoft SQL Server\150 (or 140/130)\R_SERVER\bin\x64`アクセスし、 **rgui .exe**をダブルクリックします。 R の新機能 次のチュートリアルをお試しください。[基本的な R コマンドと RevoScaleR 関数:25一般的な](https://docs.microsoft.com/machine-learning-server/r/tutorial-r-to-revoscaler)例。

Python コマンドを開くには、に`\Program files\Microsoft SQL Server\150 (or 140)\PYTHON_SERVER\bin\x64`アクセスして、 **[python .exe]** をダブルクリックします。

## <a name="get-help"></a>ヘルプの参照

インストールまたはアップグレードに関するヘルプが必要ですか? 一般的な質問と既知の問題に対する回答については、次の記事を参照してください。

* [アップグレードとインストールに関してよく寄せられる質問-Machine Learning Services](../r/upgrade-and-installation-faq-sql-server-r-services.md)

インスタンスのインストール状態を確認し、一般的な問題を解決するには、これらのカスタムレポートを試してください。

* [SQL Server のカスタムレポート](../r/monitor-r-services-using-custom-reports-in-management-studio.md)

## <a name="next-steps"></a>次の手順

R 開発者は、いくつかの簡単な例を使って開始し、SQL Server での R の動作の基本を学習できます。 次の手順については、次のリンクを参照してください。

+ [チュートリアル: T-sql で R を実行する](../tutorials/rtsql-using-r-code-in-transact-sql-quickstart.md)
+ [チュートリアル: R 開発者向けのデータベース内分析](../tutorials/sqldev-in-database-r-for-sql-developers.md)

Python 開発者は、次のチュートリアルに従って、SQL Server で Python を使用する方法を学習できます。

+ [チュートリアル: T-sql で Python を実行する](../tutorials/run-python-using-t-sql.md)
+ [チュートリアル: Python 開発者向けのデータベース内分析](../tutorials/sqldev-in-database-python-for-sql-developers.md)

実際のシナリオに基づく機械学習の例については、「 [machine learning のチュートリアル](../tutorials/machine-learning-services-tutorials.md)」を参照してください。