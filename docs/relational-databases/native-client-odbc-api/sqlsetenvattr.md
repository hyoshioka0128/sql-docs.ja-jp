---
description: SQLSetEnvAttr
title: SQLSetEnvAttr |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apitype: DLLExport
helpviewer_keywords:
- SQLSetEnvAttr function
ms.assetid: d4114571-feca-4330-b2e4-7bfd1050b812
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ba2b49fa3e5fbc8be5ed21a42604993ce0be6755
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97438695"
---
# <a name="sqlsetenvattr"></a>SQLSetEnvAttr
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  Odbc [プログラマーズリファレンス](../../odbc/reference/odbc-programmer-s-reference.md) では、odbc ドライバーが odbc 2 に書き込まれたアプリケーションから **SQLSetEnvAttr** 属性の仕様を解釈する方法を定義します。*x* または ODBC 3。*x* API。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Native CLIENT ODBC ドライバーは、これらの規則に準拠しています。  
  
 **SQLSetEnvAttr** によって制御される属性の1つは、接続プールを使用するかどうかです。 Native Client ODBC ドライバーで接続プールを使用する場合は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、 [SQLDriverConnect](../../relational-databases/native-client-odbc-api/sqldriverconnect.md)または **SQLConnect** を使用して接続するときに、 *drivercompletion* パラメーターを SQL_DRIVER_NOPROMPT に設定する必要があります。  
  
## <a name="see-also"></a>参照  
 [SQLSetEnvAttr 関数](../../odbc/reference/syntax/sqlsetenvattr-function.md)   
 [ODBC API 実装の詳細](../../relational-databases/native-client-odbc-api/odbc-api-implementation-details.md)  
  
