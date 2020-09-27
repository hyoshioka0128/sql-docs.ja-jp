---
description: ターゲット サーバーのクロックの同期
title: ターゲット サーバーのクロックの同期
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
helpviewer_keywords:
- SQL Server Agent jobs, target servers
- clocks [SQL Server]
- master servers [SQL Server], clock synchronization
- synchronization [SQL Server], target server clocks
- target servers [SQL Server], clock synchronization
ms.assetid: 4fb80502-d271-4d06-bcbc-bfbbceb5f2a2
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 01/19/2017
monikerRange: = azuresqldb-mi-current || >= sql-server-2016 || = sqlallproducts-allversions
ms.openlocfilehash: 9c0804658359c1272bfe4acb025fbded3ae2adc7
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88463024"
---
# <a name="synchronize-target-server-clocks"></a>ターゲット サーバーのクロックの同期

[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

> [!IMPORTANT]  
> 現在、[Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) によって、すべてではありませんが、ほとんどの SQL Server エージェントの機能がサポートされています。 詳細については、[Azure SQL Managed Instance と SQL Server の T-SQL の相違点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)に関するページを参照してください。

このトピックでは、[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)] を使用して、[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] のターゲット サーバーのクロックとマスター サーバーのクロックを同期する方法について説明します。 これらのシステム クロックの同期をとると、ジョブのスケジュールを効果的に管理できます。  

## <a name="before-you-begin"></a><a name="BeforeYouBegin"></a>はじめに  
  
### <a name="security"></a><a name="Security"></a>セキュリティ  
  
#### <a name="permissions"></a><a name="Permissions"></a>アクセス許可  
**sysadmin** 固定サーバー ロールのメンバーシップが必要です。  
  
## <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a>SQL Server Management Studio の使用  
  
#### <a name="to-synchronize-target-server-clocks"></a>ターゲット サーバーのクロックを同期するには  
  
1.  **オブジェクト エクスプローラー**で、ターゲット サーバーのクロックとマスター サーバーのクロックを同期するサーバーをプラス記号をクリックして展開します。  
  
2.  **[SQL Server エージェント]** を右クリックし、**[マルチ サーバーの管理]** をポイントして、**[ターゲット サーバーの管理]** を選択します。  
  
3.  **[ターゲット サーバーの管理]** ダイアログ ボックスで **[命令を通知]** をクリックします。  
  
4.  **[命令の種類]** ボックスの一覧で、 **[クロックの同期]** を選択します。  
  
5.  **[受信者]** で、次のいずれかの操作を行います。  
  
    -   すべてのターゲット サーバーのクロックとマスター サーバーのクロックを同期するには、**[すべてのターゲット サーバー]** をクリックします。  
  
    -   特定のサーバーのクロックを同期するには、**[特定のターゲット サーバー]** をクリックし、マスター サーバーのクロックと同期するターゲット サーバーを選択します。  
  
6.  完了したら、 **[OK]** をクリックします。  
  
## <a name="using-transact-sql"></a><a name="TsqlProcedure"></a>Transact-SQL の使用  
  
#### <a name="to-synchronize-target-server-clocks"></a>ターゲット サーバーのクロックを同期するには  
  
1.  **オブジェクト エクスプローラー**で、 [!INCLUDE[ssDE](../../includes/ssde_md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    USE msdb ;  
    GO  
    -- resynchronizes the SEATTLE1 target server  
    EXEC dbo.sp_resync_targetserver  
        N'SEATTLE1' ;  
    GO  
    ```  
  
詳細については、「 [sp_resync_targetserver (Transact-SQL)](https://msdn.microsoft.com/40e44df7-d3e3-44ee-b149-08aba629a21f)」を参照してください。  
  
