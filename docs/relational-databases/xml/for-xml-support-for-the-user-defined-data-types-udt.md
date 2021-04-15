---
title: ユーザー定義データ型 (UDT) の FOR XML サポート | Microsoft Docs
description: FOR XML 句を使用するときのユーザー定義データ型 (UDT) のサポートについて学習します。
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: xml
ms.topic: conceptual
helpviewer_keywords:
- UDTs [SQL Server], XML
- user-defined types [SQL Server], XML
ms.assetid: 354e2150-fa2a-4583-b1aa-6b78ae4378b6
author: rothja
ms.author: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d0f048cedaa31d171162fa44139ed80bed1fea0a
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107488169"
---
# <a name="for-xml-support-for-the-user-defined-data-types-udt"></a>ユーザー定義データ型 (UDT) の FOR XML サポート
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  FOR XML は、共通言語ランタイム (CLR) のユーザー定義データ型 (UDT) をサポートしていません。  
  
 CLR のユーザー定義データ型で FOR XML を使用するには、そのデータ型で XML シリアル化を指定して、FOR XML SELECT 句で XML への明示的なキャストを使用します。  
  
## <a name="see-also"></a>参照  
 [各種 SQL Server データ型の FOR XML サポート](../../relational-databases/xml/for-xml-support-for-various-sql-server-data-types.md)  
  
  
