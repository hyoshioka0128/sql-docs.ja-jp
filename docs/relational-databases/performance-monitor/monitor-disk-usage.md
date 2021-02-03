---
title: ディスクの使用量の監視 | Microsoft Docs
description: SQL Server のディスク監視活動には、ディスク I/O の監視、過度のページングの検出、SQL Server によって作成されたディスク利用状況の分離が含まれます。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- database monitoring [SQL Server], disk usage
- disks [SQL Server]
- monitoring performance [SQL Server], disk usage
- server performance [SQL Server], disk usage
- monitoring [SQL Server], disk activity
- excess paging [SQL Server]
- tuning databases [SQL Server], disk usage
- I/O [SQL Server], monitoring
- disks [SQL Server], monitoring activity
- isolating disk activity [SQL Server]
- database performance [SQL Server], disk usage
- monitoring server performance [SQL Server], disk usage
ms.assetid: 1525449c-ea7d-4222-b294-1ba1fe99c9ac
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: de77b88d4b82e0fd7360e76d090dd13468ef2a53
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505976"
---
# <a name="monitor-disk-usage"></a>ディスクの使用量の監視
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、Microsoft Windows オペレーティング システムの入出力 (I/O) 呼び出しを使用して、ディスクに対する読み取りおよび書き込み操作を実行します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、ディスク I/O を実行する時期と方法が管理されます。ただし、基本的な I/O 操作は Windows オペレーティング システムで実行されます。 I/O サブシステムには、システム バス、ディスク コントローラー カード、ディスク、テープ ドライブ、CD-ROM ドライブなど、多数の I/O 装置が含まれます。 ディスク I/O は、頻繁にシステムのボトルネックの原因となります。  
  
 ディスク利用状況の監視には、次の 2 つの領域があります。  
  
-   ディスク I/O の監視と、過度なページングの検出  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で生成されるディスク利用状況の隔離  
  
 詳細については、「[ディスク使用量の監視](https://social.technet.microsoft.com/wiki/contents/articles/monitoring-disk-usage.aspx)」を参照してください。 
 
 SQL Server での I/O に関する問題を解決する方法については、「[Slow I/O - SQL Server and disk I/O performance (低速な I/O - SQL Server とディスク I/O のパフォーマンス)](https://techcommunity.microsoft.com/t5/SQL-Server-Support/Slow-I-O-SQL-Server-and-disk-I-O-performance/ba-p/333983)」を参照してください。
  
  
