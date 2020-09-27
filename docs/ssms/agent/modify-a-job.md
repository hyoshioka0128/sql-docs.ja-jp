---
description: Modify a Job
title: Modify a Job
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
helpviewer_keywords:
- jobs [SQL Server Agent], modifying
- modifying jobs
- SQL Server Agent jobs, modifying
ms.assetid: dd5e5f20-20c4-4ab9-a19a-db87577dcd43
author: markingmyname
ms.author: maghan
ms.reviewer: mikeray
ms.custom: seo-lt-2019
ms.date: 01/19/2017
monikerRange: = azuresqldb-mi-current || >= sql-server-2016 || = sqlallproducts-allversions
ms.openlocfilehash: fcbe3849b9d3aa1aef06c451437511076691a4db
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88319588"
---
# <a name="modify-a-job"></a>Modify a Job

[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

> [!IMPORTANT]  
> 現在、[Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) によって、すべてではありませんが、ほとんどの SQL Server エージェントの機能がサポートされています。 詳細については、[Azure SQL Managed Instance と SQL Server の T-SQL の相違点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)に関するページを参照してください。

このトピックでは、[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] で [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、[!INCLUDE[tsql](../../includes/tsql-md.md)]、または SQL Server 管理オブジェクトを使用して、[!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント ジョブのプロパティを変更する方法について説明します。  

## <a name="before-you-begin"></a><a name="BeforeYouBegin"></a>はじめに  
  
### <a name="limitations-and-restrictions"></a><a name="Restrictions"></a>制限事項と制約事項  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントのマスター ジョブの対象サーバーを、ローカル サーバーとリモート サーバーの両方に設定することはできません。  
  
### <a name="security"></a><a name="Security"></a>セキュリティ  
**sysadmin** 固定サーバー ロールのメンバー以外は、所有しているジョブしか変更できません。 詳細については、「 [SQL Server エージェントのセキュリティの実装](../../ssms/agent/implement-sql-server-agent-security.md)」をご覧ください。  
  
## <a name="using-sql-server-management-studio"></a><a name="SSMS"></a>SQL Server Management Studio の使用  
  
#### <a name="to-modify-a-job"></a>ジョブを変更するには  
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion_md.md)]のインスタンスに接続し、そのインスタンスを展開します。  
  
2.  **[SQL Server エージェント]**、 **[ジョブ]** の順に展開し、変更するジョブを右クリックします。次に、 **[プロパティ]** をクリックします。  
  
3.  **[ジョブのプロパティ]** ダイアログ ボックスの対応するページを使用して、ジョブのプロパティ、ステップ、スケジュール、警告、および通知を変更します。  
  
## <a name="using-transact-sql"></a><a name="TSQL"></a>Transact-SQL の使用  
  
#### <a name="to-modify-a-job"></a>ジョブを変更するには  
  
1.  オブジェクト エクスプローラーで、データベース エンジンのインスタンスに接続し、そのインスタンスを展開します。  
  
2.  ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  クエリ ウィンドウで、次のシステム ストアド プロシージャを使用してジョブを変更します。  
  
    -   ジョブの属性を変更するには、 [sp_update_job (Transact-SQL)](https://msdn.microsoft.com/cbdfea38-9e42-47f3-8fc8-5978b82e2623) を実行します。  
  
    -   ジョブ定義のスケジューリングの詳細を変更するには、 [sp_update_schedule (Transact-SQL)](https://msdn.microsoft.com/97b3119b-e43e-447a-bbfb-0b5499e2fefe) を実行します。  
  
    -   [sp_add_jobstep (Transact-SQL)](https://msdn.microsoft.com/97900032-523d-49d6-9865-2734fba1c755) を実行して、新しいジョブ ステップを追加します。  
  
    -   [sp_update_jobstep (Transact-SQL)](https://msdn.microsoft.com/e158802c-c347-4a5d-bf75-c03e5ae56e6b) を実行して、既存のジョブ ステップを変更します。  
  
    -   ジョブからジョブ ステップを削除するには、 [sp_delete_jobstep (Transact-SQL)](https://msdn.microsoft.com/421ede8e-ad57-474a-9fb9-92f70a3e77e3) を実行します。  
  
    -   任意の SQL Server エージェント マスター ジョブを変更するための追加のストアド プロシージャ:  
  
        -   [sp_delete_jobserver (Transact-SQL)](https://msdn.microsoft.com/6d63ed32-68cf-4d8f-aa40-05a3826e05b8) を実行して、ジョブに現在関連付けられているサーバーを削除します。  
  
        -   [sp_add_jobserver (Transact-SQL)](https://msdn.microsoft.com/485252cc-0081-490a-9bd1-cbbd68eea286) を実行して、サーバーを現在のジョブに関連付けます。  
  
## <a name="using-sql-server-management-objects"></a><a name="SMO"></a>SQL Server 管理オブジェクトの使用  
**ジョブを変更するには**  
  
Visual Basic、Visual C#、PowerShell などの選択したプログラミング言語で **Job** クラスを使用します。 詳細については、「 [SQL Server 管理オブジェクト (SMO) プログラミング ガイド](https://msdn.microsoft.com/library/ms162169.aspx)」を参照してください。  
  
