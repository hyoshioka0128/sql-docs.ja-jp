---
title: SqlClient の構成可能な再試行ロジックの概要
description: Microsoft.Data.SqlClient の構成可能な再試行ロジックのさまざまな側面と、一時的なエラーに対するアプリケーションの回復力を高める方法について説明します。
ms.date: 03/22/2021
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-deshtehari
ms.openlocfilehash: 6018dc69c0fa6733b1482a8bdaa75d7cfaa4cb71
ms.sourcegitcommit: d8cbbeffa3faa110e02056ff97dc7102b400ffb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107004011"
---
# <a name="configurable-retry-logic-in-sqlclient-introduction"></a>SqlClient の構成可能な再試行ロジックの概要

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

構成可能な再試行ロジックにより、開発者および管理者は、一時的な障害が発生したときにアプリケーションの動作を管理できます。 この機能は、接続時またはコマンドの実行時の制御を高めます。 制御は、コードまたはアプリケーション構成ファイルを使用して定義することができます。 一時的なエラーの番号と再試行プロパティを定義して、再試行の動作を制御できます。 また、正規表現を使用して、特定の SQL ステートメントをフィルター処理することもできます。

## <a name="feature-components"></a>機能コンポーネント

この機能は、次の 3 つの主要コンポーネントで構成されます。

1. **コア API**: 開発者は、これらのインターフェイスを使用して、<xref:Microsoft.Data.SqlClient.SqlConnection> および <xref:Microsoft.Data.SqlClient.SqlCommand> オブジェクトに独自の再試行ロジックを実装できます。 詳細については、「[SqlClient の構成可能な再試行ロジックのコア API](configurable-retry-logic-core-apis-sqlclient.md)」を参照してください。
2. **事前定義済みの構成可能な再試行ロジック**: コア API を使用する組み込みの再試行ロジック メソッドは、`Microsoft.Data.SqlClient.SqlConfigurableRetryFactory` クラスからアクセスできます。 詳細については、「[SqlClient の内部再試行ロジック プロバイダー](internal-retry-logic-providers-sqlclient.md)」を参照してください。
3. **構成ファイル スキーマ**: アプリケーション内の <xref:Microsoft.Data.SqlClient.SqlConnection> および <xref:Microsoft.Data.SqlClient.SqlCommand> の既定の再試行ロジックを指定します。 詳細については、「[SqlClient での構成可能な再試行ロジック構成ファイル](configurable-retry-logic-config-file-sqlclient.md)」を参照してください。

## <a name="quick-start"></a>クイック スタート

この機能を使用するには、次の 4 つの手順に従います。

1. プレビュー バージョンで安全スイッチを有効にします。 AppContext 安全スイッチを有効にする方法については、「[構成可能な再試行ロジックを有効にする](appcontext-switches.md#enable-configurable-retry-logic)」を参照してください。

2. `Microsoft.Data.SqlClient.SqlRetryLogicOption` を使用して、再試行ロジック オプションを定義します。  
次のサンプルでは、一部の再試行パラメーターが設定されており、残りのパラメーターでは既定値が使用されます。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_OpenConnection#1](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_OpenConnection.cs#1)]

3. `Microsoft.Data.SqlClient.SqlRetryLogicOption` オブジェクトを使用して、再試行ロジック プロバイダーを作成します。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_OpenConnection#2](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_OpenConnection.cs#2)]

4. `Microsoft.Data.SqlClient.SqlRetryLogicBaseProvider` インスタンスを `Microsoft.Data.SqlClient.SqlConnection.RetryLogicProvider` または `Microsoft.Data.SqlClient.SqlCommand.RetryLogicProvider` に割り当てます。  
次のサンプルでは、`Microsoft.Data.SqlClient.SqlConfigurableRetryFactory` 内部リストの一時的なエラーの 1 つに最大 5 回ヒットした場合、connection open コマンドが再試行されます。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_OpenConnection#3](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_OpenConnection.cs#3)]

> [!NOTE]
> これらの手順は、コマンドを実行する前に `Microsoft.Data.SqlClient.SqlCommand.RetryLogicProvider` プロパティに再試行プロバイダーを割り当てることを除いて、コマンドを実行する場合と同じです。

## <a name="see-also"></a>関連項目

- [SqlClient の構成可能な再試行ロジックのコア API](configurable-retry-logic-core-apis-sqlclient.md)
- [SqlClient の内部再試行ロジック プロバイダー](internal-retry-logic-providers-sqlclient.md)
- [SqlClient での構成可能な再試行ロジック構成ファイル](configurable-retry-logic-config-file-sqlclient.md)
- [構成可能な再試行ロジックを有効にする](appcontext-switches.md#enable-configurable-retry-logic)
- [SqlClient の構成可能な再試行ロジック](configurable-retry-logic.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
