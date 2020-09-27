---
description: Change the Scheduling Details for a SQL Server Agent Master Job
title: マスター ジョブのスケジューリング詳細を変更する
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
ms.assetid: f5414451-4d8e-464b-bd9e-f2b70c6899b3
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 01/19/2017
monikerRange: = azuresqldb-mi-current || >= sql-server-2016 || = sqlallproducts-allversions
ms.openlocfilehash: 3a76eb7ec3ef8b0daa11771d75938b6b65694423
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88371768"
---
# <a name="change-the-scheduling-details-for-a-sql-server-agent-master-job"></a>Change the Scheduling Details for a SQL Server Agent Master Job

[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

> [!IMPORTANT]  
> 現在、[Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) によって、すべてではありませんが、ほとんどの SQL Server エージェントの機能がサポートされています。 詳細については、[Azure SQL Managed Instance と SQL Server の T-SQL の相違点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)に関するページを参照してください。

このトピックでは、 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] または [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使用して、 [!INCLUDE[tsql](../../includes/tsql-md.md)]でジョブ定義のスケジューリングの詳細を変更する方法について説明します。  
  
## <a name="before-you-begin"></a><a name="BeforeYouBegin"></a>はじめに  
  
### <a name="limitations-and-restrictions"></a><a name="Restrictions"></a>制限事項と制約事項  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントのマスター ジョブの対象サーバーを、ローカル サーバーとリモート サーバーの両方に設定することはできません。  
  
### <a name="security"></a><a name="Security"></a>セキュリティ  
  
#### <a name="permissions"></a><a name="Permissions"></a>アクセス許可  
**sysadmin** 固定サーバー ロールのメンバー以外は、所有しているジョブしか変更できません。 詳細については、「 [SQL Server エージェントのセキュリティの実装](../../ssms/agent/implement-sql-server-agent-security.md)」をご覧ください。  
  
## <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a>SQL Server Management Studio の使用  
  
#### <a name="to-change-the-scheduling-details-for-a-job-definition"></a>ジョブ定義のスケジューリングの詳細を変更するには  
  
1. **オブジェクト エクスプローラー** で、スケジュールを編集するジョブを含むサーバーをプラス記号をクリックして展開します。  
  
2. プラス記号をクリックして **[SQL Server エージェント]** を展開します。  
  
3. プラス記号をクリックして **[ジョブ]** フォルダーを展開します。  
  
4. スケジュールを編集するジョブを右クリックして、 **[プロパティ]** をクリックします。  
  
5. [**ジョブのプロパティ** - _job\_name_] ダイアログ ボックスで、 **[ページの選択]** の **[スケジュール]** を選択します。 このページで利用可能なオプションの詳細については、「[ジョブのプロパティ - [新しいジョブ] ([スケジュール] ページ)](../../ssms/agent/job-properties-new-job-schedules-page.md)」をご覧ください。  
  
6. 完了したら、 **[OK]** をクリックします。  
  
## <a name="using-transact-sql"></a><a name="TsqlProcedure"></a>Transact-SQL の使用  
  
#### <a name="to-change-the-scheduling-details-for-a-job-definition"></a>ジョブ定義のスケジューリングの詳細を変更するには
  
1. **オブジェクト エクスプローラー**で、 [!INCLUDE[ssDE](../../includes/ssde_md.md)]のインスタンスに接続します。  
  
2. [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3. 次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    -- changes the enabled status of the NightlyJobs schedule to 0
    -- and sets the owner to terrid.
    USE msdb ;  
    GO  
  
    EXEC dbo.sp_update_schedule  
        @name = 'NightlyJobs',  
        @enabled = 0,  
        @owner_login_name = 'terrid' ;  
    GO  
    ```  
  
詳しくは、「 [sp_update_schedule (Transact-SQL)](https://msdn.microsoft.com/97b3119b-e43e-447a-bbfb-0b5499e2fefe)」をご覧ください。
