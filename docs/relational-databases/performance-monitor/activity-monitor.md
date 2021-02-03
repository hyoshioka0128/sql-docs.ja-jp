---
title: 利用状況モニター | Microsoft Docs
description: 利用状況モニターを使用し、SQL Server プロセスに関する情報や、それらのプロセスが SQL Server の現在のインスタンスに影響を与える様子を表示する方法について説明します。
ms.custom: ''
ms.date: 04/07/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- Activity Monitor [SQL Server]
ms.assetid: 1e6c430d-3a2a-468e-a3d5-ef5459c36c15
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 747cad801170aa9ef2e4907e96dde1b20ddc268e
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96506050"
---
# <a name="activity-monitor"></a>利用状況モニター
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
利用状況モニターには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] プロセスおよびこれらのプロセスが現在の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスに与える影響に関する情報が表示されます。  
  
利用状況モニターは、 **[概要]** 、 **[プロセス]** 、 **[リソースの待機]** 、 **[データ ファイル I/O]** 、 **[最近コストの高いクエリ]** 、および **[アクティブなコストの高いクエリ]** の展開と折りたたみが可能なペインを含むタブ付きドキュメント ウィンドウです。 ペインを展開すると、利用状況モニターによってインスタンスに対して情報のクエリが実行されます。 ペインを折りたたむと、そのペインのすべての利用状況クエリが停止します。 1 つ以上のペインを同時に展開し、インスタンスのさまざまな利用状況を表示することができます。  
 
## <a name="customize-columns"></a>列をカスタマイズする 
**[プロセス]** 、 **[リソースの待機]** 、 **[データ ファイル I/O]** 、 **[最近コストの高いクエリ]** 、および **[アクティブなコストの高いクエリ]** ペインに含まれている列では、次のように表示をカスタマイズします。  
  
1.  列の順序を並べ替えるには、列見出しをクリックして見出しのリボン内の別の場所にドラッグします。  
  
2.  列を並べ替えるには、列名をクリックします。  
  
3.  1 つ以上の列をフィルター処理するには、列見出しの下矢印をクリックして値を選択します。  

## <a name="more-information"></a>詳細情報  
   
|説明|トピック|  
|-|-|  
|利用状況モニターを開く方法、および利用状況モニターの更新間隔の設定方法について説明します。|[利用状況モニターを開く方法 &#40;SQL Server Management Studio&#41;](../../relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio.md)|  
|サーバー パフォーマンスおよびアクティビティの監視に関するトピックを紹介します。|[サーバーのパフォーマンスと利用状況の監視](../../relational-databases/performance/server-performance-and-activity-monitoring.md)|  
  
  
