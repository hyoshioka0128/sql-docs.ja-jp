---
description: Create an ActiveX Script Job Step
title: Create an ActiveX Script Job Step
ms.custom: seo-lt-2019
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
helpviewer_keywords:
- ActiveX scripting jobs [SQL Server]
- job steps [Analysis Services]
ms.assetid: e6c46c6b-2d61-4571-bc8e-a831cd6e6302
author: markingmyname
ms.author: maghan
ms.reviewer: ''
monikerRange: = azuresqldb-mi-current || >= sql-server-2016 || = sqlallproducts-allversions
ms.openlocfilehash: 1d602a87258cd126d217353e94c0c600870e5a61
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88418228"
---
# <a name="create-an-activex-script-job-step"></a>Create an ActiveX Script Job Step
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

> [!IMPORTANT]  
> 現在、[Azure SQL Managed Instance](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview) によって、すべてではありませんが、ほとんどの SQL Server エージェントの機能がサポートされています。 詳細については、[Azure SQL Managed Instance と SQL Server の T-SQL の相違点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)に関するページを参照してください。

このトピックでは、[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] で [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、[!INCLUDE[tsql](../../includes/tsql-md.md)]、または SQL Server 管理オブジェクトを使用して、ActiveX スクリプトを実行する [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント ジョブ ステップを作成および定義する方法について説明します。  

**重要** [!INCLUDEssNoteDepFutureAvoid]
  
-   **作業を開始する準備:**  
  
    [制限事項と制約事項](#Restrictions)  
  
    [Security](#Security)  
  
-   **Transact-SQL ジョブ ステップを作成する方法:**  
  
    [SQL Server Management Studio](#SSMS)  
  
    [Transact-SQL](#TSQL)  
  
    [SQL Server 管理オブジェクト](#SMO)  
  
## <a name="before-you-begin"></a>はじめに  
  
### <a name="limitations-and-restrictions"></a><a name="Restrictions"></a>制限事項と制約事項  
[!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
### <a name="security"></a><a name="Security"></a>セキュリティ  
詳細については、「 [SQL Server エージェントのセキュリティの実装](../../ssms/agent/implement-sql-server-agent-security.md)」をご覧ください。  
  
## <a name="using-sql-server-management-studio"></a><a name="SSMS"></a>SQL Server Management Studio の使用  
  
#### <a name="to-create-an-activex-script-job-step"></a>ActiveX スクリプト ジョブ ステップを作成するには  
  
1.  **オブジェクト エクスプローラー** で、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion_md.md)]のインスタンスに接続し、そのインスタンスを展開します。  
  
2.  **[SQL Server エージェント]** を展開し、新しいジョブを作成するか、既存のジョブを右クリックして **[プロパティ]** をクリックします。 ジョブの作成の詳細については、「 [ジョブの作成](../../ssms/agent/create-jobs.md)」を参照してください。  
  
3.  **[ジョブのプロパティ]** ダイアログで **[ステップ]** ページをクリックし、 **[新規作成]** をクリックします。  
  
4.  **[新しいジョブ ステップ]** ダイアログの **[ステップ名]** ボックスにジョブ ステップ名を入力します。  
  
5.  **[種類]** ボックスの一覧で **[ActiveX スクリプト]** をクリックします。  
  
6.  **[実行するアカウント名]** ボックスの一覧で、ジョブで使用する資格情報を備えたプロキシ アカウントをクリックします。  
  
7.  **[言語]** でスクリプトを記述する言語を選択します。 または、 **[その他]** をクリックして、スクリプトの記述に使用する [!INCLUDE[msCoName](../../includes/msconame_md.md)] ActiveX スクリプティング言語の名前を入力します。  
  
8.  **[コマンド]** ボックスに、ジョブ ステップで実行するスクリプト構文を入力します。 または、 **[開く]** をクリックしてスクリプト構文が記述されたファイルを選択します。  
  
9. **[詳細設定]** ページをクリックして、ジョブ ステップのオプションのうち、ジョブ ステップが成功または失敗した場合のアクション、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントによるジョブ ステップの再試行回数、および再試行間隔を設定します。  
  
## <a name="using-transact-sql"></a><a name="TSQL"></a>Transact-SQL の使用  
  
#### <a name="to-create-an-activex-script-job-step"></a>ActiveX スクリプト ジョブ ステップを作成するには  
  
1.  **オブジェクト エクスプローラー**で、 [!INCLUDE[ssDE](../../includes/ssde_md.md)]のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーしてクエリ ウィンドウに貼り付け、 **[実行]** をクリックします。  
  
    ```  
    -- create an ActiveX Script job step written in VBScript that creates a restore point  
    USE msdb;  
    GO  
    EXEC sp_add_jobstep  
        @job_name = N'Weekly Sales Data Backup',  
        @step_name = N'Create a restore point',  
        @subsystem = N'ACTIVESCRIPTING',  
        @command = N'Const RESTORE_POINT = 20  
  
    strComputer = "."  
    Set objWMIService = GetObject("winmgmts:" _  
        & "{impersonationLevel=impersonate}!\\" & strComputer & "\root\default")  
  
    Set objItem = objWMIService.Get("SystemRestore")  
    errResults = objItem.Restore(RESTORE_POINT)',   
        @retry_attempts = 5,  
        @retry_interval = 5 ;  
    GO  
    ```  
  
詳細については、「 [sp_add_jobstep (Transact-SQL)](https://msdn.microsoft.com/97900032-523d-49d6-9865-2734fba1c755)」を参照してください。  
  
## <a name="using-sql-server-management-objects"></a><a name="SMO"></a>SQL Server 管理オブジェクトの使用  
**ActiveX スクリプト ジョブ ステップを作成するには**  
  
Visual Basic、Visual C#、PowerShell などの選択したプログラミング言語で **JobStep** クラスを使用します。  
  
