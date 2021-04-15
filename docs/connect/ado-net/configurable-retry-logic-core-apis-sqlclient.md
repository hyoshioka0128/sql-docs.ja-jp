---
title: SqlClient の構成可能な再試行ロジックのコア API
description: 構成可能な再試行ロジックのコア API を使用して、Microsoft.Data.SqlClient でアプリケーションにカスタムの再試行ロジックを実装する方法を学習します。
ms.date: 03/22/2021
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-deshtehari
ms.openlocfilehash: 83053651938adfb51640ee9bdb096c2b2c8b4708
ms.sourcegitcommit: d8cbbeffa3faa110e02056ff97dc7102b400ffb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107003953"
---
# <a name="configurable-retry-logic-core-apis-in-sqlclient"></a>SqlClient の構成可能な再試行ロジックのコア API

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

組み込みの再試行ロジック プロバイダーではニーズに対応できない場合、独自のカスタム プロバイダーを作成できます。 その後、これらのプロバイダーを `SqlConnection` または `SqlCommand` オブジェクトに割り当てて、カスタム ロジックを適用できます。

組み込みプロバイダーは、カスタム プロバイダーの実装に使用できる 3 つのインターフェイスを中心に設計されています。 カスタム再試行プロバイダーは、<xref:Microsoft.Data.SqlClient.SqlConnection> または <xref:Microsoft.Data.SqlClient.SqlCommand> の内部再試行プロバイダーと同じ方法で使用できます。

1. `Microsoft.Data.SqlClient.SqlRetryIntervalBaseEnumerator`: 時間間隔のシーケンスを生成します。
2. `Microsoft.Data.SqlClient.SqlRetryLogicBase`: 再試行回数を超えておらず、一時的な条件が満たされた場合に、指定された列挙子の次の時間間隔を取得します。
3. `Microsoft.Data.SqlClient.SqlRetryLogicBaseProvider`: 接続およびコマンド操作に再試行ロジックを適用します。

> [!CAUTION]
> カスタムの再試行ロジック プロバイダーを実装することにより、コンカレンシー、パフォーマンス、例外管理など、すべての側面を管理できます。

## <a name="example"></a>例

このサンプルの実装は、カスタマイズの手順を示すためのものであり、可能な限りシンプルに作成されています。 スレッド セーフ、非同期、コンカレンシーなどの高度な手法は含まれていません。 実際の実装の詳細については、[ Microsoft.Data.SqlClient GitHub リポジトリ](https://github.com/dotnet/SqlClient/)の事前定義済み再試行ロジックを参照してください。

1. カスタムの構成可能な再試行ロジックのクラスを定義します。

    - **列挙子**: 時間間隔の固定シーケンスを定義し、許容可能な時間の範囲を 2 分から 4 分に延長します。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_CustomProvider#6](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_CustomProvider.cs#6)]

    - **再試行ロジック**: アクティブなトランザクションの一部ではない任意のコマンドに再試行ロジックを実装します。 再試行回数を 60 回から 20 回に減らします。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_CustomProvider#7](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_CustomProvider.cs#7)]

    - **プロバイダー**: `Retrying` イベント発生させずに同期操作を再試行する再試行プロバイダーを実装します。 既存の <xref:Microsoft.Data.SqlClient.SqlException> 一時例外エラー番号に <xref:System.TimeoutException> を追加します。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_CustomProvider#8](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_CustomProvider.cs#8)]

2. 定義されたカスタム型で構成される再試行プロバイダー インスタンスを作成します。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_CustomProvider#4](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_CustomProvider.cs#4)]

    - 次の関数は、指定された再試行可能な例外のリストと特別な <xref:System.TimeoutException> 例外を使用して例外を評価し、再試行可能かどうかを判断します。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_CustomProvider#5](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_CustomProvider.cs#5)]

3. カスタマイズされた再試行ロジックを使用します。

    - 再試行ロジック パラメーターを定義します。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_CustomProvider#1](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_CustomProvider.cs#1)]

    - カスタムの再試行プロバイダーを作成します。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_CustomProvider#2](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_CustomProvider.cs#2)]

    - 再試行プロバイダーを `Microsoft.Data.SqlClient.SqlConnection.RetryLogicProvider` または `Microsoft.Data.SqlClient.SqlCommand.RetryLogicProvider` に割り当てます。

    [!code-csharp[SqlConfigurableRetryLogic_StepByStep_CustomProvider#3](~/../sqlclient/doc/samples/SqlConfigurableRetryLogic_StepByStep_CustomProvider.cs#3)]

> [!NOTE]
> 構成可能な再試行ロジックを使用する前に、そのスイッチを忘れずに有効にしてください。 詳細については、「[構成可能な再試行ロジックを有効にする](appcontext-switches.md#enable-configurable-retry-logic)」を参照してください。

## <a name="see-also"></a>関連項目

- [Microsoft.Data.SqlClient GitHub リポジトリ](https://github.com/dotnet/SqlClient/)
- [SqlClient の構成可能な再試行ロジック](configurable-retry-logic.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
