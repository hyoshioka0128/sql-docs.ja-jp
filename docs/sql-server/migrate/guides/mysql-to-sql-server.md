---
title: 'MySQL から SQL Server に: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for MySQL (SSMA for MySQL) を使用して、MySQL データベースを Microsoft SQL Server に移行する方法について説明します。
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: ab682d07c27417b61b608090ad80c4f0eca5ebc0
ms.sourcegitcommit: 8050df4db7a3a76e4fa03e5c79dcb49031defed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210958"
---
# <a name="migration-guide-mysql-to-sql-server"></a>移行ガイド:MySQL から SQL Server へ

[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、MySQL データベースを SQL Server に移行する方法について学習します。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。 

## <a name="prerequisites"></a>前提条件

MySQL データベースの SQL Server への移行を開始する前に、次のことを行います。

- ソース環境がサポートされていることを確認する。 現時点では、MySQL 5.6 および 5.7 がサポートされています。
- [SQL Server Migration Assistant for MySQL (SSMA for MySQL)](https://www.microsoft.com/download/details.aspx?id=54257) を取得する。
- ソースとターゲットの両方にアクセスするための接続と十分なアクセス許可を取得する。

## <a name="pre-migration"></a>移行前

前提条件を満たした後、ソースの MySQL 環境を検出し、移行の実現可能性を評価する準備が整います。

### <a name="assess"></a>アクセス

SSMA for MySQL を使用すると、データベース オブジェクトとデータを確認し、移行についてデータベースを評価できます。

評価を作成するには、次のようにします。

1. SSMA for MySQL を開きます。
1. **[ファイル]** メニューの **[新しいプロジェクト]** を選択します。
1. プロジェクト名、プロジェクトを保存する場所および移行ターゲットを入力します。 その後、 **[移行先]** オプションで **[SQL Server]** を選択します。

   ![[新しいプロジェクト] を示すスクリーンショット。](./media/mysql-to-sql-server/new-project.png)

1. **[MySQL への接続]** ダイアログ ボックスで、接続の詳細を入力してから、お使いの MySQL サーバーに接続します。

   ![[MySQL への接続] を示すスクリーンショット。](./media/mysql-to-sql-server/connect-to-mysql.png)

1. 移行する MySQL データベースを選択します。

   ![移行する MySQL データベースの選択を示すスクリーンショット。](./media/mysql-to-sql-server/select-database.png)

1. **[MySQL Metadata Explorer]** で MySQL データベースを右クリックし、 **[レポートの作成]** を選択します。 または、右上隅にある **[レポートの作成]** タブを選択することもできます。

   ![[レポートの作成] を示すスクリーンショット。](./media/mysql-to-sql-server/create-report.png)

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、MySQL オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 このレポートの既定の場所は、こちらに示すように、SSMAProjects 内のレポート フォルダーです。

    `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`.
   
   ![変換レポートを示すスクリーンショット。](./media/mysql-to-sql-server/conversion-report.png)

### <a name="validate-the-type-mappings"></a>型マッピングを検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 そのためには次を行います。

1. **[ツール]** メニューの **[プロジェクトの設定]** を選択します。
1. **[Type Mapping]\(型のマッピング\)** タブを選択します。

   ![[Type Mapping]\(型のマッピング\) を示すスクリーンショット。](./media/mysql-to-sql-server/type-mappings.png)

1. **[MySQL Metadata Explorer]** でテーブルを選択することにより、各テーブルの型マッピングを変更できます。

SSMA for MySQL の変換設定の詳細については、[プロジェクトの設定](../../../ssma/mysql/project-settings-conversion-mysqltosql.md)に関するページを参照してください。

### <a name="convert-the-schema"></a>スキーマの変換

データベース オブジェクトを変換すると、MySQL からオブジェクトの定義が取得され、類似の SQL Server オブジェクトに変換された後、この情報が SSMA for MySQL メタデータに読み込まれます。 その情報は、SQL Server のインスタンスには読み込まれません。 その後、SQL Server Metadata Explorer を使用して、オブジェクトとそれらのプロパティを表示できます。

変換中に SSMA for MySQL によって、出力メッセージが出力ペインに、エラー メッセージが **[エラー一覧]** ペインに出力されます。 出力とエラー情報を使用して、目的の変換結果を取得するために MySQL データベースまたは変換プロセスを変更する必要があるかどうかを判断します。

スキーマを変換するには、次のようにします。

1. (省略可能) 動的またはアドホックのクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。
1. **[SQL Server への接続]** タブを選択します。
     1. お使いの SQL Server インスタンス用に接続詳細を入力します。
     1. ドロップダウン リストから自分のターゲット データベースを選択するか、新しい名前を入力します。これにより、データベースはターゲット サーバー上に作成されます。
     1. 認証の詳細を入力してから、 **[接続]** を選択します。

   ![[SQL Server への接続] を示すスクリーンショット。](./media/mysql-to-sql-server/connect-to-sql-server.png)

1. **[MySQL Metadata Explorer]** で MySQL データベースを右クリックしてから、 **[スキーマの変換]** を選択します。 または、右上隅にある **[スキーマの変換]** タブを選択することもできます。

   ![[スキーマの変換] を示すスクリーンショット。](./media/mysql-to-sql-server/convert-schema.png)

1. 変換が終了した後、変換されたオブジェクトと元のオブジェクトを比較および確認して、潜在的な問題を特定し、推奨事項に基づいてそれらに対処します。

   ![オブジェクトの比較と確認を示すスクリーンショット。](./media/mysql-to-sql-server/table-comparison.png)

1. 変換された Transact-SQL テキストを元のコードと比較し、推奨事項を確認します。

   ![変換されたコードの比較と確認を示すスクリーンショット。](./media/mysql-to-sql-server/procedure-comparison.png)
   
1. [出力] ペインで **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 **[ファイル]** メニューの **[プロジェクトの保存]** を選択します。 この手順により、スキーマを SQL Server に発行する前に、ソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が得られます。

詳細については、[MySQL データベースの変換を示すスクリーンショット](../../../ssma/mysql/converting-mysql-databases-mysqltosql.md)を参照してください。

## <a name="migration"></a>移行

必要な前提条件を満たし、''*移行前*'' 段階に関連するタスクを完了した後、スキーマとデータの移行を実行する準備が整います。

データの移行には次の 2 つのオプションがあります。

- **クライアント側データの移行**
     -   クライアント側のデータ移行を実行するには、 **[プロジェクトの設定]** ダイアログ ボックスで **[Client Side Data Migration Engine]\(クライアント側のデータ移行エンジン\)** オプションを選択します。

    > [!NOTE]
    > ターゲット データベースとして SQL Express エディションが使用されている場合は、クライアント側のデータ移行のみが許可され、サーバー側のデータ移行はサポートされません。

- **サーバー側データの移行**
    -   サーバー側でデータ移行を実行する前に、次のことを確かめてください。
        - SSMA for MySQL Extension Pack が、SQL Server のインスタンスにインストールされている。
        - SQL Server エージェント サービスが、SQL Server のインスタンスで実行されている。
    -   サーバー側のデータ移行を実行するには、 **[プロジェクトの設定]** ダイアログ ボックスで **[Server Side Data Migration Engine]\(サーバー側のデータ移行エンジン\)** オプションを選択します。

> [!IMPORTANT]  
> サーバー側のデータ移行エンジンを使用する予定の場合は、データを移行する前に、SSMA for MySQL を実行しているコンピューターに SSMA for MySQL Extension Pack と MySQL プロバイダーをインストールする必要があります。 SQL Server エージェント サービスも実行されている必要があります。 拡張機能パックをインストールする方法の詳細については、[SQL Server での SQL Server Migration Assistant コンポーネントのインストール (MySQL から SQL)](../../../ssma/mysql/installing-ssma-components-on-sql-server-mysqltosql.md) に関するページを参照してください。

自分のスキーマを発行し、データを移行するには、次のようにします。

1. **[SQL Server Metadata Explorer]** でデータベースを右クリックし、 **[データベースとの同期]** を選択して、スキーマを発行します。 この操作により、MySQL データベースが SQL Server インスタンスに発行されます。

   ![[データベースとの同期] を示すスクリーンショット。](./media/mysql-to-sql-server/synchronize-database.png)

1. 自分のソース プロジェクトとターゲット間のマッピングを確認します。

   ![データベースとの同期の確認を示すスクリーンショット。](./media/mysql-to-sql-server/synchronize-database-review.png)

1. **[MySQL Metadata Explorer]** で移行するデータベースまたはオブジェクトを右クリックし、 **[データの移行]** を選択して、データを移行します。 または、 **[データの移行]** タブを選択することもできます。データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベース、 **[テーブル]** の順に展開してから、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、次のチェック ボックスをオフにします。

   ![[データの移行] を示すスクリーンショット。](./media/mysql-to-sql-server/migrate-data.png)

1. 移行が完了した後、 **[データ移行レポート]** を表示します。

   ![[データ移行レポート] を示すスクリーンショット。](./media/mysql-to-sql-server/migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してお使いの SQL Server インスタンスに接続し、データとスキーマを確認して移行を検証します。

   ![SQL Server Management Studio での検証を示すスクリーンショット。](./media/mysql-to-sql-server/validate-in-ssms.png)

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

| タイトル                    | 説明            |
| ----------------------------- | ---------------------- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションまたはデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。                |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。


## <a name="next-steps"></a>次の手順

- MySQL データベースを SQL Server に移行する方法の詳細については、[MySQL の SQL Server Migration Assistant に関するドキュメント](../../../ssma/mysql/sql-server-migration-assistant-for-mysql-mysqltosql.md)を参照してください。
- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](https://docs.microsoft.com/azure/dms/dms-tools-matrix)に関するページを参照してください。
- その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://datamigration.microsoft.com/)」を参照してください。

