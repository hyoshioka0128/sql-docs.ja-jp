---
title: 'SQL Server: Backup Device オブジェクト | Microsoft Docs'
description: バックアップ操作と復元操作に使用する Microsoft SQL Server バックアップ デバイスを監視するためのカウンターを提供する Backup Device オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- SQLServer:Backup Device
- Backup Device object
ms.assetid: 52e7febf-d5e0-4674-945b-aacc40a9ad6e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 383e1cd4ccca7a466817ac5ca226785fa56560ce
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505904"
---
# <a name="sql-server-backup-device-object"></a>SQL Server: Backup Device オブジェクト
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **Backup Device** オブジェクトには、バックアップ操作と復元操作に使用する Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バックアップ デバイスを監視するためのカウンターがあります。 バックアップ デバイスを監視するのは、バックアップ操作と復元操作のスループット、または進行状況やパフォーマンスをデバイスごとに調べる場合です。 データベースのバックアップ操作または復元操作全体のスループットを監視するには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **Databases** オブジェクトの **Backup/Restore Throughput/sec** カウンターを使用します。 詳しくは、「 [SQL Server, Databases Object](../../relational-databases/performance-monitor/sql-server-databases-object.md)」をご覧ください。  
  
 次の表では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **Backup Device** カウンターについて説明します。  
  
|SQL Server Backup Device カウンター|説明|  
|---------------------------------------|-----------------|  
|**Device Throughput Bytes/sec**|データベースをバックアップまたは復元するときに使用するバックアップ デバイスの読み取りおよび書き込み操作のスループット (1 秒あたりのバイト数)。 このカウンターは、バックアップ操作または復元操作の実行中のみ存在します。|  
  
## <a name="see-also"></a>参照  
 [バックアップ デバイス &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-devices-sql-server.md)   
 [リソースの利用状況の監視 &#40;システム モニター&#41;](../../relational-databases/performance-monitor/monitor-resource-usage-system-monitor.md)  
  
  
