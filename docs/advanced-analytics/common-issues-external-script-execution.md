---
title: スタートパッドサービスと外部スクリプトの実行に関する一般的な問題
ms.prod: sql
ms.technology: ''
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: c90c59f3b59850bb0e2d1cf4cf40eb569e965eba
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715286"
---
# <a name="common-issues-with-launchpad-service-and-external-script-execution-in-sql-server"></a>スタートパッドサービスと SQL Server での外部スクリプトの実行に関する一般的な問題
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server の信頼されたスタートパッドサービスは、R および Python の外部スクリプトの実行をサポートします。 

複数の問題により、構成の問題や変更、ネットワークプロトコルの不足など、スタートパッドが起動しないことがあります。 この記事では、多くの問題のトラブルシューティングの指針を示します。 問題が発生した場合は、 [Machine Learning Server フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?category=MicrosoftR)に質問を投稿できます。

## <a name="determine-whether-launchpad-is-running"></a>スタートパッドが実行されているかどうかを判断する

1. **[サービス]** パネル (services.msc) を開きます。 または、コマンドラインで「 **sqlservermanager13.msc**または**SQLServerManager14** 」と入力して[SQL Server 構成マネージャー](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager)を開きます。

2. スタートパッドが実行されているサービスアカウントをメモしておきます。 R または Python が有効になっている各インスタンスには、スタートパッドサービスの独自のインスタンスが必要です。 たとえば、名前付きインスタンスのサービスは、 _MSSQLLaunchpad $ InstanceName_のようなものになります。

3. サービスが停止している場合は、サービスを再開します。 再起動時に、構成に問題がある場合は、システムイベントログにメッセージが公開され、サービスが再度停止されます。 サービスが停止した理由の詳細については、システムイベントログを確認してください。

4. RSetup .log の内容を確認し、セットアップでエラーが発生していないことを確認します。 たとえば、*コード0で終了*したメッセージは、サービスの開始に失敗したことを示します。

5. 他のエラーを検索するには、rlauncher .log の内容を確認します。

## <a name="check-the-launchpad-service-account"></a>スタートパッドサービスアカウントを確認する

既定のサービスアカウントは、"nt service\$SQL2016" または "nt\$service SQL2017" の場合があります。 最後の部分は、SQL インスタンス名によって異なる場合があります。

スタートパッドサービス (スタートパッド) は、低い特権のサービスアカウントを使用して実行されます。 ただし、R と Python を起動してデータベースインスタンスと通信するには、スタートパッドサービスアカウントに次のユーザー権利が必要です。

- サービスとしてログオン (SeServiceLogonRight)
- プロセス レベル トークンを置き換える (SeAssignPrimaryTokenPrivilege)
- スキャン チェックを行わない (SeChangeNotifyPrivilege)
- プロセスのメモリクォータを調整する (SeIncreaseQuotaSizePrivilege)

これらのユーザー権利の詳細については、「 [windows サービスアカウントと権限の構成](../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)」の「windows の特権と権限」セクションを参照してください。

> [!TIP]
> SQL Server 診断のためのサポート診断プラットフォーム (SDP) ツールの使用に慣れている場合は、SDP を使用して、MachineName_UserRights という名前の出力ファイルを確認できます。

## <a name="user-group-for-launchpad-cannot-log-on-locally"></a>スタートパッドのユーザーグループがローカルにログオンできない

Machine Learning Services のセットアップ時に、SQL Server によって Windows ユーザーグループ**SQLRUserGroup**が作成され、スタートパッドが SQL Server に接続して外部スクリプトジョブを実行するために必要なすべての権限を使用してプロビジョニングされます。 このユーザーグループが有効になっている場合は、Python スクリプトの実行にも使用されます。

ただし、より制限の厳しいセキュリティポリシーが適用されている組織では、このグループに必要な権限が手動で削除されたか、ポリシーによって自動的に失効される可能性があります。 権限が削除されている場合、スタートパッドは SQL Server に接続できなくなり SQL Server 外部ランタイムを呼び出すことはできません。

問題を解決するには、グループ **SQLRUserGroup** に**ローカルのログオンを許可する**システム権限があることを確認します。

詳細については、「[Windows サービス アカウントと権限の構成](../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)」を参照してください。

## <a name="permissions-to-run-external-scripts"></a>外部スクリプトを実行する権限

スタートパッドが正しく構成されている場合でも、ユーザーが R または Python スクリプトを実行するアクセス許可を持っていないと、エラーが返されます。

SQL Server をデータベース管理者としてインストールした場合、またはデータベース所有者の場合は、このアクセス許可が自動的に付与されます。 ただし、通常、他のユーザーにはアクセス許可が制限されています。 R スクリプトを実行しようとすると、スタートパッドエラーが表示されます。

この問題を解決するには、SQL Server Management Studio で、セキュリティ管理者は次のスクリプトを実行して、SQL ログインまたは Windows ユーザーアカウントを変更できます。

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

詳細については、「 [GRANT (transact-sql](../t-sql/statements/grant-transact-sql.md))」を参照してください。

## <a name="common-launchpad-errors"></a>一般的なスタートパッドエラー

ここでは、スタートパッドが返す最も一般的なエラーメッセージの一覧を示します。

::: moniker range=">=sql-server-2016||=sqlallproducts-allversions"
## <a name="unable-to-launch-runtime-for-r-script"></a>"R スクリプトのランタイムを起動できません"

R ユーザーの Windows グループ (Python にも使用) が R Services を実行しているインスタンスにログオンできない場合は、次のエラーが表示されることがあります。

- R スクリプトを実行しようとすると、次のエラーが生成されます。

    * *'R' スクリプトのランタイムを起動できません。'R' ランタイムの構成を確認してください。*

    * *外部スクリプト エラーが発生しました。ランタイムを起動できません。*

- [!INCLUDE[rsql_launchpad](../includes/rsql-launchpad-md.md)]サービスによって生成されたエラー:

    * *ランチャー RLauncher.dll を初期化できませんでした*

    * *ランチャー dll が登録されていません!*

    * *セキュリティログは、アカウント NT サービスがログオンできなかったことを示します。*

このユーザーグループに必要なアクセス許可を付与する方法については、「 [Install SQL Server R Services](install/sql-r-services-windows-install.md)」を参照してください。

> [!NOTE]
> SQL ログインを利用し、リモート ワークステーションから R スクリプトを実行する場合、この制限は適用されません。
::: moniker-end

## <a name="logon-failure-the-user-has-not-been-granted-the-requested-logon-type"></a>"ログオンに失敗しました: ユーザーは要求されたログオンの種類を許可されていません"

既定では[!INCLUDE[rsql_launchpad_md](../includes/rsql-launchpad-md.md)] 、はスタートアップ時に次の`NT Service\MSSQLLaunchpad`アカウントを使用します:。 アカウントは、必要な[!INCLUDE[ssNoVersion_md](../includes/ssnoversion-md.md)]すべてのアクセス許可を持つようにセットアップによって構成されます。

別のアカウントをスタートパッドに割り当てるか、SQL Server マシンのポリシーによって権利が削除されると、アカウントに必要なアクセス許可がない可能性があり、次のエラーが表示されることがあります。

>*ERROR_LOGON_TYPE_NOT_GRANTED 1385 (0x569) Logon failure: the user has not been granted the requested logon type at this computer*

新しいサービスアカウントに必要なアクセス許可を付与するには、ローカルセキュリティポリシーアプリケーションを使用し、アカウントのアクセス許可を更新して次のアクセス許可を追加します。

+ プロセスに対してメモリ クォータを調整する (SeIncreaseQuotaPrivilege)
+ スキャン チェックを行わない (SeChangeNotifyPrivilege)
+ サービスとしてログオン (SeServiceLogonRight)
+ プロセス レベル トークンを置き換える (SeAssignPrimaryTokenPrivilege)

## <a name="unable-to-communicate-with-the-launchpad-service"></a>"スタートパッドサービスと通信できません"

Machine learning をインストールして有効にした後、R または Python スクリプトを実行しようとするとこのエラーが発生する場合は、インスタンスのスタートパッドサービスが実行を停止している可能性があります。

1. Windows コマンド プロンプトから SQL Server 構成マネージャーを開きます。 詳細については、「 [SQL Server Configuration Manager](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager)」を参照してください。

2. インスタンスの SQL Server Launchpad を右クリックし、 **[プロパティ]** を選択します。

3. **[サービス]** タブを選択し、サービスが実行されていることを確認します。 実行されていない場合は、**開始モード**を **[自動]** に変更し、 **[適用]** を選択します。

4. 通常、サービスを再起動すると、machine learning スクリプトを実行できるように問題が解決されます。 再起動によって問題が解決されない場合は、 **[バイナリパス]** プロパティのパスと引数を確認し、次の手順を実行します。

    A. ランチャーの .config ファイルを確認し、作業ディレクトリが有効であることを確認します。

    B. スタートパッドによって使用される Windows グループが SQL Server インスタンスに接続できることを確認します。

    c. サービスのプロパティを変更する場合は、スタートパッドサービスを再起動します。

## <a name="fatal-error-creation-of-tmpfile-failed"></a>"TmpFile の致命的なエラー作成に失敗しました"

このシナリオでは、機械学習機能が正常にインストールされ、スタートパッドが実行されています。 いくつかの単純な R または Python コードを実行しようとしても、スタートパッドは次のようなエラーで失敗します。 

>*R スクリプトのランタイムと通信できません。R ランタイムの要件を確認してください。*

同時に、外部スクリプトランタイムは、STDERR メッセージの一部として次のメッセージを書き込みます。 

>*致命的なエラー: tmpfile の作成に失敗しました。*

このエラーは、スタートパッドに使用しようとしているアカウントに、データベースにログオンする権限がないことを示します。 この状況は、厳密なセキュリティポリシーが実装されている場合に発生する可能性があります。 この問題が発生しているかどうかを確認するには、SQL Server ログを確認し、ログイン時に MSSQLSERVER01 アカウントが拒否されたかどうかを確認します。 R\_services または PYTHON\_サービスに固有のログにも同じ情報が記載されています。 ExtLaunchError .log を探します。

既定では、20個のアカウントがセットアップされ、MSSQLSERVER01 ~ MSSQLSERVER20 という名前のスタートパッドプロセスに関連付けられます。 R または Python を頻繁に使用する場合は、アカウント数を増やすことができます。

この問題を解決するには、machine learning 機能がインストールされ、有効になっているローカルインスタンスに対する *[ローカルログオンを許可*する] アクセス許可がグループにあることを確認します。 環境によっては、このアクセス許可レベルでは、ネットワーク管理者からの GPO 例外が必要になる場合があります。

## <a name="not-enough-quota-to-process-this-command"></a>"このコマンドを処理するのに十分なクォータがありません"

このエラーは、次のいずれかのことを意味します。

- 外部クエリを実行するには、スタートパッドに外部ユーザーが不足している可能性があります。 たとえば、20を超える外部クエリを同時に実行していて、既定のユーザーが20個しかない場合、1つ以上のクエリが失敗する可能性があります。

- R タスクを処理するのに十分なメモリがありません。 このエラーは、ほとんどの場合、既定の環境で、SQL Server がコンピューターのリソースの最大 70% を使用している場合に発生します。 R によるリソースの使用をサポートするようにサーバー構成を変更する方法の詳細については、「[運用 Your r code](r/operationalizing-your-r-code.md)」を参照してください。

## <a name="cant-find-package"></a>"パッケージが見つかりません"

SQL Server で R コードを実行し、このメッセージを取得したが、SQL Server 外部で同じコードを実行したときにメッセージが表示されなかった場合は、SQL Server によって使用される既定のライブラリの場所にパッケージがインストールされていないことを意味します。

このエラーは、さまざまな方法で発生する可能性があります。

- サーバーに新しいパッケージをインストールしましたが、アクセスが拒否されたため、R はユーザーライブラリにパッケージをインストールしました。

- R Services をインストールし、別の R ツールまたは一連のライブラリ (Microsoft R Server (スタンドアロン)、Microsoft R Client、RStudio など) をインストールしました。

インスタンスによって使用される R パッケージライブラリの場所を確認するには、SQL Server Management Studio (またはその他のデータベースクエリツール) を開き、インスタンスに接続して、次のストアドプロシージャを実行します。

```sql
EXEC sp_execute_external_script @language = N'R',  
@script = N' print(normalizePath(R.home())); print(.libPaths());'; 
```

#### <a name="sample-results"></a>サンプルの結果

*外部スクリプトからの STDOUT メッセージ:*

*[1] "C:\\Program Files\\Microsoft SQL Server\\mssql13.mssqlserver。SQL2016\\R_SERVICES "*

*[1] "C:/Program Files/Microsoft SQL Server/MSSQL13.MSSQLSERVER です。SQL2016/R_SERVICES/library "*

この問題を解決するには、パッケージを SQL Server インスタンスライブラリに再インストールする必要があります。

::: moniker range=">=sql-server-2016||=sqlallproducts-allversions"
>[!NOTE]
>最新バージョンの Microsoft R を使用するように SQL Server 2016 のインスタンスをアップグレードした場合、既定のライブラリの場所は異なります。 詳細については、「 [SqlBindR を使用して R Services のインスタンスをアップグレードする](install/upgrade-r-and-python.md)」を参照してください。
::: moniker-end

::: moniker range=">=sql-server-2016||=sqlallproducts-allversions"
## <a name="launchpad-shuts-down-due-to-mismatched-dlls"></a>Dll が一致しないため、スタートパッドがシャットダウンする

その他の機能を使用してデータベースエンジンをインストールする場合は、サーバーに修正プログラムを適用した後、元のメディアを使用して Machine Learning 機能を追加すると、正しくないバージョンの Machine Learning コンポーネントがインストールされる可能性があります。 スタートパッドがバージョンの不一致を検出すると、シャットダウンしてダンプファイルを作成します。

この問題を回避するには、サーバーインスタンスと同じ修正レベルで、新しい機能をインストールしてください。

**アップグレードする方法が正しくありません。**

1. R Services を使用せずに SQL Server 2016 をインストールします。
2. 2016の累積的な更新プログラム 2 SQL Server アップグレードします。
3. RTM メディアを使用して、R Services (データベース内) をインストールします。

**正しいアップグレード方法は次のとおりです。**

1. R Services を使用せずに SQL Server 2016 をインストールします。
2. 2016 SQL Server を目的のパッチレベルにアップグレードします。 たとえば、Service Pack 1、累積的な更新プログラム2をインストールします。
3. 適切なパッチレベルで機能を追加するには、SP1 を実行し、セットアップを再度 CU2 してから、[R Services (データベース内)] を選択します。 

## <a name="launchpad-fails-to-start-if-8dot3-notation-is-required"></a>8dot3 表記が必要な場合、スタートパッドが起動しない

> [!NOTE] 
> 以前のシステムでは、8dot3 表記の要件がある場合、スタートパッドを起動できません。 この要件は、今後のリリースでは削除されました。 SQL Server 2016 R Services のお客様は、次のいずれかをインストールする必要があります。
> * SQL Server 2016 SP1 および CU1:[SQL Server の累積的な更新プログラム 1](https://support.microsoft.com/help/3208177/cumulative-update-1-for-sql-server-2016-sp1)。
> * SQL Server 2016 RTM、累積的な更新プログラム3、およびこの[修正プログラム](https://support.microsoft.com/help/3210110/on-demand-hotfix-update-package-for-sql-server-2016-cu3)は、オンデマンドで利用できます。

R との互換性を確保するために、SQL Server 2016 R Services (データベース内) では、 *8dot3 表記*を使用した短いファイル名の作成をサポートするために、機能がインストールされているドライブが必要でした。 8\.3 ファイル名は*短いファイル名*とも呼ばれ、以前のバージョンの Microsoft Windows との互換性のために使用されるか、長いファイル名の代わりに使用されます。

R をインストールするボリュームが短いファイル名をサポートしていない場合、SQL Server から R を起動するプロセスでは、正しい実行可能ファイルを見つけることができず、スタートパッドが起動しません。

回避策として、SQL Server がインストールされているボリュームと R Services がインストールされているボリュームで8dot3 表記を有効にすることができます。 次に、R Services 構成ファイルに作業ディレクトリの短い名前を指定する必要があります。

1. 8dot3 表記を有効にするには、「 [fsutil 8dot3name](https://technet.microsoft.com/library/ff621566(v=ws.11).aspx)」で説明されているように、 *8dot3name*引数を指定して fsutil ユーティリティを実行します。

2. 8dot3 表記が有効になったら、RLauncher .config ファイルを開き、の`WORKING_DIRECTORY`プロパティをメモします。 このファイルの検索方法の詳細については、「 [Machine Learning のトラブルシューティングのためのデータ収集](data-collection-ml-troubleshooting-process.md)」を参照してください。

3. Fsutil ユーティリティと*file*引数を使用して、WORKING_DIRECTORY で指定されたフォルダーの短いファイルパスを指定します。

4. 構成ファイルを編集して、WORKING_DIRECTORY プロパティで入力したものと同じ作業ディレクトリを指定します。 または、別の作業ディレクトリを指定して、既に8dot3 表記と互換性のある既存のパスを選択することもできます。
::: moniker-end

## <a name="next-steps"></a>次のステップ

[Machine Learning Services のトラブルシューティングと既知の問題](machine-learning-troubleshooting-faq.md)

[Machine learning のトラブルシューティングのためのデータ収集](data-collection-ml-troubleshooting-process.md)

[アップグレードとインストールに関してよく寄せられる質問](r/upgrade-and-installation-faq-sql-server-r-services.md)

[データベースエンジン接続のトラブルシューティング](../database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine.md)
