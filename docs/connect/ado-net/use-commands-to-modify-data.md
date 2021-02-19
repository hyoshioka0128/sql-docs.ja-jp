---
title: コマンドを使用したデータ変更
description: データ プロバイダーを使用して、ストアド プロシージャまたはデータ定義言語 (DDL) のステートメントを実行する方法について説明します。
ms.date: 11/25/2020
ms.assetid: f4160389-b9ff-4b74-b655-437c76dcd586
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 98127e41b5b07c38030ef27214c9c92bf7c4b4be
ms.sourcegitcommit: c127c0752e84cccd38a7e23ac74c0362a40f952e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761480"
---
# <a name="using-commands-to-modify-data"></a>コマンドを使用したデータ変更

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

Microsoft SqlClient Data Provider for SQL Server を使用すると、ストアド プロシージャやデータ定義言語ステートメント (たとえば CREATE TABLE や ALTER COLUMN など) を実行して、データベースまたはカタログに対してスキーマ操作を実行できます。 これらのコマンドはクエリとは異なり、行を返さないため、<xref:Microsoft.Data.SqlClient.SqlCommand> オブジェクトではこれらを処理するために <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteNonQuery%2A> が提供されています。

**ExecuteNonQuery** メソッドを使用すると、スキーマを変更できるだけでなく、データを変更するが行を返さない INSERT、UPDATE、DELETE などの SQL ステートメントも処理できます。

**ExecuteNonQuery** メソッドでは行は返されませんが、**Command** オブジェクトの **Parameters** コレクションを通じて入力パラメーター、出力パラメーター、および戻り値を受け渡しできます。

## <a name="in-this-section"></a>このセクションの内容

[データ ソースのデータの更新](update-data-inside-data-source.md)  
データベース内のデータを変更するコマンドまたはストアド プロシージャを実行する方法について説明します。

[カタログ操作の実行](perform-catalog-operations.md)  
データベース スキーマを変更するコマンドを実行する方法について説明します。

## <a name="see-also"></a>関連項目

- [ADO.NET でのデータの取得と変更](retrieving-modifying-data.md)
- [コマンドとパラメーター](commands-parameters.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
