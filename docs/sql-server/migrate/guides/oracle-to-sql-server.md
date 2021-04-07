---
title: 'Oracle から SQL Server に: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant (SSMA) for Oracle を使用して、Oracle スキーマを Microsoft SQL Server に移行する方法について説明します。
ms.prod: sql
ms.reviewer: ''
ms.technology: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 1d84ff8831d04b84075e73c44a3ab9bdcd694b70
ms.sourcegitcommit: 0b37eb7aef2f358f80867cd13830dd6683da8d85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105981029"
---
# <a name="migration-guide-oracle-to-sql-server"></a>移行ガイド: Oracle から SQL Server に
[!INCLUDE[sqlserver](../../../includes/applies-to-version/sqlserver.md)]

このガイドでは、SQL Server Migration Assistant for Oracle (SSMA for Oracle) を使用して、Oracle データベースを SQL Server に移行する方法について説明します。 

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。 

## <a name="prerequisites"></a>前提条件 

Oracle データベースの SQL Server への移行を開始する前に、以下を実行します。

- ソース環境がサポートされていることを確認する。
- [SQL Server](https://www.microsoft.com/evalcenter/evaluate-sql-server-2019?filetype=EXE) をダウンロードしてインストールする。
- [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) をダウンロードしてインストールする。
- [SSMA for Oracle に必要なアクセス許可](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)と[プロバイダー](/sql/ssma/oracle/connect-to-oracle-oracletosql)を取得する。
- ソースとターゲットの両方にアクセスするための接続と十分なアクセス許可を取得する。 


## <a name="pre-migration"></a>移行前

クラウドへの移行の準備をする場合には、ソース環境がサポートされていること、およびその他の前提条件をすべて満たしていることを確認します。 そうすることで、確実に効率よく移行を成功させるのに役立ちます。

プロセスのこの部分では、移行する必要があるデータベースのインベントリを実行し、移行に関する潜在的な問題や障害の有無についてそれらを評価し、発見していなかったおそれがあるすべての項目を解決します。 


### <a name="discover"></a>発見

[Microsoft Assessment and Planning (MAP) Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) を使用して、既存のデータ ソースを特定し、組織で使用されている機能の詳細を確認します。 このプロセスには、ネットワークをスキャンして、組織内のすべての Oracle インスタンス、バージョン、機能を特定することが含まれます。

MAP Toolkit を使用してインベントリ スキャンを実行するには、以下を実行します。 

1. [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) を開きます。

1. **[概要]** ペインで **[Create/Select database]\(データベースの作成/選択\)** を選択します。 

   ![MAP Toolkit [概要] ペインの [Create/Select database]\(データベースの作成/選択\) リンクのスクリーンショット。](./media/oracle-to-sql-server/select-database.png)

1. **[Create/Select database]\(データベースの作成/選択\)** の下で **[インベントリ データベースの作成]** を選択し、作成するインベントリ データベースの名前を入力し、簡単な説明を入力して、 **[OK]** を選択します。

   :::image type="content" source="media/oracle-to-sql-server/create-inventory-database.png" alt-text="MAP Toolkit の [インベントリ データベースの作成] オプションのスクリーンショット。":::

1. **[インベントリ データの収集]** を選択して、**インベントリと評価ウィザード** を開きます。 

   :::image type="content" source="media/oracle-to-sql-server/collect-inventory-data.png" alt-text="インベントリと評価ウィザードの [インベントリ データの収集] リンクのスクリーンショット。":::

1. ウィザードで **[Oracle]** を選択し、 **[次へ]** を選択します。 

   ![インベントリと評価ウィザードの [Oracle] オプションと [次へ] ボタンのスクリーンショット。](./media/oracle-to-sql-server/choose-oracle.png)

1. 組織のニーズおよびご使用の環境に最も適したコンピューター検索オプションを選択し、 **[次へ]** を選択します。 

   ![組織のニーズに最も適したコンピューター検出方法の一覧のスクリーンショット。](./media/oracle-to-sql-server/choose-search-option.png)

1. 探索するシステムの現在の資格情報を入力するか、新しい資格情報を作成し、 **[次へ]** を選択します。

    ![コンピューターの資格情報を入力するためのウィザード ペインのスクリーンショット。](./media/oracle-to-sql-server/choose-credentials.png)

1. 資格情報の順序を設定し、 **[次へ]** を選択します。

   ![資格情報の順序を設定するためのウィザード ペインのスクリーンショット。](./media/oracle-to-sql-server/set-credential-order.png)  

1. 検出する各コンピューターの資格情報を指定します。 コンピューターまたはマシンごとに一意の資格情報を使用するか、 **[コンピューター]** 一覧から選択できます。

   ![検出する各コンピューターの資格情報を指定するための [Use all computers credential list]\(すべてのコンピューターの資格情報の一覧を使用する\) オプションのスクリーンショット。](./media/oracle-to-sql-server/specify-credentials-for-each-computer.png)

1. 選択の概要を確認し、 **[完了]** を選択します。

   ![選択内容を確認するためのウィザードの [概要] ページのスクリーンショット。](./media/oracle-to-sql-server/review-summary.png)

1. スキャンが完了したら、 **[データ収集]** 概要レポートを表示します。 スキャンには数分かかる場合がありますが、時間はデータベースの数によって異なります。 完了したら、 **[閉じる]** をクリックします。

   ![[データ収集] 概要レポート ページのスクリーンショット。](./media/oracle-to-sql-server/collection-summary-report.png)

1. **[オプション]** を選択して、Oracle の評価とデータベースの詳細に関するレポートを生成します。 レポートを生成するには、両方のオプションを (一度に 1 つずつ) 選択します。


### <a name="assess"></a>アクセス

データ ソースを特定したら、[SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) を使用して、2 つの間のギャップを理解するために、SQL Server 仮想マシンに移行する Oracle インスタンスを評価します。 Migration Assistant を使用すると、データベース オブジェクトとデータを確認し、データベースの移行の評価を行った上で、データベース オブジェクトを SQL Server に移行した後、データを SQL Server に移行できます。

評価を作成するには、以下を実行します。 

1. [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258) を開きます。 
1. **[ファイル]** を選択し、 **[新しいプロジェクト]** を選択します。 
1. プロジェクト名と場所を指定し、ドロップダウン リストで SQL Server 移行ターゲットを選択します。 **[OK]** を選択します。 

   ![SSMA for Oracle の [新しいプロジェクト] ペインのスクリーンショット。](./media/oracle-to-sql-server/new-project.png)

1. **[Oracle への接続]** を選択し、Oracle 接続の詳細を入力して、 **[接続]** を選択します。

   ![[Oracle への接続] ペインのスクリーンショット。](./media/oracle-to-sql-server/connect-to-oracle.png)

1. **[Filter objects]\(オブジェクトのフィルター\)** ペインで、移行する Oracle スキーマを選択し、 **[OK]** を選択します。

   ![読み込むスキーマを選択するための [Filter objects]\(オブジェクトのフィルター\) ペインのスクリーンショット。](./media/oracle-to-sql-server/select-schema.png)

1. **[Oracle メタデータ エクスプローラー]** ペインで、処理する Oracle スキーマを選択し、 **[レポートの作成]** を選択して、変換の統計とエラーまたは警告 (ある場合) を含む HTML レポートを生成します。 または、右上にある **[レポートの作成]** タブを選択することもできます。

   ![Oracle メタデータ エクスプローラーの [レポートの作成] リンクのスクリーンショット。](./media/oracle-to-sql-server/create-report.png)

1. HTML レポートを確認し、変換の統計情報とエラーまたは警告を把握します。 また、Excel でレポートを開き、Oracle オブジェクトのインベントリとスキーマ変換の実行に必要な作業量を確認することもできます。 このレポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。 次に例を示します。 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

   ![SSMA の変換レポートのスクリーンショット。](./media/oracle-to-sql-server/conversion-report.png)


### <a name="validate-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 そのためには次を行います。 

1. **[ツール]** を選択し、 **[プロジェクトの設定]** を選択します。  
1. **[Type Mapping]\(型のマッピング\)** タブを選択します。

   ![SSMA for Oracle の[Type Mapping]\(型のマッピング\) ペインのスクリーンショット。](./media/oracle-to-sql-server/type-mappings.png)

1. **[Oracle メタデータ エクスプローラー]** ペインでテーブル名を選択すると、各テーブルの型マッピングを変更できます。 

### <a name="convert-schema"></a>スキーマの変換

スキーマを変換するには、以下を実行します。 

1. (省略可能) 動的または特殊なクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。 

1. **[SQL Server への接続]** タブを選択し、SQL Server インスタンスの接続の詳細を入力します。  

    a. **[データベース]** ドロップダウン リストで、ターゲット データベースを選択するか、新しい名前を指定してターゲット サーバーにデータベースを作成します。   
    b. 認証の詳細を指定します。   
    c. **[接続]** を選択します。

   ![SSMA for Oracle の [SQL Server への接続] ペインのスクリーンショット。](./media/oracle-to-sql-server/connect-to-sql.png)

1. **[Oracle メタデータ エクスプローラー]** ペインで、処理するスキーマを右クリックして、 **[スキーマの変換]** を選択します。 または、右上にある **[スキーマの変換]** タブを選択することもできます。

   ![[Oracle メタデータ エクスプローラー] ペインの [スキーマの変換] コマンドのスクリーンショット。](./media/oracle-to-sql-server/convert-schema.png)

1. 変換が完了したら、変換されたオブジェクトと元のオブジェクトを比較して、潜在的な問題を特定し、推奨事項に基づいてそれらに対処します。

   ![変換されたオブジェクトと元のオブジェクトの比較を示すスクリーンショット。](./media/oracle-to-sql-server/table-mapping.png)

   変換された Transact-SQL テキストを元のコードと比較し、推奨事項を確認します。

   ![変換されたテキストと元のコードの比較を示すスクリーンショット。](./media/oracle-to-sql-server/procedure-comparison.png)

1. 出力ペインで **[結果の確認]** アイコンを選択し、 **[エラー一覧]** ペインでエラーを確認します。 
1. オフライン スキーマ修復の演習のために、 **[ファイル]**  >  **[プロジェクトの保存]** を選択して、プロジェクトをローカルに保存します。 このようにすると、スキーマを SQL Server インスタンスに公開する前に、ソースとターゲットのスキーマをオフラインで評価し、それらの修復を実行する機会が得られます。


## <a name="migrate-database"></a>データベースの移行

前提条件を満たし、*移行前* 段階に関連するタスクを完了すると、スキーマとデータベースの移行を実行する準備が整います。 移行には、スキーマの公開とデータベースの移行という 2 つの手順が必要です。 

スキーマを公開してデータベースを移行するには、以下を実行します。 

1. スキーマを公開します。 **[SQL Server メタデータ エクスプローラー]** ペインで、データベースを右クリックして **[Synchronize with Database]\(データベースと同期する\)** を選択します。 この操作により、Oracle スキーマが SQL Server インスタンスに公開されます。

   ![[SQL Server メタデータ エクスプローラー] ペインの [Synchronize with Database]\(データベースと同期する\) コマンドのスクリーンショット。](./media/oracle-to-sql-server/synchronize-database.png)

1. こちらに示すように、ソース プロジェクトとターゲット間のマッピングを確認します。

   ![データベースのマッピングを確認するための [Synchronize with Database]\(データベースと同期する\) ペインのスクリーンショット。](./media/oracle-to-sql-server/synchronize-database-review.png)

1. データを移行します。 **[Oracle メタデータ エクスプローラー]** ペインで、スキーマまたはオブジェクトを右クリックし、 **[データの移行]** を選択します。 または、右上にある **[データの移行]** タブを選択することもできます。 

   データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、 **[テーブル]** を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。

   ![[データの移行] リンクのスクリーンショット。](./media/oracle-to-sql-server/migrate-data.png)

1. **[データの移行]** ペインで、Oracle と SQL Server の両方の接続の詳細を入力します。

1. 移行が完了したら、**データ移行レポート** を表示します。

    ![データ移行レポートのスクリーンショット。](./media/oracle-to-sql-server/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してお使いの SQL Server インスタンスに接続し、データとスキーマを確認して移行を検証します。

   ![SQL Server Management Studio のスクリーンショット。](./media/oracle-to-sql-server/validate-in-ssms.png)


SSMA を使用するだけでなく、SQL Server Integration Services (SSIS) を使用してデータを移行することもできます。 詳細については、次を参照してください。 
- [SQL Server Integration Services の概要](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services) (記事)
- [SQL Server Integration Services: Azure の SSIS とハイブリッド データ移動](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx) (テクニカル ホワイト ペーパー)

## <a name="post-migration"></a>移行後 

*移行* 段階が正常に完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データをターゲット環境に移行した後、以前にソースを使用していたすべてのアプリケーションで、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) は、Java ソース コードを分析し、データ アクセス API の呼び出しとクエリを検出できるようにする Visual Studio Code の拡張機能です。 このツールキットを使用すると、新しいデータベース バックエンドをサポートするために対処する必要がある項目を 1 つのペイン ビューで表示できます。 詳細については、[Oracle からの Java アプリケーションの移行](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)に関するブログを参照してください。 

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソースとターゲットの両方のデータベースに対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。

1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。

1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。


### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題の調整、完全性の確認、およびワークロードのパフォーマンスの問題への対処のために非常に重要です。

これらの問題と、それらを軽減するための具体的な手順の詳細については、「[移行後の検証および最適化ガイド](../../../relational-databases/post-migration-validation-and-optimization-guide.md)」を参照してください。


## <a name="migration-assets"></a>移行資産 

この移行シナリオの詳細については、次のリソースを参照してください。 これらは、実際の移行プロジェクトの取り組みをサポートするために開発されました。


| タイトル | 説明 |
| --- | --- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルを提供します。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に役立ちます。 |
| [Oracle インベントリ スクリプト成果物](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts) | Oracle システム テーブルにヒットし、スキーマの種類、オブジェクトの種類、および状態別にオブジェクトの数を提供する PL/SQL クエリが含まれています。 また、各スキーマの "生データ" の概算値と、各スキーマ内のテーブルのサイズ設定も提供します。結果は CSV 形式で格納されます。 |
| [SSMA Oracle 評価コレクションと統合の自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation) | エントリとして .csv ファイル (プロジェクトのフォルダー内の sources.csv) を使用して、コンソール モードで SSMA 評価を実行するために必要な xml ファイルを生成するリソースのセットです。 source.csv ファイルは、既存の Oracle インスタンスのインベントリに基づいて、顧客によって提供されます。 出力ファイルは、AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml、および VariableValueFile.xml です。 |
| [Oracle データベースの移行時の SSMA の問題と考えられる解決方法](https://aka.ms/dmj-wp-ssma-oracle-errors) | Oracle で WHERE 句に非スカラー条件を割り当てる方法について説明します。 しかし、SQL Server ではこの種類の条件がサポートされていません。 その結果、SSMA for Oracle では、WHERE 句に非スカラー条件を含むクエリは変換されず、エラー O2SS0001 が生成されます。 このホワイト ペーパーでは、この問題とその解決方法について詳しく説明しています。 |
| [Oracle から SQL Server への移行に関するハンドブック](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf) | Oracle スキーマを最新バージョンの SQL Server ベースに移行する場合に関連するタスクに焦点を当てています。 移行によって機能の変更が必要な場合は、そのデータベースを使用するアプリケーションに対する各変更によって生じる可能性のある影響について慎重に検討する必要があります。 |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次のステップ

移行後は、「[移行後の検証と最適化ガイド](../../../relational-databases/post-migration-validation-and-optimization-guide.md)」を確認してください。 

さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](/azure/dms/dms-tools-matrix)に関するページを参照してください。

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://datamigration.microsoft.com/)」を参照してください。 

移行のビデオについては、[移行の過程の概要](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)に関するビデオをご覧ください。
