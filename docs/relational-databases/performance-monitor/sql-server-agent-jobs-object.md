---
title: SQL Server エージェントの Jobs オブジェクト | Microsoft Docs
description: SQL Server エージェントのジョブについての情報を報告するパフォーマンス カウンターが含まれる、SQL Server エージェントの Jobs パフォーマンス オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- SQLAgent:Jobs
- Jobs object
ms.assetid: 225b5e2d-4a78-4178-b2b6-b419df83c4aa
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 34b4a1e9fc276da5e629d7521609bca5570a5798
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505933"
---
# <a name="sql-server-agent-jobs-object"></a>SQL Server エージェントの Jobs オブジェクト
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  SQL Server エージェントの **Jobs** パフォーマンス オブジェクトには、SQL Server エージェントのジョブについての情報を報告するパフォーマンス カウンターが含まれています。 次の表は、このオブジェクトに含まれているカウンターを示します。  
  
 次の表には **SQLAgent:Jobs** カウンターが含まれています。  
  
|名前|説明|  
|----------|-----------------|  
|**Active Jobs**|このカウンターは、現在実行中のジョブの数を報告します。|  
|**Failed jobs**|このカウンターは、失敗して終了したジョブの数を報告します。|  
|**Job success rate**|このカウンターは、実行済みジョブのうち問題なく完了したジョブの割合を報告します。|  
|**Jobs activated/minute**|このカウンターは、最後の 1 分間に起動されたジョブの数を報告します。|  
|**Queued jobs**|このカウンターは、SQL Server エージェントで実行する準備が整っているジョブで、まだ実行が開始されていないジョブの数を報告します。|  
|**Successful jobs**|このカウンターは、問題なく終了したジョブの数を報告します。|  
  
 オブジェクトの各カウンターには、次のインスタンスが含まれています。  
  
|インスタンス|説明|  
|--------------|-----------------|  
|**_Total**|すべてのジョブの情報です。|  
|**警告**|警告によって開始されたジョブの情報です。|  
|**Others**|警告またはスケジュールによって開始されたジョブの情報です。 通常、これらのジョブは **sp_start_job** を使用して手動で開始されます。|  
|**スケジュール**|スケジュールによって開始されたジョブの情報です。|  
  
## <a name="see-also"></a>参照  
 [ジョブの実装](../../ssms/agent/implement-jobs.md)   
 [パフォーマンス オブジェクトの使用](../../ssms/agent/use-performance-objects.md)   
 [リソースの利用状況の監視 &#40;システム モニター&#41;](../../relational-databases/performance-monitor/monitor-resource-usage-system-monitor.md)  
  
  
