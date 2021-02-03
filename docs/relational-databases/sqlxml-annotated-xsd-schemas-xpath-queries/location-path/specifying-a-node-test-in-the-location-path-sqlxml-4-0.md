---
title: ロケーションパスでのノードテストの指定 (SQLXML)
description: SQLXML 4.0 XPath クエリのロケーションパスでノードテストを指定する方法について説明します。
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- XPath queries [SQLXML], location paths
- principal node types [SQLXML]
- node tests [SQLXML]
- location path for XPath query
ms.assetid: f46c30bf-1e24-4435-9ac2-f8ba43a8ff94
author: MightyPen
ms.author: genemi
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: b8fdee8bdc7f3fbc3281ecab7682efdc8378bbdf
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97414843"
---
# <a name="specifying-a-node-test-in-the-location-path-sqlxml-40"></a>ロケーション パスでのノード テストの指定 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  ノード テストによって、ロケーション ステップで選択されるノードの型が決まります。 すべての軸 (**子**、 **親**、 **属性**、または **自己**) には、主ノード型があります。 **属性** 軸の場合、主ノード型は **\<attribute>** です。 **親**、**子**、および **自己** 軸の場合、主ノード型は **\<element>** です。  
  
> [!NOTE]  
>  ワイルドカード (*) のノード テスト (たとえば `child::*`) は、サポートされていません。  
  
## <a name="node-test-example-1"></a>ノードテスト: 例1  
 ロケーションパスは `child::Customer` 、 **\<Customer>** コンテキストノードの子要素を選択します。  
  
 この例では、`child` は軸で、`Customer` はノード テストです。 **子** 軸の主ノード型は **\<element>** です。 したがって、ノードがノードの場合、ノードテストは TRUE になり **\<Customer>** **\<element>** ます。 コンテキストノードに子がない場合 **\<Customer>** は、空のノードセットが返されます。  
  
## <a name="node-test-example-2"></a>ノード テスト : 例 2  
 ロケーションパスによって、 `attribute::CustomerID` コンテキストノードの **CustomerID** 属性が選択されます。  
  
 この例で `attribute` は、は軸で、 `CustomerID` はノードテストです。 **属性** 軸の主ノード型は **\<attribute>** です。 このため、 **CustomerID** がノードの場合、ノードテストは TRUE になり **\<attribute>** ます。 コンテキストノードに **CustomerID** がない場合は、空のノードセットが返されます。  
  
> [!NOTE]  
>  この XPath の実装では、ロケーションステップがスキーマで宣言されていないまたは型を参照している場合、 **\<element>** **\<attribute>** エラーが生成されます。 これは、空のノード セットを返す MSXML の XPath の実装とは異なります。  
  
## <a name="abbreviated-syntax-for-the-axes"></a>軸の省略構文  
 ロケーション パスでは、次の省略構文がサポートされています。  
  
-   `attribute::` は `@` で省略できます。  
  
     ロケーション パス `Customer[@CustomerID="ALFKI"]` は、`child::Customer[attribute::CustomerID="ALFKI"]` と同じです。  
  
-   ロケーション ステップから `child::` を省略できます。  
  
     したがって、 **子** は既定の軸です。 ロケーション パス `Customer/Order` は、`child::Customer/child::Order` と同じです。  
  
-   `self::node()` は 1 つのピリオド (.) で省略できます。また、`parent::node()` は 2 つのピリオド (..) で省略できます。  
  
  
