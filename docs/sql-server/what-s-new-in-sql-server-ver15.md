---
title: SQL Server 2019 の新機能 | Microsoft Docs
ms.date: 08/21/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: release-landing
ms.topic: article
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=sql-server-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 275ef0ef83c073726cebf80b63e1d8f9640eca81
ms.sourcegitcommit: 676458a9535198bff4c483d67c7995d727ca4a55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903643"
---
# <a name="whats-new-in-includesql-server-2019includessssqlv15-mdmd"></a>[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] の新機能

[!INCLUDE[tsql-appliesto-ss-xxxx-xxxx-xxx-md](../includes/tsql-appliesto-ss-xxxx-xxxx-xxx-md.md)]

以前のリリースを基にして構築された [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] では、開発言語、データ型、オンプレミスまたはクラウド、オペレーティング システムを選択できるプラットフォームとしての SQL Server がいっそう成長しています。

この記事では、[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] の新機能と拡張機能について要約します。

詳細および既知の問題については、「[[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] リリース ノート](sql-server-ver15-release-notes.md)」 をご覧ください。

**[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] で最良のエクスペリエンスを得るには、[最新のツール](what-s-new-in-sql-server-ver15-prerelease.md#tools)を使用してください。**

>[!NOTE]
>内容は [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] リリース候補に関するものです。 リリース候補はプレリリース版ソフトウェアです。 情報は変更される可能性があります。 サポートのシナリオについては、「[サポート](#support)」を参照してください。
>
>このリリースには、Community Technology Preview (CTP) リリースで以前に発表された機能強化が含まれています。 機能強化では機能、バグの修正、セキュリティの強化、および最適化されたパフォーマンスが追加されています。 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] リリース候補より前の CTP リリースで導入または強化された機能の一覧については、「[[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] CTP アナウンス アーカイブ](what-s-new-in-sql-server-ver15-prerelease.md)」を参照してください。

[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] では、[!INCLUDE[sql-server](../includes/ssnoversion-md.md)] 用の [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)] が導入されています。 また、SQL Server データベース エンジン、SQL Server Analysis Services、SQL Server Machine Learning Services、SQL Server on Linux、SQL Server マスター データ サービスに対する追加機能と機能強化も提供されています。

以下のセクションでは、これらの機能の概要について説明します。

## [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]

| 新機能または更新 | 詳細 |
|:---|:---|
| スケーラブルなビッグ データ ソリューション | Kubernetes で実行している SQL Server、Spark、HDFS コンテナーの[スケーラブルなクラスターを配置する](../big-data-cluster/deploy-get-started.md) <br/><br/> Transact-SQL または Spark からビッグ データの読み取り、書き込み、処理を行う<br/><br/> 大量のビッグ データを使用して、価値の高いリレーショナル データを簡単に組み合わせて分析する<br/><br/>外部データ ソースを照会する<br/><br/>SQL Server によって管理される HDFS にビッグ データを格納する<br/><br/>クラスターを介して複数の外部データ ソースからデータを照会する<br/><br/> AI、機械学習、その他の分析タスクにデータを使用する<br/><br/> [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)] にアプリケーションをデプロイして実行する <br/>|
| &nbsp; | &nbsp; |

詳細については、「[SQL Server [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)] とは](../big-data-cluster/big-data-cluster-overview.md)」を参照してください。

[[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] (CTP) アナウンス アーカイブ](what-s-new-in-sql-server-ver15-prerelease.md)には、この機能の以前のすべての CTP リリースで発表および変更された機能の一覧が含まれています。

>[!NOTE]
>[!INCLUDE[ssbdc-rcnote](../includes/ssbigdataclusters-ver15-rcnote.md)]

## <a name="database-engine"></a>データベース エンジン

### <a name="security"></a>セキュリティ

|新機能または更新 | 詳細 |
|:---|:---|
|暗号化された列のインデックス付け|ランダム化された暗号化およびエンクレーブ対応のキーを使用して暗号化された列のインデックスを作成し、リッチなクエリのパフォーマンスを向上させます (`LIKE` と比較演算子を使用)。 「[セキュリティで保護されたエンクレーブが設定された Always Encrypted](../relational-databases/security/encryption/always-encrypted-enclaves.md)」をご覧ください。
|Transparent Data Encryption (TDE) に対する初期スキャンの一時停止および再開|[Transparent Data Encryption (TDE) スキャンの一時停止と再開](../relational-databases/security/encryption/transparent-data-encryption.md#scan-suspend-resume)に関するページを参照してください。|
|SQL Server 構成マネージャーでの証明書管理|[証明書の管理 (SQL Server 構成マネージャー)](../database-engine/configure-windows/manage-certificates.md) に関するページを参照してください。|
| &nbsp; | &nbsp; |

### <a name="graph"></a>グラフ

|新機能または更新 | 詳細 |
|:---|:---|
|エッジ制約のカスケード削除アクション |グラフ データベースでのエッジ制約で連鎖削除操作を定義します。 「[エッジ制約](../relational-databases/tables/graph-edge-constraints.md)」を参照してください。 |
|新しいグラフ関数 - `SHORTEST_PATH` | `MATCH` 内で `SHORTEST_PATH` を使用し、グラフ内の任意の 2 ノード間の最短パスを検索するか、任意の長さのトラバーサルを実行します。|
|パーティション テーブルとパーティション インデックス| パーティション テーブルとパーティション インデックスのデータは、グラフ データベース内の複数のファイル グループに分散できるように、複数の単位に分割されます。 |
|グラフ一致クエリで派生テーブルまたはビューの別名を使用する |[グラフ一致クエリ](../t-sql/queries/match-sql-graph.md)に関するページを参照してください。 |
| &nbsp; | &nbsp; |

### <a name="indexes"></a>インデックス

|新機能または更新 | 詳細 |
|:---|:---|
|`OPTIMIZE_FOR_SEQUENTIAL_KEY`|インデックスへの高コンカレンシーの挿入のスループット向上に役立つ、[!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 内での最適化を有効にします。 このオプションは、最終ページ挿入の競合が起きやすいインデックスを対象としています。これは、一般に、ID 列、シーケンス、または日付/時刻列などの連続したキーを持つインデックスでよく見られます。 詳しくは、「[CREATE INDEX](../t-sql/statements/create-index-transact-sql.md#sequential-keys)」をご覧ください。|
|オンラインでのクラスター化列ストア インデックスのビルドとリビルド | [オンラインでのインデックス操作の実行](../relational-databases/indexes/perform-index-operations-online.md)に関するページを参照してください。 |
|再開可能なオンライン行ストア インデックスのビルド | [オンラインでのインデックス操作の実行](../relational-databases/indexes/perform-index-operations-online.md)に関するページを参照してください。 |
| &nbsp; | &nbsp; |

### <a name="in-memory-databases"></a>メモリ内のデータベース

|新機能または更新 | 詳細 |
|:---|:---|
|ハイブリッド バッファー プールの DDL コントロール |[ハイブリッド バッファー プール](../database-engine/configure-windows/hybrid-buffer-pool.md)を使用すると、永続的なメモリ (PMEM) デバイス上に置かれたデータベース ファイル上のデータベース ページが必要に応じて直接アクセスされます。|
| &nbsp; | &nbsp; |

### <a name="unicode-support"></a>Unicode のサポート

|新機能または更新 | 詳細 |
|:---|:---|
|UTF-8 文字エンコードのサポート |インポート エンコードとエクスポート エンコードに対する UTF-8 文字、および文字列データのデータベース レベルまたは列レベルの照合順序がサポートされます。 これにより、グローバルな多言語データベース アプリケーションとサービスを提供する必要性が、顧客の要求と特定の市場規制を満たすために重要である、グローバルなスケールへのアプリケーションの拡張がサポートされます。 「[照合順序と Unicode のサポート](../relational-databases/collations/collation-and-unicode-support.md)」を参照してください<br/><br/>[[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] リリース候補では、Polybase 外部テーブルおよび Always Encrypted に対する UTF-8 のサポートが有効になります。|
| &nbsp; | &nbsp; |

### <a name="polybase"></a>PolyBase

|新機能または更新 | 詳細 |
|:---|:---|
|外部テーブルのクエリを実行する |外部テーブルの列名は、SQL Server、Oracle、Teradata、MongoDB および ODBC データ ソースのクエリに使用されるようになりました。 「[PolyBase とは](../relational-databases/polybase/polybase-guide.md)」を参照してください。|
|UTF-8 文字エンコードのサポート|外部テーブルでの UTF-8 文字がサポートされます。 「[照合順序と Unicode のサポート](../relational-databases/collations/collation-and-unicode-support.md)」を参照してください|
| &nbsp; | &nbsp; |

### <a name="server-settings"></a>サーバーの設定

|新機能または更新 | 詳細 |
|:---|:---|
|ハイブリッド バッファー プール| 永続的なメモリ (PMEM) デバイス上に置かれたデータベース ファイル上のデータベース ページが必要に応じて直接アクセスされる [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] の新機能。 「[ハイブリッド バッファー プール](../database-engine/configure-windows/hybrid-buffer-pool.md)」を参照してください。|
| &nbsp; | &nbsp; |

### <a name="performance-monitoring"></a>パフォーマンスの監視

|新機能または更新 | 詳細 |
|:---|:---|
|`WAIT_ON_SYNC_STATISTICS_REFRESH` | `sys.dm_os_wait_stats` 動的管理ビューの新しい待機の種類。 これには、統計更新の同期操作に費やされたインスタンス レベルの累積時間が表示されます。 [`sys.dm_os_wait_stats`](../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md)に関するページを参照してください。|
|クエリ ストア用のカスタム キャプチャ ポリシー|有効にすると、新しいクエリ ストアのキャプチャ ポリシーの設定で、特定のサーバーでのデータ収集を微調整するための追加のクエリ ストアを使用できるようになります。 詳しくは、「[ALTER DATABASE SET オプション](../t-sql/statements/alter-database-transact-sql-set-options.md)」をご覧ください。|
|`LIGHTWEIGHT_QUERY_PROFILING`|新しいデータベース スコープの構成。 [`LIGHTWEIGHT_QUERY_PROFILING`](../t-sql/statements/alter-database-scoped-configuration-transact-sql.md#lqp)に関するページを参照してください。 |
|`command` 列の `sys.dm_exec_requests` | クエリの実行を続行する前に `SELECT` が、統計更新の同期操作の完了を待機している場合は、`SELECT (STATMAN)` が表示されます。 [`sys.dm_exec_requests`](../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)に関するページを参照してください。|
|`sys.dm_exec_query_plan_stats` |新しい DMF では、ほとんどのクエリについて最後の既知の実際の実行プランと同等のものが返されます。 [sys.dm_exec_query_plan_stats](../relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql.md) に関するページを参照してください。|
|`LAST_QUERY_PLAN_STATS` | `sys.dm_exec_query_plan_stats` を有効にする新しいデータベース スコープの構成。 「[ALTER DATABASE SCOPED CONFIGURATION (ALTER データベース スコープ ベースの構成)](../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)」を参照してください。|
|`query_post_execution_plan_profile` | 拡張イベントでは、標準プロファイリングを使用する `query_post_execution_showplan` とは異なり、軽量プロファイリングに基づいて、実際の実行プランと同等のものを収集します。 [クエリ プロファイリング インフラストラクチャ](../relational-databases/performance/query-profiling-infrastructure.md)に関するページを参照してください。|
| &nbsp; | &nbsp; |

### <a name="language-extensions"></a>言語拡張機能

|新機能または更新 | 詳細 |
|:---|:---|
|新しい Java 言語 SDK | SQL Server から実行できる Java プログラムの開発を簡略化します。 「[SQL Server 用の Microsoft Extensibility SDK for Java](../language-extensions/how-to/extensibility-sdk-java-sql-server.md)」を参照してください。 |
|Java 言語の SDK はオープンソースです |[Microsoft SQL Server 用の Microsoft Extensibility SDK for Java](https://docs.microsoft.com/sql/language-extensions/how-to/extensibility-sdk-java-sql-server) がオープン ソース化され、[GitHub で入手可能](https://github.com/microsoft/sql-server-language-extensions)になりました。|
|Java データ型のサポート|[Java のデータ型](../language-extensions/how-to/java-to-sql-data-types.md)に関するページを参照してください。|
|新しい既定の Java Runtime | SQL Server には、製品全体での Java サポート用に Azul Systems Zulu Embedded が含まれようになりました。 詳細については、「[Free supported Java in SQL Server 2019 is now available](https://cloudblogs.microsoft.com/sqlserver/2019/07/24/free-supported-java-in-sql-server-2019-is-now-available/)」 (SQL Server 2019 で無料でサポートされる Java が利用可能になりました) を参照してください。 |
|SQL Server 言語拡張機能| 拡張性フレームワークで外部コードを実行します。 「[SQL Server 言語拡張機能](https://docs.microsoft.com/sql/language-extensions/language-extensions-overview)」を参照してください。
|外部言語を登録する|新しい DDL `CREATE EXTERNAL LANGUAGE` では、Java などの外部の言語を SQL Server に登録します。 [CREATE EXTERNAL LANGUAGE](../t-sql/statements/create-external-language-transact-sql.md) に関するページを参照してください。 |
| &nbsp; | &nbsp; |

### <a name="spatial"></a>空間インデックス

|新機能または更新 | 詳細 |
|:---|:---|
| 新しい Spatial Reference Identifier (SRID) |[Australian GDA2020](http://www.ga.gov.au/scientific-topics/positioning-navigation/geodesy/datums-projections/gda2020) により、全地球測位システム (GPS) に対しさらに緊密に配置された、より堅牢で正確な測量基準点が提供されます。 新しい SRID は次のとおりです。<br/><br/> - 7843: 地理 2D 用<br/> - 7844: 地理 3D 用 <br/><br/>[sys.spatial_reference_systems](../relational-databases/system-catalog-views/sys-spatial-reference-systems-transact-sql.md) ビューには、新しい SRID の定義が含まれています。 |
| &nbsp; | &nbsp; |

### <a name="performance"></a>パフォーマンス

|新機能または更新 | 詳細 |
|:---|:---|
|高速データベース復旧 | データベースごとに高速データベース復旧を有効にする [高速データベース復旧](../relational-databases/backup-restore/restore-and-recovery-overview-sql-server.md#adr)に関するページを参照してください。|
|高速順方向カーソルと静的カーソルを強制する | 高速順方向カーソルと静的カーソルのサポートを強制するクエリ ストア プラン [高速順方向カーソルと静的カーソルのサポートを強制するプラン](../relational-databases/performance/monitoring-performance-by-using-the-query-store.md#ctp23)に関するページを参照してください。|
|ワークロードの再コンパイルの削減| 複数のスコープを超えて一時テーブルを使用して改善します。 [ワークロードの再コンパイルの削減](../relational-databases/tables/tables.md#ctp23)に関するページを参照してください。 |
|間接チェックポイントのスケーラビリティ |[間接チェックポイントのスケーラビリティの向上](../relational-databases/logs/database-checkpoints-sql-server.md#ctp23)に関するページを参照してください。|
|メモリ最適化 `tempdb` メタデータ| [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] では、[メモリ内データベース](../relational-databases/in-memory-database.md)機能ファミリの一部として、メモリ最適化 `tempdb` メタデータという新機能が導入されています。この機能により、効果的にこのボトルネックが除去され、`tempdb` が多用されるワークロードに対して新たなレベルのスケーラビリティが実現されます。 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] では、一時テーブルのメタデータの管理に関連するシステム テーブルを、ラッチ フリーの非持続的メモリ最適化テーブルに移動できます。 「[メモリ最適化 `tempdb` メタデータ](../relational-databases/databases/tempdb-database.md#memory-optimized-tempdb-metadata)」を参照してください。|
|PFS の同時更新|[PFS ページ](https://techcommunity.microsoft.com/t5/SQL-Server/Under-the-covers-GAM-SGAM-and-PFS-pages/ba-p/383125)はデータベース ファイル内の特別なページであり、オブジェクト用の領域を割り当てるときに空き領域を探すために SQL Server によって使用されます。 PFS ページでのページ ラッチの競合は、一般に [`tempdb`](https://support.microsoft.com/en-us/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d) に関連付けられますが、多数の同時オブジェクト割り当てスレッドがあるときは、ユーザー データベースでも発生する可能性があります。 この機能強化により、PFS の更新でのコンカレンシー管理方法が変更され、排他的ラッチではなく共有ラッチで更新できるようになります。 この動作は、[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 以降のすべてのデータベース (`tempdb` を含む) で、既定でオンになります。|
|行モード メモリ許可フィードバック |バッチ モードと行モード両方の演算子のメモリ許可サイズを調整することで、バッチ モード メモリ許可フィードバックの機能が拡張されます。 これにより、メモリが無駄になってコンカレンシーが低下する過度の許可を自動的に修正し、負荷の高いディスクへの書き込みが発生する原因になる不十分なメモリ許可を修正できます。 「[行モード メモリ許可フィードバック](../relational-databases/performance/intelligent-query-processing.md#row-mode-memory-grant-feedback)」を参照してください。 |
|テーブル変数の遅延コンパイル|テーブル変数を参照するクエリのプランの品質および全体的なパフォーマンスが向上します。 最適化と最初のコンパイルの実行中に、この機能は実際テーブル変数の行数に基づくカーディナリティの推定を反映します。 この正確な行数の情報によって、ダウンストリーム プラン操作が最適化されます。 「[テーブル変数の遅延コンパイル](../relational-databases/performance/intelligent-query-processing.md#table-variable-deferred-compilation)」を参照してください。 |
|`APPROX_COUNT_DISTINCT `|絶対精度は重要でないが応答性は重要であるシナリオでは、優れたコンカレンシーのための `COUNT(DISTINCT())` よりリソース使用量が少ない `APPROX_COUNT_DISTINCT` で大規模なデータセットを集計します。 「[概数クエリ処理](../relational-databases/performance/intelligent-query-processing.md#approximate-query-processing)」を参照してください。|
|行ストアでのバッチ モード|行ストアのバッチ モードでは、列ストア インデックスを必要とせずに、バッチ モードで実行できます。 バッチ モードの実行では、分析ワークロードの間の CPU 使用効率が向上しますが、[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] までは、クエリに列ストア インデックスを使用する操作が含まれている場合にのみ使用されました。 ただし、一部のアプリケーションでは、列ストア インデックスでサポートされていない機能が使用されている可能性があるため、バッチ モードを利用できませんでした。 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 以降では、任意の種類のインデックス (行ストアまたは列ストア) を使用する操作がクエリに含まれる適格な分析ワークロードで、バッチ モードが有効になります。 「[行ストアでのバッチ モード](../relational-databases/performance/intelligent-query-processing.md#batch-mode-on-rowstore)」を参照してください。 |
|スカラー UDF のインライン化|スカラー UDF が関係式に自動的に変換され、それらが呼び出し元の SQL クエリに埋め込まれます。 この変換により、スカラー UDF を利用するワークロードのパフォーマンスが向上します。 「[スカラー UDF のインライン化](../relational-databases/performance/intelligent-query-processing.md#scalar-udf-inlining)」を参照してください。|
| &nbsp; | &nbsp; |

### <a name="availability-groups"></a>可用性グループ

|新機能または更新 | 詳細 |
|:---|:---|
|最大 5 つの同期レプリカ|[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] では 3 つであった同期レプリカの最大数が、[!INCLUDE[ssSQL17](../includes/sssql17-md.md)] では 5 つに増加します。 この 5 つのレプリカのグループを、グループ内で自動フェールオーバーするように構成できます。 1 つのプライマリ レプリカと、4 つの同期セカンダリ レプリカがあります。|
|セカンダリからプライマリ レプリカへの接続のリダイレクト| 接続文字列に指定されたターゲット サーバーに関係なく、クライアント アプリケーションの接続先をプライマリ レプリカにすることができます。 詳しくは、「[セカンダリからプライマリ レプリカへの読み取り/書き込み接続のリダイレクト (Always On 可用性グループ)](../database-engine/availability-groups/windows/secondary-replica-connection-redirection-always-on-availability-groups.md)」をご覧ください。|
| &nbsp; | &nbsp; |

### <a name="setup"></a>セットアップ 

|新機能または更新 | 詳細 | 
|:---|:---| 
|新しいメモリ セットアップ オプション | インストール中に "*最小サーバー メモリ (MB)* " および "*最大サーバー メモリ (MB)* " のサーバー構成を設定します。 詳細については、「[[データベース エンジンの構成] - [メモリ] ページ](https://docs.microsoft.com/sql/sql-server/install/instance-configuration?view=sql-server-ver15#memory)」および「[コマンド プロンプトからの SQL Server のインストール](../database-engine/install-windows/install-sql-server-from-the-command-prompt.md#Install)」の `USESQLRECOMMENDEDMEMORYLIMITS`、`SQLMINMEMORY`、`SQLMAXMEMORY` パラメーターを参照してください。 提案される値は、「[サーバー メモリ構成オプション](../database-engine/configure-windows/server-memory-server-configuration-options.md#setting-the-memory-options-manually)」のメモリ構成ガイドラインと一致します。| 
|新しい並列処理セットアップ オプション | インストールの間に "*並列処理の最大限度*" サーバー構成オプションを設定します。 詳細については、「[[データベース エンジンの構成] - [MAXDOP] ページ](https://docs.microsoft.com/sql/sql-server/install/instance-configuration?view=sql-server-ver15#maxdop)」および「[コマンド プロンプトからの SQL Server のインストール](../database-engine/install-windows/install-sql-server-from-the-command-prompt.md#Install)」の `SQLMAXDOP` パラメーターを参照してください。 既定値は、「[max degree of parallelism サーバー構成オプションの構成](../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md#Guidelines)」の並列処理の最大限度ガイドラインと一致します。| 
| &nbsp; | &nbsp; |

### <a name="error-messages"></a>エラー メッセージ

|新機能または更新 | 詳細 |
|:---|:---|
|詳細な切り捨ての警告 | 切り捨てエラー メッセージに、テーブル名および列名と切り捨てられた値が既定で含まれます。 [VERBOSE_TRUNCATION_WARNINGS](../t-sql/statements/alter-database-scoped-configuration-transact-sql.md#verbose-truncation)に関するページを参照してください。|
| &nbsp; | &nbsp; |

## <a name="sql-server-on-linux"></a>Linux 上の SQL Server

| 新機能または更新 | 詳細 |
|:-----|:-----|
|新しいコンテナー レジストリ|[Docker で SQL Server のコンテナーの使用を開始する](../linux/quickstart-install-connect-docker.md) |
|レプリケーションのサポート |[Linux での SQL Server のレプリケーション](../linux/sql-server-linux-replication.md)
|Microsoft 分散トランザクション コーディネーター (MSDTC) のサポート |[Linux で MSDTC を構成する方法](../linux/sql-server-linux-configure-msdtc.md) |
|サード パーティの AD プロバイダーに対する OpenLDAP のサポート |[チュートリアル: SQL Server on Linux で Active Directory 認証を使用する](../linux/sql-server-linux-active-directory-authentication.md) |
|Linux 上の Machine Learning |[Linux に Machine Learning を構成する](../linux/sql-server-linux-setup-machine-learning.md) |
|`tempdb` の強化機能 | 既定では、Linux 上に SQL Server を新しくインストールすると、論理コアの数に基づいて複数の `tempdb` データ ファイルが作成されます (最大で 8 個のデータ ファイル)。 これは、マイナー バージョンまたはメジャー バージョンのインプレース アップグレードには適用されません。 各 `tempdb` ファイルは 8 MB で、64 MB まで自動拡張されます。 この動作は、Windows への SQL Server の既定のインストールに似ています。 |
| Linux での PolyBase | 非 Hadoop コネクタ向けに Linux に [PolyBase をインストール](../relational-databases/polybase/polybase-linux-setup.md)します。<br/><br/>[PolyBase 型のマッピング](../relational-databases/polybase/polybase-type-mapping.md) |
| 変更データ キャプチャ (CDC) のサポート | SQL Server 2019 では、変更データ キャプチャ (CDC) が Linux でサポートされるようになりました。 |
| &nbsp; | &nbsp; |

### <a name="setup"></a>セットアップ

|新機能または更新 | 詳細 |
|:---|:---|
|新しいメモリ セットアップ オプション | インストール中に "*最小サーバー メモリ (MB)* " および "*最大サーバー メモリ (MB)* " のサーバー構成を設定します。 詳細については、「[コマンド プロンプトからの SQL Server のインストール](../database-engine/install-windows/install-sql-server-from-the-command-prompt.md#Install)」の `USESQLRECOMMENDEDMEMORYLIMITS`、`SQLMINMEMORY`、および `SQLMAXMEMORY` パラメーターを参照してください。 提案される値は、「[サーバー メモリ構成オプション](../database-engine/configure-windows/server-memory-server-configuration-options.md#setting-the-memory-options-manually)」のメモリ構成ガイドラインと一致します。|
|新しい並列処理セットアップ オプション | インストールの間に "*並列処理の最大限度*" サーバー構成オプションを設定します。 詳細については、「[コマンド プロンプトからの SQL Server のインストール](../database-engine/install-windows/install-sql-server-from-the-command-prompt.md#Install)」の `SQLMAXDOP` パラメーターを参照してください。 既定値は、「[max degree of parallelism サーバー構成オプションの構成](../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md#Guidelines)」の並列処理の最大限度ガイドラインと一致します。|
| &nbsp; | &nbsp; |

## <a id="ml"></a> SQL Server Machine Learning Services

|新機能または更新 | 詳細 |
|:---|:---|
|パーティション ベースのモデリング|`sp_execute_external_script` に追加された新しいパラメーターを使用し、データのパーティションごとに外部スクリプトが処理されます。 この機能は、1 つの大きいモデルではなく、多数の小さいモデル (データのパーティションごとに 1 つのモデル) のトレーニングをサポートします。 [パーティション ベースのモデルの作成](../advanced-analytics/tutorials/r-tutorial-create-models-per-partition.md)に関するページを参照してください。|
|Windows Server フェールオーバー クラスター| Windows Server フェールオーバー クラスター上の Machine Learning Services に高可用性を構成します。|
| &nbsp; | &nbsp; |

## [!INCLUDE[master-data-services](../includes/ssmdsshort-md.md)]

| 新機能または更新 | 詳細 |
|:---|:---|
|Azure SQL Database Managed Instance データベースのサポート。| マネージド インスタンス上で [!INCLUDE[master-data-services](../includes/ssmdsshort-md.md)] をホストします。 [[!INCLUDE[master-data-services](../includes/ssmdsshort-md.md)] のインストールと構成](../master-data-services/master-data-services-installation-and-configuration.md#SetUpWeb)に関するページを参照してください。|
|新しい HTML コントロール| HTML コントロールでは、以前の Silverlight コンポーネントがすべて置き換えられます。 Silverlight の依存関係が削除されました。|
| &nbsp; | &nbsp; |

## <a name="analysis-services"></a>Analysis Services

| 新機能または更新 | 詳細 |
|:---|:---|
|クエリ インターリーブ| 「[クエリ インターリーブ](https://docs.microsoft.com/analysis-services/tabular-models/query-interleaving)」を参照してください |
|計算グループを使用した表形式モデルに対する MDX クエリのサポート | [計算グループ](what-s-new-in-sql-server-ver15-prerelease.md#calc-ctp24)に関するページを参照してください。 |
|表形式モデルでの計算グループ| [テーブル モデルでの計算グループ](what-s-new-in-sql-server-ver15-prerelease.md#calc-ctp24) |
|計算グループを使用した表形式モデルに対する MDX クエリのサポート | [計算グループ](what-s-new-in-sql-server-ver15-prerelease.md#calc-ctp24)に関するページを参照してください。 |
|計算グループを使用したメジャーの動的な書式設定 |この機能を使用すると、[計算グループ](what-s-new-in-sql-server-ver15-prerelease.md#calc-ctp24)を使用してメジャーの書式設定文字列を条件付きで変更できます。 たとえば、通貨換算を使用すると、さまざまな外貨形式を使ってメジャーを表示することができます。|
|表形式モデルでの多対多リレーションシップ|[表形式モデルでの多対多リレーションシップ](what-s-new-in-sql-server-ver15-prerelease.md#many-to-many-ctp24)|
|リソース ガバナンス用のプロパティ設定|[リソース ガバナンス用のプロパティ設定](what-s-new-in-sql-server-ver15-prerelease.md#property-ctp24)|
| Power BI キャッシュに対するガバナンス設定の更新  | Power BI サービスは、Live Connect レポートの初期読み込みのダッシュボード タイル データとレポート データをキャッシュします。これにより、大量のキャッシュ クエリが SSAS に送信され、極端な場合はサーバーが過負荷になります。 このリリースでは、**ClientCacheRefreshPolicy** プロパティが導入されています。 このプロパティによって、サーバー レベルでこの動作をオーバーライドすることが許可されます。 詳細については、「[全般プロパティ](https://docs.microsoft.com/analysis-services/server-properties/general-properties)」を参照してください。 |
| オンラインのアタッチ  | この機能を使用すると、表形式モデルをオンライン操作としてアタッチすることができます。 オンラインのアタッチは、オンプレミスのクエリ スケールアウト環境で読み取り専用レプリカを同期するために使用できます。 詳細については、[オンラインのアタッチ](what-s-new-in-sql-server-ver15-prerelease.md#online-attach-ctp32)に関するページを参照してください。 |
| &nbsp; | &nbsp; |

[!INCLUDE[ctp-support-exclusion](../includes/ctp-support-exclusion.md)]

サポートから除外される特定の機能については、[リリース ノート](sql-server-ver15-release-notes.md)を参照してください。

また、[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] CTP 3.2 では、次の機能が追加または強化されています。

## <a name="see-also"></a>参照

- [`SqlServer` PowerShell モジュール](https://www.powershellgallery.com/packages/Sqlserver)
- [SQL Server PowerShell ドキュメント](../powershell/sql-server-powershell.md)

## <a name="next-steps"></a>次の手順

- [[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] リリース ノート](sql-server-ver15-release-notes.md)

- [Microsoft [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)]:テクニカル ホワイト ペーパー](http://info.microsoft.com/rs/157-GQE-382/images/EN-US-CNTNT-white-paper-DBMod-Microsoft-SQL-Server-2019-Technical-white-paper.pdf)<br />2018 年 9 月に公開されました。 Windows、Linux、Docker コンテナー向けの Microsoft [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] CTP 2.0 に適用されます。

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]
