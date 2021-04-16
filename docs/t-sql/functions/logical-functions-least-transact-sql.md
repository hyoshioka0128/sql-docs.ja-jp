---
description: 論理関数 - LEAST (Transact-SQL)
title: LEAST (Transact-SQL)
ms.custom: ''
ms.date: 04/14/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.technology: t-sql
ms.topic: reference
f1_keywords:
- LEAST
- LEAST_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- LEAST function
author: jmsteen
ms.author: josteen
ms.reviewer: wiassaf
ms.openlocfilehash: 88d54f6ebc98712aa02ce29ff9b2922629a95f10
ms.sourcegitcommit: 233be9adaee3d19b946ce15cfcb2323e6e178170
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107560798"
---
# <a name="logical-functions---least-transact-sql"></a>論理関数 - LEAST (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-svrless-poolonly](../../includes/applies-to-version/sql-asdb-asdbmi-asa-svrless-poolonly.md)]

 この関数は、1 つまたは複数の式のリストから最小値を返します。 

 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
LEAST ( expression1 [ ,...expressionN ] ) 
```  

## <a name="arguments"></a>引数
 *expression1, expressionN*  
 比較可能なデータ型のコンマ区切りの式のリスト。  `LEAST`  関数には、少なくとも 1 つの引数が必要です。また、254 個を超える引数はサポートされていません。  
 
 それぞれの式は、定数、変数、列名または関数、そして算術演算子、ビット演算子、文字列演算子の組み合わせにすることができます。 集計関数とスカラーのサブクエリを使用することができます。  
  
## <a name="return-types"></a>戻り値の型  
 関数に渡される一連の型の中から最も優先順位の高いデータ型を返します。 詳細については、「[データ型の優先順位 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)」を参照してください。  

 すべての引数のデータ型が同じで、その型の比較がサポートされている場合、 `LEAST` はその型を返します。 

 それ以外の場合、関数は、比較の前に、すべての引数を最も優先順位の高いデータ型に暗黙的に変換し、この型を戻り値の型として使用します。 

 数値型の場合、戻り値の型のスケールは、最も優先順位の高い引数と同じになります。または、最も優先順位の高いデータ型の引数が複数ある場合は、最大のスケールになります。
  
## <a name="remarks"></a>解説  
 引数リスト内のすべての式は、比較可能なデータ型である必要があり、最も優先順位の高い引数のデータ型に暗黙的に変換できます。 

 すべての引数の、最も優先順位の高いデータ型への暗黙的な変換は、比較の前に行われます。 

 引数間の暗黙的な型変換がサポートされていない場合、関数は失敗し、エラーが返されます。 

 明示的な、および暗黙的な変換について詳しくは、「[データ型の変換 &#40;データベース エンジン&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)」を参照してください。 

 1 つ以上の引数が `NULL` でない場合、比較時に `NULL` 引数が無視されます。 すべての引数が `NULL` である場合、`LEAST` は `NULL` を返します。 

 文字引数の比較は、「[照合順序の優先順位 &#40;Transact-SQL&#41;](../../t-sql/statements/collation-precedence-transact-sql.md)」の規則に従います。 

 次の型は `LEAST` の比較ではサポートされて **いません**: **varchar(max)、varbinary(max) または 8,000 バイトを超える nvarchar(max)、cursor、geometry、geography、image、バイト順序が指定されていないユーザー定義型、ntext、table、text**、および **xml**。 

 varchar(max)、varbinary(max)、および nvarchar(max) の各データ型は、8,000 バイト以下の引数でサポートされています。また、比較の前に、それぞれ varchar(n)、varbinary(n)、nvarchar(n) に暗黙的に変換されます。 

 たとえば、varchar(max) は、1 バイト エンコード文字セットを使用している場合に最大 8,000 文字をサポートし、nvarchar(max) は最大 4,000 バイトペアをサポートできます (UTF-16 文字エンコードを想定)。 
  
## <a name="examples"></a>例  

### <a name="a-simple-example"></a>A. 簡単な例

 次の例では、入力される定数のリストから最小値が返されます。 

 戻り値の型のスケールは、最も優先順位の高いデータ型の引数のスケールによって決まります。 
 
```sql 
SELECT LEAST ( '6.62', 3.1415, N'7' ) AS LeastVal; 
GO 
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
LeastVal 
------- 
 3.1415 

(1 rows affected)  
```  

### <a name="b-simple-example-with-character-types"></a>B. 文字型を使用した簡単な例

 次の例では、入力される文字定数のリストから最小値が返されます。  
  
```sql  
SELECT LEAST ('Glacier', N'Joshua Tree', 'Mount Rainier') AS LeastString;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
LeastString 
------------- 
Glacier 

(1 rows affected)  
```  

### <a name="c-simple-example-with-table"></a>C. テーブルを使用した簡単な例
  
 この例では、列引数のリストから最小値が返され、比較中の `NULL` 値は無視されます。 
  
```sql  
USE AdventureWorks2019; 
GO 

SELECT sp.SalesQuota, sp.SalesYTD, sp.SalesLastYear 
      , LEAST(sp.SalesQuota, sp.SalesYTD, sp.SalesLastYear) AS Sales 
FROM Sales.SalesPerson AS sp 
WHERE sp.SalesYTD < 3000000; 
GO  
  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
SalesQuota            SalesYTD              SalesLastYear         Sales 
--------------------- --------------------- --------------------- --------------------- 
                 NULL           559697.5639                 .0000                 .0000 
          250000.0000          1453719.4653          1620276.8966           250000.0000 
          300000.0000          2315185.6110          1849640.9418           300000.0000 
          250000.0000          1352577.1325          1927059.1780           250000.0000 
          250000.0000          2458535.6169          2073505.9999           250000.0000 
          250000.0000          2604540.7172          2038234.6549           250000.0000 
          250000.0000          1573012.9383          1371635.3158           250000.0000 
          300000.0000          1576562.1966                 .0000                 .0000 
                 NULL           172524.4512                 .0000                 .0000 
          250000.0000          1421810.9242          2278548.9776           250000.0000 
                 NULL           519905.9320                 .0000                 .0000 
          250000.0000          1827066.7118          1307949.7917           250000.0000 

(12 rows affected) 
  
```  
### <a name="d-using-least-with-local-variables"></a>D. ローカル変数での `LEAST` の使用

 この例では、`LEAST` を使用して、`WHERE` 句の述語内にあるローカル変数のリストの最小値を決定します。 
  
```sql  
CREATE TABLE dbo.studies (    
    VarX varchar(10) NOT NULL,    
    Correlation decimal(4, 3) NULL 
); 

INSERT INTO dbo.studies VALUES ('Var1', 0.2), ('Var2', 0.825), ('Var3', 0.61); 
GO 

DECLARE @PredictionA DECIMAL(2,1) = 0.7;  
DECLARE @PredictionB DECIMAL(3,1) = 0.65;  

SELECT VarX, Correlation  
FROM dbo.studies 
WHERE Correlation < LEAST(@PredictionA, @PredictionB); 
GO 
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
VarX   Correlation 
---------- ----------- 
Var1              .200 
Var3              .610 

(2 rows affected)
```  

### <a name="e-using-least-with-columns-constants-and-variables"></a>E. 列、定数、変数での `LEAST` の使用

 この例では、`LEAST` を使用して、列、定数、および変数を含むリストの最小値を決定します。 
  
```sql  
CREATE TABLE dbo.products (    
    prod_id INT IDENTITY(1,1),    
    listprice smallmoney NULL 
); 

INSERT INTO dbo.products VALUES (14.99), (49.99), (24.99); 
GO 

DECLARE @PriceX smallmoney = 19.99;  

SELECT LEAST(listprice, 40, @PriceX) as LeastPrice  
FROM dbo.products;
GO 
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
LeastPrice
------------
     14.9900
     19.9900
     19.9900

(3 rows affected) 
```  

  
## <a name="see-also"></a>関連項目  
 [GREATEST &#40;Transact-SQL&#41;](../../t-sql/functions/logical-functions-greatest-transact-sql.md)  
 [MAX &#40;Transact-SQL&#41;](../../t-sql/functions/max-transact-sql.md)  
 [MIN &#40;Transact-SQL&#41;](../../t-sql/functions/min-transact-sql.md)  
 [CASE &#40;Transact-SQL&#41;](../../t-sql/language-elements/case-transact-sql.md)  
 [CHOOSE &#40;Transact-SQL&#41;](../../t-sql/functions/logical-functions-choose-transact-sql.md)  
  
  
