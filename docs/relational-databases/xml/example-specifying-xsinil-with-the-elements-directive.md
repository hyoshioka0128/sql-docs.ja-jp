---
title: ELEMENTS ディレクティブで XSINIL を指定する | Microsoft Docs
description: ELEMENTS ディレクティブを含む XSINIL を指定して、クエリ結果から要素中心の XML を生成する例を示します。
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: xml
ms.topic: conceptual
helpviewer_keywords:
- RAW mode, specifying XSINIL example
ms.assetid: 07c873ff-1f9d-480e-8536-862c39eb8249
author: rothja
ms.author: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 474e1604dd90a88db8e2f9e21fdeac9caf602437
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107488673"
---
# <a name="example-specifying-xsinil-with-the-elements-directive"></a>例: ELEMENTS ディレクティブで XSINIL を指定する
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  次のクエリでは、 `ELEMENTS` ディレクティブを指定し、クエリ結果から要素中心の XML を生成します。  
  
## <a name="example"></a>例  
  
```  
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name, Color  
FROM Production.Product  
FOR XML RAW, ELEMENTS;  
GO  
```  
  
 次に結果の一部を示します。  
  
```  
<row>  
  <ProductID>1</ProductID>  
  <Name>Adjustable Race</Name>  
</row>  
...  
<row>  
  <ProductID>317</ProductID>  
  <Name>LL Crankarm</Name>  
  <Color>Black</Color>  
</row>  
```  
  
 一部の製品では `Color` 列に NULL 値が含まれるので、その結果の XML では対応する <`Color`> 要素が生成されません。 `XSINIL` と共に `ELEMENTS` ディレクティブを追加することにより、結果セット内の Color 列が NULL 値の行も <`Color`> 要素を生成できます。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name, Color  
FROM Production.Product  
FOR XML RAW, ELEMENTS XSINIL ;  
```  
  
 結果の一部を次に示します。  
  
```  
<row xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
  <ProductID>1</ProductID>  
  <Name>Adjustable Race</Name>  
  <Color xsi:nil="true" />  
</row>  
...  
<row>  
  <ProductID>317</ProductID>  
  <Name>LL Crankarm</Name>  
  <Color>Black</Color>  
</row>  
```  
  
## <a name="see-also"></a>参照  
 [FOR XML での RAW モードの使用](../../relational-databases/xml/use-raw-mode-with-for-xml.md)  
  
  
