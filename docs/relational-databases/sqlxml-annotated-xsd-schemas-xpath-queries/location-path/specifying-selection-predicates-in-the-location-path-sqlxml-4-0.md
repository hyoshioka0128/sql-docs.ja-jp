---
title: ロケーションパスでの選択述語の設定 (SQLXML)
description: XPath (SQLXML 4.0) クエリのロケーションパス式で選択述語を指定する方法について説明します。クエリ対象のノードセットをフィルター処理します。
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- XPath queries [SQLXML], predicates
- predicates [SQLXML]
- position-based filtering [SQLXML]
- XPath queries [SQLXML], location paths
- filtering [SQLXML]
- location path for XPath query
ms.assetid: dbef4cf4-a89b-4d7e-b72b-4062f7b29a80
author: MightyPen
ms.author: genemi
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 8c02a33e76fd478e77e2b5a743b5cc6e1d32f1ff
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97414494"
---
# <a name="specifying-selection-predicates-in-the-location-path-sqlxml-40"></a>ロケーション パスでの選択述語の指定 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  述語は、SELECT ステートメントの WHERE 句と同様に、軸についてノード セットをフィルター選択するものです。 述語はかっこで囲みます。 フィルター選択されたノード セットの各ノードに対し、ノードをコンテキスト ノード、ノード セット内のノード数をコンテキストのサイズとして、述語式が評価されます。 述語式が TRUE と評価された場合、そのノードは結果のノード セットに含められます。  
  
 XPath では、位置に基づくフィルター選択を行うこともできます。 数値として評価される述語式を使用すると、その序数に対応するノードが選択されます。 たとえば、ロケーション パス `Customer[3]` では、3 番目の顧客が返されますが、 このような数値述語はサポートされていません。 サポートされているのは、ブール値の結果を返す述語式のみです。  
  
> [!NOTE]  
>  Xpath のこの XPath 実装と W3C 仕様の違いについては、「 [Xpath クエリの使用の概要 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/introduction-to-using-xpath-queries-sqlxml-4-0.md)」を参照してください。  
  
## <a name="selection-predicate-example-1"></a>選択述語: 例1  
 次の XPath 式 (ロケーションパス) は、現在のコンテキストノードから、 **\<Customer>** **CustomerID** 属性が ALFKI の値がであるすべての子要素を選択します。  
  
```  
/child::Customer[attribute::CustomerID="ALFKI"]  
```  
  
 この XPath クエリでは、`child` と `attribute` は軸名で、 `Customer` はノードテストです。がである場合は TRUE になり `Customer` **\<element node>** ます。これは、 **\<element>** が軸の主ノード型であるためです `child` 。 `attribute::CustomerID="ALFKI"` は述語です。 述語では、 `attribute` は軸で、は `CustomerID` ノードテストです (  **\<attribute>** は **属性** 軸の主ノード型であるため、CustomerID がコンテキストノードの属性である場合は TRUE になります)。  
  
 省略構文を使用した場合、XPath クエリは次のように指定できます。  
  
```  
/Customer[@CustomerID="ALFKI"]  
```  
  
## <a name="selection-predicate-example-2"></a>選択述語: 例2  
 次の XPath 式 (ロケーションパス) では、現在のコンテキストノードから、 **\<Order>** **salesorderid** 属性を持つすべての孫が値1で選択されます。  
  
```  
/child::Customer/child::Order[attribute::SalesOrderID="1"]  
```  
  
 この XPath 式では、`child` と `attribute` は軸名で、 `Customer`、`Order`、および `SalesOrderID` はノード テストです。 `attribute::OrderID="1"` は述語です。  
  
 省略構文を使用した場合、XPath クエリは次のように指定できます。  
  
```  
/Customer/Order[@SalesOrderID="1"]  
```  
  
## <a name="selection-predicate-example-3"></a>選択述語: 例3  
 次の XPath 式 (ロケーションパス) では、現在のコンテキストノードから、 **\<Customer>** 1 つまたは複数の子を持つすべての子が選択され **\<ContactName>** ます。  
  
```  
child::Customer[child::ContactName]  
```  
  
 この例では、が XML ドキュメント内の要素の子要素であることを前提としてい **\<ContactName>** **\<Customer>** ます。これは、注釈付き XSD スキーマでは *要素中心のマッピング* と呼ばれています。  
  
 この XPath 式では、`child` が軸名で、 `Customer` はノードテストです `Customer` **\<element>** 。は軸の主ノード型であるため、がノードの場合は TRUE に **\<element>** `child` なります。 `child::ContactName` は述語です。 述語では、 `child` は軸で、は `ContactName` ノードテストです (がノードの場合は TRUE `ContactName` **\<element>** )。  
  
 この式は、 **\<Customer>** 子要素を持つコンテキストノードの子要素のみを返し **\<ContactName>** ます。  
  
 省略構文を使用した場合、XPath クエリは次のように指定できます。  
  
```  
Customer[ContactName]  
```  
  
## <a name="selection-predicate-example-4"></a>選択述語: 例4  
 次の XPath 式は、 **\<Customer>** 要素の子を持たないコンテキストノードの子要素を選択し **\<ContactName>** ます。  
  
```  
child::Customer[not(child::ContactName)]  
```  
  
 この例で **\<ContactName>** は、が XML ドキュメント内の要素の子要素であり、データベースでは [担当する] フィールドが必須ではないことを前提としてい **\<Customer>** ます。  
  
 この例では、`child` は軸で、 `Customer` はノードテストです。がノードの場合は TRUE に `Customer` \<element> なります。 `not(child::ContactName)` は述語です。 述語では、 `child` は軸で、は `ContactName` ノードテストです (がノードの場合は TRUE `ContactName` \<element> )。  
  
 省略構文を使用した場合、XPath クエリは次のように指定できます。  
  
```  
Customer[not(ContactName)]  
```  
  
## <a name="selection-predicate-example-5"></a>選択述語: 例5  
 次の XPath 式は、現在のコンテキストノードから、 **\<Customer>** **CustomerID** 属性を持つすべての子を選択します。  
  
```  
child::Customer[attribute::CustomerID]  
```  
  
 この例では、は軸で、は `child` `Customer` ノードテストです ( `Customer` がノードの場合は TRUE \<element> )。 `attribute::CustomerID` は述語です。 述語では、 `attribute` は軸で、は `CustomerID` 述語です ( `CustomerID` がノードの場合は TRUE **\<attribute>** )。  
  
 省略構文を使用した場合、XPath クエリは次のように指定できます。  
  
```  
Customer[@CustomerID]  
```  
  
## <a name="selection-predicate-example-6"></a>選択述語 : 例 6  
 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] SQLXML 4.0 では、次の例に示すように、述語にクロス積を含む XPath クエリがサポートされています。  
  
```  
Customer[Order/@OrderDate=Order/@ShipDate]  
```  
  
 このクエリでは、`Order` の `OrderDate` が任意の `ShipDate` の `Order` と等しいすべての顧客が選択されます。  
  
## <a name="see-also"></a>参照  
 [注釈付き XSD スキーマの概要 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml/annotated-xsd-schemas/introduction-to-annotated-xsd-schemas-sqlxml-4-0.md)   
 [クライアント側の XML 書式設定 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml/formatting/client-side-xml-formatting-sqlxml-4-0.md)  
  
  
