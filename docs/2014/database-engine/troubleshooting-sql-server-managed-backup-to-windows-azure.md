---
title: Azure への管理されたバックアップ SQL Server のトラブルシューティング |Microsoft Docs
ms.custom: ''
ms.date: 03/08/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
ms.assetid: a34d35b0-48eb-4ed1-9f19-ea14754650da
author: mashamsft
ms.author: mathoma
manager: craigg
ms.openlocfilehash: cbdf7f9b9e8a428ed3d3bf6bfcfe14dbd7da68fc
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70153934"
---
# <a name="troubleshooting-sql-server-managed--backup-to-azure"></a>Azure への管理されたバックアップ SQL Server のトラブルシューティング
  このトピックでは、[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]の操作中に発生する可能性があるエラーのトラブルシューティングに使用できるツールとタスクについて説明します。  
  
## <a name="overview"></a>概要  
 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]には、組み込みのチェックとトラブルシューティング機能が用意されているため、多くの場合、内部エラーは [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] プロセス自体によって対処されます。  
  
 このような場合の例として、バックアップファイルを削除した結果、回復性[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]に影響を与えるログチェーンが壊れている場合は、ログチェーンの中断を特定し、すぐにバックアップを実行するようにスケジュールします。 ただし、状態を監視して、手動による介入を必要とするエラーには対処することをお勧めします。  
  
 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]では、システム ストアド プロシージャ、システム ビュー、および拡張イベントを使用して、イベントとエラーがログに記録されます。 システム ビューとストアド プロシージャでは、[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]の構成情報、バックアップの状態、定期的なバックアップ、さらに、拡張イベントによってキャプチャされたエラーが提供されます。 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]は、拡張イベントを使用して、トラブルシューティングに使用するエラーをキャプチャします。 SQL Server Smart Admin ポリシーは、イベントのログ記録に加え、正常性状態も提供します。正常性状態は、通知またはエラーや問題を示すために電子メール通知ジョブで使用されます。 詳細については、「 [Monitor SQL Server Managed Backup To Azure」を](../relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure.md)参照してください。  
  
 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]では、Azure storage に手動でバックアップするときに使用するのと同じログ記録も使用されます (SQL Server Backup to URL)。 Backup to URL 関連の問題の詳細については、「 [SQL Server backup TO url のベストプラクティスとトラブルシューティング](../relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting.md)」のトラブルシューティングのセクションを参照してください。  
  
### <a name="general-troubleshooting-steps"></a>一般的なトラブルシューティング手順  
  
1.  電子メール通知を有効にして、エラーと警告に関する電子メールの受信を開始します。  
  
     また、定期的に `smart_admin.fn_get_health_status` を実行して集計されたエラーおよび数を確認することもできます。 たとえば、`number_of_invalid_credential_errors` は、スマート バックアップでバックアップを試行して "無効な資格情報" エラーが発生した回数です。 `Number_of_backup_loops` と `number_of_retention_loops` はエラーではなく、バックアップ スレッドと保有期間スレッドがデータベースの一覧をスキャンした回数を示します。 通常、と@begin_time @end_timeが指定されていない場合、関数は過去30分間の情報を表示します。この2つの列には、通常、0以外の値が表示されます。 これらの値がゼロの場合は、システムがオーバーロードされたかシステムが応答していないことを意味します。 詳細については、このトピックで後述する「**システムの問題のトラブルシューティング**」を参照してください。  
  
2.  拡張イベント ログを確認して、エラーと関連するその他のイベントの詳細を理解します。  
  
3.  ログの情報を使用して、問題を解決します。  システムに関する問題またはエラーが発生した場合は、サービスまたは SQL Server エージェントの再起動が必要になることがあります。  
  
### <a name="common-causes-of-errors"></a>エラーの一般的な原因  
 エラーの一般的な原因の一覧を次に示します。  
  
1.  **SQL 資格情報に対する変更:** によって[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]使用される資格情報の名前が変更された場合[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] 、または削除された場合、はバックアップを実行できません。 変更は [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]の構成設定に適用する必要があります。  
  
2.  **ストレージアクセスキーの値の変更:** Azure アカウントのストレージキーの値が変更されても、SQL 資格情報が新しい値で更新さ[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]れない場合、はストレージに対する認証時に失敗し、このアカウントを使用するように構成されたデータベースのバックアップに失敗します。  
  
3.  **Azure Storage アカウントの変更:** SQL 資格情報[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]に対応する変更を行わずにストレージアカウントを削除または名前変更すると、が失敗し、バックアップは作成されません。 ストレージ アカウントを削除する場合は、有効なストレージ アカウント情報を使用してデータベースを再構成してください。 ストレージ アカウントの名前を変更したり、キー値を変更したりする場合は、[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]で使用されている SQL 資格情報にこれらの変更が反映されるようにしてください。  
  
4.  **データベースのプロパティに対する変更:** 復旧モデルを変更したり、名前を変更したりすると、バックアップが失敗する可能性があります。  
  
5.  **復旧モデルの変更:** データベースの復旧モデルが完全または一括ログから単純に変更された場合、バックアップは停止し、によって[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]データベースがスキップされます。 詳細については[、「Azure へのマネージバックアップの SQL Server」を参照してください。相互運用性と共存](../../2014/database-engine/sql-server-managed-backup-to-windows-azure-interoperability-and-coexistence.md)  
  
### <a name="most-common-error-messages-and-solutions"></a>最も一般的なエラー メッセージと解決方法  
  
1.  **有効化または構成[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]中にエラーが発生した場合:**  
  
     エラー:"ストレージ URL にアクセスできませんでした....有効な SQL 資格情報を指定してください... ":SQL 資格情報を参照しているこのようなエラーが表示されることがあります。  このような場合は、指定した SQL 資格情報の名前と、SQL 資格情報に格納されている情報 (ストレージアカウント名とストレージアクセスキー) を確認し、それらが最新で有効であることを確認してください。  
  
     エラー: "...データベースを構成できません...システムデータベースであるため、次のようになります。システムデータベースに対してを有効[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]にしようとすると、このエラーが表示されます。  [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]では、システム データベースのバックアップがサポートされません。  システム データベースのバックアップを構成するには、メンテナンス プランなどの別の SQL Server バックアップ テクノロジを使用してください。  
  
     エラー: "...保有期間を指定してください.... ":これらの値を初めて構成するときに、データベースまたはインスタンスの保有期間を指定していない場合は、保有期間に関するエラーが表示されることがあります。 また、1 ～ 30 の数値以外の値を指定した場合にもエラーが表示されることがあります。 保有期間に許可される値は 1 ～ 30 の数値です。  
  
2.  **電子メール通知エラー:**  
  
     エラー:"データベースメールが有効になっていません..."-電子メール通知を有効にしたが、データベースメールがインスタンスで構成されていない場合に、このエラーが表示されます。 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]の正常性状態の通知を受信できるように、インスタンス上のデータベース メールを構成する必要があります。 データベースメールを有効にする方法の詳細については、「 [Configure データベースメール](../relational-databases/database-mail/configure-database-mail.md)」を参照してください。 また、通知にデータベース メールを使用するには、SQL Server エージェントを有効にする必要があります。 詳細については、「[開始する前に](../relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail.md#BeforeYouBegin)」を参照してください。  
  
     電子メール通知に関連するエラー番号を次に示します。  
  
    -   エラー番号45209  
  
    -   エラー番号45210  
  
    -   エラー番号45211  
  
3.  **接続エラー:**  
  
    -   **SQL 接続に関連するエラー:** これらのエラーは、SQL Server インスタンスに接続するときに問題が発生した場合に発生します。 拡張イベントは、管理チャネルを介してこの種のエラーを公開します。 次の 2 つの拡張イベントにより、この種の接続の問題に関連するエラーを確認できます。  
  
         event_type が SqlError である FileRetentionAdminXEvent。 このエラーの詳細については、このイベントの error_code、error_message、および stack_trace を確認してください。 Error_code は、SqlException のエラー番号です。  
  
         次のメッセージまたはメッセージ プレフィックスが含まれた SmartBackupAdminXevent。  
  
         *"SQL Server マネージバックアップを Azure の既定の設定に構成中に内部エラーが発生しました。エラーは一時的なものである可能性があります。 "*  
  
         *「SQL Server で接続の問題が発生していることが考えられます。現在の反復処理でデータベースをスキップしています。 "*  
  
         *"ログの使用状況情報を照会できませんでした。これは一時的なエラーである可能性があります。現在の反復処理でデータベースをスキップしています。 "*  
  
         *SSMBackup2WA エージェントメタデータの読み込み中に SQL 例外が発生しました。これは一時的なエラーである可能性があります。操作は再試行されます。 "*  
  
         *"SSMBackup2WA で SQL 例外が発生しました..."*  
  
    -   **ストレージアカウントに接続中のエラー:**  
  
         event_type が XstoreError である FileRetentionAdminXEvent では、ストレージ例外が報告されます。 このエラーの詳細については、このイベントの error_message と stack_trace を確認してください。  
  
         SQL Server マネージド バックアップでは基になる Backup to URL テクノロジを使用するため、ストレージ接続に関連するエラーは両方の機能に適用されます。 トラブルシューティングの手順の詳細については、 [SQL Server Backup TO URL のベストプラクティスとトラブルシューティング](../relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting.md)に関する記事の**トラブルシューティング**に関するセクションを参照してください。  
  
### <a name="troubleshooting-system-issues"></a>システムに関する問題のトラブルシューティング  
 システム (SQL Server、SQL Server エージェント) で問題が発生した場合のシナリオと、その場合に [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]が受ける影響を次に示します。  
  
-   **が実行されている場合、sqlservr.exe [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]は応答を停止するか、動作を停止します。** SQL Server が動作を停止した場合、sql エージェントは[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]正常にシャットダウンします。また、イベントは停止し、sql エージェントの出力ファイルにも記録されます。  
  
     SQL Server が応答を停止した場合は、イベント ログが管理チャネルに記録されます。  イベント ログの例を次に示します。  
  
     *Sql エラー (エンジンが応答していないか、sqlException を取得しています:SqlException*   
     *エラーコード、メッセージ、および stacktrace は、次のような追加情報と共に管理チャネル xevent に表示されます。*    
    *「SQL Server で接続の問題が発生していることが考えられます。現在の反復処理でデータベースをスキップしています "*  
  
-   **が実行されている場合、 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] SQL エージェントが応答を停止するか、動作を停止します。**  
  
     SQL エージェントが動作を停止すると、[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]も停止され、イベント ログが管理チャネルに記録されます。 これは、SQL Server が応答を停止した場合のシナリオに似ています。  
  
     SQL エージェントが応答を停止した場合、[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]はバックアップ操作を継続できず、イベント ログが管理チャネルに記録されます。 イベント ログの例を次に示します。  
  
     *ジョブがハングした場合: 管理チャネル xevent を参照してください*   
    *"SQL Server からの進行状況の更新は、データベースのバックアップに対して" + DBBackupInfoMsgMaxWaitTime + "時間を超えています。 SSM Cloud Backup は引き続き待機します。 "*  
  
 電子メール通知を有効にした場合は、**バックアップループの数**と**保有期間のループの数**を含む通知が表示されます。 これらの列の一方または両方について、通知に示されている値がゼロであれば、システムが応答していない可能性があります。  
  
> [!WARNING]  
>  レポートの結果を生成する内部プロセスは、エンジンの診断ログが SQL エージェント エラー ログと同じ場所に存在することを想定します。SQL エージェント エラー ログは、既定では SQL Server インスタンスのエラー ログと同じフォルダーにあります。 エンジンの診断ログを、SQL エージェント エラー ログとは異なる場所に移動した場合は、システムはスマート バックアップの診断ログを見つけることができなくなるため、電子メール通知内のレポートが正しくなくなる可能性があります。 たとえば、バックアップループの数と保有期間のループの数を含む、報告されたすべてのフィールドに**0**という値が表示される場合があります。 この場合、診断ログを別の場所に移動した状況で、システムが応答しなくなるわけではなく、システムがログを見つけられなくなることを意味します。 最初に、診断ログと SQL エージェント エラー ログが同じ場所にあることを確認してください。 診断ログの現在の場所を確認するには、 [_os_server_diagnostics_log_configurations](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-server-diagnostics-log-configurations)を使用します。 列`path`は、エンジン診断ログの現在の場所を返します。  SQL エージェントのエラーログと同じフォルダーに配置する必要があります。 `dbo.sp_get_sqlagent_properties` ストアド プロシージャを使用して、SQL エージェント エラー ログのパスを取得できます。  
  
 拡張イベント ログでエラーの詳細を確認してください。 エラーを修正するか、SQL Server エージェントを再起動して状況を修正します。  
  
  
