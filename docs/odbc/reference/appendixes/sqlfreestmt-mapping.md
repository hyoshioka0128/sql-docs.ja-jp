---
title: SQLFreeStmt のマッピング |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- SQLFreeStmt function [ODBC], mapping
- mapping deprecated functions [ODBC], SQLFreeStmt
ms.assetid: 267d95f2-4f0c-47ab-9411-5afe105215a2
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: 1872806265470327f3e7bff468be2ba6d9011421
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47792770"
---
# <a name="sqlfreestmt-mapping"></a>SQLFreeStmt のマッピング
アプリケーションを呼び出すと**SQLFreeStmt**で、*オプション*SQL_DROP、ODBC 3 までの引数 *.x*ドライバーへの呼び出し  
  
```  
SQLFreeStmt(hstmt, SQL_DROP)   
```  
  
 をマップされます。  
  
```  
SQLFreeHandle(SQL_HANDLE_STMT,Handle)  
```  
  
 *処理*引数の値に設定*hstmt*します。
