---
description: 環境ハンドルの割り当て
title: 環境ハンドルを割り当てる |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client ODBC driver, environment handles
- ODBC applications, connections
- handles [SQL Server Native Client]
- environment handles [SQLNCLI]
ms.assetid: 15c1b428-ea6d-4672-894c-f0e289e2da3f
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f4411aaa8f6c32bb2939439075011377ffb13091
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97464983"
---
# <a name="allocating-an-environment-handle"></a>環境ハンドルの割り当て
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  どの ODBC 関数をアプリケーションから呼び出す場合でも、呼び出す前に ODBC 環境を初期化して環境ハンドルを割り当てる必要があります。 環境ハンドルはグローバルなコンテキスト ハンドルで、ODBC の他のハンドルのプレースホルダーです。 これを行うには、 *Handletype* パラメーターを SQL_HANDLE_ENV に設定し、 *InputHandle* を SQL_NULL_HANDLE に設定して、 **SQLAllocHandle** を呼び出します。  
  
 環境ハンドルを割り当てたら、使用する ODBC 関数呼び出しのバージョンを指定する環境属性を設定する必要があります。 ODBC 3 を使用する場合はです。*x* 関数は、*属性* パラメーターを SQL_ATTR_ODBC_VERSION に設定し、 *valueptr* を SQL_OV_ODBC3 に設定して、 [SQLSetEnvAttr](../../relational-databases/native-client-odbc-api/sqlsetenvattr.md)を呼び出します。  
  
## <a name="see-also"></a>参照  
 [ODBC&#41;&#40;SQL Server との通信 ](../../relational-databases/native-client-odbc-communication/communicating-with-sql-server-odbc.md)  
  
  
