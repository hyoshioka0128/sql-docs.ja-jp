---
title: SQL Server:SQL Errors オブジェクト | Microsoft Docs
description: SQL Server で SQL エラーを監視するためのカウンターを提供する SQLServer:SQL Errors オブジェクトについて説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- SQL Errors object
- SQLServer:SQL Errors
ms.assetid: 6e5273ca-29cb-4618-88a2-70b9b8d6cf76
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: e0433802954301c9ea5a9e483c0e54ca893f6b2d
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96505583"
---
# <a name="sql-server-sql-errors-object"></a>SQL Server:SQL Errors オブジェクト
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **SQLServer:SQL Errors** オブジェクトには、 **SQL Errors** を監視するカウンターが用意されています。  
  
 次の表では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **SQL Errors** カウンターについて説明します。  
  
|SQL Server SQL Errors のカウンター|説明|  
|------------------------------------|-----------------|  
|**Errors/sec**|1 秒あたりのエラー数|  
  
 オブジェクトの各カウンターには、次のインスタンスが含まれています。  
  
|Item|定義|  
|----------|----------------|  
|**_Total**|すべてのエラーに関する情報。|  
|**DB Offline Errors**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で現在のデータベースがオフラインになる原因となる重大なエラーを追跡します。|  
|**Info Errors**|問題を起こしているわけではない、ユーザーに情報提供を行っているエラーメッセージの情報。|  
|**Kill Connection Errors**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の現在の接続を強制的に切断する原因となる重大なエラーを追跡します。|  
|**User Errors**|ユーザー エラーに関する情報。|  
  
## <a name="see-also"></a>参照  
 [リソースの利用状況の監視 &#40;システム モニター&#41;](../../relational-databases/performance-monitor/monitor-resource-usage-system-monitor.md)  
  
  
