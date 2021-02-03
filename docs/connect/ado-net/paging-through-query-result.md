---
title: クエリ結果のページング
description: クエリの結果をデータ ページとして表示する例を示します。
ms.date: 11/30/2020
dev_langs:
- csharp
ms.assetid: fa360c46-e5f8-411e-a711-46997771133d
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 2de305e1069964f2d1a67b7f201578c7448d3e09
ms.sourcegitcommit: c127c0752e84cccd38a7e23ac74c0362a40f952e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96772218"
---
# <a name="paging-through-a-query-result"></a>クエリ結果のページング

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

クエリ結果のページングとは、クエリ結果をデータの小さなサブセット、つまりページに分けて返すプロセスです。 クエリ結果のページングは、結果を管理しやすい小さな単位でユーザーに表示するために行われる一般的な処理です。

<xref:Microsoft.Data.SqlClient.SqlDataAdapter> には、<xref:System.Data.Common.DbDataAdapter.Fill%2A> メソッドのオーバーロードを通じて、1 ページ分のデータだけを返す機能が用意されています。 しかしこれは大きなクエリ結果のページングには適していません。**DataAdapter** が目的の <xref:System.Data.DataTable> または <xref:System.Data.DataSet> に、要求されたレコードだけを格納する一方で、クエリ全体を返すためのリソースが使用されるためです。

クエリ全体を返す必要があるリソースを使用せずにデータ ソースから 1 ページ分のデータを返すには、必要な行だけ返すように限定する抽出条件をクエリに追加します。

<xref:System.Data.Common.DbDataAdapter.Fill%2A> メソッドを使用して 1 ページ分のデータを返すには、データ ページの先頭レコードを指定する **startRecord** パラメーターおよびデータ ページのレコード数を指定する **maxRecords** パラメーターを指定します。

## <a name="example"></a>例

<xref:System.Data.Common.DbDataAdapter.Fill%2A> メソッドを使用してクエリ結果の最初のページ (ページ サイズは 5 レコード) を返す方法のコード例を次に示します。

[!code-csharp[SqlDataAdapter_Paging#1](~/../sqlclient/doc/samples/SqlDataAdapter_Paging.cs#1)]

上の例では、**DataSet** に 5 つのレコードだけが格納されますが、**Orders** テーブル全体が返されます。 **DataSet** にこれと同じ 5 つのレコードを格納し、5 つのレコードだけを返すには、次のコード サンプルに示すように SQL ステートメントで `TOP` 句と `WHERE` 句を使用します。

[!code-csharp[SqlDataAdapter_Paging#2](~/../sqlclient/doc/samples/SqlDataAdapter_Paging.cs#2)]

> [!NOTE]
> この方法でクエリの結果をページングする場合は、次のコード例に示すように、レコードの次のページを返すコマンドに一意の ID を渡すために、行を順序付けする `unique identifier` を保持する必要があります。

[!code-csharp[SqlDataAdapter_Paging#4](~/../sqlclient/doc/samples/SqlDataAdapter_Paging.cs#4)]

**startRecord** および **maxRecords** のパラメーターを受け取る **Fill** メソッドのオーバーロードを使用して `next page of records` を返すには、現在のレコード インデックスをページ サイズの分だけインクリメントし、テーブルにレコード ページを格納します。

> [!NOTE]
> **DataSet** に 1 ページ分のレコードだけを追加する場合でも、データベース サーバーはクエリ結果全体を返すことに注意してください。

次のデータ ページを格納する前にテーブル行をクリアするコード サンプルを次に示します。 データベース サーバーとのやり取りを減らすために、ローカルのキャッシュに、返された一定数の行を保存することもできます。

[!code-csharp[SqlDataAdapter_Paging#3](~/../sqlclient/doc/samples/SqlDataAdapter_Paging.cs#3)]

データベース サーバーによってクエリ全体を返さずに次のレコード ページを返すには、SELECT ステートメントに限定的な抽出条件を指定します。 上の例では最後に返されたレコードが保存されますが、次のコード サンプルに示すようにそのレコードを WHERE 句で使用するとクエリの開始点を指定できます。

[!code-csharp[SqlDataAdapter_Paging#5](~/../sqlclient/doc/samples/SqlDataAdapter_Paging.cs#5)]

## <a name="see-also"></a>関連項目

- [DataAdapter と DataReader](dataadapters-datareaders.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
