---
title: 'Sql: relationship (SQLXML) での sql: 逆属性の設定'
description: 'Sql: relationship 要素で sql: 逆属性を使用して、アップデートグラム操作でデータベース列間のリレーションシップを指定する方法について説明します。'
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- sql:relationship
- bulk load [SQLXML]
- inverse attribute
- relationships [SQLXML], inverse orders
- relationship annotation
- XSD schemas [SQLXML], relationships
- annotated XSD schemas, relationships
- updategrams [SQLXML], relationships
- sql:inverse
ms.assetid: 08904cbd-9c86-493d-90c3-f5e1d13ce59d
author: MightyPen
ms.author: genemi
ms.reviewer: ''
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: b714a70cb3d537d3f9859945b5fdee6842aa5d58
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97479343"
---
# <a name="specifying-the-sqlinverse-attribute-on-sqlrelationship-sqlxml-40"></a>sql:relationship での sql:inverse 属性の指定 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  **Sql: 逆** 属性は、XSD スキーマが一括読み込みまたはアップデートグラムで使用されている場合にのみ役立ちます。 **Sql: 逆** 属性は、要素で指定でき **\<sql:relationship>** ます。 アップデートグラムでは、アップデートグラム ロジックによってスキーマが解釈され、アップデートグラム操作で更新されるテーブルと列が決定されます。 また、スキーマで指定される親子リレーションシップによって、レコードが変更、挿入、または削除される順序が決定されます。  
  
 XSD スキーマを使用しており、そこで指定されている親子リレーションシップが、対応するデータベース列間の主キー/外部キー リレーションシップの逆順である場合は、アップデートグラムで挿入または削除操作を実行すると、主キー/外部キー違反で失敗します。 このような場合、要素には **sql: 逆** 属性が指定されています (**sql: 逆 = "true"**) **\<sql:relationship>** 。アップデートグラムのロジックは、スキーマで指定されている親子リレーションシップの解釈を逆します。  
  
 **Sql: 逆** 属性はブール値 (0 = false、1 = true) をとります。 指定できる値は 0、1、true、false です。  
  
 **Sql: 逆** 注釈を使用した実際のサンプルについては、「[アップデートグラムでの注釈付きマッピングスキーマの指定](../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/updategrams/specifying-an-annotated-mapping-schema-in-an-updategram-sqlxml-4-0.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [Sql: relationship を使用したリレーションシップの指定 &#40;SQLXML 4.0&#41;](../../relational-databases/sqlxml-annotated-xsd-schemas-using/specifying-relationships-using-sql-relationship-sqlxml-4-0.md)  
  
  
