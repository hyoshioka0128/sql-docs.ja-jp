---
description: GetRoot (データベース エンジン)
title: GetRoot (データベース エンジン) | Microsoft Docs
ms.custom: ''
ms.date: 07/22/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- GetRoot
- GetRoot_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- GetRoot [Database Engine]
ms.assetid: 240b70f1-eeda-44ab-b4bb-9e4af80fa7c0
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 5db6206cd555705ec5b167dc023f585293f45bf8
ms.sourcegitcommit: cc23d8646041336d119b74bf239a6ac305ff3d31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91111234"
---
# <a name="getroot-database-engine"></a>GetRoot (データベース エンジン)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

階層ツリーのルートを返します。 GetRoot() 静的メソッドです。
  
## <a name="syntax"></a>構文  
  
```syntaxsql
-- Transact-SQL syntax  
hierarchyid::GetRoot ( )   
```  
  
```syntaxsql
-- CLR syntax  
static SqlHierarchyId GetRoot ( )   
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>戻り値の型  
**SQL Server の戻り値の型: hierarchyid**
  
**CLR 戻り値の型:SqlHierarchyId**
  
## <a name="remarks"></a>解説  
階層ツリー内のルート ノードを決定するために使用されます。
  
## <a name="examples"></a>例  
  
### <a name="a-transact-sql-example"></a>A. Transact-SQL の例  
次の例では、階層ツリーのルートを返します。
  
```sql
SELECT OrgNode.ToString() AS Text_OrgNode, *  
FROM HumanResources.EmployeeDemo  
WHERE OrgNode = hierarchyid::GetRoot()  
```  
  
### <a name="b-clr-example"></a>B. CLR の例  
次のコード スニペットの呼び出し、 GetRoot() メソッド。
  
```sql
SqlHierarchyId.GetRoot()  
```  
  
## <a name="see-also"></a>参照
[hierarchyid データ型メソッド リファレンス](https://msdn.microsoft.com/library/01a050f5-7580-4d5f-807c-7f11423cbb06)  
[階層データ (SQL Server)](../../relational-databases/hierarchical-data-sql-server.md)  
[hierarchyid &#40;Transact-SQL&#41;](../../t-sql/data-types/hierarchyid-data-type-method-reference.md)
  
  
