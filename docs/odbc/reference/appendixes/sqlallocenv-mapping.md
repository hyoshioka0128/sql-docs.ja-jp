---
description: SQLAllocEnv のマッピング
title: SQLAllocEnv Mapping |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- SQLAllocEnv function [ODBC], mapping
- mapping deprecated functions [ODBC], SQLAllocEnv
ms.assetid: 4bb51845-ee91-4b97-9dd4-2fab977f2aec
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 134f517b8374aafa223da329fc53c2eeabeefbe4
ms.sourcegitcommit: 33f0f190f962059826e002be165a2bef4f9e350c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99202967"
---
# <a name="sqlallocenv-mapping"></a>SQLAllocEnv のマッピング
アプリケーションが ODBC *3. x* ドライバーを使用して **sqlallocenv** を呼び出すと **、sqlallocenv**(*phenv*) への呼び出しは次のように **SQLAllocHandle** にマップされます。  
  
1.  ドライバーマネージャーは、環境ハンドルを割り当ててアプリケーションに返します。 ドライバーマネージャーは **SQLSetEnvAttr** を呼び出して、SQL_ATTR_ODBC_VERSION 環境属性を SQL_OV_ODBC2 に設定します。  
  
2.  アプリケーションがドライバーへの最初の接続を確立すると、ドライバーマネージャーはを呼び出します。  
  
    ```  
    SQLAllocHandle(SQL_HANDLE_ENV, SQL_NULL_HANDLE, OutputHandlePtr)  
    ```  
  
     *OutputHandlePtr* が *phenv* に設定されているドライバー。
