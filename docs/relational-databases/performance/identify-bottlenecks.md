---
title: ボトルネックの特定 | Microsoft Docs
description: SQL Server で不足しているリソース、正しく機能していないリソース、間違って構成されているリソースなど、ボトルネックの原因について説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- resource bottlenecks [SQL Server]
- database monitoring [SQL Server], bottlenecks
- performance [SQL Server], bottlenecks
- tuning databases [SQL Server], bottlenecks
- monitoring server performance [SQL Server], bottlenecks
- monitoring performance [SQL Server], bottlenecks
- database performance [SQL Server], bottlenecks
- server performance [SQL Server], bottlenecks
- bottlenecks [SQL Server]
- identifying bottlenecks [SQL Server]
ms.assetid: db079e65-ee80-4105-aec9-f8230d0d6635
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 66a29131862d0b7cb3972fd8a52b5fa21ab79ef3
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97463273"
---
# <a name="identify-bottlenecks"></a>ボトルネックの特定
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  共有リソースへの同時アクセスは、ボトルネックの原因になります。 一般に、ボトルネックはあらゆるソフトウェア システムに存在し、避けられないものです。 ただし、共有リソースに対する過剰な要求により応答時間が遅くなる場合は、これを特定してチューニングする必要があります。  
  
 ボトルネックが発生する原因には、次のようなものがあります。  
  
-   リソースが不十分なため、コンポーネントの追加やアップグレードが必要な場合。  
  
-   同じ種類のリソースで負荷が均等に分散されていない (たとえば、1 台のディスクが占有されている) 場合。  
  
-   リソースが誤動作する場合。  
  
-   リソースが不適切に構成されている場合。  
  
## <a name="analyzing-bottlenecks"></a>ボトルネックの分析  
 さまざまなイベントに対して過剰な要求が行われている期間は、ボトルネックをチューニングできる指標になります。  
  
 次に例を示します。  
  
-   他のいくつかのコンポーネントにより、このコンポーネントの負荷が低下しているため、負荷を完了するまでの時間がかかっている可能性があります。  
  
-   ネットワークの混雑により、クライアントからの要求に時間がかかっている可能性があります。  
  
 サーバーのパフォーマンスを追跡し、ボトルネックを特定するときに監視する、5 つの主な要素を次に示します。  
  
|ボトルネックになる可能性のある要素|サーバーへの影響|  
|------------------------------|---------------------------|  
|メモリ使用量|Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に割り当てられたメモリまたは使用できるメモリが不足していると、パフォーマンスが低下します。 データをデータ キャッシュから直接読み取るのではなく、ディスクから読み取る必要があります。 Microsoft Windows オペレーティング システムでは、ページが必要になるたびに、ディスクとの間でデータを交換するので、過剰なページングが実行されます。|  
|CPU 使用率|CPU の使用率が一様に高い場合は、 [!INCLUDE[tsql](../../includes/tsql-md.md)] クエリをチューニングするか、CPU のアップグレードが必要であることを示します。|  
|ディスクの入力/出力 (I/O)|[!INCLUDE[tsql](../../includes/tsql-md.md)] クエリについて、インデックスを使用するなどしてチューニングし、不要な I/O を減らすことができます。|  
|ユーザー接続数|サーバーに同時にアクセスしているユーザー数が多すぎるとパフォーマンスが低下します。|  
|ブロッキング ロック|アプリケーションが適切にデザインされてないために、コンカレンシーがロックされて妨害され、応答時間が長くなり、トランザクションのスループット率が低下します。|  
  
## <a name="see-also"></a>参照  
 [CPU 使用率の監視](../../relational-databases/performance-monitor/monitor-cpu-usage.md)   
 [ディスクの使用量の監視](../../relational-databases/performance-monitor/monitor-disk-usage.md)   
 [メモリ使用率の監視](../../relational-databases/performance-monitor/monitor-memory-usage.md)   
 [SQL Server: General Statistics オブジェクト](../../relational-databases/performance-monitor/sql-server-general-statistics-object.md)   
 [SQL Server の Locks オブジェクト](../../relational-databases/performance-monitor/sql-server-locks-object.md)  
  
  
