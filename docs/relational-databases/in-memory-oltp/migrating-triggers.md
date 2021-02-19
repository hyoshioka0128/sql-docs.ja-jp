---
title: トリガーの移行 | Microsoft Docs
description: SQL Server インスタンスの CREATE、ALTER、DROP、GRANT、DENY、REVOKE、または UPDATE STATISTICS に対して起動されるメモリ最適化テーブルと DDL トリガーについて説明します。
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: ad5385c5-5a50-40ca-a319-97d5606b8511
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 4147c69de6eb360820ed7bd47c7ee7c8c867d52b
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97465363"
---
# <a name="migrating-triggers"></a>トリガーの移行
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  このトピックでは、DDL トリガーとメモリ最適化テーブルについて説明します。  
  
 DML トリガーはメモリ最適化テーブルでサポートされていますが、FOR または AFTER トリガー イベントを使用する場合に限られます。 例については「 [FROM またはサブクエリを使用した UPDATE を実装する](../../relational-databases/in-memory-oltp/implementing-update-with-from-or-subqueries.md)」を参照して下さい。 
  
 LOGON トリガーとは、LOGON イベントで起動するように定義されたトリガーです。 LOGON トリガーは、メモリ最適化テーブルに影響しません。  
  
## <a name="ddl-triggers"></a>DDL トリガー  
 DDL トリガーとは、自らを定義しているデータベースまたはサーバーに対して CREATE、ALTER、DROP、GRANT、DENY、REVOKE、または UPDATE STATISTICS の各ステートメントを実行したときに起動するように定義されているトリガーです。  
  
 データベースまたはサーバーで、CREATE_TABLE イベントまたはこのイベントを含むイベント グループに対応する 1 つ以上の DDL トリガーが定義されている場合は、メモリ最適化テーブルを作成できません。 データベースまたはサーバーで、DROP_TABLE イベントまたはこのイベントを含むイベント グループに対応する 1 つ以上の DDL トリガーが定義されている場合は、メモリ最適化テーブルを削除できません。  
  
 CREATE_PROCEDURE、DROP_PROCEDURE、またはこれらのイベントを含むイベント グループに対応する 1 つ以上の DDL トリガーが存在している場合は、ネイティブ コンパイル ストアド プロシージャを作成できません。  
  
## <a name="see-also"></a>参照  
 [インメモリ OLTP への移行](./plan-your-adoption-of-in-memory-oltp-features-in-sql-server.md)  
  
