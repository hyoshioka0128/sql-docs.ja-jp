---
title: 注釈の解釈 (SQLXML)
description: SQLXML 4.0 での XML 一括読み込みで XSD および XDR スキーマの注釈を解釈する方法について説明します。
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- annotated XSD schemas, XML Bulk Load
- XML Bulk Load [SQLXML], annotation intepretations
- annotations [SQLXML]
- bulk load [SQLXML], annotation interpretations
- annotated XDR schemas, XML Bulk Load
ms.assetid: 1c46bdb6-2812-4a13-b60b-7101c04b299f
author: MightyPen
ms.author: genemi
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 9763f36a2a2d4678ec76f77cec65bc68d5cbef85
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97462923"
---
# <a name="annotation-interpretation-sqlxml-40"></a>注釈の解釈 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  ここでは、XML 一括読み込みで XSD スキーマの注釈がどのように解釈されるかを説明します。 ここで説明する動作は、XDR スキーマの注釈にも当てはまります。  
  
> [!NOTE]  
>  ここで提供する情報は、XML 一括読み込みの処理で使用される注釈に関するものだけです。 SQLXML 4.0 でサポートされている XSD スキーマの注釈の完全な一覧については、「 [Xsd スキーマでの注釈の使用 &#40;sqlxml 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-using/using-annotations-in-xsd-schemas-sqlxml-4-0.md)」を参照してください。 XDR スキーマでサポートされている注釈の一覧については、「 [SQLXML 4.0&#41;では、注釈付き Xdr スキーマ &#40;非推奨 ](../../../relational-databases/sqlxml/annotated-xsd-schemas/annotated-xdr-schemas-deprecated-in-sqlxml-4-0.md))」を参照してください。  
  
## <a name="in-this-section"></a>このセクションの内容  
 [sql: リレーションシップとキーの順序付け規則 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/bulk-load-xml/annotation-interpretation-sql-relationship-and-key-ordering-rule.md)  
 XML 一括読み込みでの **sql: relationship** 注釈の解釈方法について説明します。  
  
 [sql: &#40;SQLXML 4.0&#41;にマップされています ](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/bulk-load-xml/annotation-interpretation-sql-mapped.md)  
 **Sql: マップ** された注釈を XML 一括読み込みで解釈する方法について説明します。  
  
 [sql: limit-field および sql: limit-value &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/bulk-load-xml/annotation-interpretation-sql-limit-field-and-sql-limit-value.md)  
 **Sql: limit-field** 注釈と **sql: limit-VALUE** 注釈が XML 一括読み込みでどのように解釈されるかについて説明します。  
  
 [sql: overflow フィールド &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/bulk-load-xml/annotation-interpretation-sql-overflow-field.md)  
 XML 一括読み込みでの **sql: overflow** 注釈の解釈方法について説明します。  
  
 [SQLXML 4.0 &#40;その他の注釈&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/bulk-load-xml/annotation-interpretation-other-annotations.md)  
 XML 一括読み込みでは、次の注釈がどのように解釈されるかを説明します。 **sql: id プレフィックス**、 **sql: 使用-cdata**、 **sql: url エンコード**、sql: **マッピングスキーマ**、 **sql: キーフィールド**。  
  
  
