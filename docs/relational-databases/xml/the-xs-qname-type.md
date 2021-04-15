---
title: Xs:QName 型 | Microsoft Docs
description: XML スキーマ制約要素として、または共用体のメンバー型として xs:QName 型を使用する方法について学習します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: xml
ms.topic: conceptual
helpviewer_keywords:
- xs:QName type
ms.assetid: 72c5bfde-b0b2-4372-bf36-97ba930dea06
author: rothja
ms.author: jroth
ms.openlocfilehash: c620a1134795cfede3d706ae5e624d2e6ddbb67e
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107487402"
---
# <a name="the-xsqname-type"></a>The xs:QName Type
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、XML スキーマ制約要素を使用する **xs:QName** から派生した型はサポートしません。 また、現在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、メンバー型に **QName** を指定した共用体型をサポートしません。  
  
## <a name="example"></a>例  
 次の `CREATE XML SCHEMA COLLECTION` ステートメントでは、共用体のメンバー型に `xs:QName` 型を指定しているため、XML スキーマを読み込めません。  
  
```  
CREATE XML SCHEMA COLLECTION QNameLimitation1 AS N'  
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">  
    <xs:simpleType name="myUnion">  
        <xs:union memberTypes="xs:int xs:QName"/>  
    </xs:simpleType>  
</xs:schema>'  
GO  
  
CREATE XML SCHEMA COLLECTION QNameLimitation2 AS N'  
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">  
    <xs:simpleType name="myUnion">  
        <xs:union memberTypes="xs:integer">  
   <xs:simpleType>  
    <xs:list itemType="xs:QName"/>  
   </xs:simpleType>  
  </xs:union>  
    </xs:simpleType>  
</xs:schema>'  
GO  
```  
  
 これらのステートメントはどちらも、エラーが発生して失敗します。  
  
## <a name="see-also"></a>参照  
 [サーバー上の XML スキーマ コレクションの要件と制限](../../relational-databases/xml/requirements-and-limitations-for-xml-schema-collections-on-the-server.md)  
  
  
