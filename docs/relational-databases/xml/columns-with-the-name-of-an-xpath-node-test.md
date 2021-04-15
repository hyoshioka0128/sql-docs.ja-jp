---
title: XPath ノード テストの名前が付いた列 | Microsoft Docs
description: text() や comment () などの XPath ノード テストの名前の列が SQL クエリに含まれているときに、XML コンテンツをマップする方法について学習します。
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: xml
ms.topic: conceptual
helpviewer_keywords:
- names [SQL Server], columns with
- XPath node test
ms.assetid: b48adccd-3b6b-486a-b326-20f57170186f
author: rothja
ms.author: jroth
ms.openlocfilehash: 7106a3be18c5a159f31f3b15f4866a29e47e3ebb
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107490987"
---
# <a name="columns-with-the-name-of-an-xpath-node-test"></a>XPath ノード テストの名前が付いた列
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  XPath ノード テストのいずれかが列名である場合、内容は次の表に示すようにマップされます。 列名がいずれかの XPath ノード テストであれば、対応するノードに内容がマップされます。 列の SQL 型が **xml** の場合は、エラーが返されます。  
  
|列名|動作|  
|-----------------|--------------|  
|text()|text() という名前の列では、列内の文字列値がテキスト ノードとして追加されます。|  
|comment()|comment() という名前の列では、列内の文字列値が XML のコメントとして追加されます。|  
|node()|node() という名前の列では、列名がワイルドカード文字 (*) の場合と同じ結果が返されます。|  
|processing-instruction(name)|処理命令を名前とする列では、列内の文字列値が、処理命令ターゲット名に対応する PI 値として追加されます。|  
  
 次のクエリでは、列名としてノード テストを使用しています。 結果の XML では、テキスト ノードとコメントが追加されます。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT E.BusinessEntityID "@EmpID",   
        'Example of using node tests such as text(), comment(), processing-instruction()'                as "comment()",  
        'Some PI'                   as "processing-instruction(PI)",  
        'Employee name and address data' as "text()",  
        'middle name is optional'        as "EmpName/text()",  
        FirstName                        as "EmpName/First",   
        MiddleName                       as "EmpName/Middle",   
        LastName                         as "EmpName/Last",  
        AddressLine1                     as "Address/AddrLine1",  
        AddressLine2                     as "Address/AddrLIne2",  
        City                             as "Address/City"  
FROM   HumanResources.Employee AS E  
INNER JOIN Person.Person AS P   
    ON P.BusinessEntityID = E.BusinessEntityID  
INNER JOIN Person.BusinessEntityAddress AS BAE  
    ON BAE.BusinessEntityID = E.BusinessEntityID  
INNER JOIN Person.Address AS A  
    ON BAE.AddressID = A.AddressID  
WHERE  E.BusinessEntityID=1  
FOR XML PATH;  
```  
  
 結果を次に示します。  
  
 `<row EmpID="1">`  
  
 `<!--Example of using node tests such as text(), comment(), processing-instruction() -->`  
  
 `<?PI Some PI?>`  
  
 `Employee name and address data`  
  
 `<EmpName>middle name is optional`  
  
 `<First>Ken</First>`  
  
 `<Last>Sánchez</Last>`  
  
 `</EmpName>`  
  
 `<Address>`  
  
 `<AddrLine1>4350 Minute Dr.</AddrLine1>`  
  
 `<City>Minneapolis</City>`  
  
 `</Address>`  
  
 `</row>`  
  
## <a name="see-also"></a>参照  
 [FOR XML での PATH モードの使用](../../relational-databases/xml/use-path-mode-with-for-xml.md)  
  
  
