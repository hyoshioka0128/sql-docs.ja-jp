---
title: 'SQL Server: Cursor Manager by Type オブジェクト | Microsoft Docs'
description: 種類別にグループ化されたカーソルを監視するカウンターを提供する SQLServer:Cursor Manager by Type オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- Cursor Manager by Type object
- SQLServer:Cursor Manager by Type
ms.assetid: d67fbd8a-7554-4a16-96f1-d9ee857a95e3
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 95f88849070d95703b611a00c5c1e5abef94fffb
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505793"
---
# <a name="sql-server-cursor-manager-by-type-object"></a>SQL Server: Cursor Manager by Type オブジェクト
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  **SQLServer:Cursor Manager by Type** オブジェクトには、種類別にグループ化されたカーソルを監視するカウンターが用意されています。  
  
 次の表で、SQL Server **Cursor Manager by Type** カウンターについて説明します。  
  
|Cursor Manager by Type カウンター|説明|  
|-------------------------------------|-----------------|  
|**Active cursors**|アクティブなカーソルの数。|  
|**Cache Hit Ratio**|キャッシュ ヒットとキャッシュ参照の比率。|  
|**Cache Hit Ratio Base**|内部使用専用です。| 
|**Cached Cursor Counts**|キャッシュ内の特定種類のカーソルの数。|  
|**Cursor Cache Use Count/sec**|キャッシュされている各種のカーソルが使用された時間。|  
|**Cursor memory usage**|カーソルによって消費されたメモリ量 (KB)。|  
|**Cursor Requests/sec**|サーバーが受け取った SQL カーソル要求の数。|  
|**Cursor worktable usage**|カーソルによって使用された作業テーブルの数。|  
|**Number of active cursor plans**|カーソル プランの数。|  
  
 オブジェクトの各カウンターには、次のインスタンスが含まれています。  
  
|Cursor Manager のインスタンス|説明|  
|-----------------------------|-----------------|  
|**_Total**|すべてのカーソルに関する情報。|  
|**API Cursor**|API カーソルのみに関する情報。|  
|**TSQL Global Cursor**|[!INCLUDE[tsql](../../includes/tsql-md.md)] グローバル カーソルのみに関する情報。|  
|**TSQL Local Cursor**|[!INCLUDE[tsql](../../includes/tsql-md.md)] ローカル カーソルのみに関する情報。|  
  
## <a name="see-also"></a>参照  
 [リソースの利用状況の監視 &#40;システム モニター&#41;](../../relational-databases/performance-monitor/monitor-resource-usage-system-monitor.md)  
  
  
