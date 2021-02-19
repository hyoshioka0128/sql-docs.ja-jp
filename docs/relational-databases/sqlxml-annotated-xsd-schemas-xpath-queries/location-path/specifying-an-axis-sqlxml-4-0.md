---
title: 軸の指定 (SQLXML)
description: SQLXML 4.0 XPath クエリで軸を指定する方法について説明します。これにより、location ステップで選択したノードとコンテキストノードの間のツリーリレーションシップが指定されます。
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- XPath queries [SQLXML], axes
- XPath queries [SQLXML], location paths
- self axis
- attribute axis [SQLXML]
- axis [SQLXML]
- child axis
- parent axis
- location path for XPath query
- axes [SQLXML]
ms.assetid: 65631795-3389-40cf-90ea-85e9438956c5
author: MightyPen
ms.author: genemi
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 03c0fff271ef3774116eb9c97025b1f602f7852c
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97431160"
---
# <a name="specifying-an-axis-sqlxml-40"></a>軸の指定 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
    
-   軸によって、ロケーション ステップで選択されるノードと、コンテキスト ノードの間のツリー リレーションシップが指定されます。 次の軸がサポートされています:  **child**  
  
     コンテキスト ノードの子を含みます。  
  
     次の XPath 式 (ロケーションパス) では、現在のコンテキストノードからすべての子が選択され **\<Customer>** ます。  
  
    ```  
    child::Customer  
    ```  
  
     この XPath クエリでは、`child` は軸で、 `Customer` はノード テストです。  
  
-   **parent**  
  
     コンテキスト ノードの親を含みます。  
  
     次の XPath 式は、子のすべての親を選択し **\<Customer>** **\<Order>** ます。  
  
    ```  
    child::Customer/child::Order[parent::Customer/@customerID="ALFKI"]  
    ```  
  
     これは、`child::Customer` を指定した場合と同じです。 この XPath クエリでは、`child` と `parent` は軸で、 `Customer` と `Order` はノード テストです。  
  
-   **attribute**  
  
     コンテキスト ノードの属性を含みます。  
  
     次の XPath 式では、コンテキストノードの **CustomerID** 属性が選択されます。  
  
    ```  
    attribute::CustomerID  
    ```  
  
-   **自身**  
  
     コンテキスト ノードそのものを含みます。  
  
     次の XPath 式は、ノードの場合、現在のノードを選択し **\<Order>** ます。  
  
    ```  
    self::Order  
    ```  
  
     この XPath クエリでは、`self` は軸で、`Order` はノード テストです。  
  
  
