---
title: ストアド プロシージャから tempdb テーブルを作成およびアクセスする
description: TempDB によって、ネイティブ コンパイル ストアド プロシージャからのテーブルの作成およびアクセスはサポートされていません。 メモリ最適化テーブル、またはテーブル型とテーブル変数を使用してください。
ms.custom: seo-dt-2019
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: 12be8011-b76c-45c1-8f55-7f46e0e374e9
author: rothja
ms.author: jroth
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 55987ab8038eb1a7703e7cbf25abd894a35fb9c1
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107489650"
---
# <a name="create-and-access-tables-in-tempdb-from-stored-procedures"></a>ストアド プロシージャから TempDB 内のテーブルを作成およびアクセスする
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  ネイティブ コンパイル ストアド プロシージャからの TempDB 内のテーブルの作成およびアクセスはサポートされていません。 代わりに、DURABILITY=SCHEMA_ONLY のメモリ最適化テーブルまたはテーブルの種類とテーブル変数を使用します。 

一時テーブルおよびテーブル変数のメモリを最適化するシナリオについては、「[メモリ最適化を使用した一時テーブルとテーブル変数の高速化](../../relational-databases/in-memory-oltp/faster-temp-table-and-table-variable-by-using-memory-optimization.md)」を参照してください。
  
  次の例では、3 つの列 (ID、ProductID、Quantity) がある temp テーブルの使用を、**dbo.OrderQuantityByProduct** 型のテーブル変数 **\@OrderQuantityByProduct** を使用して置き換える方法を示します。  
  
```sql  
CREATE TYPE dbo.OrderQuantityByProduct   
  AS TABLE   
   (id INT NOT NULL PRIMARY KEY NONCLUSTERED HASH WITH (BUCKET_COUNT=100000),   
    ProductID INT NOT NULL,   
    Quantity INT NOT NULL) WITH (MEMORY_OPTIMIZED=ON)  
GO  
CREATE PROCEDURE dbo.usp_OrderQuantityByProduct   
WITH NATIVE_COMPILATION, SCHEMABINDING, EXECUTE AS OWNER  
AS BEGIN ATOMIC WITH   
(  
    TRANSACTION ISOLATION LEVEL = SNAPSHOT,  
    LANGUAGE = N'ENGLISH'  
)  
  -- declare table variables for the list of orders   
  DECLARE @OrderQuantityByProduct dbo.OrderQuantityByProduct  
  
  -- populate input  
  INSERT @OrderQuantityByProduct SELECT ProductID, Quantity FROM dbo.[Order Details]  
  end  
```  
  
## <a name="see-also"></a>参照  
 [ネイティブ コンパイル ストアド プロシージャの移行に関する問題](./a-guide-to-query-processing-for-memory-optimized-tables.md)   
 [インメモリ OLTP でサポートされていない Transact-SQL の構造](../../relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp.md)  
  
