---
title: メモリ内 OUTER JOIN | Microsoft Docs
description: LEFT と RIGHT OUTER JOIN について説明します。 ネイティブ コンパイル T-SQL モジュールでは、SQL Server の LEFT および RIGHT OUTER JOIN がサポートされています。
ms.custom: ''
ms.date: 06/01/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: 67084043-6b23-4975-b9db-6e49923d4bab
author: rothja
ms.author: jroth
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 494f8464e84b3f85bffa6f8ff3443286f2712ef2
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107489635"
---
# <a name="implementing-an-outer-join"></a>外部結合の実装

[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  LEFT OUTER JOIN と RIGHT OUTER JOIN は、 [!INCLUDE[sssql16-md](../../includes/sssql16-md.md)]以降のネイティブ コンパイル T-SQL モジュールでサポートされています。  
  
OUTER JOIN の詳細については、[FROM 句と JOIN、APPLY、PIVOT](../../t-sql/queries/from-transact-sql.md)に関するページを参照してください。
