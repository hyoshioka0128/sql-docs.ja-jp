---
title: 'SAP ASE から SQL Server: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for SAP ASE (SSMA for SAP ASE) を使用して、SAP ASE データベースを Microsoft SQL Server に移行する方法について説明します。
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 7c413bb99520e30882e1746763d803d57006e6bb
ms.sourcegitcommit: 8050df4db7a3a76e4fa03e5c79dcb49031defed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210905"
---
# <a name="migration-guide-sap-ase-to-sql-server"></a>移行ガイド: SAP ASE から SQL Server

[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、SQL Server Migration Assistant for SAP ASE (SSMA for SAP ASE) を使用して、SAP ASE データベースを SQL Server に移行する方法について学習します。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。

## <a name="prerequisites"></a>前提条件

SAP ASE データベースの SQL Server への移行を開始する前に、次のことを行います。

- ソース環境がサポートされていることを確認する。
- [SQL Server Migration Assistant for SAP Adaptive Server Enterprise (旧称 SAP Sybase ASE)](https://www.microsoft.com/download/details.aspx?id=54256) を取得する。
- ソースとターゲットの両方にアクセスするための接続と十分なアクセス許可を取得する。

## <a name="pre-migration"></a>移行前

前提条件を満たしたら、環境のトポロジを検出し、移行の実現可能性を評価する準備が整いました。

### <a name="assess"></a>アクセス

SSMA for SAP ASE を使用すると、データベース オブジェクトとデータを確認し、データベースの移行の評価を行った上で、Sybase データベース オブジェクトを SQL Server に移行した後、データを SQL Server に移行することができます。 詳細については、「[SQL Server Migration Assistant for Sybase (SybaseToSQL)](../../../ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql.md)」を参照してください。

評価を作成するには、次のようにします。

1. [SSMA for SAP ASE](https://www.microsoft.com/download/details.aspx?id=54256) を開きます。
1. **[ファイル]** メニューの **[新しいプロジェクト]** を選択します。
1. プロジェクト名と、プロジェクトを保存する場所を入力します。 その後、ドロップダウン リストから移行ターゲットとして **[SQL Server]** を選び、 **[OK]** を選択します。
1. **[Sybase への接続]** ダイアログ ボックスで、SAP 接続の詳細の値を入力します。
1. 移行する SAP スキーマを右クリックし、 **[レポートの作成]** を選択して HTML レポートを生成します。
1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、SAP ASE オブジェクトのインベントリとスキーマ変換の実行に必要な作業を確認することもできます。 このレポートの既定の場所は、こちらに示すように、SSMAProjects 内のレポート フォルダーです。

    `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>`.

### <a name="validate-the-type-mappings"></a>型マッピングを検証する

スキーマ変換を実行する前に、既定のデータ型マッピングを検証するか、要件に基づいて変更します。 **[ツール]** メニューに移動して **[プロジェクトの設定]** を選択することも、 **[SAP ASE Metadata Explorer]** でテーブルを選択してテーブルごとの型マッピングを変更することもできます。

### <a name="convert-the-schema"></a>スキーマの変換

スキーマを変換するには、次のようにします。

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。
1. **[SQL Server への接続]** タブを選択し、SQL Server の詳細を入力します。 既存のデータベースへの接続を選択することも、新しい名前を入力することもできます。後者の場合、データベースはターゲット サーバー上に作成されます。
1. **[SAP ASE Metadata Explorer]** で移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、 **[データの移行]** タブを選択することもできます。データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベース、 **[テーブル]** の順に展開してから、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。
1. スキーマの構造を比較および確認して、潜在的な問題を特定します。

   スキーマ変換が終了した後、オフライン スキーマ修復の演習のためにこのプロジェクトをローカルに保存できます。 **[ファイル]** メニューの **[プロジェクトの保存]** を選択します。 この手順により、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。

詳細については、[スキーマの変換](../../../ssma/sybase/converting-sybase-ase-database-objects-sybasetosql.md)に関する記事を参照してください。

## <a name="migrate"></a>Migrate

必要な前提条件を満たし、''*移行前*'' 段階に関連するタスクを完了した後、スキーマとデータの移行を実行する準備が整います。

自分のスキーマを発行し、データを移行するには、次のようにします。

1. **[SQL Server Metadata Explorer]** でデータベースを右クリックし、 **[データベースとの同期]** を選択して、スキーマを発行します。 この操作により、SAP ASE スキーマが SQL Server インスタンスに発行されます。
1. **[SAP ASE Metadata Explorer]** で移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択して、データを移行します。 または、 **[データの移行]** タブを選択することもできます。データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベース、 **[テーブル]** の順に展開してから、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。
1. 移行が終了した後、 **[データ移行レポート]** を表示します。
1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してお使いの SQL Server インスタンスに接続し、データとスキーマを確認して移行を検証します。

## <a name="post-migration"></a>移行後

*移行* 段階が正常に完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データをターゲット環境に移行した後、以前にソースが使用されていたすべてのアプリケーションで、ターゲットの使用を開始する必要があります。 このタスクを実現するために、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データベース移行のテストア プローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。
1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題の調整、完全性の確認、およびワークロードのパフォーマンスの問題への対処のために非常に重要です。

> [!Note]
> これらの問題と、それらを軽減するための具体的な手順の詳細については、「[移行後の検証および最適化ガイド](../../../relational-databases/post-migration-validation-and-optimization-guide.md)」を参照してください。

## <a name="migration-assets"></a>移行資産

この移行シナリオの詳細については、次のリソースを参照してください。 これは、実世界の移行プロジェクトの取り組みをサポートするために開発されたものです。

| タイトル                                                                                                       | 説明                                                                                                                                                                                                                                                                                                                                                                                                     |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET および SQL Server に再コンパイルされたメインフレーム アプリおよびデータの最適化ガイド](https://aka.ms/dmj-wp-mainframe-optimize) | このガイドでは、.NET から SQL Server に対して可能な限り効率的にポイント検索を実行するための最適化に関するアドバイスを提供します。 メインフレーム データベースから SQL Server に移行するお客様は、特にサードパーティ製ツール (Raincode コンパイラなど) を使用してメインフレーム コード (COBOL や JCL など) を T-SQL および C# .NET に自動的に移行する場合に、既存のメインフレーム最適化設計パターンを移行することができます。 |

> [!NOTE]
> データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次の手順

- Azure データベース移行ガイドとその内容の概要については、ビデオ「[データ移行ガイドの使い方](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/)」を参照してください。
- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関するページを参照してください。
- その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://datamigration.microsoft.com/)」を参照してください。
- 移行ビデオについて、「[移行の行程の概要、および評価と移行を実行するために推奨されるツールとサービス](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)」をご覧ください。
