---
title: SQL Server Profiler - パフォーマンス カウンター制限 |Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- database-engine
ms.topic: conceptual
f1_keywords:
- sql12.pro.performancecounterlimit.f1
helpviewer_keywords:
- Performance Counters List dialog box
ms.assetid: d10140ef-36c4-449c-b365-1cff1b2524e4
author: mashamsft
ms.author: mathoma
manager: craigg
ms.openlocfilehash: 74ac3839567366c429c64e0139f8e86793aa942a
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48165222"
---
# <a name="sql-server-profiler---performance-counters-limit"></a>[SQL Server Profiler] - [パフォーマンス カウンター制限ダイアログ]
  [パフォーマンス カウンター制限ダイアログ] ダイアログ ボックスを使用すると、システム モニターのパフォーマンス ログ ファイルと [!INCLUDE[ssSqlProfiler](../includes/sssqlprofiler-md.md)] トレースを相関させるときに、ファイルからの情報を制限できます。 このダイアログ ボックスで、相関のために表示して使用するカウンターを選択できます。  
  
 **[パフォーマンス カウンター制限ダイアログ ボックス]** ダイアログ ボックスには、パフォーマンス ログ ファイルに含まれているパフォーマンス オブジェクトおよびカウンターによって値が入力されます。  
  
### <a name="to-select-performance-objects-and-counters-to-correlate-with-a-trace"></a>トレースと相関させるパフォーマンス オブジェクトおよびカウンターを選択するには  
  
1.  パフォーマンス オブジェクトを展開して、パフォーマンス ログ ファイルに含まれているカウンターを確認します。  
  
2.  [!INCLUDE[ssSqlProfiler](../includes/sssqlprofiler-md.md)] トレース ファイルと相関させる対象カウンターをオンにします。  
  
     パフォーマンス オブジェクトに対してすべてのカウンターを選択する場合は、パフォーマンス オブジェクトの横にあるボックスをオンにします。 コンピューターを示している最上位ノードをオンにすると、パフォーマンス ログ ファイル内のすべてのパフォーマンス オブジェクトおよびカウンターが選択されます。  
  
## <a name="see-also"></a>参照  
 [トレースと Windows パフォーマンス ログ データの関連付け &#40;SQL Server Profiler&#41;](../tools/sql-server-profiler/correlate-a-trace-with-windows-performance-log-data.md)  
  
  
