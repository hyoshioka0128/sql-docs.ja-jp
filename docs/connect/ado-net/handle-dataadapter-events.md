---
title: DataAdapter イベントを処理する
description: DataAdapter イベントとその使用方法について説明します。
ms.date: 11/30/2020
dev_langs:
- csharp
ms.assetid: 11515b25-ee49-4b1d-9294-a142147c1ec5
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: e3715f2060857bf6d5fabb37c049d6e9cff1ee40
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559214"
---
# <a name="handle-dataadapter-events"></a>DataAdapter イベントを処理する

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

Microsoft SqlClient data provider for SQL Server <xref:Microsoft.Data.SqlClient.SqlDataAdapter> では、データ ソースで行われたデータの変更に対応するために使用できる 3 つのイベントが公開されています。 `DataAdapter` のイベントを次の表に示します。

|event|説明|  
|-----------|-----------------|  
|`RowUpdating`|行に対する UPDATE、INSERT、または DELETE の各操作が (`Update` メソッドの 1 つの呼び出しによって) 開始しようとしています。|  
|`RowUpdated`|行に対する UPDATE、INSERT、DELETE の各操作が (`Update` メソッドの 1 つの呼び出しによって) 完了しました。|  
|`FillError`|`Fill` 操作中にエラーが発生しました。|  

## <a name="rowupdating-and-rowupdated-events"></a>RowUpdating と RowUpdated イベント

`RowUpdating` は、<xref:System.Data.DataSet> 側で生じた行に対する更新が、データ ソース側で処理される前に発生します。 `RowUpdated` は、`DataSet` 側で生じた行に対する更新が、データ ソース側で処理された後で発生します。 したがって、更新が始まる前に `RowUpdating` を使用して更新の動作を変更することで、更新発生時に行う追加の処理の提供、更新行への参照の保存、現在の更新のキャンセル、後で処理するバッチ処理のための更新スケジュールなどを提供できます。 `RowUpdated` は、更新中に発生するエラーや例外の応答に便利です。 `DataSet` には、**エラー情報** や **再試行ロジック** などを追加できます。

`RowUpdating` イベントおよび `RowUpdated` イベントに渡される <xref:System.Data.Common.RowUpdatingEventArgs> 引数および <xref:System.Data.Common.RowUpdatedEventArgs> 引数には、更新を実行するために使用される `Command` オブジェクトを参照する `Command` プロパティ、更新情報を格納する `DataRow` オブジェクトを参照する `Row` プロパティ、どのタイプの更新を実行するかを示す `StatementType` プロパティ、適用可能な場合は `TableMapping`、および、操作の `Status` などがあります。

`Status` プロパティを使用すると、操作中にエラーが発生したかどうかを確認したり、必要に応じて現在の行および結果行に対するアクションを制御したりできます。 イベントが発生すると、`Status` プロパティは `Continue` または `ErrorsOccurred` のいずれかになります。 次の表では、更新の後続のアクションを制御するために `Status` プロパティに設定できる値を示しています。

|Status|説明|  
|------------|-----------------|  
|`Continue`|更新操作を続行します。|  
|`ErrorsOccurred`|更新操作を中止し、例外をスローします。|  
|`SkipCurrentRow`|現在の行を無視し、更新操作を続行します。|  
|`SkipAllRemainingRows`|更新操作を中止しますが、例外はスローしません。|  

`Status` プロパティを `ErrorsOccurred` に設定すると、例外がスローされます。 `Errors` プロパティを例外として設定することで、どの例外をスローするかを制御できます。 `Status` に他の値を使用すると、例外はスローされません。

`ContinueUpdateOnError` プロパティを使用して更新行に関するエラーを処理することもできます。 `DataAdapter.ContinueUpdateOnError` を `true` に設定すると、行を更新した結果、例外がスローされようとしているときに、例外のテキストをその行の `RowError` 情報の中に格納し、例外をスローせずに処理を続行できます。 これにより、`Update` が完了した時点でエラーに応答できるようになります。これに対して `RowUpdated` イベントを使用すると、エラーが発生した時点でエラーに応答できます。

イベント ハンドラーを追加および削除する方法を次のコード サンプルに示します。 `RowUpdating` イベント ハンドラーは、削除されたすべてのレコードのログをタイムスタンプと共に記録します。 `RowUpdated` イベント ハンドラーでは、`DataSet` の行の `RowError` プロパティにエラー情報を追加し、例外をスローせずに処理を続行します (`ContinueUpdateOnError` = `true` の場合と同等の動作です)。

[!code-csharp[SqlDataAdapter_Events#1](~/../sqlclient/doc/samples/SqlDataAdapter_Events.cs#1)]

## <a name="fillerror-event"></a>FillError イベント

`DataAdapter` は、`FillError` 操作中にエラーが発生すると `Fill` イベントを発行します。 この種類のエラーは、通常、追加する行のデータを、精度を損なうことなく .NET 型に変換できない場合に発生します。

`Fill` 操作中にエラーが発生した場合、現在の行は `DataTable` に追加されません。 `FillError` イベントを使用すると、エラーを解決してその行を追加するか、または除外された行を無視し、`Fill` 操作を続行できます。

<xref:System.Data.FillErrorEventArgs> イベントに渡す `FillError` には、エラーに応答してエラーを解決できるいくつかのプロパティを含めることができます。 `FillErrorEventArgs` オブジェクトのプロパティを次の表に示します。

|プロパティ|説明|  
|--------------|-----------------|  
|`Errors`|発生した `Exception`。|  
|`DataTable`|エラー発生時にデータを格納しようとしていた `DataTable` オブジェクト。|  
|`Values`|エラー発生時に追加しようとしていた行の値を保持しているオブジェクトの配列。 `Values` 配列の序数参照は、追加しようとしていた行の列の序数参照に対応します。 たとえば、`Values[0]` は、行の第 1 列として追加しようとした値です。|  
|`Continue`|例外をスローするかどうかを選択できます。 `Continue` プロパティを `false` に設定すると、エラーが発生したときに現在の `Fill` 操作を停止し、例外をスローします。 `Continue` を `true` に設定すると、エラーに関係なく `Fill` 操作を続行します。|  

次のコード例では、`FillError` の `DataAdapter` イベントにイベント ハンドラーを追加しています。 この `FillError` イベント コードの例は、有効桁の消失が発生したかどうかを確認し、例外に応答する機会を与えます。

[!code-csharp[SqlDataAdapter_Events#2](~/../sqlclient/doc/samples/SqlDataAdapter_Events.cs#2)]

## <a name="see-also"></a>関連項目

- [DataAdapter と DataReader](dataadapters-datareaders.md)
- [イベント](/dotnet/standard/events/index)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
