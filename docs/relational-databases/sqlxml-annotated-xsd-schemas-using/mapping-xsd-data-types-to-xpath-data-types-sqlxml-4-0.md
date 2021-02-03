---
title: XSD データ型から XPath データ型へのマッピング (SQLXML)
description: SQLXML 4.0 で XPath クエリを実行するときに、XSD データ型を XPath データ型にマップする方法について説明します。
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- mapping data types [SQLXML]
- data types [SQLXML], converting
- annotated XSD schemas, mapping data types
- XPath queries [SQLXML], mapping data types
- converting data types [SQLXML]
- data types [SQLXML], mapping data types
- XPath data types [SQLXML]
- XSD schemas [SQLXML], mapping data types
ms.assetid: ced1a95e-18d4-4a5a-8da8-dbb6d58bbd45
author: MightyPen
ms.author: genemi
ms.reviewer: ''
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 75a7ef44e18566781215bab806caa43860c57cb7
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97415833"
---
# <a name="mapping-xsd-data-types-to-xpath-data-types-sqlxml-40"></a>XSD データ型から XPath データ型へのマッピング (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  Xsd スキーマに対して XPath クエリが実行され、xsd **: type** 属性で xsd 型が指定されている場合、xpath はクエリの処理時に指定されたデータ型を使用します。  
  
 ノードの XPath データ型は、次の表に示すように、スキーマ内に指定されている XSD データ型から派生します。 ここでは説明のため、EmployeeID というノードを使用します。  
  
|XSD データ型|XDR データ型|同等の<br /><br /> XPath データ型|SQL Server<br /><br /> SQL Server 変換|  
|-------------------|-------------------|------------------------------------|--------------------------------------------|  
|**Base64Binary**<br /><br /> **HexBinary**|**なし**<br /><br /> **base64bin**|**適用不可**|なし<br /><br /> EmployeeID|  
|**Boolean**|**boolean**|**boolean**|CONVERT(bit, EmployeeID)|  
|**Decimal、integer、float、byte、short、int、long、float、double、unsignedByte、Unsignedbyte、unsignedInt、Unsignedbyte**|**number、int、float,i1、i2、i4、i8、r4、r8ui1、ui2、ui4、ui8**|**number**|CONVERT(float(53), EmployeeID)|  
|**id、idref、idrefsentity、entities、notation、nmtoken、nmtokens、DateTime、string、AnyURI**|**id、idref、idrefsentity、entities、enumeration、notation、nmtoken、nmtokens、char、dateTime、dateTime.tz、string、uri、uuid**|**string**|CONVERT(nvarchar(4000), EmployeeID, 126)|  
|**decimal**|**fixed14.4**|**適用できません (XPath には、固定の 14.4 XDR データ型と等価なデータ型はありません)。**|CONVERT(money, EmployeeID)|  
|**date**|**date**|**string**|LEFT(CONVERT(nvarchar(4000), EmployeeID, 126), 10)|  
|**time**|**time**<br /><br /> **time.tz**|**string**|SUBSTRING(CONVERT(nvarchar(4000), EmployeeID, 126), 1 + CHARINDEX(N'T', CONVERT(nvarchar(4000), EmployeeID, 126)), 24)|  
  
  
