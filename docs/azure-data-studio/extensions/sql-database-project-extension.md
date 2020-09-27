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
ms.date: 09/22/2020
ms.openlocfilehash: 65006891a6633a75482f9a32c328dea0d8bf76fc
ms.sourcegitcommit: cc23d8646041336d119b74bf239a6ac305ff3d31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91123238"
---
# <a name="sql-database-projects-extension-preview"></a>SQL Database プロジェクトの拡張機能 (プレビュー)

SQL Database プロジェクトの拡張機能 (プレビュー) は、プロジェクトベースの開発環境で SQL データベースを開発するための拡張機能です。 

## <a name="features"></a>特徴

1. 接続されたデータベースからプロジェクトを作成します。
2. 空のプロジェクトを新しく作成します。
3. [Azure Data Studio](sql-database-project-extension-getting-started.md) または [SQL Server Data Tools](../../ssdt/sql-server-data-tools.md) で以前に作成されたプロジェクトを開きます。
4. プロジェクトのテーブル、ビュー、ストアド プロシージャ、またはカスタム スクリプトを追加または削除してプロジェクトを編集します。
5. フォルダー内のファイルまたはスクリプトを整理します。
6. システム データベースまたはユーザー DACPAC への参照を追加します。
7. 1 つのプロジェクトをビルドします。
8. 1 つのプロジェクトをデプロイします。
9. デプロイ プロファイルから接続の詳細 (SQL Windows 認証) と SQLCMD 変数を読み込みます。

## <a name="install-the-sql-database-projects-extension"></a>SQL Database プロジェクトの拡張機能をインストールする

1. 拡張機能マネージャーを開いて、使用可能な拡張機能にアクセスします。  そのためには、拡張機能アイコンを選択するか、 **[表示]** メニューの **[拡張機能]** を選択します。
2. 拡張機能の検索ボックスに名前の一部または全部を入力して、"*SQL Database プロジェクト*" の拡張機能を特定します。 使用可能な拡張機能を選択すると、その詳細が表示されます。

   ![拡張機能のインストール](media/sql-database-projects-extension/install-database-projects.png)

3. 必要な拡張機能を選択して**インストール**します。
4. **[再読み込み]** を選択して拡張機能を有効にします (拡張機能を初めてインストールするときにのみ必要です)。
5. アクティビティ バーから [ファイル] アイコンを選択するか、 **[表示]** メニューから **[エクスプローラー]** を選択します。 **プロジェクト**用の新しい viewlet が使用できるようになりました。

   > [!NOTE]
   > プロジェクトのビルド機能には .NET Core SDK が必要であり、拡張機能でそれを検出できない場合は、.NET Core SDK をインストールするように求められます。  .NET Core SDK (v3.1 以降) は、[https://dotnet.microsoft.com/download/dotnet-core/3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) からダウンロードしてインストールできます。

   > [!NOTE]
   > すべての機能を使用するには、SQL Database プロジェクトの拡張機能と共に [Schema Compare の拡張機能](schema-compare-extension.md)をインストールすることをお勧めします。

## <a name="known-limitations"></a>既知の制限事項

1. Azure Data Studio viewlet でプロジェクト参照を追加したり、既存のプロジェクト参照を読み込んだりすることは、現在はサポートされていません。
2. 現在、Azure Data Studio viewlet では、リンクとしてのファイルの読み込みはサポートされていませんが、ファイルはツリーの最上位レベルに読み込まれ、ビルドによりこれらのファイルが正常に組み込まれます。
3. 現在、viewlet でのデプロイ前およびデプロイ後のスクリプトの追加と読み込みはサポートされていません。ただし、これらのファイルをプロジェクトに手動で追加すると、ビルド時に受け入れられます。
4. プロジェクト内の SQLCLR オブジェクトは、.NET Core バージョンの DacFx ではサポートされていません。
5. タスク (ビルド、発行) はユーザー定義ではありません。
6. DacFx によって定義されたターゲットを発行します。
7. ソース管理の統合と新しいプロジェクトの作成では、.gitignore ファイルは自動的に作成されません。
8. WSL 環境のサポートは制限されています。

## <a name="next-steps"></a>次のステップ

- [SQL Database プロジェクトの拡張機能をお使いになる前に](sql-database-project-extension-getting-started.md)
- [Azure Data Studio の SQL Database プロジェクトの拡張機能を使用してプロジェクトをビルドして発行する](sql-database-project-extension-build.md)