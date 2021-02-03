---
description: SQLFreeStmt
title: SQLFreeStmt |Microsoft Docs
ms.custom: ''
ms.date: 11/23/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLFreeStmt function
ms.assetid: d9666d0b-3446-480e-bf1a-10b01213e411
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 956ce78ae72c39e1986955fcc562c4a2472eb4a1
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97465193"
---
# <a name="sqlfreestmt"></a>SQLFreeStmt
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  一般的に   
      **SQLFreeStmt** は、ODBC 3.0 以降では推奨されません。 ただし、アプリケーションでステートメントを再利用する必要がある場合でも、 **SQL_RESET_PARAMS** と **SQL_UNBIND** のオプションを指定して **SQLFreeStmt** を使用する必要があります。 [SqlcloSQLBindParameter](../../relational-databases/native-client-odbc-api/sqlclosecursor.md)、 [SQLBindCol](../../relational-databases/native-client-odbc-api/sqlbindcol.md)、 [SQLSetDescField](../../relational-databases/native-client-odbc-api/sqlsetdescfield.md)、および [](../../relational-databases/native-client-odbc-api/sqlbindparameter.md) [sqlfreehandle](../../relational-databases/native-client-odbc-api/sqlfreehandle.md)を使用して、 **SQLFreeStmt** の関数を置き換えたり複製したりすることもできます。代わりに、これらの関数を使用する必要があります。  
  
 一般に、ステートメントを再利用して新しいステートメントを割り当てるよりも効率的です。 ただし、ステートメントを再利用する場合のように、SQLFreeStmt を使用する必要がある場合もあります。  
  
## <a name="see-also"></a>参照  
 [SQLFreeStmt 関数](../../odbc/reference/syntax/sqlfreestmt-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
