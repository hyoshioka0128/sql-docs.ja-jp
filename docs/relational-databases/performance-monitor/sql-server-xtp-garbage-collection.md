---
title: SQL Server XTP Garbage Collection | Microsoft Docs
description: インメモリ OLTP エンジンのガベージ コレクターに関連するカウンターが含まれる SQL Server XTP Garbage Collection パフォーマンス オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
ms.assetid: 64ae91e5-b420-44b4-af1a-f8bca83d7f41
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 48e4b2864465e095220ab238d8288c6e12639dec
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505511"
---
# <a name="sql-server-xtp-garbage-collection"></a>SQL Server XTP Garbage Collection
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  SQL Server XTP Garbage Collection パフォーマンス オブジェクトには、インメモリ OLTP エンジンのガベージ コレクターに関連するカウンターが含まれています。  
  
 次の表では、 **SQL Server XTP Garbage Collection** カウンターについて説明します。  
  
|カウンター|説明|  
|-------------|-----------------|  
|**Dusty corner scan retries/sec (GC-issued)**|ガベージ コレクターによって発行されたダスティ コーナー スウィープ (詳細なクリーンアップ) の実行中に、書き込みの競合が原因で発生したスキャン再試行回数に関する 1 秒あたりの平均です。 これは非常に低レベルのカウンターであり、お客様による使用は想定されていません。|  
|**Main GC work items/sec**|メイン GC スレッドで処理された作業項目の数です。|  
|**Parallel GC work item/sec**|並列スレッドが GC 作業項目を実行した回数です。|  
|**Rows processed/sec**|ガベージ コレクターにより 1 秒間に処理された (平均) 行数です。|  
|**Rows processed/sec (first in bucket and removed)**|ガベージ コレクターによって処理され、最初は対応するハッシュ バケット内に存在し、直ちに削除することができた行の数に関する 1 秒あたりの平均です。|  
|**Rows processed/sec (first in bucket)**|ガベージ コレクターによって処理され、最初は対応するハッシュ バケット内に存在していた行の数に関する 1 秒あたりの平均です。|  
|**Rows processed/sec (marked for unlink)**|ガベージ コレクターによって処理され、既にリンク解除のマークが付いていた行の数に関する 1 秒あたりの平均です。|  
|**Rows processed/sec (no sweep needed)**|ガベージ コレクターによって処理され、ダスティ コーナー スウィープ (詳細なクリーンアップ) を必要としない行の数に関する 1 秒あたりの平均です。|  
|**Sweep expired rows removed/sec**|ダスティ コーナー スウィープの実行中に削除された、有効期限切れの行の数に関する 1 秒あたりの平均です。|  
|**Sweep expired rows touched/sec**|ダスティ コーナー スウィープの実行中に操作された、有効期限切れの行の数に関する 1 秒あたりの平均です。|  
|**Sweep expiring rows touched/sec**|ダスティ コーナー スウィープの実行中に操作された、間もなく期限切れになる行の数に関する 1 秒あたりの平均です。|  
|**Sweep rows touched/sec**|ダスティ コーナー スウィープの実行中に操作された行の数に関する 1 秒あたりの平均です。|  
|**Sweep scans started/sec**|ダスティ コーナー スウィープ スキャンが開始された回数に関する 1 秒あたりの平均。|  
  
## <a name="see-also"></a>参照  
 [SQL Server XTP &#40;インメモリ OLTP&#41; パフォーマンス カウンター](../../relational-databases/performance-monitor/sql-server-xtp-in-memory-oltp-performance-counters.md)  
  
  
