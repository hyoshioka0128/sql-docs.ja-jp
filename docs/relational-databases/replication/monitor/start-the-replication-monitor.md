---
description: レプリケーション モニターの開始
title: レプリケーション モニターの開始 | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
helpviewer_keywords:
- Replication Monitor, starting
ms.assetid: e037bd27-cc87-4ee9-9e5f-83f6d717cfa4
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: d88c974bbf6d8f18271f4ea6f68b95e5c0506203
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97432570"
---
# <a name="start-the-replication-monitor"></a>レプリケーション モニターの開始
[!INCLUDE[sql-asdbmi](../../../includes/applies-to-version/sql-asdbmi.md)]
  レプリケーション モニターは、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] から [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]の任意のインスタンスに対して起動するか、コマンド プロンプトから起動できます。 レプリケーション モニターを起動した後、1 つ以上のパブリッシャーをモニターに追加します。 詳細については、「 [レプリケーション モニターのパブリッシャーの追加および削除](../../../relational-databases/replication/monitor/add-and-remove-publishers-from-replication-monitor.md)」を参照してください。  
  
### <a name="to-start-replication-monitor-from-sql-server-management-studio"></a>SQL Server Management Studio からレプリケーション モニターを起動するには  
  
1.  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] で [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]のインスタンスに接続して、サーバー ノードを展開します。  
  
2.  **[レプリケーション]** フォルダーまたはそのサブフォルダーを右クリックして、 **[レプリケーション モニターの起動]** をクリックします。  

### <a name="to-start-replication-monitor-from-the-command-prompt"></a>コマンド プロンプトからレプリケーション モニターを起動するには  
  
1.  コマンド プロンプトで、ツールをインストールしたディレクトリに移動します。 既定のパスは [!INCLUDE[ssInstallPath](../../../includes/ssinstallpath-md.md)]Tools\Binn\ です。  
  
2.  sqlmonitor.exe を実行します。  
  
## <a name="see-also"></a>参照  
 [レプリケーションの監視](../../../relational-databases/replication/monitor/monitoring-replication.md)  
  
  
