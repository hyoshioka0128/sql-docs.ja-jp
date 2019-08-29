---
title: DATALENGTH (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/20/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DATALENGTH_TSQL
- DATALENGTH
dev_langs:
- TSQL
helpviewer_keywords:
- number of bytes representing expression
- data types [SQL Server], length
- DATALENGTH function
- expressions [SQL Server], length
- lengths [SQL Server], data
ms.assetid: 00f377f1-cc3e-4eac-be47-b3e3f80267c9
author: pmasl
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: cca069fe3bb0aa0f489c13c022aeeebbd08db053
ms.sourcegitcommit: 5e838bdf705136f34d4d8b622740b0e643cb8d96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69653794"
---
# <a name="datalength-transact-sql"></a>DATALENGTH (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-all-md](../../includes/tsql-appliesto-ss2008-all-md.md)]

この関数では、式を表すために必要なバイト数が返されます。

> [!NOTE]
> 文字列式内の文字数を取得するには、[LEN](../../t-sql/functions/len-transact-sql.md) 関数を使用します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```
DATALENGTH ( expression )   
```  
  
## <a name="arguments"></a>引数  
*式 (expression)*  
任意の型の[式](../../t-sql/language-elements/expressions-transact-sql.md)。
  
## <a name="return-types"></a>戻り値の型
*expression* が **varchar(max)** 、**nvarchar(max)** 、**varbinary(max)** データ型の場合は **bigint**、それ以外の場合は **int**。
  
## <a name="remarks"></a>Remarks  
次のような可変長データを格納できるデータ型で使用すると、`DATALENGTH` は非常に便利です。
- **image**
- **ntext**
- **nvarchar**
- **text**
- **varbinary**
- **varchar**
  
NULL 値の場合、`DATALENGTH` は NULL を返します。
  
> [!NOTE]  
> 戻り値は、互換性レベルによって変わることがあります。 互換性レベルの詳細については、「[ALTER DATABASE 互換性レベル &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)」を参照してください。  

> [!NOTE]
> 特定の文字列式にエンコードされた文字数を取得するには [LEN](../../t-sql/functions/len-transact-sql.md) を使用し、特定の文字列式のバイト サイズを取得するには [DATALENGTH](../../t-sql/functions/datalength-transact-sql.md) を使用します。 これらの出力は、データ型および列で使用されているエンコードの種類によっては、異なる場合があります。 異なる種類のエンコードでの記憶域の違いについて詳しくは、「[照合順序と Unicode のサポート](../../relational-databases/collations/collation-and-unicode-support.md)」をご覧ください。

## <a name="examples"></a>使用例  
この例では、`Product` テーブルの `Name` 列の長さが返されます。
  
```sql
USE AdventureWorks2016  
GO
SELECT length = DATALENGTH(EnglishProductName), EnglishProductName  
FROM dbo.DimProduct  
ORDER BY EnglishProductName;  
GO  
```  
  
## <a name="see-also"></a>参照
[LEN &#40;Transact-SQL&#41;](../../t-sql/functions/len-transact-sql.md)  
[CAST および CONVERT &#40;Transact-SQL&#41;](../../t-sql/functions/cast-and-convert-transact-sql.md)  
[データ型 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-types-transact-sql.md)  
[システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-for-transact-sql.md)
