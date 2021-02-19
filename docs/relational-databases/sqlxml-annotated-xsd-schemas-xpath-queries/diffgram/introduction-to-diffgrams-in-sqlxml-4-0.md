---
title: SQLXML 4.0 の DiffGram の概要
description: SQLXML 4.0 での Diffgram の形式、注釈、および処理ロジックについて説明します。
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- annotations [SQLXML]
- DiffGrams [SQLXML], about DiffGrams
ms.assetid: 1902d67f-baf3-46e6-a36c-b24b5ba6f8ea
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 66a58dfb19cf8f53f775bac663d5ba3e6147711b
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97414985"
---
# <a name="introduction-to-diffgrams-in-sqlxml-40"></a>SQLXML 4.0 の DiffGram の概要
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  ここでは、DiffGram の概要を説明します。  
  
## <a name="diffgram-format"></a>DiffGram 形式  
 一般的な DiffGram の書式は次のとおりです。  
  
```  
<?xml version="1.0"?>  
<diffgr:diffgram   
         xmlns:msdata="urn:schemas-microsoft-com:xml-msdata"  
         xmlns:diffgr="urn:schemas-microsoft-com:xml-diffgram-v1"  
         xmlns:xsd="http://www.w3.org/2001/XMLSchema">  
   <DataInstance>  
      ...  
   </DataInstance>  
   [<diffgr:before>  
        ...  
   </diffgr:before>]  
  
   [<diffgr:errors>  
        ...  
   </diffgr:errors>]  
</diffgr:diffgram>  
```  
  
 DiffGram の書式は次のブロックで構成されています。  
  
 **\<DataInstance>**  
 この要素の名前 **DataInstance** は、このドキュメントでの説明用に使用するものです。 たとえば、.NET Framework のデータセットから DiffGram が生成された場合、この要素の名前としてデータセットの **name** プロパティの値が使用されます。 このブロックには、変更後のすべての関連データを指定します。変更されていないデータも指定できます。 DiffGram 処理ロジックでは、次のブロック内の要素は無視されます。このブロックは、次の **よう** な属性が指定されていません。  
  
 **\<diffgr:before>**  
 省略可能なブロックです。更新または削除する必要がある元のレコード インスタンス (要素) を指定します。 DiffGram によって変更 (更新または削除) されるすべてのデータベーステーブルは、ブロック内の最上位レベルの要素として表示される必要があり **\<before>** ます。  
  
 **\<diffgr:errors>**  
 省略可能なブロックです。DiffGram の処理ロジックでは無視されます。  
  
## <a name="diffgram-annotations"></a>DiffGram 注釈  
 これらの注釈は、DiffGram 名前空間 **"urn: schema-01"** で定義されています。  
  
 **id**  
 この属性は、とブロックの要素をペアにするために使用され **\<before>** **\<DataInstance>** ます。  
  
 **hasChanges**  
 挿入操作または更新操作の場合、DiffGram では、 **挿入** または **変更** された値を使用してこの属性を指定する必要があります。 この属性が存在しない場合、内の対応する要素 **\<DataInstance>** は処理ロジックによって無視され、更新は実行されません。 実際のサンプルについては、「 [DiffGram の例 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/diffgram/diffgram-examples-sqlxml-4-0.md)」を参照してください。  
  
 **parentID**  
 この属性は、DiffGram の要素間の親子リレーションシップを指定するときに使用します。 この属性はブロックにのみ表示され \<before> ます。 この属性は更新の適用時に SQLXML で使用されます。 親子リレーションシップは、DiffGram の要素の処理順序を決定するために使用されます。  
  
## <a name="understanding-the-diffgram-processing-logic"></a>DiffGram の処理ロジックについて  
 DiffGram の処理ロジックでは、一定の規則に従い、挿入、更新、削除のうちどの操作であるかが判断されます。 次の表は、この規則についてまとめたものです。  
  
|操作|説明|  
|---------------|-----------------|  
|挿入|DiffGram は、要素がブロック内に存在 **\<DataInstance>** しても対応するブロックに含まれておらず、 **\<before>** 要素に対して "、" と **いう** 属性が指定されている (**diffgram gr: haschanges = inserted**) 場合に挿入操作を示します。 この場合、DiffGram はブロックで指定されたレコードインスタンスを **\<DataInstance>** データベースに挿入します。<br /><br /> 次の **よう** に、属性が指定されていない場合、要素は処理ロジックによって無視され、挿入は実行されません。 実際のサンプルについては、「 [DiffGram の例 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/diffgram/diffgram-examples-sqlxml-4-0.md)」を参照してください。|  
|更新|DiffGram は、ブロック内に対応する要素が存在する (つまり、両方の要素の値が同じである) 要素がブロック内に存在 \<before> **\<DataInstance>** し 、ブロック内の要素で **変更** された値を使用し **て、すべて** の要素の属性が指定されている場合に、更新操作を示し **\<DataInstance>** ます。<br /><br /> ブロック内の要素で、"次の値に **変更** する" 属性が指定されていない場合 **\<DataInstance>** 、処理ロジックによってエラーが返されます。 実際のサンプルについては、「 [DiffGram の例 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/diffgram/diffgram-examples-sqlxml-4-0.md)」を参照してください。<br /><br /> 場合 **、** このブロックで指定されている **\<before>** 要素の親子リレーションシップ **は、** レコードを更新する順序を決定するために使用されます。|  
|削除|DiffGram は、要素が **\<before>** ブロックにあり、対応するブロックには存在しない場合に、削除操作を示し **\<DataInstance>** ます。 この場合、DiffGram は、ブロックで指定されたレコードインスタンスを **\<before>** データベースから削除します。 実際のサンプルについては、「 [DiffGram の例 &#40;SQLXML 4.0&#41;](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/diffgram/diffgram-examples-sqlxml-4-0.md)」を参照してください。<br /><br /> 場合 **、** このブロックで指定されている **\<before>** 要素の親子リレーションシップ **は、** レコードを削除する順序を決定するために使用されます。|  
  
> [!NOTE]  
>  DiffGram にパラメーターを渡すことはできません。  
  
  
