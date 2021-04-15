---
title: SQL Database プロジェクトの拡張機能
description: Azure Data Studio の SQL Database プロジェクトの拡張機能をインストールして使用します。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: conceptual
author: dzsquared
ms.author: drskwier
ms.reviewer: maghan
ms.custom: ''
ms.date: 12/15/2020
ms.openlocfilehash: 58375b6512c259d07dd25fb10d539421162997c0
ms.sourcegitcommit: cfffd03fe39b04034fa8551165476e53c4bd3c3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107298725"
---
# <a name="sql-database-projects-extension-preview"></a>SQL Database プロジェクトの拡張機能 (プレビュー)

SQL Database プロジェクトの拡張機能 (プレビュー) は、プロジェクトベースの開発環境で SQL データベースを開発するための拡張機能です。 


## <a name="features"></a>特徴

- 新しい空のプロジェクト、または接続されたデータベースからプロジェクトを作成します。
- [Azure Data Studio](sql-database-project-extension-getting-started.md) または [SQL Server Data Tools](../../ssdt/sql-server-data-tools.md) で以前に作成されたプロジェクトを開きます。
- プロジェクトでオブジェクト (テーブル、ビュー、ストアド プロシージャ)、またはカスタム スクリプトを追加または削除してプロジェクトを編集します。
- フォルダー内のファイルまたはスクリプトを整理します。
- システム データベースまたはユーザー DACPAC への参照を追加します。
- 1 つのプロジェクトをビルドします。
- 1 つのプロジェクトをデプロイします。
- デプロイ プロファイルから接続の詳細 (SQL Windows 認証) と SQLCMD 変数を読み込みます。

Azure Data Studio での SQL Database Projects 拡張機能の概要については、次の 10 分間の短いビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Build-SQL-Database-Projects-Easily-in-Azure-Data-Studio/player?WT.mc_id=dataexposed-c9-niner]

## <a name="installation"></a>インストール

1. 拡張機能マネージャーを開いて、使用可能な拡張機能にアクセスします。  そのためには、拡張機能アイコンを選択するか、 **[表示]** メニューの **[拡張機能]** を選択します。
2. 拡張機能の検索ボックスに名前の一部または全部を入力して、"*SQL Database プロジェクト*" の拡張機能を特定します。 使用可能な拡張機能を選択すると、その詳細が表示されます。

   ![拡張機能のインストール](media/sql-database-projects-extension/install-database-projects.png)

3. 必要な拡張機能を選択して **インストール** します。
4. **[再読み込み]** を選択して拡張機能を有効にします (拡張機能を初めてインストールするときにのみ必要です)。
5. アクティビティ バーから [ファイル] アイコンを選択するか、 **[表示]** メニューから **[エクスプローラー]** を選択します。 **プロジェクト** 用の新しい viewlet が使用できるようになりました。

   > [!NOTE]
   > プロジェクトのビルド機能には .NET Core SDK が必要であり、拡張機能でそれを検出できない場合は、.NET Core SDK をインストールするように求められます。  .NET Core SDK (v3.1 以降) は、[https://dotnet.microsoft.com/download/dotnet-core/3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) からダウンロードしてインストールできます。

   > [!NOTE]
   > すべての機能を使用するには、SQL Database プロジェクトの拡張機能と共に [Schema Compare の拡張機能](schema-compare-extension.md)をインストールすることをお勧めします。

## <a name="net-core-sdk"></a>.NET Core SDK
プロジェクトのビルド機能には .NET Core SDK が必要であり、拡張機能でそれを検出できない場合は、.NET Core SDK をインストールするように求められます。  .NET Core SDK (v3.1 以降) は、[https://dotnet.microsoft.com/download/dotnet-core/3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) からダウンロードしてインストールできます。

以前にインストールした .NET Core SDK は、最低限必要なバージョンを下回る可能性があり、SQL Database プロジェクトの拡張機能では使用できません。  .NET SDK の[現在インストールされているバージョンを確認する](https://docs.microsoft.com/dotnet/core/install/how-to-detect-installed-versions)場合は、ターミナルを開き、次のコマンドを実行します。

```dotnetcli
dotnet --list-sdks
```

サポートされていない .NET Core SDK バージョンでは、次のようなエラー メッセージが発生する可能性があります。
- `error MSB4018: The "SqlBuildTask" task failed unexpectedly.`
- ` error MSB4018: System.TypeInitializationException: The type initializer for 'SqlSchemaModelStaticState' threw an exception. ---> System.IO.FileNotFoundException: Could not load file or assembly 'System.Runtime, Version=4.2.2.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'. The system cannot find the file specified. [c:\Users\ .sqlproj]_` (存在しないファイルのリンクでは、閉じ角かっこが一致しません)

## <a name="known-limitations"></a>既知の制限事項

- 現在、Azure Data Studio viewlet では、リンクとしてのファイルの読み込みはサポートされていませんが、ファイルはツリーの最上位レベルに読み込まれ、ビルドによりこれらのファイルが正常に組み込まれます。
- プロジェクト内の SQLCLR オブジェクトは、.NET Core バージョンの DacFx ではサポートされていません。
- タスク (ビルド、発行) はユーザー定義ではありません。
- DacFx によって定義されたターゲットを発行します。
- WSL 環境のサポートは制限されています。

## <a name="workspace"></a>ワークスペース
Azure Data Studio の SQL データベース プロジェクトは論理ワークスペース内に含まれています。  ワークスペースによって、[エクスプローラー] ウィンドウに表示されるフォルダーと [プロジェクト] ウィンドウに表示されるプロジェクトが管理されます。 ワークスペースでのプロジェクトの追加と削除は、[プロジェクト] ウィンドウの Azure Data Studio インターフェイスから実行できます。 ただし、ワークスペースの設定は、必要であれば `.code-workspace` ファイルで手動編集できます。

下の例の `.code-workspace` ファイルでは、`folders` 配列によって、[エクスプローラー] ウィンドウに含まれるすべてのフォルダーが一覧表示され、`settings` 内の `dataworkspace.projects` 配列によって、[プロジェクト] ウィンドウに含まれるすべての SQL プロジェクトが一覧表示されます。

```json
{
    "folders": [
        {
            "path": "."
        },
        {
            "name": "WideWorldImportersDW",
            "path": "..\\WideWorldImportersDW"
        }
    ],
    "settings": {
        "dataworkspace.projects": [
            "AdventureWorksLT.sqlproj",
            "..\\WideWorldImportersDW\\WideWorldImportersDW.sqlproj"
        ]
    }
}
```

## <a name="next-steps"></a>次のステップ

- [SQL Database プロジェクトの拡張機能をお使いになる前に](sql-database-project-extension-getting-started.md)
- [Azure Data Studio の SQL Database プロジェクトの拡張機能を使用してプロジェクトをビルドして発行する](sql-database-project-extension-build.md)
