---
title: リリース ノート
titleSuffix: SQL Server big data clusters
description: この記事では、(プレビュー) の最新[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]の更新プログラムと既知の問題について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 63caee60bc96c5ff8579471ae0a98322e4fc99b7
ms.sourcegitcommit: 71fac5fee00e0eca57e555f44274dd7e08d47e1e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70160690"
---
# <a name="release-notes-for-sql-server-big-data-clusters"></a>SQL Server ビッグデータクラスターのリリースノート

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

この記事では、の[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]最新リリースに関する更新プログラムと既知の問題の一覧を示します。

## <a id="rc"></a>リリース候補 (8 月)

以下のセクションでは、SQL Server 2019 リリース候補のビッグデータクラスターの新機能と既知の問題について説明します。

### <a name="whats-new"></a>新機能

|新機能または更新 | 詳細 |
|:---|:---|
|Always On 可用性グループの SQL Server |SQL Server ビッグデータクラスターをデプロイするときに、次の機能を提供する可用性グループを作成するようにデプロイを構成できます。<br/><br/>-高可用性 <br/><br/>-読み取り-スケールアウト <br/><br/>-データプールへのデータ挿入のスケールアウト<br/><br>「[高可用性を使用したデプロイ」を](../big-data-cluster/deployment-high-availability.md)参照してください。 |
|`azdata` |[インストールマネージャー](./deploy-install-azdata-linux-package.md)を使用したツールのインストールの簡略化<br/><br/>[`azdata notebook`メニュー](./reference-azdata-notebook.md)<br/><br/>[`azdata bdc status`メニュー](./reference-azdata-bdc-status.md) |
|Azure Data Studio|[Azure Data Studio のリリース候補ビルドをダウンロード](deploy-big-data-tools.md#download-and-install-azure-data-studio-sql-server-2019-release-candidate-rc)します。<br/><br/>SQL Server 2019 ガイド Jupyter Book を通じて、トラブルシューティングノートブックを追加しました。<br/><br/>コントローラーのログインエクスペリエンスが追加されました。<br/><br/>サービスエンドポイントを表示したり、クラスターの正常性状態を表示したり、トラブルシューティングノートブックにアクセスしたりするためのコントローラーダッシュボードを追加しました。<br/><br/>ノートブックセルの出力/編集のパフォーマンスが向上しました。|
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題

SQL Server 2019 ビッグデータクラスターリリース候補更新ビルド番号は`15.0.1900.47`です。

## <a id="ctp32"></a> CTP 3.2 (7 月)

以下のセクションでは、SQL Server 2019 CTP 3.2 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="whats-new"></a>新機能

|新機能または更新 | 詳細 |
|:---|:---|
|パブリック プレビュー |CTP 3.2 より前の SQL Server ビッグ データ クラスターは、登録した早期導入者に提供されていました。 このリリースでは、あらゆるユーザーが SQL Server ビッグ データ クラスターの機能を操作できるようにします。 <br/><br/> 「[使用を開始[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](deploy-get-started.md)する」を参照してください。|
|`azdata` |CTP 3.2 での `azdata` の導入 - Python で作成されたコマンドライン ユーティリティを使用して、クラスター管理者が REST API 経由でビッグ データ クラスターをブートストラップし、管理できるようにします。 `mssqlctl` が `azdata` に置き換えられます。 [`azdata` のインストール](deploy-install-azdata.md)に関するページを参照してください。 |
|PolyBase |外部テーブルの列名は、SQL Server、Oracle、Teradata、MongoDB、ODBC データ ソースに対してクエリを実行するために使用されるようになりました。 以前の CTP リリースでは、外部データ ソース内の列は序数位置に基づいてのみバインドされ、EXTERNAL TABLE の定義で指定された名前は使用されませんでした。 |
|HDFS の階層の更新 |リモート データの最新のスナップショットに対して既存のマウントを更新できるように、HDFS の階層に対して更新機能を導入しました。 [HDFS の階層](hdfs-tiering.md)に関するページを参照してください。 |
|Notebook ベースのトラブルシューティング |CTP 3.2 では、Jupyter Notebook が導入されており、SQL Server ビッグ データ クラスター内のコンポーネントの[配置](deploy-notebooks.md)と[検出、診断、トラブルシューティング](manage-notebooks.md)の支援が行われます。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題

以下のセクションでは、このリリースの既知の問題と制限事項について説明します。

#### <a name="polybase"></a>PolyBase

- カウントが 1000 を超える場合の TOP 句のプッシュダウンは、このリリースではサポートされません。 このような場合は、リモート データ ソースからすべての行が読み取られます。 (リリース候補で修正済み)

- 外部データ ソースへの併置結合のプッシュダウンは、このリリースではサポートされません。 たとえば、分布の種類が ROUND_ROBIN である 2 つのデータ プール テーブルをプッシュダウンすると、そのデータは、結合操作を実行する SQL マスター インスタンスまたはコンピューティング プール インスタンスに渡されます。

#### <a name="compute-pool"></a>コンピューティング プール

- ビッグ データ クラスターのデプロイでは、1 つのインスタンスを持つコンピューティング プールのみがサポートされます。 (リリース候補で修正済み)

#### <a name="storage-pool"></a>記憶域プール

- 記憶域プール内の parquet ファイルへのクエリ実行では、特定のデータ型がサポートされません。

- MAP および LIST 親列や型がない親列に対してはクエリを実行できません。 それにより、エラーが返されます。

- REPEATED ノードにはクエリを実行できず、間違った結果が返されることがあります。

## <a id="ctp31"></a> CTP 3.1 (6 月)

以下のセクションでは、SQL Server 2019 CTP 3.1 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="whats-new"></a>新機能

| 新機能または更新 | 詳細 |
|:---|:---|
| `mssqlctl` コマンドの変更 | `mssqlctl cluster` コマンドの名前が、`mssqlctl bdc` に変更されました。 詳しくは、[`mssqlctl` のリファレンス](reference-azdata.md)に関する記事をご覧ください。 |
| 新しい `mssqlctl` ステータス コマンドとクラスター管理ポータルの削除。 | クラスター管理ポータルはこのリリースで削除されました。 既存の監視コマンドを補完する新しいステータス コマンドが `mssqlctl` に追加されました。 |
| Spark コンピューティング プール | ストレージをスケールアップする必要なしに、追加ノードを作成して Spark のコンピューティング能力を増強します。 さらに、Spark に使われない記憶域プールのノードを開始することができます。 Spark と記憶域は切り離されています。 詳しくは、「[Configure storage without spark (Spark なしで記憶域を構成する)](deployment-custom-configuration.md#sparkstorage)」をご覧ください。 |
| MSSQL Spark コネクタ | データ プール外部テーブルの読み取り/書き込みのサポート。 以前のリリースでは、MASTER インスタンス テーブルの読み取り/書き込みだけがサポートされていました。 詳しくは、「[How to read and write to SQL Server from Spark using the MSSQL Spark Connector (MSSQL Spark コネクタを使用して Spark から SQL Server の読み取りと書き込みを行う方法)](spark-mssql-connector.md)」をご覧ください。 |
| MLeap を使用する機械学習 | [Spark で MLeap 機械学習モデルをトレーニングし、Java 言語拡張機能を使用して SQL Server でスコアリングします](spark-create-machine-learning-model.md)。 |

### <a name="known-issues"></a>既知の問題

以下のセクションでは、このリリースの既知の問題と制限事項について説明します。

#### <a name="hdfs"></a>HDFS

- HDFS 内のファイルを右クリックしてプレビューすると、次のエラーが表示されることがあります。

   `Error previewing file: File exceeds max size of 30MB`

   Azure Data Studio では現在、30 MB を超えるファイルをプレビューする方法はありません。

- hdfs-site.xml への変更が含まれた HDFS の構成変更はサポートされていません。

#### <a name="deployment"></a>展開

- 以前のリリースからのビッグ データ クラスターのアップグレードはサポートされていません。

   > [!IMPORTANT]
   > 最新リリースをデプロイする前に、データをバックアップし、(以前のバージョンの **azdata** を使用して) 既存のビッグ データ クラスターを削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

- AKS にデプロイした後、そのデプロイから次の 2 つの警告イベントが表示されることがあります。 これらのイベントはどちらも既知の問題ですが、AKS にビッグ データ クラスターを正常にデプロイすることを妨げるものではありません。

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- ビッグ データ クラスターのデプロイが失敗した場合、関連付けられた名前空間が削除されません。 これにより、クラスター上に孤立した名前空間が残される可能性があります。 回避策として、同じ名前を持つクラスターをデプロイする前に、名前空間を手動で削除します。

#### <a name="external-tables"></a>外部テーブル

- ビッグ データ クラスターのデプロイでは、**SqlDataPool** および **SqlStoragePool** の外部データ ソースが作成されなくなりました。 データ プールや記憶域プールへのデータ仮想化をサポートするには、これらのデータ ソースを手動で作成できます。

   > [!NOTE]
   > これらの外部データ ソースを作成するための URI は CTP によって異なります。 その作成方法を確認するには、次の Transact-SQL コマンドを参照してください。 

   ```sql
   -- Create default data sources for SQL Big Data Cluster
   IF NOT EXISTS(SELECT * FROM sys.external_data_sources WHERE name = 'SqlDataPool')
       CREATE EXTERNAL DATA SOURCE SqlDataPool
       WITH (LOCATION = 'sqldatapool://controller-svc/default');
 
   IF NOT EXISTS(SELECT * FROM sys.external_data_sources WHERE name = 'SqlStoragePool')
       CREATE EXTERNAL DATA SOURCE SqlStoragePool
       WITH (LOCATION = 'sqlhdfs://controller-svc/default');
   ```

- サポートされていない列の型を含むテーブルのデータ プール外部テーブルを作成できます。 その外部テーブルにクエリを実行すると、次のようなメッセージが表示されます。

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- 記憶域プール外部テーブルにクエリを実行したときに、基になるファイルが同時に HDFS にコピーされていると、エラーが発生することがあります。

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

- 文字データ型を使用する外部テーブルを Oracle に対して作成している場合、Azure Data Studio 仮想化ウィザードでは、これらの列が外部テーブル定義内の VARCHAR として解釈されます。 これにより、外部テーブル DDL でエラーが発生します。 NVARCHAR2 型を使用するように Oracle スキーマを変更するか、またはウィザードを使用する代わりに手動で EXTERNAL TABLE ステートメントを作成し、NVARCHAR を指定してください。

#### <a name="application-deployment"></a>アプリケーションの展開

- RESTful API から R、Python、または MLeap アプリケーションを呼び出すと、その呼び出しが 5 分でタイムアウトします。

#### <a name="spark-and-notebooks"></a>Spark とノートブック

- POD が再起動すると、Kubernetes 環境で POD IP アドレスが変更される可能性があります。 マスター ポッドの再起動のシナリオでは、Spark セッションが `NoRoteToHostException` で失敗する可能性があります。 これは、新しい IP アドレスで更新されない JVM キャッシュによって発生します。

- Jupyter が既にインストールされ、Windows 上に別の Python が存在する場合は、Spark ノートブックが失敗することがあります。 この問題を回避するには、Jupyter を最新バージョンにアップグレードします。

- ノートブックでは、 **[Add Text] (テキストの追加)** コマンドをクリックすると、テキスト セルが編集モードではなくプレビュー モードで追加されます。 プレビュー アイコンをクリックして編集モードに切り替え、そのセルを編集することができます。

#### <a name="security"></a>セキュリティ

- SA_PASSWORD は環境の一部であり (コード ダンプ ファイルなどで) 検出できます。 デプロイの後、マスター インスタンス上の SA_PASSWORD をリセットする必要があります。 これはバグではなく、セキュリティ手順です。 Linux コンテナー内の SA_PASSWORD を変更する方法の詳細については、[SA パスワードの変更](../linux/quickstart-install-connect-docker.md#sapassword)に関するページを参照してください。

- AKS ログに、ビッグ データ クラスターのデプロイのための SA パスワードが含まれている可能性があります。

#### <a name="kibana-logs-dashboards"></a>Kibana ログのダッシュボード

- Aris CTP 3.0 および 3.1 の間で、Kibana バージョンが 6.3.1 から 7.0.1 にアップグレードされました。  これにより、Edge ブラウザーと Kibana との互換性がなくなりました。 ユーザーが Edge で Kibana ダッシュボードの現在のバージョンを読み込むと、空白のページが表示されます。 Kibana.rs でサポートされるブラウザーについては、[こちら]( https://www.elastic.co/support/matrix#matrix_browse)を参照してください。 


## <a id="ctp30"></a> CTP 3.0 (5 月)

以下のセクションでは、SQL Server 2019 CTP 3.0 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="whats-new"></a>新機能

| 新機能または更新 | 詳細 |
|:---|:---|
| **mssqlctl** の更新 | 複数の **mssqlctl** [コマンドとパラメーターが更新されました](reference-azdata.md)。 これには、コントローラーのユーザー名とエンドポイントをターゲットにするようになった **mssqlctl login** コマンドの更新も含まれています。 |
| ストレージの機能強化 | ログとデータのさまざまなストレージ構成のサポート。 また、ビッグ データ クラスターへの永続ボリューム要求の数が削減されました。 |
| 複数のコンピューティング プール インスタンス | 複数のコンピューティング プール インスタンスのサポート。 |
| 新しいプールの動作と機能 | コンピューティング プールは、既定で **ROUND_ROBIN** 配布内​​のストレージ プールおよびデータ プールの操作にのみ使用されるようになりました。 データ プールで新しい **REPLICATED** 分布の種類が使用できるようになりました。これは、同じデータがすべてのデータ プール インスタンスに存在することを意味します。 |
| 外部テーブルの機能強化 | HADOOP データ ソースの種類の外部テーブルで、最大サイズ 1 MB の行の読み取りがサポートされるようになりました。 外部テーブル (ODBC、記憶域プール、データ プール) で、SQL Server テーブルと同じ幅の行がサポートされるようになりました。 |

### <a name="known-issues"></a>既知の問題

以下のセクションでは、このリリースの既知の問題と制限事項について説明します。

#### <a name="hdfs"></a>HDFS

- HDFS 内に新しいフォルダーを作成しようとすると、Azure Data Studio からエラーが返されます。 この機能を有効にするには、Azure Data Studio の Insider ビルドをインストールします。
  
   - [Windows ユーザー インストーラー - **Insider ビルド**](https://azuredatastudio-update.azurewebsites.net/latest/win32-x64-user/insider)
   - [Windows システム インストーラー - **Insider ビルド**](https://azuredatastudio-update.azurewebsites.net/latest/win32-x64/insider)
   - [Windows ZIP - **Insider ビルド**](https://azuredatastudio-update.azurewebsites.net/latest/win32-x64-archive/insider)
   - [macOS ZIP - **Insider ビルド**](https://azuredatastudio-update.azurewebsites.net/latest/darwin/insider)
   - [Linux TAR.GZ - **Insider ビルド**](https://azuredatastudio-update.azurewebsites.net/latest/linux-x64/insider)

- HDFS 内のファイルを右クリックしてプレビューすると、次のエラーが表示されることがあります。

   `Error previewing file: File exceeds max size of 30MB`

   Azure Data Studio では現在、30 MB を超えるファイルをプレビューする方法はありません。

- hdfs-site.xml への変更が含まれた HDFS の構成変更はサポートされていません。

#### <a name="deployment"></a>展開

- GPU 対応のビッグ データ クラスターのための以前のデプロイ手順は CTP 3.0 ではサポートされません。 代わりのデプロイ手順は、現在調査中です。 現在のところ、"GPU がサポートされたビッグ データ クラスターのデプロイと TensorFlow の実行" に関する記事は、混乱を避けるために一時的に未公開になっています。

- 以前のリリースからのビッグ データ クラスターのアップグレードはサポートされていません。

   > [!IMPORTANT]
   > 最新リリースをデプロイする前に、データをバックアップし、(以前のバージョンの **azdata** を使用して) 既存のビッグ データ クラスターを削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

- AKS にデプロイした後、そのデプロイから次の 2 つの警告イベントが表示されることがあります。 これらのイベントはどちらも既知の問題ですが、AKS にビッグ データ クラスターを正常にデプロイすることを妨げるものではありません。

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- ビッグ データ クラスターのデプロイが失敗した場合、関連付けられた名前空間が削除されません。 これにより、クラスター上に孤立した名前空間が残される可能性があります。 回避策として、同じ名前を持つクラスターをデプロイする前に、名前空間を手動で削除します。

#### <a name="external-tables"></a>外部テーブル

- ビッグ データ クラスターのデプロイでは、**SqlDataPool** および **SqlStoragePool** の外部データ ソースが作成されなくなりました。 データ プールや記憶域プールへのデータ仮想化をサポートするには、これらのデータ ソースを手動で作成できます。

   > [!NOTE]
   > これらの外部データ ソースを作成するための URI は CTP によって異なります。 その作成方法を確認するには、次の Transact-SQL コマンドを参照してください。 

   ```sql
   -- Create default data sources for SQL Big Data Cluster
   IF NOT EXISTS(SELECT * FROM sys.external_data_sources WHERE name = 'SqlDataPool')
       CREATE EXTERNAL DATA SOURCE SqlDataPool
       WITH (LOCATION = 'sqldatapool://controller-svc/default');
 
   IF NOT EXISTS(SELECT * FROM sys.external_data_sources WHERE name = 'SqlStoragePool')
       CREATE EXTERNAL DATA SOURCE SqlStoragePool
       WITH (LOCATION = 'sqlhdfs://controller-svc/default');
   ```

- サポートされていない列の型を含むテーブルのデータ プール外部テーブルを作成できます。 その外部テーブルにクエリを実行すると、次のようなメッセージが表示されます。

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- 記憶域プール外部テーブルにクエリを実行したときに、基になるファイルが同時に HDFS にコピーされていると、エラーが発生することがあります。

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

- 文字データ型を使用する外部テーブルを Oracle に対して作成している場合、Azure Data Studio 仮想化ウィザードでは、これらの列が外部テーブル定義内の VARCHAR として解釈されます。 これにより、外部テーブル DDL でエラーが発生します。 NVARCHAR2 型を使用するように Oracle スキーマを変更するか、またはウィザードを使用する代わりに手動で EXTERNAL TABLE ステートメントを作成し、NVARCHAR を指定してください。

#### <a name="application-deployment"></a>アプリケーションの展開

- RESTful API から R、Python、または MLeap アプリケーションを呼び出すと、その呼び出しが 5 分でタイムアウトします。

#### <a name="spark-and-notebooks"></a>Spark とノートブック

- POD が再起動すると、Kubernetes 環境で POD IP アドレスが変更される可能性があります。 マスター ポッドの再起動のシナリオでは、Spark セッションが `NoRoteToHostException` で失敗する可能性があります。 これは、新しい IP アドレスで更新されない JVM キャッシュによって発生します。

- Jupyter が既にインストールされ、Windows 上に別の Python が存在する場合は、Spark ノートブックが失敗することがあります。 この問題を回避するには、Jupyter を最新バージョンにアップグレードします。

- ノートブックでは、 **[Add Text] (テキストの追加)** コマンドをクリックすると、テキスト セルが編集モードではなくプレビュー モードで追加されます。 プレビュー アイコンをクリックして編集モードに切り替え、そのセルを編集することができます。

#### <a name="security"></a>セキュリティ

- SA_PASSWORD は環境の一部であり (コード ダンプ ファイルなどで) 検出できます。 デプロイの後、マスター インスタンス上の SA_PASSWORD をリセットする必要があります。 これはバグではなく、セキュリティ手順です。 Linux コンテナー内の SA_PASSWORD を変更する方法の詳細については、[SA パスワードの変更](../linux/quickstart-install-connect-docker.md#sapassword)に関するページを参照してください。

- AKS ログに、ビッグ データ クラスターのデプロイのための SA パスワードが含まれている可能性があります。

## <a id="ctp25"></a> CTP 2.5 (4 月)

以下のセクションでは、SQL Server 2019 CTP 2.5 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="whats-new"></a>新機能

| 新機能または更新 | 詳細 |
|:---|:---|
| 展開プロファイル | ビッグ データ クラスターの展開には、環境変数の代わりに、既定およびカスタマイズした[展開構成 JSON ファイル](deployment-guidance.md#configfile)を使用します。 |
| 入力が求められる展開 | `azdata cluster create` では、既定の展開に必要な設定の入力が求められるようになりました。 |
| サービス エンドポイントとのポッド名の変更 | 次の外部エンドポイントは名前が変更されました。<br/>&nbsp;&nbsp;&nbsp;- **endpoint-master-pool** => **master-svc-external**<br/>&nbsp;&nbsp;&nbsp;- **endpoint-controller** => **controller-svc-external**<br/>&nbsp;&nbsp;&nbsp;- **endpoint-service-proxy** => **mgmtproxy-svc-external**<br/>&nbsp;&nbsp;&nbsp;- **endpoint-security** => **gateway-svc-external**<br/>&nbsp;&nbsp;&nbsp;- **endpoint-app-service-proxy** => **appproxy-svc-external**|
| **azdata** の機能強化 | **azdata** を使用して[外部エンドポイントを一覧表示](deployment-guidance.md#endpoints)し、`--version` パラメーターで **azdata** のバージョンを確認します。 |
| オフライン インストール | ビッグ データ クラスターのオフライン デプロイのためのガイダンス。 |
| HDFS 階層の機能強化 | ADLS Gen2 での S3 階層、マウント キャッシュ、および OAuth のサポート。 |
| 新しい `mssql` Spark-SQL Server コネクタ | |

### <a name="known-issues"></a>既知の問題

以下のセクションでは、このリリースの既知の問題と制限事項について説明します。

#### <a name="deployment"></a>展開

- 以前のリリースからのビッグ データ クラスターのアップグレードはサポートされていません。

   > [!IMPORTANT]
   > 最新リリースをデプロイする前に、データをバックアップし、(以前のバージョンの **azdata** を使用して) 既存のビッグ データ クラスターを削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

- AKS にデプロイした後、そのデプロイから次の 2 つの警告イベントが表示されることがあります。 これらのイベントはどちらも既知の問題ですが、AKS にビッグ データ クラスターを正常にデプロイすることを妨げるものではありません。

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- ビッグ データ クラスターのデプロイが失敗した場合、関連付けられた名前空間が削除されません。 これにより、クラスター上に孤立した名前空間が残される可能性があります。 回避策として、同じ名前を持つクラスターをデプロイする前に、名前空間を手動で削除します。

#### <a name="external-tables"></a>外部テーブル

- ビッグ データ クラスターのデプロイでは、**SqlDataPool** および **SqlStoragePool** の外部データ ソースが作成されなくなりました。 データ プールや記憶域プールへのデータ仮想化をサポートするには、これらのデータ ソースを手動で作成できます。

   ```sql
   -- Create default data sources for SQL Big Data Cluster
   IF NOT EXISTS(SELECT * FROM sys.external_data_sources WHERE name = 'SqlDataPool')
       CREATE EXTERNAL DATA SOURCE SqlDataPool
       WITH (LOCATION = 'sqldatapool://service-mssql-controller:8080/datapools/default');
 
   IF NOT EXISTS(SELECT * FROM sys.external_data_sources WHERE name = 'SqlStoragePool')
       CREATE EXTERNAL DATA SOURCE SqlStoragePool
       WITH (LOCATION = 'sqlhdfs://nmnode-0-svc:50070');
   ```

- サポートされていない列の型を含むテーブルのデータ プール外部テーブルを作成できます。 その外部テーブルにクエリを実行すると、次のようなメッセージが表示されます。

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- 記憶域プール外部テーブルにクエリを実行したときに、基になるファイルが同時に HDFS にコピーされていると、エラーが発生することがあります。

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

- 文字データ型を使用する外部テーブルを Oracle に対して作成している場合、Azure Data Studio 仮想化ウィザードでは、これらの列が外部テーブル定義内の VARCHAR として解釈されます。 これにより、外部テーブル DDL でエラーが発生します。 NVARCHAR2 型を使用するように Oracle スキーマを変更するか、またはウィザードを使用する代わりに手動で EXTERNAL TABLE ステートメントを作成し、NVARCHAR を指定してください。

#### <a name="application-deployment"></a>アプリケーションの展開

- RESTful API から R、Python、または MLeap アプリケーションを呼び出すと、その呼び出しが 5 分でタイムアウトします。

#### <a name="spark-and-notebooks"></a>Spark とノートブック

- POD が再起動すると、Kubernetes 環境で POD IP アドレスが変更される可能性があります。 マスター ポッドの再起動のシナリオでは、Spark セッションが `NoRoteToHostException` で失敗する可能性があります。 これは、新しい IP アドレスで更新されない JVM キャッシュによって発生します。

- Jupyter が既にインストールされ、Windows 上に別の Python が存在する場合は、Spark ノートブックが失敗することがあります。 この問題を回避するには、Jupyter を最新バージョンにアップグレードします。

- ノートブックでは、 **[Add Text] (テキストの追加)** コマンドをクリックすると、テキスト セルが編集モードではなくプレビュー モードで追加されます。 プレビュー アイコンをクリックして編集モードに切り替え、そのセルを編集することができます。

#### <a name="hdfs"></a>HDFS

- HDFS 内のファイルを右クリックしてプレビューすると、次のエラーが表示されることがあります。

   `Error previewing file: File exceeds max size of 30MB`

   Azure Data Studio では現在、30 MB を超えるファイルをプレビューする方法はありません。

- hdfs-site.xml への変更が含まれた HDFS の構成変更はサポートされていません。

#### <a name="security"></a>セキュリティ

- SA_PASSWORD は環境の一部であり (コード ダンプ ファイルなどで) 検出できます。 デプロイの後、マスター インスタンス上の SA_PASSWORD をリセットする必要があります。 これはバグではなく、セキュリティ手順です。 Linux コンテナー内の SA_PASSWORD を変更する方法の詳細については、[SA パスワードの変更](../linux/quickstart-install-connect-docker.md#sapassword)に関するページを参照してください。

- AKS ログに、ビッグ データ クラスターのデプロイのための SA パスワードが含まれている可能性があります。

## <a id="ctp24"></a> CTP 2.4 (3 月)

以下のセクションでは、SQL Server 2019 CTP 2.4 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="whats-new"></a>新機能

| 新機能または更新 | 詳細 |
|:---|:---|
| Spark で TensorFlow を使用しディープ ラーニングを実行する場合の GPU でのサポートに関するガイダンス。 | [GPU をサポートしているビッグ データ クラスターを展開し、TensorFlow を実行します](spark-gpu-tensorflow.md)。 |
| データ ソース **SqlDataPool** と **SqlStoragePool** は、既定では作成されなくなりました。 | 必要に応じてこれらを手動で作成します。 [既知の問題](#externaltablesctp24)を参照してください。 |
| データ プール用の `INSERT INTO SELECT` のサポート。 | 例については、「[Tutorial:Ingest data into a SQL Server data pool with Transact-SQL](tutorial-data-pool-ingest-sql.md)」 (チュートリアル: Transact SQL を使用して SQL Server のデータ プールにデータを取り込む) を参照してください。 |
| オプション `FORCE SCALEOUTEXECUTION` と `DISABLE SCALEOUTEXECUTION` | 外部テーブルに対するクエリでのコンピューティング プールの使用を強制するか、または無効にします。 たとえば、`SELECT TOP(100) * FROM web_clickstreams_hdfs_book_clicks OPTION(FORCE SCALEOUTEXECUTION)` のようにします。 |
| 更新された AKS の展開に関する推奨事項 | AKS でビッグ データ クラスターを評価するときには、サイズ **Standard_L8s** の単一ノードを使用することをお勧めします。 |
| Spark 2.4 への Spark のランタイムのアップグレード。 | |

### <a name="known-issues"></a>既知の問題

以下のセクションでは、このリリースの既知の問題と制限事項について説明します。

#### <a name="deployment"></a>展開

- 以前のリリースからのビッグ データ クラスターのアップグレードはサポートされていません。

   > [!IMPORTANT]
   > 最新リリースをデプロイする前に、データをバックアップし、(以前のバージョンの **azdata** を使用して) 既存のビッグ データ クラスターを削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

- AKS にデプロイした後、そのデプロイから次の 2 つの警告イベントが表示されることがあります。 これらのイベントはどちらも既知の問題ですが、AKS にビッグ データ クラスターを正常にデプロイすることを妨げるものではありません。

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- ビッグ データ クラスターのデプロイが失敗した場合、関連付けられた名前空間が削除されません。 これにより、クラスター上に孤立した名前空間が残される可能性があります。 回避策として、同じ名前を持つクラスターをデプロイする前に、名前空間を手動で削除します。

#### <a name="kubeadm-deployments"></a>kubeadm のデプロイ

kubeadm を使用して複数のコンピューターに Kubernetes をデプロイした場合、ビッグ データ クラスターに接続するために必要なエンドポイントがクラスター管理ポータルに正しく表示されません。 この問題が発生している場合は、サービス エンドポイントの IP アドレスを検出するために次の回避策を使用します。

- クラスター内から接続している場合は、接続先のエンドポイントのサービス IP を取得するために Kubernetes にクエリを実行します。 たとえば、次の **kubectl** コマンドは SQL Server マスター インスタンスの IP アドレスを表示します。

   ```bash
   kubectl get service endpoint-master-pool -n <clusterName> -o=custom-columns="IP:.spec.clusterIP,PORT:.spec.ports[*].nodePort"
   ```

- クラスターの外部から接続している場合は、次の手順を使用して接続します。

   1. `kubectl get pod mssql-master-pool-0 -o jsonpath="Name: {.metadata.name} Status: {.status.hostIP}" -n <clusterName>` で、SQL Server マスター インスタンスを実行しているノードの IP アドレスを取得します。

   1. この IP アドレスを使用して、SQL Server マスター インスタンスに接続します。

   1. 他の外部エンドポイントを取得するために、マスター データベース内の **cluster_endpoint_table** にクエリを実行します。

      これが接続タイムアウトで失敗した場合は、対応するノードがファイアウォールで保護されている可能性があります。 この場合は、Kubernetes クラスター管理者に連絡して、外部に公開されているノード IP を教えてもらう必要があります。 これはどのノードでも発生する可能性があります。 その後、その IP とそれに対応するポートを使用して、クラスターで実行されているさまざまなサービスに接続できます。 たとえば、管理者は次を実行してこの IP を見つけることができます。

      ```
      [root@m12hn01 config]# kubectl cluster-info
      Kubernetes master is running at https://172.50.253.99:6443
      KubeDNS is running at https://172.30.243.91:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
      ```

#### <a name="delete-cluster-fails"></a>クラスターの削除が失敗する

**azdata** を使用してクラスターを削除しようとすると、次のエラーで失敗します。

```
2019-03-26 20:38:11.0614 UTC | INFO | Deleting cluster ...
Error processing command: "TypeError"
delete_namespaced_service() takes 3 positional arguments but 4 were given
Makefile:61: recipe for target 'delete-cluster' failed
make[2]: *** [delete-cluster] Error 1
Makefile:223: recipe for target 'deploy-clean' failed
make[1]: *** [deploy-clean] Error 2
Makefile:203: recipe for target 'deploy-clean' failed
make: *** [deploy-clean] Error 2
```

新しい Python Kubernetes クライアント (バージョン 9.0.0) では名前空間の削除 API が変更され、現在はそれによって **azdata** が停止します。 これは、新しい Kubernetes python クライアントがインストールされている場合にのみ発生します。 この問題は、**kubectl** (`kubectl delete ns <ClusterName>`) を使用してクラスターを直接削除することによって回避できます。あるいは、`sudo pip install kubernetes==8.0.1` を使用して以前のバージョンをインストールできます。

#### <a id="externaltablesctp24"></a> 外部テーブル

- ビッグ データ クラスターのデプロイでは、**SqlDataPool** および **SqlStoragePool** の外部データ ソースが作成されなくなりました。 データ プールや記憶域プールへのデータ仮想化をサポートするには、これらのデータ ソースを手動で作成できます。

   ```sql
   -- Create default data sources for SQL Big Data Cluster
   IF NOT EXISTS(SELECT * FROM sys.external_data_sources WHERE name = 'SqlDataPool')
       CREATE EXTERNAL DATA SOURCE SqlDataPool
       WITH (LOCATION = 'sqldatapool://service-mssql-controller:8080/datapools/default');
 
   IF NOT EXISTS(SELECT * FROM sys.external_data_sources WHERE name = 'SqlStoragePool')
       CREATE EXTERNAL DATA SOURCE SqlStoragePool
       WITH (LOCATION = 'sqlhdfs://service-master-pool:50070');
   ```

- サポートされていない列の型を含むテーブルのデータ プール外部テーブルを作成できます。 その外部テーブルにクエリを実行すると、次のようなメッセージが表示されます。

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- 記憶域プール外部テーブルにクエリを実行したときに、基になるファイルが同時に HDFS にコピーされていると、エラーが発生することがあります。

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

- 文字データ型を使用する外部テーブルを Oracle に対して作成している場合、Azure Data Studio 仮想化ウィザードでは、これらの列が外部テーブル定義内の VARCHAR として解釈されます。 これにより、外部テーブル DDL でエラーが発生します。 NVARCHAR2 型を使用するように Oracle スキーマを変更するか、またはウィザードを使用する代わりに手動で EXTERNAL TABLE ステートメントを作成し、NVARCHAR を指定してください。

#### <a name="application-deployment"></a>アプリケーションの展開

- RESTful API から R、Python、または MLeap アプリケーションを呼び出すと、その呼び出しが 5 分でタイムアウトします。

#### <a name="spark-and-notebooks"></a>Spark とノートブック

- POD が再起動すると、Kubernetes 環境で POD IP アドレスが変更される可能性があります。 マスター ポッドの再起動のシナリオでは、Spark セッションが `NoRoteToHostException` で失敗する可能性があります。 これは、新しい IP アドレスで更新されない JVM キャッシュによって発生します。

- Jupyter が既にインストールされ、Windows 上に別の Python が存在する場合は、Spark ノートブックが失敗することがあります。 この問題を回避するには、Jupyter を最新バージョンにアップグレードします。

- ノートブックでは、 **[Add Text] (テキストの追加)** コマンドをクリックすると、テキスト セルが編集モードではなくプレビュー モードで追加されます。 プレビュー アイコンをクリックして編集モードに切り替え、そのセルを編集することができます。

#### <a name="hdfs"></a>HDFS

- HDFS 内のファイルを右クリックしてプレビューすると、次のエラーが表示されることがあります。

   `Error previewing file: File exceeds max size of 30MB`

   Azure Data Studio では現在、30 MB を超えるファイルをプレビューする方法はありません。

- hdfs-site.xml への変更が含まれた HDFS の構成変更はサポートされていません。

#### <a name="security"></a>セキュリティ

- SA_PASSWORD は環境の一部であり (コード ダンプ ファイルなどで) 検出できます。 デプロイの後、マスター インスタンス上の SA_PASSWORD をリセットする必要があります。 これはバグではなく、セキュリティ手順です。 Linux コンテナー内の SA_PASSWORD を変更する方法の詳細については、[SA パスワードの変更](../linux/quickstart-install-connect-docker.md#sapassword)に関するページを参照してください。

- AKS ログに、ビッグ データ クラスターのデプロイのための SA パスワードが含まれている可能性があります。

## <a id="ctp23"></a> CTP 2.3 (2 月)

以下のセクションでは、SQL Server 2019 CTP 2.3 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="whats-new"></a>新機能

| 新機能または更新 | 詳細 |
| :---------- | :------ |
| IntelliJ のビッグ データ クラスターでの Spark ジョブの送信。 | [IntelliJ で Spark ジョブ[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を送信する](spark-submit-job-intellij-tool-plugin.md) |
| アプリケーションの展開およびクラスター管理の一般的な CLI。 | [アプリを展開する方法[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]](big-data-cluster-create-apps.md) |
| ビッグ データ クラスターにアプリケーションを展開するための VS Code の拡張機能。 | [VS Code を使用してアプリケーションを展開する方法[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](app-deployment-extension.md) |
| **azdata** ツール コマンドの使用法の変更。 | 詳細については、[azdata の既知の問題](#azdatactp23)を参照してください。 |
| ビッグ データ クラスターで Sparklyr を使用する | [SQL Server 2019 のビッグ データ クラスターで Sparklyr を使用する](sparklyr-from-RStudio.md) |
| **HDFS 階層**を使用した、外部の HDFS 互換ストレージのビッグ データ クラスターへのマウント。 | [HDFS 階層](hdfs-tiering.md)に関するページを参照してください。 |
| SQL Server のマスター インスタンスと HDFS/Spark ゲートウェイの新しい統一された接続エクスペリエンス。 | [SQL Server のマスター インスタンスと HDFS/Spark ゲートウェイ](connect-to-big-data-cluster.md)に関するページを参照してください。 |
| **azdata cluster delete** を使用してクラスターを削除すると、ビッグ データ クラスターの一部であった名前空間内のオブジェクトのみが削除されるようになりました。 | 名前空間は削除されません。 ただし、以前のリリースでは、このコマンドを使用すると、名前空間全体が削除されました。 |
| _セキュリティ_ エンドポイントの名前が変更され、統合されました。 | **service-security-lb** と **service-security-nodeport** は、**endpoint-security** エンドポイントに統合されました。 |
| _プロキシ_ エンドポイントの名前が変更され、統合されました。 | **service-proxy-lb** と **service-proxy-nodeport** は、**endpoint-service-proxy** エンドポイントに統合されました。 |
| _コントローラー_ エンドポイントの名前が変更され、統合されました。 | **service-mssql-controller-lb** と **service-mssql-controller-nodeport** は、**endpoint-controller** エンドポイントに統合されました。 |
| &nbsp; | &nbsp; |

### <a name="known-issues"></a>既知の問題

以下のセクションでは、このリリースの既知の問題と制限事項について説明します。

#### <a name="deployment"></a>展開

- 以前のリリースからのビッグ データ クラスターのアップグレードはサポートされていません。

   > [!IMPORTANT]
   > 最新リリースをデプロイする前に、データをバックアップし、(以前のバージョンの **azdata** を使用して) 既存のビッグ データ クラスターを削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

- EULA に同意するには、**ACCEPT_EULA** 環境変数が "yes" または "Yes" である必要があります。 以前のリリースでは "y" や "Y" が許可されましたが、これらは受け付けられなくなり、デプロイは失敗します。

- **CLUSTER_PLATFORM** 環境変数には、以前のリリースに存在した既定値がありません。

- AKS にデプロイした後、そのデプロイから次の 2 つの警告イベントが表示されることがあります。 これらのイベントはどちらも既知の問題ですが、AKS にビッグ データ クラスターを正常にデプロイすることを妨げるものではありません。

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- ビッグ データ クラスターのデプロイが失敗した場合、関連付けられた名前空間が削除されません。 これにより、クラスター上に孤立した名前空間が残される可能性があります。 回避策として、同じ名前を持つクラスターをデプロイする前に、名前空間を手動で削除します。

#### <a name="kubeadm-deployments"></a>kubeadm のデプロイ

kubeadm を使用して複数のコンピューターに Kubernetes をデプロイした場合、ビッグ データ クラスターに接続するために必要なエンドポイントがクラスター管理ポータルに正しく表示されません。 この問題が発生している場合は、サービス エンドポイントの IP アドレスを検出するために次の回避策を使用します。

- クラスター内から接続している場合は、接続先のエンドポイントのサービス IP を取得するために Kubernetes にクエリを実行します。 たとえば、次の **kubectl** コマンドは SQL Server マスター インスタンスの IP アドレスを表示します。

   ```bash
   kubectl get service endpoint-master-pool -n <clusterName> -o=custom-columns="IP:.spec.clusterIP,PORT:.spec.ports[*].nodePort"
   ```

- クラスターの外部から接続している場合は、次の手順を使用して接続します。

   1. `kubectl get pod mssql-master-pool-0 -o jsonpath="Name: {.metadata.name} Status: {.status.hostIP}" -n <clusterName>` で、SQL Server マスター インスタンスを実行しているノードの IP アドレスを取得します。

   1. この IP アドレスを使用して、SQL Server マスター インスタンスに接続します。

   1. 他の外部エンドポイントを取得するために、マスター データベース内の **cluster_endpoint_table** にクエリを実行します。

      これが接続タイムアウトで失敗した場合は、対応するノードがファイアウォールで保護されている可能性があります。 この場合は、Kubernetes クラスター管理者に連絡して、外部に公開されているノード IP を教えてもらう必要があります。 これはどのノードでも発生する可能性があります。 その後、その IP とそれに対応するポートを使用して、クラスターで実行されているさまざまなサービスに接続できます。 たとえば、管理者は次を実行してこの IP を見つけることができます。

      ```
      [root@m12hn01 config]# kubectl cluster-info
      Kubernetes master is running at https://172.50.253.99:6443
      KubeDNS is running at https://172.30.243.91:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
      ```

#### <a id="azdatactp23"></a> azdata

- **azdata** ツールが、動詞-名詞のコマンドの順序から名詞-動詞の順序に変更されました。 たとえば、`azdata create cluster` が `azdata cluster create` になりました。

- `azdata cluster create` を使用してクラスターを作成する場合は、`--name` パラメーターが必要になりました。

   ```bash
   azdata cluster create --name <cluster_name>
   ```

- ビッグ データ クラスターと **azdata** の最新バージョンへのアップグレードに関する重要な情報については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

#### <a name="external-tables"></a>外部テーブル

- サポートされていない列の型を含むテーブルのデータ プール外部テーブルを作成できます。 その外部テーブルにクエリを実行すると、次のようなメッセージが表示されます。

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- 記憶域プール外部テーブルにクエリを実行したときに、基になるファイルが同時に HDFS にコピーされていると、エラーが発生することがあります。

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

- 文字データ型を使用する外部テーブルを Oracle に対して作成している場合、Azure Data Studio 仮想化ウィザードでは、これらの列が外部テーブル定義内の VARCHAR として解釈されます。 これにより、外部テーブル DDL でエラーが発生します。 NVARCHAR2 型を使用するように Oracle スキーマを変更するか、またはウィザードを使用する代わりに手動で EXTERNAL TABLE ステートメントを作成し、NVARCHAR を指定してください。

#### <a name="application-deployment"></a>アプリケーションの展開

- RESTful API から R、Python、または MLeap アプリケーションを呼び出すと、その呼び出しが 5 分でタイムアウトします。

#### <a name="spark-and-notebooks"></a>Spark とノートブック

- POD が再起動すると、Kubernetes 環境で POD IP アドレスが変更される可能性があります。 マスター ポッドの再起動のシナリオでは、Spark セッションが `NoRoteToHostException` で失敗する可能性があります。 これは、新しい IP アドレスで更新されない JVM キャッシュによって発生します。

- Jupyter が既にインストールされ、Windows 上に別の Python が存在する場合は、Spark ノートブックが失敗することがあります。 この問題を回避するには、Jupyter を最新バージョンにアップグレードします。

- ノートブックでは、 **[Add Text] (テキストの追加)** コマンドをクリックすると、テキスト セルが編集モードではなくプレビュー モードで追加されます。 プレビュー アイコンをクリックして編集モードに切り替え、そのセルを編集することができます。

#### <a name="hdfs"></a>HDFS

- HDFS 内のファイルを右クリックしてプレビューすると、次のエラーが表示されることがあります。

   `Error previewing file: File exceeds max size of 30MB`

   Azure Data Studio では現在、30 MB を超えるファイルをプレビューする方法はありません。

- hdfs-site.xml への変更が含まれた HDFS の構成変更はサポートされていません。

#### <a name="security"></a>セキュリティ

- SA_PASSWORD は環境の一部であり (コード ダンプ ファイルなどで) 検出できます。 デプロイの後、マスター インスタンス上の SA_PASSWORD をリセットする必要があります。 これはバグではなく、セキュリティ手順です。 Linux コンテナー内の SA_PASSWORD を変更する方法の詳細については、[SA パスワードの変更](../linux/quickstart-install-connect-docker.md#sapassword)に関するページを参照してください。

- AKS ログに、ビッグ データ クラスターのデプロイのための SA パスワードが含まれている可能性があります。

## <a id="ctp22"></a> CTP 2.2 (2018 年 12 月)

以下のセクションでは、SQL Server 2019 CTP 2.2 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="new-features"></a>新しい機能

- `/portal` (**https://\<ip-address\>:30777/portal**) でアクセスされるクラスター管理ポータル。
- マスター プールのサービス名が `service-master-pool-lb` と `service-master-pool-nodeport` から `endpoint-master-pool` に変更されました。
- 新しいバージョンの **azdata** と更新されたイメージ。
- その他のバグ修正および機能強化。

### <a name="known-issues"></a>既知の問題

以下のセクションでは、このリリースの既知の問題と制限事項について説明します。

#### <a name="deployment"></a>展開

- 以前のリリースからのビッグ データ クラスターのアップグレードはサポートされていません。 最新リリースをデプロイする前に、既存のビッグ データ クラスターをすべてバックアップして削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

- AKS にデプロイした後、そのデプロイから次の 2 つの警告イベントが表示されることがあります。 これらのイベントはどちらも既知の問題ですが、AKS にビッグ データ クラスターを正常にデプロイすることを妨げるものではありません。

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- ビッグ データ クラスターのデプロイが失敗した場合、関連付けられた名前空間が削除されません。 これにより、クラスター上に孤立した名前空間が残される可能性があります。 回避策として、同じ名前を持つクラスターをデプロイする前に、名前空間を手動で削除します。

#### <a name="cluster-administration-portal"></a>クラスターの管理ポータル

クラスター管理ポータルに SQL Server マスター インスタンスのエンドポイントが表示されません。 マスター インスタンスの IP アドレスとポートを見つけるには、次の **kubectl** コマンドを使用します。

```
kubectl get svc endpoint-master-pool -n <your-big-data-cluster-name>
```

#### <a name="external-tables"></a>外部テーブル

- サポートされていない列の型を含むテーブルのデータ プール外部テーブルを作成できます。 その外部テーブルにクエリを実行すると、次のようなメッセージが表示されます。

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- 記憶域プール外部テーブルにクエリを実行したときに、基になるファイルが同時に HDFS にコピーされていると、エラーが発生することがあります。

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

#### <a name="spark-and-notebooks"></a>Spark とノートブック

- POD が再起動すると、Kubernetes 環境で POD IP アドレスが変更される可能性があります。 マスター ポッドの再起動のシナリオでは、Spark セッションが `NoRoteToHostException` で失敗する可能性があります。 これは、新しい IP アドレスで更新されない JVM キャッシュによって発生します。

- Jupyter が既にインストールされ、Windows 上に別の Python が存在する場合は、Spark ノートブックが失敗することがあります。 この問題を回避するには、Jupyter を最新バージョンにアップグレードします。

- ノートブックでは、 **[Add Text] (テキストの追加)** コマンドをクリックすると、テキスト セルが編集モードではなくプレビュー モードで追加されます。 プレビュー アイコンをクリックして編集モードに切り替え、そのセルを編集することができます。

#### <a name="hdfs"></a>HDFS

- HDFS 内のファイルを右クリックしてプレビューすると、次のエラーが表示されることがあります。

   `Error previewing file: File exceeds max size of 30MB`

   Azure Data Studio では現在、30 MB を超えるファイルをプレビューする方法はありません。

- hdfs-site.xml への変更が含まれた HDFS の構成変更はサポートされていません。

#### <a name="security"></a>セキュリティ

- SA_PASSWORD は環境の一部であり (コード ダンプ ファイルなどで) 検出できます。 デプロイの後、マスター インスタンス上の SA_PASSWORD をリセットする必要があります。 これはバグではなく、セキュリティ手順です。 Linux コンテナー内の SA_PASSWORD を変更する方法の詳細については、[SA パスワードの変更](../linux/quickstart-install-connect-docker.md#sapassword)に関するページを参照してください。

- AKS ログに、ビッグ データ クラスターのデプロイのための SA パスワードが含まれている可能性があります。

## <a id="ctp21"></a> CTP 2.1 (2018 年 11 月)

以下のセクションでは、SQL Server 2019 CTP 2.1 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="new-features"></a>新しい機能

- ビッグ データ クラスターでの [Python および R アプリのデプロイ](big-data-cluster-create-apps.md)。
- 新しいバージョンの **azdata** と更新されたイメージ。 
- その他のバグ修正および機能強化。

### <a name="known-issues"></a>既知の問題

以下のセクションでは、CTP [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] 2.1 でのに関する既知の問題について説明します。

#### <a name="deployment"></a>展開

- 以前のリリースからのビッグ データ クラスターのアップグレードはサポートされていません。 最新リリースをデプロイする前に、既存のビッグ データ クラスターをすべてバックアップして削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

- AKS にデプロイした後、そのデプロイから次の 2 つの警告イベントが表示されることがあります。 これらのイベントはどちらも既知の問題ですが、AKS にビッグ データ クラスターを正常にデプロイすることを妨げるものではありません。

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- ビッグ データ クラスターのデプロイが失敗した場合、関連付けられた名前空間が削除されません。 これにより、クラスター上に孤立した名前空間が残される可能性があります。 回避策として、同じ名前を持つクラスターをデプロイする前に、名前空間を手動で削除します。

#### <a name="admin-portal"></a>管理ポータル

- [msqlctl-ctp コマンドを使用してアプリを作成](big-data-cluster-create-apps.md)し、それを SQL Server ビッグ データ クラスターにデプロイすると、クラスター管理ポータルでは、そのアプリケーションがデプロイされたポッドが管理者ポータルの [コントローラー] セクションに [不明] として表示されます。

#### <a name="external-tables"></a>外部テーブル

- サポートされていない列の型を含むテーブルのデータ プール外部テーブルを作成できます。 その外部テーブルにクエリを実行すると、次のようなメッセージが表示されます。

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- 記憶域プール外部テーブルにクエリを実行したときに、基になるファイルが同時に HDFS にコピーされていると、エラーが発生することがあります。

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

#### <a name="spark-and-notebooks"></a>Spark とノートブック

- POD が再起動すると、Kubernetes 環境で POD IP アドレスが変更される可能性があります。 マスター ポッドの再起動のシナリオでは、Spark セッションが `NoRoteToHostException` で失敗する可能性があります。 これは、新しい IP アドレスで更新されない JVM キャッシュによって発生します。

- Jupyter が既にインストールされ、Windows 上に別の Python が存在する場合は、Spark ノートブックが失敗することがあります。 この問題を回避するには、Jupyter を最新バージョンにアップグレードします。

- ノートブックでは、 **[Add Text] (テキストの追加)** コマンドをクリックすると、テキスト セルが編集モードではなくプレビュー モードで追加されます。 プレビュー アイコンをクリックして編集モードに切り替え、そのセルを編集することができます。

#### <a name="hdfs"></a>HDFS

- HDFS 内のファイルを右クリックしてプレビューすると、次のエラーが表示されることがあります。

   `Error previewing file: File exceeds max size of 30MB`

   Azure Data Studio では現在、30 MB を超えるファイルをプレビューする方法はありません。

- hdfs-site.xml への変更が含まれた HDFS の構成変更はサポートされていません。

#### <a name="security"></a>セキュリティ

- SA_PASSWORD は環境の一部であり (コード ダンプ ファイルなどで) 検出できます。 デプロイの後、マスター インスタンス上の SA_PASSWORD をリセットする必要があります。 これはバグではなく、セキュリティ手順です。 Linux コンテナー内の SA_PASSWORD を変更する方法の詳細については、[SA パスワードの変更](../linux/quickstart-install-connect-docker.md#sapassword)に関するページを参照してください。

- AKS ログに、ビッグ データ クラスターのデプロイのための SA パスワードが含まれている可能性があります。

## <a id="ctp20"></a> CTP 2.0 (2018 年 10 月)

以下のセクションでは、SQL Server 2019 CTP 2.0 のビッグ データ クラスターの新機能と既知の問題について説明します。

### <a name="new-features"></a>新しい機能

- azdata 管理ツールを使用した簡単なデプロイ エクスペリエンス
- Azure Data Studio でのネイティブなノートブック エクスペリエンス
- SQL Server のストレージ インスタンスを経由した HDFS ファイルへのクエリ実行
- SQL Server、Oracle、MongoDB、および HDFS へのマスターを経由したデータ仮想化
- Azure Data Studio での SQL Server と Oracle のためのデータ仮想化ウィザード
- マスター上の ML サービス
- 監視とトラブルシューティングのために使用できるクラスター管理ポータル
- Azure Data Studio での Spark ジョブの送信 
- クラスター管理ポータルでの Spark UI
- ストレージ クラスへのボリューム マウント
- マスターからのデータ プールに対するクエリ
- SSMS での分散クエリの計画の表示
- azdata 管理ツール用の Pip パッケージ
- コントローラー サービスを使用した組み込みのデプロイ エンジン

### <a name="known-issues"></a>既知の問題

以下のセクションでは、CTP [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] 2.0 でのに関する既知の問題について説明します。

#### <a name="deployment"></a>展開

- Azure Kubernetes Service (AKS) を使用している場合、Kubernetes の推奨されるバージョンは、ディスクのサイズ変更がサポートされない 1.10.* です。 デプロイ時にストレージのサイズを適切に設定していることを確認してください。 ストレージ サイズを調整する方法の詳細については、[データ永続化](concept-data-persistence.md)に関する記事を参照してください。 VM にデプロイされた Kubernetes の場合、推奨されるバージョンは 1.11 です。

- AKS にデプロイした後、そのデプロイから次の 2 つの警告イベントが表示されることがあります。 これらのイベントはどちらも既知の問題ですが、AKS にビッグ データ クラスターを正常にデプロイすることを妨げるものではありません。

   `Warning  FailedMount: Unable to mount volumes for pod "mssql-storage-pool-default-1_sqlarisaksclus(c83eae70-c81b-11e8-930f-f6b6baeb7348)": timeout expired waiting for volumes to attach or mount for pod "sqlarisaksclus"/"mssql-storage-pool-default-1". list of unmounted volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs]. list of unattached volumes=[storage-pool-storage hdfs storage-pool-mlservices-storage hadoop-logs storage-pool-java-storage secrets default-token-q9mlx]`

   `Warning  Unhealthy: Readiness probe failed: cat: /tmp/provisioner.done: No such file or directory`

- ビッグ データ クラスターのデプロイが失敗した場合、関連付けられた名前空間が削除されません。 これにより、クラスター上に孤立した名前空間が残される可能性があります。 回避策として、同じ名前を持つクラスターをデプロイする前に、名前空間を手動で削除します。

#### <a name="external-tables"></a>外部テーブル

- サポートされていない列の型を含むテーブルのデータ プール外部テーブルを作成できます。 その外部テーブルにクエリを実行すると、次のようなメッセージが表示されます。

   `Msg 7320, Level 16, State 110, Line 44 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 105079; Columns with large object types are not supported for external generic tables.`

- 記憶域プール外部テーブルにクエリを実行したときに、基になるファイルが同時に HDFS にコピーされていると、エラーが発生することがあります。

   `Msg 7320, Level 16, State 110, Line 157 Cannot execute the query "Remote Query" against OLE DB provider "SQLNCLI11" for linked server "(null)". 110806;A distributed query failed: One or more errors occurred.`

#### <a name="spark-and-notebooks"></a>Spark とノートブック

- POD が再起動すると、Kubernetes 環境で POD IP アドレスが変更される可能性があります。 マスター ポッドの再起動のシナリオでは、Spark セッションが `NoRoteToHostException` で失敗する可能性があります。 これは、新しい IP アドレスで更新されない JVM キャッシュによって発生します。

- Jupyter が既にインストールされ、Windows 上に別の Python が存在する場合は、Spark ノートブックが失敗することがあります。 この問題を回避するには、Jupyter を最新バージョンにアップグレードします。

- ノートブックでは、 **[Add Text] (テキストの追加)** コマンドをクリックすると、テキスト セルが編集モードではなくプレビュー モードで追加されます。 プレビュー アイコンをクリックして編集モードに切り替え、そのセルを編集することができます。

#### <a name="hdfs"></a>HDFS

- HDFS 内のファイルを右クリックしてプレビューすると、次のエラーが表示されることがあります。

   `Error previewing file: File exceeds max size of 30MB`

   Azure Data Studio では現在、30 MB を超えるファイルをプレビューする方法はありません。

- hdfs-site.xml への変更が含まれた HDFS の構成変更はサポートされていません。

#### <a name="security"></a>セキュリティ

- SA_PASSWORD は環境の一部であり (コード ダンプ ファイルなどで) 検出できます。 デプロイの後、マスター インスタンス上の SA_PASSWORD をリセットする必要があります。 これはバグではなく、セキュリティ手順です。 Linux コンテナー内の SA_PASSWORD を変更する方法の詳細については、[SA パスワードの変更](../linux/quickstart-install-connect-docker.md#sapassword)に関するページを参照してください。

- AKS ログに、ビッグ データ クラスターのデプロイのための SA パスワードが含まれている可能性があります。

## <a name="next-steps"></a>次の手順

の詳細[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]について[は[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]](big-data-cluster-overview.md)、「」を参照してください。
