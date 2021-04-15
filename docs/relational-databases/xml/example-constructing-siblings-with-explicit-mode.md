---
title: '例: EXPLICIT モードを使用した兄弟の構築 | Microsoft Docs'
description: FOR XML 句で EXPLICIT モードを使用して、XML の兄弟を構築する SQL クエリの例を示します。
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: xml
ms.topic: conceptual
helpviewer_keywords:
- EXPLICIT FOR XML mode
ms.assetid: 8a57b765-a890-46a3-8b5f-5754e921ea6e
author: rothja
ms.author: jroth
ms.openlocfilehash: ce29236ad51fcdc3c0a29c92e8dd0764b0cccdc9
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107488615"
---
# <a name="example-constructing-siblings-with-explicit-mode"></a>例:EXPLICIT モードを使用した兄弟の構築
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  販売注文情報を提供する XML を生成するとします。 <`SalesPerson`> 要素と <`OrderDetail`> 要素は兄弟です。 各注文には、<`OrderHeader`> 要素が 1 つ、<`SalesPerson`> 要素が 1 つ、<`OrderDetail`> 要素が 1 つ以上あります。  
  
```  
<OrderHeader SalesOrderID=... OrderDate=... CustomerID=... >  
  <SalesPerson SalesPersonID=... />  
  <OrderDetail SalesOrderID=... LineTotal=... ProductID=... OrderQty=... />  
  <OrderDetail SalesOrderID=... LineTotal=... ProductID=... OrderQty=.../>  
      ...  
</OrderHeader>  
<OrderHeader ...</OrderHeader>  
```  
  
 次に示す EXPLICIT モードのクエリは、上記の形式の XML を生成します。 このクエリでは、<`OrderHeader`> 要素の `Tag` 列に 1 を、<`SalesPerson`> 要素には 2 を、<`OrderDetail`> 要素には 3 を指定しています。 <`SalesPerson`> 要素と <`OrderDetail`> 要素は兄弟なので、これらの要素の `Parent` 列に値 1 を指定し、<`OrderHeader`> 要素を親要素として特定しています。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT  1 as Tag,  
        0 as Parent,  
        SalesOrderID  as [OrderHeader!1!SalesOrderID],  
        OrderDate     as [OrderHeader!1!OrderDate],  
        CustomerID    as [OrderHeader!1!CustomerID],  
        NULL          as [SalesPerson!2!SalesPersonID],  
        NULL          as [OrderDetail!3!SalesOrderID],  
        NULL          as [OrderDetail!3!LineTotal],  
        NULL          as [OrderDetail!3!ProductID],  
        NULL          as [OrderDetail!3!OrderQty]  
FROM   Sales.SalesOrderHeader  
WHERE     SalesOrderID=43659 or SalesOrderID=43661  
UNION ALL   
SELECT 2 as Tag,  
       1 as Parent,  
        SalesOrderID,  
        NULL,  
        NULL,  
        SalesPersonID,    
        NULL,           
        NULL,           
        NULL,  
        NULL           
FROM   Sales.SalesOrderHeader  
WHERE     SalesOrderID=43659 or SalesOrderID=43661  
UNION ALL  
SELECT 3 as Tag,  
       1 as Parent,  
        SOD.SalesOrderID,  
        NULL,  
        NULL,  
        SalesPersonID,  
        SOH.SalesOrderID,  
        LineTotal,  
        ProductID,  
        OrderQty     
FROM    Sales.SalesOrderHeader SOH,Sales.SalesOrderDetail SOD  
WHERE   SOH.SalesOrderID = SOD.SalesOrderID  
AND     (SOH.SalesOrderID=43659 or SOH.SalesOrderID=43661)  
ORDER BY [OrderHeader!1!SalesOrderID], [SalesPerson!2!SalesPersonID],  
         [OrderDetail!3!SalesOrderID],[OrderDetail!3!LineTotal]  
FOR XML EXPLICIT;  
```  
  
 結果の一部を次に示します。  
  
```
<OrderHeader SalesOrderID="43659" OrderDate="2005-07-01T00:00:00" CustomerID="676">
  <SalesPerson SalesPersonID="279" />
  <OrderDetail SalesOrderID="43659" LineTotal="10.373000" ProductID="712" OrderQty="2" />
  <OrderDetail SalesOrderID="43659" LineTotal="28.840400" ProductID="716" OrderQty="1" />
  <OrderDetail SalesOrderID="43659" LineTotal="34.200000" ProductID="709" OrderQty="6" />
    ...
</OrderHeader>
<OrderHeader SalesOrderID="43661" OrderDate="2005-07-01T00:00:00" CustomerID="442">
  <SalesPerson SalesPersonID="282" />
  <OrderDetail SalesOrderID="43661" LineTotal="20.746000" ProductID="712" OrderQty="4" />
  <OrderDetail SalesOrderID="43661" LineTotal="40.373000" ProductID="711" OrderQty="2" />
  ...
</OrderHeader>
```
  
## <a name="see-also"></a>参照  
 [FOR XML での EXPLICIT モードの使用](../../relational-databases/xml/use-explicit-mode-with-for-xml.md)  
  
  
