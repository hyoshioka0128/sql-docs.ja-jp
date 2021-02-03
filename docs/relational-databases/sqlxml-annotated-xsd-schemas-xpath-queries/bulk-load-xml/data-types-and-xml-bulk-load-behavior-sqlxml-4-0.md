---
title: データ型と XML 一括読み込みの動作 (SQLXML)
description: SQLXML 4.0 でのデータ型と XML 一括読み込みの動作について説明します。
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- bulk load [SQLXML], data types
- data types [SQLXML], XML Bulk Load
- XML Bulk Load [SQLXML], data types
ms.assetid: d1ac1939-1f6c-4398-b7a7-a79ca608a4f1
author: MightyPen
ms.author: genemi
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 3cbf84c0f5bf4f80bc3bf12d6dad91479888d595
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97462903"
---
# <a name="data-types-and-xml-bulk-load-behavior-sqlxml-40"></a>データ型と XML 一括読み込みの動作 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  マッピングスキーマで指定されているデータ型 (XSD または XDR 型および **sql: datatype**) は、通常は無視されます。ただし、次の場合は例外です。  
  
 XSD では、次の場合に注意してください。  
  
-   型が **dateTime** または **time** の場合は、データを Microsoft に送信する前に XML 一括読み込みでデータ変換が実行されるため、 **sql: datatype** を指定する必要があり [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ます。  
  
-   で **uniqueidentifier** 型の列に一括読み込みを行うときに、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] XSD 値が中かっこ ({および}) を含む GUID である場合、 **sql: datatype = "uniqueidentifier"** を指定して、列に値を挿入する前に中かっこを削除する必要があります。 **Sql: datatype** が指定されていない場合、値は中かっこで送信され、挿入は失敗します。  
  
 **Sql: datatype** の詳細については、「[データ型の強制型変換」および「Sql: DATATYPE 注釈 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-using/data-type-coercions-and-the-sql-datatype-annotation-sqlxml-4-0.md)」を参照してください。  
  
 XDR では、次の場合に注意してください。  
  
-   **Dt: type** が **datetime**、 **time**、 **dateTime.tz**、または **time.tz** の場合、XML 一括読み込みでは、データがに送信される前にデータ変換が実行されるため、 **dt: type** と **sql: datatype** の両方のデータ型を指定する必要があり [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ます。  
  
-   XML データの種類が **uuid** の場合は、 **sql: datatype** を指定する必要があります。データが文字列データの場合を除き、 **dt: type = "uuid"** も必要です。 **Dt: uuid** を指定しない場合、XML 一括読み込みでは、中かっこで囲まれた文字列が受け入れられます (必要な場合は削除されます)。  
  
-   XML データが **bin. base64** または **bin. hex** の場合は、 **dt: type** で xml データ型を指定する必要があります。 指定すると、XML 一括読み込みではデータを 16 進数表記として [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に読み込みます。  
  
  
