---
description: sys.dm_db_missing_index_columns (Transact-SQL)
title: sys.dm_db_missing_index_columns (Transact-SQL)
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: reference
f1_keywords:
- dm_db_missing_index_columns_TSQL
- sys.dm_db_missing_index_columns_TSQL
- sys.dm_db_missing_index_columns
- dm_db_missing_index_columns
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_db_missing_index_columns dynamic management function
- missing indexes feature [SQL Server], sys.dm_db_missing_index_columns dynamic management function
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: ec00a68faedb48e63988d91f6e612c62a705caf3
ms.sourcegitcommit: c6cc0b669b175ae290cf5b08952010661ebd03c3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530859"
---
# <a name="sysdm_db_missing_index_columns-transact-sql"></a>sys.dm_db_missing_index_columns (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  空間インデックスを除く、インデックスが欠落しているデータベーステーブルの列に関する情報を返します。 **sys.dm_db_missing_index_columns** は動的管理関数です。  

## <a name="syntax"></a>構文  
  
```  
  
sys.dm_db_missing_index_columns(index_handle)  
```  
  
## <a name="arguments"></a>引数  
 *index_handle*  
 欠落インデックスを一意に識別する整数値です。 次の動的管理オブジェクトから取得できます。  
  
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)  
  
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)  
  
## <a name="table-returned"></a>返されるテーブル  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**column_id**|**int**|列の ID。|  
|**column_name**|**sysname**|テーブル列の名前。|  
|**column_usage**|**varchar (20)**|クエリでの列の使用方法。 使用可能な値とその説明は次のとおりです。<br /><br /> 等値: 列は、次の形式の等価性を表す述語に貢献します。 <br />                        *表. 列*  = *constant_value*<br /><br /> 非等値: 列は、次のような非等値を表す述語に寄与し *ます。たとえば*、  >  *constant_value* の形式の述語です。 "=" 以外の比較演算子はすべて、不等値を表します。<br /><br /> INCLUDE: 列は述語の評価には使用されませんが、別の理由 (たとえば、クエリに対応するため) に使用されます。|  
  
## <a name="remarks"></a>Remarks  
 **sys.dm_db_missing_index_columns** によって返される情報は、クエリ オプティマイザーでクエリが最適化されるときに更新されますが、保存されません。 欠落インデックスの情報が保持されるのは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の再起動までです。 欠落インデックスの情報を、サーバーの再利用後も保持する場合は、データベース管理者が情報のバックアップ コピーを定期的に作成する必要があります。  
  
## <a name="transaction-consistency"></a>トランザクションの一貫性  
 トランザクションでテーブルを作成または削除する場合、削除されたオブジェクトに関する欠落インデックス情報を含む行は、トランザクションの一貫性を保持するためこの動的管理オブジェクトから削除されます。  
  
## <a name="permissions"></a>アクセス許可  
 この動的管理関数をクエリするには、VIEW SERVER STATE 権限、または VIEW SERVER STATE が暗黙的に与えられる権限が許可されている必要があります。  
  
## <a name="examples"></a>使用例  
 次の例では、`Address` テーブルに対してクエリを実行した後、`sys.dm_db_missing_index_columns` 動的管理ビューを使用してクエリを実行し、インデックスが欠落しているテーブル列を返します。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT City, StateProvinceID, PostalCode  
FROM Person.Address  
WHERE StateProvinceID = 9;  
GO  
SELECT mig.*, statement AS table_name,  
    column_id, column_name, column_usage  
FROM sys.dm_db_missing_index_details AS mid  
CROSS APPLY sys.dm_db_missing_index_columns (mid.index_handle)  
INNER JOIN sys.dm_db_missing_index_groups AS mig ON mig.index_handle = mid.index_handle  
ORDER BY mig.index_group_handle, mig.index_handle, column_id;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [sys.dm_db_missing_index_details &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql.md)   
 [sys.dm_db_missing_index_groups &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql.md)   
 [sys.dm_db_missing_index_group_stats &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-transact-sql.md)  
 [sys.dm_db_missing_index_group_stats_query &#40;Transact-sql&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-query-transact-sql.md)     
  
