---
title: SQL Server:HTTP_STORAGE_OBJECT | Microsoft Docs
description: Azure Storage アカウントを監視する各種のパフォーマンス カウンターで構成される SQLServer:HTTP Storage パフォーマンス オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
ms.assetid: ae849f79-c581-42a5-a5cc-0a9ebea171b9
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 6ca729c6097b02142e4459a0c11e5ad77741ea37
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505711"
---
# <a name="sql-server-http-storage"></a>SQL Server、HTTP ストレージ
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  **SQLServer:HTTP Storage** パフォーマンス オブジェクトは、Microsoft Azure Storage アカウントを監視する各種のパフォーマンス カウンターで構成されています。 [Microsoft Azure 内の SQL Server データ ファイル](../../relational-databases/databases/sql-server-data-files-in-microsoft-azure.md)機能を使用すると、データベース ファイルを Azure Storage Blob に格納できます。 このパフォーマンス オブジェクトでは、各 Azure ストレージ アカウントが別々のドライブとして処理されます。  
  
|カウンター名|説明|  
|------------------|-----------------|  
|**平均Bytes/Read**|読み取りごとに HTTP ストレージから転送された平均バイト数。|  
|**平均Bytes/Transfer**|読み取りまたは書き込み操作中に HTTP ストレージから転送された平均バイト数。|  
|**平均Bytes/Write**|書き込みごとに HTTP ストレージから転送された平均バイト数。|  
|**Avg. microsec/Read**|HTTP ストレージからの読み取りごとに要する平均マイクロ秒数。|  
|**Avg. microsec/Read Comp**|HTTP がストレージへの読み取り完了にかかる平均マイクロ秒数。| 
|**Avg. microsec/Transfer**|HTTP ストレージへの転送ごとに要する平均マイクロ秒数。|  
|**Avg. microsec/Write**|HTTP ストレージへの書き込みごとに要する平均マイクロ秒数。|  
|**Avg. microsec/Write Comp**|HTTP がストレージへの書き込み完了にかかる平均マイクロ秒数。|  
|**HTTP Storage IO failed/sec**|HTTP ストレージに送信されて失敗した書き込み要求の 1 秒あたりの数。| 
|**HTTP Storage I/O retry/sec**|HTTP ストレージに送信された 1 秒あたりの再試行要求数。|  
|**Outstanding HTTP Storage I/O**|HTTP ストレージに対する未処理 I/O の合計数。|  
|**Read Bytes/sec**|読み取り操作中に HTTP ストレージから転送されている 1 秒あたりのデータ量。|  
|**Reads/sec**|HTTP ストレージでの 1 秒あたりの読み取り数。|  
|**Total Bytes/sec**|読み取りまたは書き込み操作中に HTTP ストレージから転送されている 1 秒あたりのデータ量。|  
|**Transfers/sec**|HTTP ストレージでの 1 秒あたりの読み取りおよび書き込み操作数。|  
|**Write Bytes/sec**|書き込み操作中に HTTP ストレージから転送されている 1 秒あたりのデータ量。|  
|**Writes/sec**|HTTP ストレージでの 1 秒あたりの書き込み数。|  
  
## <a name="see-also"></a>参照  
 [リソースの利用状況の監視 &#40;システム モニター&#41;](../../relational-databases/performance-monitor/monitor-resource-usage-system-monitor.md)  
  
  
