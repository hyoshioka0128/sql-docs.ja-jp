---
description: Disable or Enable a Job
title: Disable or Enable a Job
ms.custom: seo-lt-2019
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
helpviewer_keywords:
- stopping jobs
- disabling jobs
- SQL Server Agent jobs, disabling
- jobs [SQL Server Agent], disabling
ms.assetid: 5041261f-0c32-4d4a-8bee-59a6c16200dd
author: markingmyname
ms.author: maghan
ms.reviewer: ''
monikerRange: = azuresqldb-mi-current || >= sql-server-2016 || = sqlallproducts-allversions
ms.openlocfilehash: 830a9271c84fc4b76325f33711ed35b9e543f256
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88371588"
---
# <a name="disable-or-enable-a-job"></a>Disable or Enable a Job
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

> [!IMPORTANT]  
> 現在、[Azure SQL Managed Instance](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview) によって、すべてではありませんが、ほとんどの SQL Server エージェントの機能がサポートされています。 詳細については、[Azure SQL Managed Instance と SQL Server の T-SQL の相違点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)に関するページを参照してください。

このトピックでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] を使用して、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] で [!INCLUDE[tsql](../../includes/tsql-md.md)]エージェント ジョブを無効にする方法について説明します。 ジョブを無効にしても、ジョブは削除されるわけではなく、必要に応じて再び有効にすることができます。  
  
## <a name="before-you-begin"></a><a name="BeforeYouBegin"></a>はじめに  
  
### <a name="security"></a><a name="Security"></a>セキュリティ  
詳細については、「 [SQL Server エージェントのセキュリティの実装](../../ssms/agent/implement-sql-server-agent-security.md)」をご覧ください。  
  
## <a name="using-sql-server-management-studio"></a><a name="SSMS"></a>SQL Server Management Studio の使用  
  
#### <a name="to-disable-or-enable-a-job"></a>ジョブを無効または有効にするには  
  
1.  **オブジェクト エクスプローラー**で、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion_md.md)]のインスタンスに接続し、そのインスタンスを展開します。  
  
2.  **[SQL Server エージェント]** を展開します。  
  
3.  **[ジョブ]** を展開し、無効または有効にするジョブを右クリックします。  
  
4.  ジョブを無効にするには、 **[無効化]** をクリックします。 ジョブを有効にするには、 **[有効化]** をクリックします。  
  
## <a name="using-transact-sql"></a><a name="TSQL"></a>Transact-SQL の使用  
  
#### <a name="to-disable-or-enable-a-job"></a>ジョブを無効または有効にするには  
  
1.  **オブジェクト エクスプローラー**で、 [!INCLUDE[ssDE](../../includes/ssde_md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    -- changes the name, description, and disables status of the job NightlyBackups.  
    USE msdb ;  
    GO  
  
    EXEC dbo.sp_update_job  
        @job_name = N'NightlyBackups',  
        @new_name = N'NightlyBackups -- Disabled',  
        @description = N'Nightly backups disabled during server migration.',  
        @enabled = 0 ;  
    GO  
    ```  
  
詳細については、「 [sp_update_job (Transact-SQL)](https://msdn.microsoft.com/cbdfea38-9e42-47f3-8fc8-5978b82e2623)」を参照してください。  
  
