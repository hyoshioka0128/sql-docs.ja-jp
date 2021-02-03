---
title: デッドロック ファイルを開く、表示、印刷する (SSMS)
description: SQL Server プロファイラーによって生成されるデッドロック情報をキャプチャし、それを SQL Server Management Studio で表示する方法について説明します。
ms.custom: seo-dt-2019
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- deadlocks [SQL Server], printing files
- deadlocks [SQL Server], opening files
- opening deadlock files
- printing deadlock files
ms.assetid: 5061b13f-2cb7-457a-b8d0-fbd437b510ab
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: f06fbda4382042ffa51afa0eaa14eb18175874f7
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97483103"
---
# <a name="open-view-and-print-a-deadlock-file-in-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) でデッドロック ファイルを開く、表示、印刷する

[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] でデッドロックが発生したら、デッドロック情報をキャプチャしてファイルに保存できます。 保存したデッドロック ファイルは、[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] で開いて表示または印刷できます。  
  
## <a name="open-and-view-a-deadlock-file"></a>デッドロック ファイルを開いて表示する  
  
1. [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] で **[ファイル]** メニューの **[開く]** をポイントし、 **[ファイル]** を選択します。  
  
2. **[ファイルを開く]** ダイアログ ボックスで、 **[ファイルの種類]** ボックスの一覧から [SQL デッドロック ファイル] を選択します。 これにより、デッドロック ファイルだけが一覧表示されます。  
  
## <a name="print-a-deadlock-file"></a>デッドロック ファイルを印刷する  
  
1. [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] で **[ファイル]** メニューの **[開く]** をポイントし、 **[ファイル]** を選択します。  
  
2. **[ファイルを開く]** ダイアログ ボックスで、 **[ファイルの種類]** ボックスの一覧から [SQL デッドロック ファイル] を選択します。 これにより、デッドロック ファイルだけが一覧表示されます。  
  
3. 印刷するデッドロック ファイルを選択して、 **[開く]** を選択します。  
  
4. **[ファイル]** メニューの **[印刷]** を選択します。  
  
## <a name="see-also"></a>関連項目  
 [Deadlock Graph の保存 (SQL Server Profiler)](../../relational-databases/performance/save-deadlock-graphs-sql-server-profiler.md)  
  
  
