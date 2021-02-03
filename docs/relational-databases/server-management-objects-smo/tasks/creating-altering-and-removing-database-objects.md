---
description: データベース オブジェクトの作成、変更、および削除
title: データベース オブジェクトでの作業
ms.custom: seo-dt-2019
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
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: f2ae7dbbfc762ed342ddc085afcb799f9b292966
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97468503"
---
# <a name="creating-altering-and-removing-database-objects"></a>データベース オブジェクトの作成、変更、および削除
[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  SMO オブジェクトの作成には次の段階があります。  
  
1.  オブジェクトのインスタンスの作成。  
  
2.  オブジェクト プロパティの設定。  
  
3.  子オブジェクトのインスタンスの作成。  
  
4.  子オブジェクト プロパティの設定。  
  
5.  オブジェクトの作成。  

 Smo オブジェクトのインスタンスは、SMO アプリケーションで作成された場合、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] **Create** メソッドが発行されるまで、のインスタンスには存在しません。 ただし、個別のオブジェクトごとに **Create** メソッドを発行する必要はありません。 オブジェクトに子オブジェクトのセットがある場合、 **Create** メソッドを実行するには親オブジェクトのみが必要です。 たとえば、テーブルの定義には、テーブルに含まれている列が 1 つ以上必要になります。 一方、テーブルがなくては列が独立して存在することはできません。 テーブルとテーブルの列の間には共存関係があります。  
  
 <xref:Microsoft.SqlServer.Management.Dmf.Policy.Alter%2A> メソッドを使用すると、オブジェクトへの変更を行うことができます。 オブジェクト コレクションの 1 つへの子オブジェクトの追加や、プロパティ値の変更など、1 つのオブジェクトに対する複数の変更は、バッチ化されて 1 つの変更として実行されます。 **Alter** メソッドを行うと、ネットワークトラフィックが減少し、全体的なパフォーマンスが向上します。  
  
 **Drop** ステートメントを使用して、オブジェクトと、オブジェクトを最初に作成するために必要だったすべての共存子オブジェクトを削除します。  
  
## <a name="see-also"></a>参照  
 [SMO オブジェクト モデル](../../../relational-databases/server-management-objects-smo/smo-object-model.md)  
  
  
