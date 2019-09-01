---
title: Master データベースの復元-Analytics Platform System (APS) |Microsoft Docs
description: Analytics Platform System (APS) で master データベースを復元します。
author: mzaman1
ms.prod: sql
ms.technology: data-warehouse
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: murshedz
ms.reviewer: martinle
ms.openlocfilehash: 624e1199fb953945ae6476a1f935dded48508bab
ms.sourcegitcommit: 3b1f873f02af8f4e89facc7b25f8993f535061c9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70176139"
---
# <a name="restore-the-master-database-in-analytics-platform-system-aps"></a>Analytics Platform System (APS) での master データベースの復元
SQL Server PDW Configuration Manager の**復元マスター**ページを使用すると、バックアップから master データベースを復元できます。  
  
## <a name="before-you-begin"></a>はじめに  
  
> [!IMPORTANT]  
> 復元を実行するには、ユーザーのセキュリティ情報とデータベースカタログを含む現在の master データベースを削除 SQL Server PDW 必要があります。 復元を実行する前に、現在の master データベースのバックアップを作成することをお勧めします。  
  
## <a name="to-restore-the-master-database"></a>master データベースを復元するには  
  
1.  Configuration Manager を起動します。 詳細については、「 [Launch &#40;The Configuration Manager Analytics&#41;Platform System](launch-the-configuration-manager.md)」を参照してください。  
  
2.  Configuration Manager の左側のウィンドウで、 **[Master の復元]** をクリックします。  
  
3.  復元するマスターバックアップを選択します。  
  
4.  **[適用]** をクリックします。  
  
5.  復元を実行するために、SQL Server PDW によってすべてのアプライアンスサービスがシャットダウンされ、すべてのユーザーが切断されます。 復元が完了すると、SQL Server PDW によってアプライアンスサービスが再開されます。  
  
![Dwconfig アプライアンス PDW の復元マスタ](./media/restore-the-master-database/SQL_Server_PDW_DWConfig_ApplPDWRestore.png "SQL_Server_PDW_DWConfig_ApplPDWRestore")  
  
