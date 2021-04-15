---
title: '&lt;xsd:redefine&gt; 要素 | Microsoft Docs'
description: W3C XSD の redefine 要素のサポートと、XML スキーマまたはそのコンポーネントを更新する方法について説明します。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: xml
ms.topic: conceptual
helpviewer_keywords:
- xsd:redefine element
ms.assetid: 5f3e9b65-f10e-4db2-a62c-b270ac11d04e
author: rothja
ms.author: jroth
ms.openlocfilehash: 6137e814b85ee3670bd10711962ba3cec47dd43e
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107487389"
---
# <a name="the-ltxsdredefinegt-element"></a>&lt;xsd:redefine&gt; 要素
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  W3C XSD の **redefine** 要素は、スキーマ コンポーネントの再定義をサポートします。 ただし、このディレクティブのサポートはパフォーマンスの点で高コストになる可能性があるうえ、再定義したスキーマに関連付けられた [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] xml **データ型のインスタンスはすべて** で再検証する必要があります。 このため、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ではこれらの要素をサポートしません。 **\<xsd:redefine>** 要素を含む XML スキーマは、サーバーに拒否されます。  
  
 スキーマまたはスキーマ コンポーネントを更新するには、代わりに次の方法を使用できます。  
  
1.  変更されたスキーマ コンポーネントを使用して新しい XML スキーマ コレクションを作成します。  
  
2.  再定義する XML スキーマ コレクションを使用しているすべての **xml** データ型 (XML DT) の型を再指定して、新しい XML スキーマ コレクションを使用するようにします。 そのためには、ALTER TABLE コマンドの ALTER COLUMN オプションを使用して列の型を再指定するか、変数やパラメーターの XML スキーマ コレクション制約を変更します。  
  
3.  古いバージョンの XML スキーマ コレクションを削除します。  

## <a name="see-also"></a>参照  
 [サーバー上の XML スキーマ コレクションの要件と制限](../../relational-databases/xml/requirements-and-limitations-for-xml-schema-collections-on-the-server.md)  
  
  
