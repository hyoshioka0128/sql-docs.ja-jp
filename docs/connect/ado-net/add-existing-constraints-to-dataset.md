---
title: DataSet に既存の制約を追加する
description: DataSet に既存の制約を追加する方法について説明します。
ms.date: 11/30/2020
dev_langs:
- csharp
ms.assetid: 307d2809-208b-4cf8-b6a9-5d16f15fc16c
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 9471f62c36604cb01ea78f558ac3bfbcb7f4bc01
ms.sourcegitcommit: c127c0752e84cccd38a7e23ac74c0362a40f952e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96772342"
---
# <a name="add-existing-constraints-to-a-dataset"></a>DataSet に既存の制約を追加する

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

<xref:Microsoft.Data.SqlClient.SqlDataAdapter> の <xref:System.Data.Common.DbDataAdapter.Fill%2A> メソッドを使うと、<xref:System.Data.DataSet> にデータ ソースからのテーブルの列および行だけが格納されます。制約は一般にデータ ソースで設定されますが、既定では **Fill** メソッドによって **DataSet** にこのスキーマ情報は追加されません。

データ ソースからの既存の主キー制約情報を **DataSet** に設定するには、**DataAdapter** の <xref:System.Data.Common.DbDataAdapter.FillSchema%2A> メソッドを呼び出すか、または **Fill** を呼び出す前に **DataAdapter** の <xref:System.Data.Common.DataAdapter.MissingSchemaAction%2A> プロパティを **AddWithKey** に設定します。 これにより、データ ソースの主キー制約が **DataSet** の主キー制約に反映されます。

> [!NOTE]
> 外部キー制約情報はインクルードされないため、明示的に作成する必要があります。

データを格納する前に **DataSet** にスキーマ情報を追加すると、**DataSet** 内の <xref:System.Data.DataTable> オブジェクトに主キー制約が含まれるようになります。 その結果、**DataSet** に対して格納を行う追加の呼び出しを行うと、主キー列の情報を使用して、データ ソースからの新しい行と、各 **DataTable** の現在の行が照合されて、テーブルの現在のデータがデータ ソースのデータで上書きされます。 スキーマ情報がないと、**DataSet** にデータ ソースからの新しい行が付け加えられ、重複行が発生します。

> [!NOTE]
> データ ソースの列が自動インクリメントとして指定されている場合、<xref:System.Data.Common.DbDataAdapter.FillSchema%2A> メソッドまたは <xref:System.Data.Common.DataAdapter.MissingSchemaAction%2A> が **AddWithKey** に設定された <xref:System.Data.Common.DbDataAdapter.Fill%2A> メソッドでは、**AutoIncrement** プロパティが `true` に設定された **DataColumn** が作成されます。 ただし、**AutoIncrementStep** 値と **AutoIncrementSeed** 値は開発者自身が明示的に設定する必要があります。

> [!NOTE]
> **FillSchema** を使用したり、**MissingSchemaAction** を **AddWithKey** に設定したりすると、データ ソースで主キー列情報を確認するための追加の処理が必要になります。 この追加の処理によりパフォーマンスが低下する場合があります。 デザイン時に主キー情報がわかっている場合は、最適のパフォーマンスを得るために主キー列 (複数の場合もある) を明示的に指定することをお勧めします。

次のコード例では、<xref:System.Data.Common.DbDataAdapter.FillSchema%2A> を使用して **DataSet** にスキーマ情報を追加する方法を示します。

[!code-csharp[SqlDataAdapter_FillDataSet#1](~/../sqlclient/doc/samples/SqlDataAdapter_FillDataSet.cs#1)]

<xref:System.Data.Common.DataAdapter.MissingSchemaAction%2A> プロパティと <xref:System.Data.Common.DbDataAdapter.Fill%2A> メソッドを使用して、**DataSet** にスキーマ情報を追加する方法を次のコード サンプルに示します。

[!code-csharp[SqlDataAdapter_FillDataSet#2](~/../sqlclient/doc/samples/SqlDataAdapter_FillDataSet.cs#2)]

## <a name="handling-multiple-result-sets"></a>複数の結果セットの処理

**DataAdapter** が <xref:Microsoft.Data.SqlClient.SqlDataAdapter.SelectCommand%2A> から返された複数の結果セットに一致する場合、**DataSet** に複数のテーブルが作成されます。 テーブルには、**Table** *N* という既定の名前が設定されます。N は 0 から始まりインクリメントされますが、"Table0" ではなく **Table** から始まります。 テーブル名が <xref:System.Data.Common.DbDataAdapter.FillSchema%2A> メソッドに引数として渡されると、0 からから始まるインクリメンタル名 **TableName** *N* が割り当てられます。ここでは、"TableName0" ではなく **TableName** から始まります。

## <a name="see-also"></a>関連項目

- [DataAdapter と DataReader](dataadapters-datareaders.md)
- [ADO.NET でのデータの取得と変更](retrieving-modifying-data.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
