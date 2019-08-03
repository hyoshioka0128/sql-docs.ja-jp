---
title: 動的管理ビュー (Dmv) を使用した R と Python スクリプトの実行の監視
description: 動的管理ビュー (Dmv) を使用して、SQL Server Machine Learning Services で R および Python の外部スクリプトの実行を監視します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/29/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: ade3714459ebc0457b6afea2600cc0547c9940a1
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715320"
---
# <a name="monitor-sql-server-machine-learning-services-using-dynamic-management-views-dmvs"></a>動的管理ビュー (Dmv) を使用して SQL Server Machine Learning Services を監視する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

動的管理ビュー (Dmv) を使用して、外部スクリプト (R および Python) の実行を監視したり、使用されているリソースを監視したり、問題を診断したり、SQL Server Machine Learning Services のパフォーマンスを調整したりします。

この記事では、SQL Server Machine Learning Services に固有の Dmv について説明します。 また、次のようなクエリの例も紹介します。

+ Machine learning の設定と構成オプション
+ 外部 R または Python スクリプトを実行しているアクティブセッション
+ R および Python の外部ランタイムの実行の統計
+ 外部スクリプトのパフォーマンスカウンター
+ OS、SQL Server、および外部リソースプールのメモリ使用量
+ SQL Server と外部リソースプールのメモリ構成
+ 外部リソースプールを含む Resource Governor リソースプール
+ R および Python 用にインストールされたパッケージ

Dmv に関する一般的な情報については、「[システム動的管理ビュー](../../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)」を参照してください。

> [!TIP]
> カスタムレポートを使用して、SQL Server Machine Learning Services を監視することもできます。 詳細については、「 [Management Studio でカスタムレポートを使用して machine learning を監視する](../../advanced-analytics/r/monitor-r-services-using-custom-reports-in-management-studio.md)」を参照してください。

## <a name="dynamic-management-views"></a>動的管理ビュー

SQL Server で machine learning ワークロードを監視するときに、次の動的管理ビューを使用できます。 Dmv に対してクエリを実行`VIEW SERVER STATE`するには、インスタンスに対する権限が必要です。

| 動的管理ビュー | 型 | 説明 |
|-------------------------|------|-------------|
| [sys.dm_external_script_requests](../../relational-databases/system-dynamic-management-views/sys-dm-external-script-requests.md) | 実行 | 外部スクリプトを実行しているアクティブなワーカー アカウントごとに行を返します。 |
| [sys.dm_external_script_execution_stats](../../relational-databases/system-dynamic-management-views/sys-dm-external-script-execution-stats.md) | 実行 | 外部スクリプト要求の種類ごとに 1 つの行を返します。 |
| [sys.dm_os_performance_counters](../../relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md) | 実行 | サーバーで管理されているパフォーマンス カウンターごとに 1 行のデータを返します。 検索条件`WHERE object_name LIKE '%External Scripts%'`を使用する場合は、この情報を使用して、実行されたスクリプトの数、認証モードを使用して実行されたスクリプト、またはインスタンス全体に対して発行された R または Python の呼び出しの数を確認できます。 |
| [sys.dm_resource_governor_external_resource_pools](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-external-resource-pools.md) | [リソース ガバナー] | Resource Governor 内の現在の外部リソースプールの状態、リソースプールの現在の構成、およびリソースプールの統計に関する情報を返します。 |
| [sys.dm_resource_governor_external_resource_pool_affinity](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-external-resource-pool-affinity-transact-sql.md) | [リソース ガバナー] | Resource Governor 内の現在の外部リソースプールの構成に関する CPU の関係情報を返します。 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] のスケジューラごとに 1 行のデータを返します。各スケジューラは個別のプロセッサにマップされています。 このビューは、スケジューラの状況の監視やランナウェイ タスクの特定に使用できます。 |

インスタンスの監視[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]の詳細については、「[カタログビュー](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md) 」および「 [Resource Governor 関連の動的管理ビュー](../../relational-databases/system-dynamic-management-views/resource-governor-related-dynamic-management-views-transact-sql.md)」を参照してください。

## <a name="settings-and-configuration"></a>設定と構成

Machine Learning Services のインストール設定と構成オプションを表示します。

![設定と構成クエリからの出力](media/dmv-settings-and-configuration.png "設定と構成クエリからの出力")

この出力を取得するには、次のクエリを実行します。 使用するビューと関数の詳細については、「 [_server_registry](../../relational-databases/system-dynamic-management-views/sys-dm-server-registry-transact-sql.md)、 [Sys](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md)、および[serverproperty](../../t-sql/functions/serverproperty-transact-sql.md)」を参照してください。

```sql
SELECT CAST(SERVERPROPERTY('IsAdvancedAnalyticsInstalled') AS INT) AS IsMLServicesInstalled
    , CAST(value_in_use AS INT) AS ExternalScriptsEnabled
    , COALESCE(SIGN(SUSER_ID(CONCAT (
                    CAST(SERVERPROPERTY('MachineName') AS NVARCHAR(128))
                    , '\SQLRUserGroup'
                    , CAST(serverproperty('InstanceName') AS NVARCHAR(128))
                    ))), 0) AS ImpliedAuthenticationEnabled
    , COALESCE((
            SELECT CAST(r.value_data AS INT)
            FROM sys.dm_server_registry AS r
            WHERE r.registry_key LIKE 'HKLM\Software\Microsoft\Microsoft SQL Server\%\SuperSocketNetLib\Tcp'
            AND r.value_name = 'Enabled'
            ), - 1) AS IsTcpEnabled
FROM sys.configurations
WHERE name = 'external scripts enabled';
```

このクエリでは、次の列が返されます。

| [列] | 説明 |
|--------|-------------|
| インストールされている Ismlサービス | インスタンスに対して SQL Server Machine Learning Services がインストールされている場合は1を返します。 それ以外の場合、は0を返します。 |
| Externalスクリプト有効 | インスタンスに対して外部スクリプトが有効になっている場合は1を返します。 それ以外の場合、は0を返します。 |
| ImpliedAuthenticationEnabled | 暗黙の認証が有効になっている場合は1を返します。 それ以外の場合、は0を返します。 暗黙の認証の構成は、SQLRUserGroup のログインが存在するかどうかを確認することによって確認されます。 |
| IsTcpEnabled | インスタンスに対して TCP/IP プロトコルが有効になっている場合は1を返します。 それ以外の場合、は0を返します。 詳細については、「[既定の SQL Server ネットワークプロトコルの構成](../../database-engine/configure-windows/default-sql-server-network-protocol-configuration.md)」を参照してください。 |

## <a name="active-sessions"></a>Active sessions

外部スクリプトを実行しているアクティブなセッションを表示します。

![アクティブな設定クエリからの出力](media/dmv-active-sessions.png "アクティブな設定クエリからの出力")

この出力を取得するには、次のクエリを実行します。 使用される動的管理ビューの詳細については、「 [_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-external-script-requests.md)」 、「[_exec_sessions](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md)」、および「 [_exec_sessions](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)」を参照してください。

```sql
SELECT r.session_id, r.blocking_session_id, r.status, DB_NAME(s.database_id) AS database_name
    , s.login_name, r.wait_time, r.wait_type, r.last_wait_type, r.total_elapsed_time, r.cpu_time
    , r.reads, r.logical_reads, r.writes, er.language, er.degree_of_parallelism, er.external_user_name
FROM sys.dm_exec_requests AS r
INNER JOIN sys.dm_external_script_requests AS er
ON r.external_script_request_id = er.external_script_request_id
INNER JOIN sys.dm_exec_sessions AS s
ON s.session_id = r.session_id;
```

このクエリでは、次の列が返されます。

| [列] | 説明 |
|--------|-------------|
| session_id | アクティブな各プライマリ接続に関連付けられたセッションの識別子。 |
| blocking_session_id | 要求をブロックしているセッションの ID。 この列が NULL の場合は、要求がブロックされていないか、ブロックしているセッションのセッション情報が使用または識別できません。 |
| status | 要求の状態。 |
| database_name | 各セッションの現在のデータベースの名前。 |
| login_name | セッションを現在実行しているログイン名 SQL Server ます。 |
| wait_time | 要求が現在ブロックされている場合の現時点での待機時間 (ミリ秒単位)。 NULL 値は許可されません。 |
| wait_type | 要求が現在ブロックされている場合の待機の種類。 待機の種類の詳細については、「 [_os_wait_stats](../../relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql.md)」を参照してください。 |
| last_wait_type | 要求がブロックされていた場合の最後の待機の種類。 |
| total_elapsed_time | 要求を受信してから経過した総時間 (ミリ秒単位)。 |
| cpu_time | 要求で使用される CPU 時間 (ミリ秒単位)。 |
| reads | 要求で実行された読み取りの数。 |
| logical_reads | 要求で実行された論理読み取りの数。 |
| writes | 要求で実行された書き込みの数。 |
| language | サポートされているスクリプト言語を表すキーワードです。 |
| degree_of_parallelism | 作成された並列処理の数を示す数値です。 この値は、要求された並列処理の数と異なる場合があります。 |
| external_user_name | スクリプトが実行されたときの Windows ワーカー アカウント。 |

## <a name="execution-statistics"></a>実行の統計

R および Python の外部ランタイムの実行の統計情報を表示します。 現在使用できるのは、RevoScaleR、revoscalepy、または microsoft ml パッケージ関数の統計情報のみです。

![実行統計クエリからの出力](media/dmv-execution-statistics.png "実行統計クエリからの出力")

この出力を取得するには、次のクエリを実行します。 使用される動的管理ビューの詳細については、「」[を参照し](../../relational-databases/system-dynamic-management-views/sys-dm-external-script-execution-stats.md)てください。 クエリは、複数回実行された関数のみを返します。

```sql
SELECT language, counter_name, counter_value
FROM sys.dm_external_script_execution_stats
WHERE counter_value > 0
ORDER BY language, counter_name;
```

このクエリでは、次の列が返されます。

| [列] | 説明 |
|--------|-------------|
| language | 登録されている外部スクリプト言語の名前です。 |
| counter_name | 登録されている外部スクリプト関数の名前です。 |
| counter_value | 登録されている外部スクリプト関数がサーバーで呼び出されたインスタンスの合計数です。 この値は、機能がインスタンスにインストールされてからの累積値で、リセットすることはできません。 |

## <a name="performance-counters"></a>パフォーマンス カウンター

外部スクリプトの実行に関連するパフォーマンスカウンターを表示します。

![パフォーマンスカウンタークエリからの出力](media/dmv-performance-counters.png "パフォーマンスカウンタークエリからの出力")

この出力を取得するには、次のクエリを実行します。 使用される動的管理ビューの詳細については、「 [_os_performance_counters](../../relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql.md)」を参照してください。

```sql
SELECT counter_name, cntr_value
FROM sys.dm_os_performance_counters 
WHERE object_name LIKE '%External Scripts%'
```

**_os_performance_counters**は、外部スクリプトに対して次のパフォーマンスカウンターを出力します。

| カウンター | 説明 |
|---------|-------------|
| Total Executions | ローカル呼び出しまたはリモート呼び出しによって開始された外部プロセスの数。 |
| Parallel Executions | スクリプトに仕様が _@parallel_ 含まれて[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]おり、並列クエリプランを生成して使用できる回数。 |
| Streaming Executions | ストリーミング機能が呼び出された回数。 |
| SQL CC Executions | 呼び出しがリモートでインスタンス化され、SQL Server が計算コンテキストとして使用された場合に実行される外部スクリプトの数。 |
| Implied Auth.Login | 暗黙の認証を使用して ODBC ループバック呼び出しが行われた回数。つまり、は[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 、スクリプト要求を送信するユーザーの代わりに呼び出しを実行します。 |
| Total Execution Time (ms) | 呼び出しから呼び出しの完了までの経過時間。 |
| Execution Errors | スクリプトがエラーを報告した回数。 この数には、R または Python のエラーは含まれません。 |

## <a name="memory-usage"></a>メモリ使用量

OS、SQL Server、および外部プールによって使用されているメモリに関する情報を表示します。

![メモリ使用量クエリからの出力](media/dmv-memory-usage.png "メモリ使用量クエリからの出力")

この出力を取得するには、次のクエリを実行します。 使用される動的管理ビューの詳細については、「 [_resource_governor_external_resource_pools](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-external-resource-pools.md) 」および「 [_os_sys_info](../../relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql.md)」を参照してください。

```sql
SELECT physical_memory_kb, committed_kb
    , (SELECT SUM(peak_memory_kb)
        FROM sys.dm_resource_governor_external_resource_pools AS ep
        ) AS external_pool_peak_memory_kb
FROM sys.dm_os_sys_info;
```

このクエリでは、次の列が返されます。

| [列] | 説明 |
|--------|-------------|
| physical_memory_kb | コンピューターの物理メモリの合計容量。 |
| committed_kb | メモリマネージャーでコミットされたメモリ (KB 単位)。 メモリ マネージャー内の予約済みメモリは含まれません。 |
| external_pool_peak_memory_kb | すべての外部リソースプールに使用されるメモリの最大量 (kb 単位) の合計。 |

## <a name="memory-configuration"></a>メモリ構成

SQL Server と外部リソースプールの割合での最大メモリ構成に関する情報を表示します。 SQL Server が既定値の`max server memory (MB)`で実行されている場合、OS メモリの 100% と見なされます。

![メモリ構成クエリからの出力](media/dmv-memory-configuration.png "メモリ構成クエリからの出力")

この出力を取得するには、次のクエリを実行します。 使用されるビューの詳細については[、「](../../relational-databases/system-catalog-views/sys-configurations-transact-sql.md) _resource_governor_external_resource_pools」および「 [sys](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-external-resource-pools.md)」を参照してください。

```sql
SELECT 'SQL Server' AS name
    , CASE CAST(c.value AS BIGINT)
        WHEN 2147483647 THEN 100
        ELSE (SELECT CAST(c.value AS BIGINT) / (physical_memory_kb / 1024.0) * 100 FROM sys.dm_os_sys_info)
        END AS max_memory_percent
FROM sys.configurations AS c
WHERE c.name LIKE 'max server memory (MB)'
UNION ALL
SELECT CONCAT ('External Pool - ', ep.name) AS pool_name, ep.max_memory_percent
FROM sys.dm_resource_governor_external_resource_pools AS ep;
```

このクエリでは、次の列が返されます。

| [列] | 説明 |
|--------|-------------|
| NAME | 外部リソースプールの名前または SQL Server。 |
| max_memory_percent | SQL Server または外部リソースプールが使用できる最大メモリ。 |

## <a name="resource-pools"></a>[リソース プール]

[SQL Server Resource Governor](../../relational-databases/resource-governor/resource-governor.md)では、[リソースプール](../../relational-databases/resource-governor/resource-governor-resource-pool.md)はインスタンスの物理リソースのサブセットを表します。 外部スクリプトの実行を含む、受信するアプリケーション要求がリソースプール内で使用できる CPU、物理 IO、およびメモリの量に制限を指定できます。 SQL Server と外部スクリプトに使用されるリソースプールを表示します。

![リソースプールクエリからの出力](media/dmv-resource-pools.png "リソースプールクエリからの出力")

この出力を取得するには、次のクエリを実行します。 使用される動的管理ビューの詳細については、「 [_resource_governor_resource_pools](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql.md) 」および「 [_resource_governor_external_resource_pools](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-external-resource-pools.md)」を参照してください。

```sql
SELECT CONCAT ('SQL Server - ', p.name) AS pool_name
    , p.total_cpu_usage_ms, p.read_io_completed_total, p.write_io_completed_total
FROM sys.dm_resource_governor_resource_pools AS p
UNION ALL
SELECT CONCAT ('External Pool - ', ep.name) AS pool_name
    , ep.total_cpu_user_ms, ep.read_io_count, ep.write_io_count
FROM sys.dm_resource_governor_external_resource_pools AS ep;
```

このクエリでは、次の列が返されます。

| [列] | 説明 |
|--------|-------------|
| pool_name | リソースプールの名前。 SQL Server リソースプールにはプレフィックス`SQL Server`が付き、外部リソースプールに`External Pool`はプレフィックスが付きます。
| total_cpu_usage_hours | リソースガバナー統計がリセットされた後の累積 CPU 使用率 (ミリ秒単位)。 |
| read_io_completed_total | リソース ガバナー統計がリセットされた後に完了した読み取り IO の合計。 |
| write_io_completed_total | リソース ガバナー統計がリセットされた後に完了した書き込み IO の合計。 |

## <a name="installed-packages"></a>インストールされたパッケージ

SQL Server Machine Learning Services にインストールされている R パッケージと Python パッケージを表示するには、これらを出力する R または Python スクリプトを実行します。

### <a name="installed-packages-for-r"></a>R 用にインストールされたパッケージ

SQL Server Machine Learning Services にインストールされている R パッケージを表示します。

![R クエリ用にインストールされたパッケージからの出力](media/dmv-installed-packages-r.png "R クエリ用にインストールされたパッケージからの出力")

この出力を取得するには、次のクエリを実行します。 このクエリでは、R スクリプトを使用して、SQL Server と共にインストールされた R パッケージを特定します。

```sql
EXEC sp_execute_external_script @language = N'R'
, @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
    , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
```

返される列は次のとおりです。

| [列] | 説明 |
|--------|-------------|
| [パッケージ] | インストールされているパッケージの名前。 |
| バージョン | パッケージのバージョン。 |
| 依存 | インストールされているパッケージが依存しているパッケージを一覧表示します。 |
| ライセンス | インストールされているパッケージのライセンス。 |
| LibPath | パッケージが格納されているディレクトリ。 |

### <a name="installed-packages-for-python"></a>Python 用にインストールされたパッケージ

SQL Server Machine Learning Services にインストールされている Python パッケージを表示します。

![Python クエリ用にインストールされたパッケージからの出力](media/dmv-installed-packages-python.png "Python クエリ用にインストールされたパッケージからの出力")

この出力を取得するには、次のクエリを実行します。 このクエリでは、Python スクリプトを使用して、SQL Server と共にインストールされた Python パッケージを判別します。

```sql
EXEC sp_execute_external_script @language = N'Python'
, @script = N'
import pip
OutputDataSet = pandas.DataFrame([(i.key, i.version, i.location) for i in pip.get_installed_distributions()])'
WITH result sets((Package NVARCHAR(128), Version NVARCHAR(128), Location NVARCHAR(1000)));
```

返される列は次のとおりです。

| [列] | 説明 |
|--------|-------------|
| [パッケージ] | インストールされているパッケージの名前。 |
| バージョン | パッケージのバージョン。 |
| 場所 | パッケージが格納されているディレクトリ。 |

## <a name="next-steps"></a>次の手順

+ [Machine Learning ソリューションの管理と監視](../../advanced-analytics/r/managing-and-monitoring-r-solutions.md)
+ [Machine learning の拡張イベント](../../advanced-analytics/r/extended-events-for-sql-server-r-services.md)
+ [関連する動的管理ビューの Resource Governor](../../relational-databases/system-dynamic-management-views/resource-governor-related-dynamic-management-views-transact-sql.md)
+ [システム動的管理ビュー](../../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)
+ [Management Studio でカスタムレポートを使用して machine learning を監視する](../../advanced-analytics/r/monitor-r-services-using-custom-reports-in-management-studio.md)