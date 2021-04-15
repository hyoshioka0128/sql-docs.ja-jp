---
title: SqlClient の内部再試行ロジック プロバイダー
description: アプリケーションで組み込みの構成可能な再試行ロジック プロバイダーを使用して、データベースに対する一時的なエラーを処理する方法について説明します。
ms.date: 03/22/2021
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-deshtehari
ms.openlocfilehash: 98a601b0d61f79bed6a802b86caecfabd16f75a6
ms.sourcegitcommit: d8cbbeffa3faa110e02056ff97dc7102b400ffb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107004008"
---
# <a name="internal-retry-logic-providers-in-sqlclient"></a>SqlClient の内部再試行ロジック プロバイダー

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

最も一般的な再試行パターンについては、組み込みの内部再試行ロジック プロバイダーが実装されています。 この再試行プロバイダーを使用するには、次の `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory` 静的メソッドを使用します。

- `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory.CreateFixedRetryProvider`
- `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory.CreateIncrementalRetryProvider`
- `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory.CreateExponentialRetryProvider`
- `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory.CreateNoneRetryProvider`

> [!NOTE]
> すべての内部再試行プロバイダーは、各再試行までの間隔のギャップ時間を多少ランダム化します。 このランダム化により、複数のクライアントが同じ構成で接続しようとしたとき、またはコマンドを実行しようとしたときに、データベースに同時にアクセスすることを回避できます。

> [!WARNING]
> 内部再試行プロバイダーは、開いているトランザクションで実行されるコマンドの再試行をサポートしません。 この操作は、再試行ロジックを使用しないで実行されます。 この動作は、カスタムの再試行ロジックを使用してオーバーライドできます。 詳細については、「[SqlClient の構成可能な再試行ロジックのコア API](configurable-retry-logic-core-apis-sqlclient.md)」を参照してください。

<!-- These links won't be live until after the feature is released in a GA version.
## Example

You can find samples for `connection` and `command` retry logic at the following links:

- [Microsoft.Data.SqlClient.SqlConnection.RetryLogicProvider#example](/dotnet/api/microsoft.data.sqlclient.sqlconnection.RetryLogicProvider?view=sqlclient-dotnet-core-2.1&preserve-view=true#examples)
- [Microsoft.Data.SqlClient.SqlCommand.RetryLogicProvider#example](/dotnet/api/microsoft.data.sqlclient.sqlcommand.RetryLogicProvider?view=sqlclient-dotnet-core-2.1&preserve-view=true#examples)
-->

## <a name="see-also"></a>関連項目

- [SqlClient の構成可能な再試行ロジックのコア API](configurable-retry-logic-core-apis-sqlclient.md)
- [SqlClient の構成可能な再試行ロジック](configurable-retry-logic.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
