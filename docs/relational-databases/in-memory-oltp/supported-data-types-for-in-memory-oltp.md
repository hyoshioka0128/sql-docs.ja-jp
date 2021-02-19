---
title: インメモリ OLTP に対してサポートされるデータ型 | Microsoft Docs
description: インメモリ OLTP の機能であるメモリ最適化テーブルとネイティブ コンパイル T-SQL モジュールでサポートされていないデータ型について説明します。
ms.custom: ''
ms.date: 06/19/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: a7380ef0-c9d7-49e4-b6de-fad34752b9f3
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: cdbcd80b0cb369607dc7b38cc524f53fdaed3a79
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485154"
---
# <a name="supported-data-types-for-in-memory-oltp"></a>インメモリ OLTP に対してサポートされるデータ型
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  この記事では、次のインメモリ OLTP 機能でサポートされていないデータ型の一覧を示します。  
  
-   メモリ最適化テーブル  
  
-   ネイティブ コンパイル T-SQL モジュール  
  
## <a name="unsupported-data-types"></a>サポートされていないデータ型  
 次のデータ型はサポートされません。  

:::row:::
    :::column:::
        [datetimeoffset &#40;Transact-SQL&#41;](../../t-sql/data-types/datetimeoffset-transact-sql.md)

        [hierarchyid &#40;Transact-SQL&#41;](../../t-sql/data-types/hierarchyid-data-type-method-reference.md)

        [sql_variant &#40;Transact-SQL&#41;](../../t-sql/data-types/sql-variant-transact-sql.md)
    :::column-end:::
    :::column:::
        [geography &#40;Transact-SQL&#41;](../../t-sql/spatial-geography/spatial-types-geography.md)

        [rowversion &#40;Transact-SQL&#41;](../../t-sql/data-types/rowversion-transact-sql.md)

        ユーザー定義型
    :::column-end:::
    :::column:::
        [geometry &#40;Transact-SQL&#41;](../../t-sql/spatial-geometry/spatial-types-geometry-transact-sql.md)

        [xml &#40;Transact-SQL&#41;](../../t-sql/xml/xml-transact-sql.md)
    :::column-end:::
:::row-end:::
  
## <a name="notable-supported-data-types"></a>サポートされる重要なデータ型  
 インメモリ OLTP の機能では、ほとんどのデータ型がサポートされています。 次のデータ型は特に重要です。  
  
|文字列型とバイナリ型|詳細情報|  
|-----------------------------|--------------------------|  
|binary と varbinary*|[binary と varbinary &#40;Transact-SQL&#41;](../../t-sql/data-types/binary-and-varbinary-transact-sql.md)|  
|char と varchar*|[char および varchar &#40;Transact-SQL&#41;](../../t-sql/data-types/char-and-varchar-transact-sql.md)|  
|nchar と nvarchar*|[nchar および nvarchar &#40;Transact-SQL&#41;](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)|  
  
上記の文字列データ型とバイナリ データ型については、SQL Server 2016 以降は次のようになります。  
  
- 個々のメモリ最適化テーブルには、それらの長さが 8,060 バイトの物理行サイズを超えることがあっても、 `nvarchar(4000)`などの複数の長い列が含まれている場合があります。  
  
- メモリ最適化テーブルには、 `varchar(max)`などのデータ型の最大長の文字列やバイナリ列がある場合があります。  


### <a name="identify-lobs-and-other-columns-that-are-off-row"></a>行外の LOB 列およびその他の列の特定

SQL Server 2016 以降では、メモリ最適化テーブルが[行外の列をサポートする](../../relational-databases/in-memory-oltp/table-and-row-size-in-memory-optimized-tables.md)ため、1 つのテーブルの行を 8060 バイトよりも大きくすることができます。 次の Transact-SQL SELECT ステートメントにより、メモリ最適化テーブルの行外のすべての列のレポートが生成されます。 以下の点に注意してください。

- インデックス キー列はすべて行内に格納されます。
  - メモリ最適化テーブルでは、一意ではないインデックス キーが、NULL 値を許容する列を含むことができるようになりました。
  - メモリ最適化テーブルでは、インデックスを UNIQUE として宣言できます。
- すべての LOB 列は行外に格納されます。
- max_length の -1 は、ラージ オブジェクト (LOB) 列を表します。


```sql
SELECT
        OBJECT_NAME(m.object_id) as [table],
        c.name                   as [column],
        c.max_length
    FROM
             sys.memory_optimized_tables_internal_attributes AS m
        JOIN sys.columns                                     AS c
                ON  m.object_id = c.object_id
                AND m.minor_id  = c.column_id
    WHERE
        m.type = 5;
```


### <a name="other-data-types"></a>その他のデータ型


|その他の型|詳細情報|  
|-----------------|--------------------------|  
|テーブル型|[メモリ最適化テーブル変数](../../relational-databases/in-memory-oltp/faster-temp-table-and-table-variable-by-using-memory-optimization.md)|  
  
## <a name="see-also"></a>参照  
 [Transact-SQL によるインメモリ OLTP のサポート](../../relational-databases/in-memory-oltp/transact-sql-support-for-in-memory-oltp.md)   
 [メモリ最適化テーブルへの SQL_VARIANT の実装](../../relational-databases/in-memory-oltp/implementing-sql-variant-in-a-memory-optimized-table.md)  
 [メモリ最適化テーブルのテーブルと行のサイズ](../../relational-databases/in-memory-oltp/table-and-row-size-in-memory-optimized-tables.md)  
  
  
