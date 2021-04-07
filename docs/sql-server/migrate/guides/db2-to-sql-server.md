---
title: 'IBM Db2 から SQL Server に: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant (SSMA) for Db2 を使用して、お使いの Db2 データベースを Microsoft SQL Server に移行する方法について説明します。
ms.custom: ''
ms.date: 03/19/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.openlocfilehash: 994d78b9e1eec46659e97c114d99355ccf4a974b
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981059"
---
# <a name="migration-guide-ibm-db2-to-sql-server"></a>移行ガイド: IBM Db2 から SQL Server
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、SQL Server Migration Assistant (SSMA) for Db2 を使用して、ユーザー データベースを IBM Db2 から SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。 


## <a name="prerequisites"></a>前提条件

Db2 データベースの SQL Server への移行を開始する前に、以下を実行します。

- [ソース環境がサポートされている](../../../ssma/db2/installing-ssma-for-db2-client-db2tosql.md#prerequisites)ことを確認する。
- [SSMA for Db2](https://www.microsoft.com/download/details.aspx?id=54254) をダウンロードしてインストールする。
- ソースとターゲットの両方にアクセスするための接続と十分なアクセス許可を取得する。 

## <a name="pre-migration"></a>移行前

前提条件を満たすと、環境のトポロジを検出し、移行の可能性を評価する準備は完了です。 

### <a name="assess-and-convert"></a>評価と変換 

SSMA for Db2 を使用して、データベース オブジェクトとデータを確認し、データベースの移行を評価します。 

評価を作成するには、以下を実行します。

1. SSMA for Db2 を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名と場所を指定し、ドロップダウン リストで SQL Server 移行ターゲットを選択します。 **[OK]** を選択します。

   :::image type="content" source="media/db2-to-sql-server/new-project.png" alt-text="SSMA for Db2 の [新しいプロジェクト] ペインのスクリーンショット。":::


1. **[Connect to Db2]\(Db2 への接続\)** を選択し、Db2 接続の詳細を入力します。

   :::image type="content" source="media/db2-to-sql-server/connect-to-db2.png" alt-text="[Connect to Db2]\(Db2 への接続\) ペインのスクリーンショット。":::


1. 移行する Db2 スキーマを右クリックし、 **[レポートの作成]** を選択して HTML レポートを生成します。 または、右上にある **[レポートの作成]** を選択することもできます。

   :::image type="content" source="media/db2-to-sql-server/create-report.png" alt-text="Db2 メタデータ エクスプローラーの [レポートの作成] リンクのスクリーンショット。":::

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Db2 オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 このレポートの既定の場所は、こちらに示すように、SSMAProjects 内のレポート フォルダーです。  

   `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>` 

   :::image type="content" source="media/db2-to-sql-server/report.png" alt-text="SSMA の変換レポートのスクリーンショット。":::


### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 そのためには次を行います。 

1. **[ツール]** を選択し、 **[プロジェクトの設定]** を選択します。  
1. **[Type Mapping]\(型のマッピング\)** タブを選択します。

   :::image type="content" source="media/db2-to-sql-server/type-mapping.png" alt-text="SSMA for Db2 の[Type Mapping]\(型のマッピング\) ペインのスクリーンショット。":::

1. **[Db2 メタデータ エクスプローラー]** ペインでテーブル名を選択すると、各テーブルの型マッピングを変更できます。 

### <a name="convert-schema"></a>スキーマの変換 

スキーマを変換するには、以下を実行します。

1. (省略可能) 動的または特殊なクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。 

1. **[SQL Server への接続]** タブを選択し、SQL Server インスタンスの接続の詳細を入力します。 

    a. **[データベース]** ドロップダウン リストで、ターゲット データベースを選択するか、新しい名前を指定してターゲット サーバーにデータベースを作成します。   
    b. 認証の詳細を指定します。   
    c. **[接続]** を選択します。

   :::image type="content" source="media/db2-to-sql-server/connect-to-sql-server.png" alt-text="SSMA for Db2 の [SQL Server への接続] ペインのスクリーンショット。":::

1. 処理するスキーマを右クリックして、 **[スキーマの変換]** を選択します。 または、右上にある **[スキーマの変換]** タブを選択することもできます。

   :::image type="content" source="media/db2-to-sql-server/convert-schema.png" alt-text="[Db2 メタデータ エクスプローラー] ペインの [スキーマの変換] コマンドのスクリーンショット。":::

1. 変換が完了したら、変換された構造と元の構造を比較して、潜在的な問題を特定し、推奨事項に基づいてそれらに対処します。

   :::image type="content" source="media/db2-to-sql-server/compare-review-schema-structure.png" alt-text="変換されたオブジェクトと元のオブジェクトの比較を示すスクリーンショット。":::

1. 出力ペインで **[結果の確認]** アイコンを選択し、 **[エラー一覧]** ペインでエラーを確認します。 
1. オフライン スキーマ修復の演習のために、 **[ファイル]**  >  **[プロジェクトの保存]** を選択して、プロジェクトをローカルに保存します。 このようにすると、スキーマを SQL Server インスタンスに公開する前に、ソースとターゲットのスキーマをオフラインで評価し、それらの修復を実行する機会が得られます。



## <a name="migrate"></a>移行

データベースの評価と不整合への対処が完了したら、次の手順は移行プロセスの実行です。

スキーマを公開し、データを移行するには、以下を実行します。

1. スキーマを公開します。 **[SQL Server メタデータ エクスプローラー]** ペインで、データベースを右クリックして **[Synchronize with Database]\(データベースと同期する\)** を選択します。

   :::image type="content" source="media/db2-to-sql-server/synchronize-with-database.png" alt-text="[SQL Server メタデータ エクスプローラー] ペインの [Synchronize with Database]\(データベースと同期する\) コマンドのスクリーンショット。":::

1. データを移行します。 **[Db2 メタデータ エクスプローラー]** ペインで、スキーマまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、右上にある **[データの移行]** タブを選択することもできます。
 
   データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、 **[テーブル]** を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。

   :::image type="content" source="media/db2-to-sql-server/migrate-data.png" alt-text="[データの移行] リンクのスクリーンショット。":::

1. Db2 と SQL Server インスタンスの両方の接続の詳細を指定します。 
1. 移行が完了したら、**データ移行レポート** を表示します。  

   :::image type="content" source="media/db2-to-sql-server/data-migration-report.png" alt-text="データ移行レポートのスクリーンショット。":::

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してお使いの SQL Server インスタンスに接続し、データとスキーマを確認して移行を検証します。 

   :::image type="content" source="media/db2-to-sql-server/compare-schema-in-ssms.png" alt-text="SQL Server Management Studio のスクリーンショット。":::

## <a name="post-migration"></a>移行後 

*移行* 段階が正常に完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する 

データをターゲット環境に移行した後、以前にソースを使用していたすべてのアプリケーションで、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソースとターゲットの両方のデータベースに対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。

1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。

1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。


## <a name="migration-assets"></a>移行資産 

この移行シナリオの詳細については、次のリソースを参照してください。 これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。

| タイトル | 説明 |
| --- | --- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルを提供します。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に役立ちます。 |
| [IBM Db2 zOS データ資産の検出および評価パッケージ](https://github.com/Microsoft/DataMigrationTeam/tree/master/Db2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package) | データベース上で SQL スクリプトを実行した後、結果をファイル システム上のファイルにエクスポートできます。 スプレッドシートなど、外部ツールで結果をキャプチャできるように、CSV などの複数のファイル形式がサポートされています。 この方法を使用すると、ワークベンチをインストールしていないチームと結果を簡単に共有することができます。|
| [IBM Db2 LUW インベントリ スクリプトと成果物](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts) | IBM Db2 LUW バージョン 11.1 システム テーブルを対象とする、スキーマおよびオブジェクトの種類ごとのオブジェクトの数、各スキーマの "生データ" の概算値、および各スキーマのテーブルのサイズを提供し、結果を CSV 形式で格納する SQL クエリが含まれます。 |
| [Azure 上の IBM Db2 LUW pureScale - 設定ガイド](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Db2%20PureScale%20on%20Azure.pdf) | IBM Db2 の実装計画の開始点として役立ちます。 業務要件は違っても、同じ基本パターンが適用されます。 このアーキテクチャ パターンは、Azure 上の OLAP アプリケーションにも使用できます。 |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次のステップ

移行後は、「[移行後の検証と最適化ガイド](../../../relational-databases/post-migration-validation-and-optimization-guide.md)」を確認してください。 

さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](/azure/dms/dms-tools-matrix)に関するページを参照してください。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://datamigration.microsoft.com/)」を参照してください。 

移行のビデオについては、[移行の過程の概要](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)に関するビデオをご覧ください。
