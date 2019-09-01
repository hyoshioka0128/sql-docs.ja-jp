---
title: データベースオブジェクトの操作 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- database objects [SMO]
- objects [SMO]
ms.assetid: 702fd63d-8734-4a02-872e-aecfb037c787
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: db9333ec36ce9993ff0b8e3199ce5288a945d5e7
ms.sourcegitcommit: f3f83ef95399d1570851cd1360dc2f072736bef6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "70148529"
---
# <a name="creating-altering-and-removing-database-objects"></a>データベース オブジェクトの作成、変更、および削除
[!INCLUDE[appliesto-ss-asdb-asdw-xxx-md](../../../includes/appliesto-ss-asdb-asdw-xxx-md.md)]

  SMO オブジェクトの作成には次の段階があります。  
  
1.  オブジェクトのインスタンスの作成。  
  
2.  オブジェクト プロパティの設定。  
  
3.  子オブジェクトのインスタンスの作成。  
  
4.  子オブジェクト プロパティの設定。  
  
5.  オブジェクトの作成。  

[!INCLUDE[freshInclude](../../../includes/paragraph-content/fresh-note-steps-feedback.md)]

 Smo オブジェクトのインスタンスは、smo アプリケーションで作成された場合、 **Create**メソッドが発行さ[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]れるまで、のインスタンスには存在しません。 ただし、個別のオブジェクトごとに**Create**メソッドを発行する必要はありません。 オブジェクトに子オブジェクトのセットがある場合、 **Create**メソッドを実行するには親オブジェクトのみが必要です。 たとえば、テーブルの定義には、テーブルに含まれている列が 1 つ以上必要になります。 一方、テーブルがなくては列が独立して存在することはできません。 テーブルとテーブルの列の間には共存関係があります。  
  
 <xref:Microsoft.SqlServer.Management.Dmf.Policy.Alter%2A> メソッドを使用すると、オブジェクトへの変更を行うことができます。 オブジェクト コレクションの 1 つへの子オブジェクトの追加や、プロパティ値の変更など、1 つのオブジェクトに対する複数の変更は、バッチ化されて 1 つの変更として実行されます。 **Alter**メソッドを行うと、ネットワークトラフィックが減少し、全体的なパフォーマンスが向上します。  
  
 **Drop**ステートメントを使用して、オブジェクトと、オブジェクトを最初に作成するために必要だったすべての共存子オブジェクトを削除します。  
  
## <a name="see-also"></a>関連項目  
 [SMO オブジェクト モデル](../../../relational-databases/server-management-objects-smo/smo-object-model.md)  
  
  
