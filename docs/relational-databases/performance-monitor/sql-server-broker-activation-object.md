---
title: 'SQL Server: Broker Activation オブジェクト | Microsoft Docs'
description: ストアド プロシージャのアクティブ化に関する情報を報告するパフォーマンス カウンターが含まれる SQLServer:BrokerActivation パフォーマンス オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- SQLServer:Broker Activation
- Broker Activation object
ms.assetid: cd9b6880-c924-42c7-b333-09c303317c0b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 8c257bee159905042ddece42102ef0a55a3512f1
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505868"
---
# <a name="sql-server-broker-activation-object"></a>SQL Server: Broker Activation オブジェクト
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  **SQLServer:BrokerActivation** パフォーマンス オブジェクトには、ストアド プロシージャのアクティブ化に関する情報を報告するパフォーマンス カウンターが含まれています。 次の表は、このオブジェクトに含まれているカウンターを示します。  
  
|SQL Server Broker Activation カウンター|説明|  
|-------------------------------------------|-----------------|  
|**Stored Procedures Invoked/sec**|インスタンス内のすべてのキュー モニターによって 1 秒間に呼び出されたアクティブ化ストアド プロシージャの合計数が報告されます。|  
|**Task Limit Reached**|キューに登録できる最大数のタスクが既に実行されているために、キュー モニターで新しいタスクの開始に失敗した合計回数が報告されます。|  
|**Task Limit Reached/sec**|キューに登録できる最大数のタスクが既に実行されているために、キュー モニターで 1 秒間に新しいタスクの開始に失敗した回数が報告されます。|  
|**Tasks Aborted/sec**|エラーが発生して終了したか、メッセージを受信できないためにキュー モニターによって中断されたアクティブ化ストアド プロシージャのタスク数が報告されます。|  
|**Tasks Running**|現在実行中のアクティブ化ストアド プロシージャの数が報告されます。|  
|**Tasks Started/sec**|インスタンス内のすべてのキュー モニターによって 1 秒間に開始されたアクティブ化ストアド プロシージャの数が報告されます。|  
  
## <a name="see-also"></a>参照  
 [sys.dm_broker_activated_tasks &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-broker-activated-tasks-transact-sql.md)   
 [sys.dm_broker_queue_monitors &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-broker-queue-monitors-transact-sql.md)   
 [リソースの利用状況の監視 &#40;システム モニター&#41;](../../relational-databases/performance-monitor/monitor-resource-usage-system-monitor.md)  
  
  
