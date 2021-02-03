---
title: SQL Server, クエリ ストア オブジェクト | Microsoft Docs
description: SQL Server のリソース使用率を監視し、クエリ テキスト、実行プラン、ランタイム統計を格納するためのカウンターを提供する Query Store オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/17/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- Query Store object
- SQL Server:Query Store
ms.assetid: b4a04acd-0b66-44a5-b72d-1a45b49e13e6
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 57892eac5224bb3b90f490644c3c49e1317b1a78
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505610"
---
# <a name="sql-server-query-store-object"></a>SQL Server, クエリ ストア オブジェクト

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

クエリ ストア オブジェクトは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のリソース使用率を監視するカウンターを提供し、クエリ テキスト、オブジェクト (ストアド プロシージャなど) の実行プランとランタイム統計情報、アドホックおよび準備された [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメント、トリガーを格納します。  
  
次の表では、**SQLServer:Query Store** カウンターについて説明します。  
  
|SQL Server のクエリ ストア カウンター|説明|  
|-------------------------------------|-----------------|  
|**Query Store CPU usage (クエリ ストアの CPU 使用率)**|クエリ ストアの CPU の使用率が、他のプロセスによる CPU の使用率のパーセンタイルとして示されます。|  
|**Query Store logical reads (クエリ ストアの論理読み取り数)**|クエリ ストアによって行われた論理読み取りの数を示します。|  
|**Query Store logical writes (クエリ ストアの論理書き込み数)**|クエリ ストアからフラッシュするためにキューに登録されているデータの量を示します。 キューへのアイテムの追加の頻度と遅延 (ランタイム統計情報を表します) は、データ フラッシュ間隔の設定によって制御されます。|  
|**Query Store physical reads (クエリ ストアの物理読み取り数)**|クエリ ストアによって行われた物理読み取りの数を示します。|  
  
 オブジェクトの各カウンターには、次のインスタンスが含まれています。  
  
|クエリ ストアのインスタンス|説明|  
|--------------------------|-----------------|  
|**_Total**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のこのインスタンスのクエリ ストアの情報です。|  
|\<database name>|このデータベースのクエリ ストアの情報です。|  
  
## <a name="see-also"></a>参照  

- [クエリのストアを使用した、パフォーマンスの監視](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)
- [クエリ ストアのストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/query-store-stored-procedures-transact-sql.md)
- [クエリ ストアのカタログ ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/query-store-catalog-views-transact-sql.md)
- [リソースの利用状況の監視 &#40;システム モニター&#41;](../../relational-databases/performance-monitor/monitor-resource-usage-system-monitor.md)  
