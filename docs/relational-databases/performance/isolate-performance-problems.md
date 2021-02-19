---
title: パフォーマンスの問題の特定 | Microsoft Docs
description: SQL Server プロファイラーとシステム モニターを使用し、Transact-SQL、アプリケーション関連の問題、ハードウェア、システム関連の問題を監視し、トラブルシューティングします。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- isolating performance problems [SQL Server]
- monitoring performance [SQL Server], isolating problems
- database monitoring [SQL Server], isolating problems
- tuning databases [SQL Server], isolating problems
- monitoring server performance [SQL Server], isolating problems
- database performance [SQL Server], isolating problems
- server performance [SQL Server], isolating problems
ms.assetid: 2eb425cb-9166-4027-ae08-c8fc2d236f44
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: ed6728b164ab0d8562965935ad869c767a8a9961
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97460325"
---
# <a name="isolate-performance-problems"></a>パフォーマンスの問題の特定
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  一般に、[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または Microsoft Windows の複数のツールを併用した方が、ツールを 1 つずつ使用するよりも、データベースのパフォーマンスに関する問題を効率よく特定できます。 たとえば、グラフィカル実行プラン機能 (プラン表示) を使用すると、1 つのクエリ内のデッドロックをすばやく特定できます。 さらに、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の監視機能と Windows の監視機能を同時に使用すると、その他のパフォーマンス問題をより簡単に特定できます。  
  
 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] を使用すると、Transact-SQL とアプリケーションに関連する問題を監視して、問題点を突き止めることができます。 システム モニターを使用すると、ハードウェアとその他のシステムに関連する問題を監視できます。  
  
 次の要素を監視することによって、問題のトラブルシューティングを行うことができます。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ストアド プロシージャまたは [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントのバッチ (ユーザー アプリケーションから送信されたもの)。  
  
-   ブロッキング ロックまたはデッドロックなどのユーザーの利用状況  
  
-   ディスクの使用量などのハードウェア利用状況  
  
 問題としては、次のようなものがあります。  
  
-   不適切に記述された [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントに関係するアプリケーション開発エラー  
  
-   ディスクまたはネットワークに関連するエラーなどのハードウェア エラー  
  
-   不適切に設計されたデータベースによる過剰なブロッキング  
  
## <a name="tools-for-common-performance-problems"></a>パフォーマンスの一般的な問題を解決するツール  
 各ツールでパフォーマンス問題を監視または調整する場合は、問題を慎重に選択することも重要になります。 ツールとユーティリティは、解決するパフォーマンス問題の種類によって異なります。  
  
 監視および調整ツールの種類と特定可能な問題については、次のトピックで説明します。  
  
 [ボトルネックの特定](../../relational-databases/performance/identify-bottlenecks.md)  
  
 [メモリ使用率の監視](../../relational-databases/performance-monitor/monitor-memory-usage.md)  
  
  
