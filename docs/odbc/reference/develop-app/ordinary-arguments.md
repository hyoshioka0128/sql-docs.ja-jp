---
title: 通常の引数 |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- arguments in catalog functions [ODBC], ordinary
- catalog functions [ODBC], arguments
- ordinary arguments [ODBC]
ms.assetid: a18cdae1-6b85-41cb-875c-b5a01ec90aeb
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: 31d83b00fd70cd54587a19ebfea7310154167493
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47706690"
---
# <a name="ordinary-arguments"></a>通常の引数
カタログ関数の文字列引数に通常の引数がある場合は、リテラル文字列として扱われます。 通常の引数は、文字列の検索パターンも値の一覧を受け入れます。 通常の引数の大文字と小文字が重要でと、文字列内の引用符文字が文字どおり表示。 SQL_ATTR_METADATA_ID ステートメントの属性が SQL_FALSE; に設定されている場合、これらの引数は通常の引数として扱われますとして扱われます識別子引数代わりにこの属性が SQL_TRUE に設定されている場合。  
  
 SQL_ERROR と SQLSTATE HY009 場合、通常の引数が null ポインターに設定されているし、引数が必要な引数、関数を返します (null ポインターの無効な使用)。 通常の引数が null ポインターに設定されている、引数が必要な引数ではない場合は、ドライバーによって異なりますが、引数の動作。 必須の引数は、次の表に一覧表示されます。  
  
|機能|必須の引数|  
|--------------|------------------------|  
|**SQLColumnPrivileges**|*TableName*|  
|**SQLForeignKeys**|*PKTableName*、 *FKTableName*|  
|**SQLPrimaryKeys**|*TableName*|  
|**SQLSpecialColumns**|*TableName*|  
|**SQLStatistics**|*TableName*|
