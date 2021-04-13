---
title: 'SQL Server へのアクセス: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for Access (SSMA for Access) を使用して、Microsoft Access データベースを Microsoft SQL Server に移行する方法について説明します。
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 11c604a2e50b21138419e5c2d5a6f213f8fc5559
ms.sourcegitcommit: 8050df4db7a3a76e4fa03e5c79dcb49031defed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210968"
---
# <a name="migration-guide-access-to-sql-server"></a>移行ガイド: SQL Server へのアクセス

[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、SQL Server Migration Assistant for Acces (SSMA for Acces) を使用して、Microsoft Access データベースを SQL Database に移行する方法について学習します。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://datamigration.microsoft.com/)」を参照してください。

## <a name="prerequisites"></a>前提条件

Access データベースの SQL Server への移行を開始する前に、次のようにします。

- ソース環境がサポートされていることを確認する。
- [SSMA for Access](https://www.microsoft.com/download/details.aspx?id=54255) を取得する。
- ソースとターゲットの両方にアクセスするための接続と十分なアクセス許可を取得する。

## <a name="pre-migration"></a>移行前

前提条件を満たしたら、環境のトポロジを検出し、移行の実現可能性を評価する準備が整いました。

### <a name="assess"></a>アクセス

SSMA for Access を使用すると、データベース オブジェクトとデータを確認し、移行についてデータベースを評価できます。 このツールの詳細については、[SQL Server Migration Assistant for Access](/sql/ssma/access/sql-server-migration-assistant-for-access-accesstosql) に関するページを参照してください。

評価を作成するには、次のようにします。

1. [SSMA for Access](https://www.microsoft.com/download/details.aspx?id=54255) を開きます。
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。
1. プロジェクト名と、プロジェクトを保存する場所を入力します。 その後、ドロップダウン リストから SQL Server の移行ターゲットを選び、 **[OK]** を選択します。

   ![[新しいプロジェクト] を示すスクリーンショット。](./media/access-to-sql-server/new-project.png)

1. **[データベースの追加]** を選択し、自分のプロジェクトに追加するデータベースを選びます。

   ![[データベースの追加] を示すスクリーンショット。](./media/access-to-sql-server/add-databases.png)

1. **[Access Metadata Explorer]** で、評価するデータベースを右クリックしてから、 **[レポートの作成]** を選択します。 または、右上隅にある **[レポートの作成]** タブを選択することもできます。

   ![[レポートの作成] を示すスクリーンショット。](./media/access-to-sql-server/create-report.png)

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Access オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 このレポートの既定の場所は、こちらに示すように、SSMAProjects 内のレポート フォルダーです。

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_2020_11_12T02_47_55\`.

   ![サンプル レポートを示すスクリーンショット。](./media/access-to-sql-server/sample-report.png)

### <a name="validate-the-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 そのためには次を行います。

1. **[ツール]** メニューの **[プロジェクトの設定]** を選択します。
1. **[Type Mapping]\(型のマッピング\)** タブを選択します。

   ![[Type Mapping]\(型のマッピング\) を示すスクリーンショット。](./media/access-to-sql-server/type-mappings.png)

1. **[Access Metadata Explorer]** でテーブルを選択し、各テーブルの型マッピングを変更することができます。

### <a name="convert"></a>Convert

データベース オブジェクトを変換するには、次のようにします。

1. **[SQL Server への接続]** を選択し、接続の詳細を入力します。

   ![[SQL Server への接続] を示すスクリーンショット。](./media/access-to-sql-server/connect-to-sql-server.png)

1. **[Access Metadata Explorer]** でデータベースを右クリックし、 **[スキーマの変換]** を選択します。 または、右上隅にある **[スキーマの変換]** タブを選択することもできます。

   ![[スキーマの変換] を示すスクリーンショット。](./media/access-to-sql-server/convert-schema.png)

1. 変換が終了した後、変換されたオブジェクトと元のオブジェクトを比較および確認して、潜在的な問題を特定し、推奨事項に基づいてそれらに対処します。

   ![変換されたクエリの比較を示すスクリーンショット。](./media/access-to-sql-server/query-comparison.png)

1. 変換された Transact-SQL テキストを元のコードと比較し、推奨事項を確認します。

   ![変換されたオブジェクトの確認を示すスクリーンショット。](./media/access-to-sql-server/table-comparison.png)

1. (省略可能) 個々のオブジェクトを変換するには、そのオブジェクトを右クリックし、 **[スキーマの変換]** を選択します。 変換されたオブジェクトは、 **[Access Metadata Explorer]** 内に太字で表示されます。

   ![Metadata Explorer の太字のオブジェクトが変換されていることを示すスクリーンショット。](./media/access-to-sql-server/converted-items-bold.png)
 
1. [出力] ペインで **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューの **[プロジェクトの保存]** を選択します。 この手順により、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。

## <a name="migrate"></a>Migrate

データベースの評価と不整合への対処が完了した後、次の手順で移行プロセスを実行します。 データの移行は、トランザクション内でデータの行を SQL Server に移動する一括読み込み操作です。 各トランザクションで SQL Server に読み込まれる行数は、プロジェクトの設定で構成されます。

SSMA for Access を使用してスキーマを発行し、データを移行するには次のようにします。

1. まだ行っていない場合は、 **[SQL Server への接続]** を選択し、接続の詳細を入力します。

1. **[SQL Server Metadata Explorer]** でデータベースを右クリックし、 **[データベースとの同期]** を選択して、スキーマを発行します。 この操作により、MySQL スキーマが SQL Server に公開されます。

   ![[データベースとの同期] を示すスクリーンショット。](./media/access-to-sql-server/synchronize-with-database.png)

1. 自分のソース プロジェクトとターゲット間のマッピングを確認します。

   ![データベースとの同期の確認を示すスクリーンショット。](./media/access-to-sql-server/synchronize-with-database-review.png)

1. **[Access Metadata Explorer]** で、移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択して、データを移行します。 または、 **[データの移行]** タブを選択することもできます。データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベース、 **[テーブル]** の順に展開してから、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。

   ![[データの移行] を示すスクリーンショット。](./media/access-to-sql-server/migrate-data.png)

1. 移行が完了した後、 **[データ移行レポート]** を表示します。

   ![[データ移行レポート] を示すスクリーンショット。](./media/access-to-sql-server/migrate-data-review.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してお使いの SQL Server インスタンスに接続し、データとスキーマを確認して移行を検証します。

   ![SQL Server Management Studio での検証を示すスクリーンショット。](./media/access-to-sql-server/validate-in-ssms.png)

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

| タイトル | 説明 |
| -------------- | --------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションまたはデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。 |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。


## <a name="next-steps"></a>次のステップ

- 移行後は、「[移行後の検証と最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を確認してください。
- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](/azure/dms/dms-tools-matrix)に関するページを参照してください。
- その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://datamigration.microsoft.com/)」を参照してください。
- 移行のビデオについては、[移行の過程の概要](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)に関するビデオをご覧ください。
