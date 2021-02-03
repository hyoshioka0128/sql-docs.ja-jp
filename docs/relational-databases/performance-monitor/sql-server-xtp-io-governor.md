---
title: SQL Server XTP IO ガバナー | Microsoft Docs
description: インメモリ OLTP IO レート ガバナーに関連するカウンターが含まれる、SQL Server XTP IO ガバナーのパフォーマンス オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
ms.assetid: 91e176fe-c838-44e9-b4fc-2814a0551ca3
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c8a18e0d6466e6792dee8395fbbde741f3094696
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505486"
---
# <a name="sql-server-xtp-io-governor"></a>SQL Server XTP IO ガバナー
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

SQL Server XTP IO ガバナーのパフォーマンス オブジェクトには、インメモリ OLTP IO レート ガバナーに関連するカウンターが含まれています。

次の表で、 **SQL Server XTP IO ガバナー** カウンターについて説明します。

|カウンター|説明|  
|-------------|-----------------|  
|**1 秒あたりの不十分なクレジット待機数**|レート オブジェクトのクレジット不足に起因する待機数 (1 秒あたり)。|
|**1 秒あたりに発行された Io 数**|フラッシュ スレッドにより発行された Io 数 (1 秒あたり)。|
|**1 秒あたりのログ ブロック数**|コントローラーによって処理されたログ ブロック数 (1 秒あたり)。|
|**実行されなかったクレジット スロット数**|レート オブジェクトからのクレジットを待ったために実行されなかったクレジット スロット数。|
|**1 秒あたりの古いレート オブジェクト待機数**|古いレート オブジェクトに起因する待機数 (1 秒あたり)。|
|**公開されたレート オブジェクトの合計**|公開されたレート オブジェクトの合計数。|
 

## <a name="see-also"></a>参照  
[SQL Server XTP &#40;インメモリ OLTP&#41; パフォーマンス カウンター](../../relational-databases/performance-monitor/sql-server-xtp-in-memory-oltp-performance-counters.md)
