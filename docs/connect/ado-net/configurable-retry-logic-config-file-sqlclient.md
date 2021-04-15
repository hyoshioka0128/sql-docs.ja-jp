---
title: SqlClient での構成可能な再試行ロジック構成ファイル
description: Microsoft.Data.SqlClient で、構成ファイルを使用して、既定の再試行ロジック プロバイダーを指定する方法と再試行ロジック オプションをカスタマイズする方法について説明します。
ms.date: 03/22/2021
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-deshtehari
ms.openlocfilehash: da47a26e992b91729853d5a81a5dff3660c5df0c
ms.sourcegitcommit: d8cbbeffa3faa110e02056ff97dc7102b400ffb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107004019"
---
# <a name="configurable-retry-logic-configuration-file-with-sqlclient"></a>SqlClient での構成可能な再試行ロジック構成ファイル

[!INCLUDE[appliesto-netfx-netcore-xxxx-md](../../includes/appliesto-netfx-netcore-xxxx-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

安全スイッチが有効な場合の既定の再試行メソッドは、<xref:Microsoft.Data.SqlClient.SqlConnection> および <xref:Microsoft.Data.SqlClient.SqlCommand> のどちらの場合も `Microsoft.Data.SqlClient.SqlConnection.SqlConfigurableRetryFactory.CreateNoneRetryProvider` です。 構成ファイルを使用すると、別の再試行メソッドを指定できます。

## <a name="configuration-sections"></a>構成セクション

アプリケーションの既定の再試行ロジック オプションは、構成ファイルの `configSections` セクション内に次のセクションを追加することで変更できます。

- `SqlConfigurableRetryLogicConnection`: <xref:Microsoft.Data.SqlClient.SqlConnection> の既定の再試行ロジックを指定します。

```csharp
<section name="SqlConfigurableRetryLogicConnection"
        type="Microsoft.Data.SqlClient.SqlConfigurableRetryConnectionSection, Microsoft.Data.SqlClient"/>
```

- `SqlConfigurableRetryLogicCommand`: <xref:Microsoft.Data.SqlClient.SqlCommand> の既定の再試行ロジックを指定します。

```csharp
<section name="SqlConfigurableRetryLogicCommand"
        type="Microsoft.Data.SqlClient.SqlConfigurableRetryCommandSection, Microsoft.Data.SqlClient"/>
```

- `AppContextSwitchOverrides`: .NET Framework は、明示的に定義する必要のない `AppContextSwitchOverrides` セクションを介して AppContext スイッチをサポートします。 .NET Core でスイッチをオンにするには、このセクションを指定する必要があります。

```csharp
<section name="AppContextSwitchOverrides"
        type="Microsoft.Data.SqlClient.AppContextSwitchOverridesSection, Microsoft.Data.SqlClient"/>
```

> [!NOTE]
> `configuration` セクション内で、次の構成を指定する必要があります。 アプリケーション構成ファイルを使用して既定の再試行ロジックを構成するには、これらの新しいセクションを宣言します。

### <a name="enable-safety-switch"></a>安全スイッチを有効にする

安全スイッチは、構成ファイルを使用して有効にすることができます。 アプリケーション コードを使用して有効にする方法については、「[構成可能な再試行ロジックを有効にする](appcontext-switches.md#enable-configurable-retry-logic)」を参照してください。

- **.NET Framework**: 詳細については、「[AppContextSwitchOverrides 要素](/dotnet/framework/configure-apps/file-schema/runtime/appcontextswitchoverrides-element)」を参照してください。

```csharp
<runtime>
    <AppContextSwitchOverrides value="Switch.Microsoft.Data.SqlClient.EnableRetryLogic=true"/>
</runtime>
```

- **.NET Core**: .NET Framework と同様に、複数のセミコロン (;) で区切られたスイッチをサポートします。

```csharp
<AppContextSwitchOverrides value="Switch.Microsoft.Data.SqlClient.EnableRetryLogic=true"/>
```

### <a name="connection-section"></a>接続セクション

次の属性を使用して、アプリケーション内のすべての `Microsoft.Data.SqlClient.SqlConnection` インスタンスの既定の再試行ロジックを指定できます。

- **numberOfTries**: 試行回数を設定します。

- **deltaTime**: ギャップ時間間隔を `System.TimeSpan` オブジェクトとして設定します。

- **minTime**: 許容される最小ギャップ時間間隔を `System.TimeSpan` オブジェクトとして設定します。

- **maxTime**: 許容される最大ギャップ時間間隔を `System.TimeSpan` オブジェクトとして設定します。

- **transientErrors**: 再試行する一時的なエラー番号のリストを設定します。

- **retryMethod**: `Microsoft.Data.SqlClient.SqlRetryLogicOption` パラメーターを介して再試行の構成を受け取り、`Microsoft.Data.SqlClient.SqlRetryLogicBaseProvider` オブジェクトを返す再試行メソッドの作成者を指定します。

- **retryLogicType**: `retryMethod` を提供する再試行メソッドの作成者を含むカスタム再試行ロジック プロバイダーを設定します。 これらのメソッドは、`retryMethod` の条件を満たす必要があります。 プロバイダーの完全修飾型名を使用する必要があります。 詳細については、「[完全修飾型名の指定](/dotnet/framework/reflection-and-codedom/specifying-fully-qualified-type-names)」を参照してください。

> [!NOTE]
> 組み込みの再試行プロバイダーを使用する場合は、`retryLogicType` を指定する必要はありません。 組み込みの再試行プロバイダーを見つけるには、「[SqlClient の内部再試行ロジック プロバイダー](internal-retry-logic-providers-sqlclient.md)」を参照してください。

### <a name="command-section"></a>コマンド セクション

アプリケーション内のすべての <xref:Microsoft.Data.SqlClient.SqlCommand> インスタンスに対して、次の属性を設定することもできます。

- **authorizedSqlCondition**: <xref:Microsoft.Data.SqlClient.SqlCommand.CommandText> の再試行前の正規表現を設定して、特定の SQL ステートメントをフィルター処理します。

> [!NOTE]
> 正規表現では、大文字と小文字が区別されます。

### <a name="examples"></a>使用例

- `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory.CreateFixedRetryProvider` メソッドと既定の一時的なエラー リストを使用して、試行間の遅延時間を約 1 秒に設定し、接続の確立を最大 3 回試みます。

    ```csharp
    <SqlConfigurableRetryLogicConnection retryMethod ="CreateFixedRetryProvider" 
                                            numberOfTries ="3" deltaTime ="00:00:01"/>
    ```

- `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory.CreateExponentialRetryProvider` メソッドと既定の一時的なエラー リストを使用して、試行間の遅延時間を最長 45 秒に設定し、接続の確立を最大 5 回試みます。

    ```csharp
    <SqlConfigurableRetryLogicConnection retryMethod ="CreateExponentialRetryProvider" 
                        numberOfTries ="5" deltaTime ="00:00:03" maxTime ="00:00:45"/>
    ```

- `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory.CreateIncrementalRetryProvider` メソッドと既定の一時的なエラー リストを使用して、試行間の遅延時間を 2 秒から 30 秒までに設定し、コマンドの実行を最大 4 回試みます。

    ```csharp
    <SqlConfigurableRetryLogicCommand retryMethod ="CreateIncrementalRetryProvider"
                        numberOfTries ="4" deltaTime ="00:00:02" maxTime ="00:00:30"/>
    ```

- 遅延時間を 1 秒から 1 分までに設定し、コマンドの実行を最大 8 回試みます。 これは、単語 `SELECT` と例外番号 102 または 997 を含む`CommandText` のコマンドに限定されます。 これは、組み込みの `Microsoft.Data.SqlClient.SqlConfigurableRetryFactory.CreateIncrementalRetryProvider` メソッドを使用します。

    ```csharp
    <SqlConfigurableRetryLogicCommand retryMethod ="CreateIncrementalRetryProvider" 
                            numberOfTries ="8" deltaTime ="00:00:01" maxTime ="00:01:00"
                            transientErrors="102, 997"
                            authorizedSqlCondition="\b(SELECT)\b"/>
    ```

> [!NOTE]
> 次の 2 つの例では、「[SqlClient の構成可能な再試行ロジックのコア API](configurable-retry-logic-core-apis-sqlclient.md#example)」のカスタム再試行ロジックのソース コードを見つけることができます。 `CreateCustomProvider` メソッドは、アプリケーションの実行ディレクトリ内にある `CustomCRL_Doc.dll` アセンブリの `CustomCRL_Doc.CustomRetry` クラスで定義されていることを前提としています。

- 指定されたカスタム再試行プロバイダーを使用して、遅延時間を 3 秒から 45 秒までに設定し、リスト内のエラー番号 4060、997、および 233 で、接続の確立を最大 5 回試みます。

    ```csharp
    <SqlConfigurableRetryLogicConnection retryLogicType ="CustomCRL_Doc.CustomRetry, CustomCRL_Doc, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"
                        retryMethod ="CreateCustomProvider" 
                        numberOfTries ="5" deltaTime ="00:00:03" maxTime ="00:00:45"
                        transientErrors ="4060, 997, 233"/>
    ```

- このサンプルは、前のサンプルと同様に動作します。

    ```csharp
    <SqlConfigurableRetryLogicConnection retryLogicType ="CustomCRL_Doc.CustomRetry, CustomCRL_Doc"
                        retryMethod ="CreateCustomProvider" 
                        numberOfTries ="5" deltaTime ="00:00:03" maxTime ="00:00:45"
                        transientErrors ="4060, 997, 233"/>
    ```

> [!NOTE]
> アプリケーションの有効期間中に将来使用できるように、再試行ロジック プロバイダーは、接続またはコマンドでの最初の使用時にキャッシュされます。

> [!NOTE]
> 再試行ロジックの設定のためにアプリケーション構成ファイルを読み取るときにエラーが発生しても、アプリケーションでエラーが発生することはありません。 代わりに、既定の `Microsoft.Data.SqlClient.SqlConnection.SqlConfigurableRetryFactory.CreateNoneRetryProvider` が使用されます。
>
> イベント ソース トレースを使用して、再試行ロジックの構成に関する問題を検証またはトラブルシューティングできます。 詳細については、「[SqlClient でのイベントのトレースの有効化](enable-eventsource-tracing.md)」を参照してください。

## <a name="see-also"></a>関連項目

- [構成可能な再試行ロジックを有効にする](appcontext-switches.md#enable-configurable-retry-logic)
- [SqlClient の内部再試行ロジック プロバイダー](internal-retry-logic-providers-sqlclient.md)
- [SqlClient でのイベントのトレースの有効化](enable-eventsource-tracing.md)
- [完全修飾型名の指定](/dotnet/framework/reflection-and-codedom/specifying-fully-qualified-type-names)
- [SqlClient の構成可能な再試行ロジック](configurable-retry-logic.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
