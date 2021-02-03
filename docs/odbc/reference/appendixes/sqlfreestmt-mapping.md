---
description: SQLFreeStmt のマッピング
title: SQLFreeStmt Mapping |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- SQLFreeStmt function [ODBC], mapping
- mapping deprecated functions [ODBC], SQLFreeStmt
ms.assetid: 267d95f2-4f0c-47ab-9411-5afe105215a2
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: a90ddbaab036efd22003b84b551398b0d896226b
ms.sourcegitcommit: 33f0f190f962059826e002be165a2bef4f9e350c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99202807"
---
# <a name="sqlfreestmt-mapping"></a>SQLFreeStmt のマッピング
アプリケーションが ODBC *3. x* ドライバーを使用して SQL_DROP の *オプション* 引数を使用して **SQLFreeStmt** を呼び出すと、が呼び出されます。  
  
```  
SQLFreeStmt(hstmt, SQL_DROP)   
```  
  
 がにマップされています  
  
```  
SQLFreeHandle(SQL_HANDLE_STMT,Handle)  
```  
  
 *ハンドル* 引数が *hstmt* の値に設定されている。
