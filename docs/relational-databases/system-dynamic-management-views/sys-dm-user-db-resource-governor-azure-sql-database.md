---
title: SQL server の管理 (Transact-sql) (_d) |。Microsoft Docs
ms.custom: ''
ms.date: 03/27/2019
ms.prod: sql
ms.technology: system-objects
ms.prod_service: sql-database
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- sys.resource_governance
- sys.resource_governance_TSQL
- resource_governance
- resource_governance_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.resource_governance catalog view
ms.assetid: ''
author: joesackmsft
ms.author: josack
monikerRange: =azuresqldb-current||=sqlallproducts-allversions
ms.openlocfilehash: ea07ba28efc4ac50fdeef04bb1b3643c359ead28
ms.sourcegitcommit: 3b1f873f02af8f4e89facc7b25f8993f535061c9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70176257"
---
# <a name="sysdm_user_db_resource_governance-transact-sql"></a>システムの管理 (Transact-sql) (_d) (_c)

[!INCLUDE[appliesto-xx-asdb-xxxx-xxx-md](../../includes/appliesto-xx-asdb-xxxx-xxx-md.md)]

Azure SQL Database データベースのリソースガバナンスの構成と容量の設定を返します。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**database_id**|ssNoversion|データベースの ID。 Azure SQL Database サーバー内で一意です。|
|**logical_database_guid**|UNIQUEIDENTIFIER|ユーザーデータベースの論理 guid であり、ユーザーデータベースの有効期間中は維持されます。  データベースの名前を変更するか、別の SLO に設定すると、GUID は変更されません。 |
|**physical_database_guid**|UNIQUEIDENTIFIER|ユーザーデータベースの物理インスタンスが存続しているユーザーデータベースの物理 guid。 を別の SLO に設定すると、この列が変更されます。|
|**server_name**|NVARCHAR|論理サーバー名。|
|**database_name**|NVARCHAR|論理データベース名。|
|**slo_name**|NVARCHAR|サービスレベル目標とハードウェアの生成。|
|**dtu_limit**|ssNoversion|データベースの DTU 制限 (vCore の場合は NULL)。|
|**cpu_limit**|ssNoversion|データベースの vCore の制限 (DTU データベースの場合は NULL)。|
|**min_cpu**|TINYINT|ユーザーワークロードで使用できる最小 CPU%。|
|**max_cpu**|TINYINT|ユーザーワークロードで使用できる最大 CPU パーセント。|
|**cap_cpu**|TINYINT|ユーザーワークロードグループの CPU 使用率の上限。|
|**min_cores**|SMALLINT|SQL によって使用されている Cpu の数。|
|**max_dop**|SMALLINT|ユーザーワークロードで使用される並列処理の最大限度。|
|**min_memory**|ssNoversion|ユーザーのワークロードで使用できる最小メモリの割合。|
|**max_memory**|ssNoversion|ユーザーワークロードで使用できる最大メモリ割合 (%)。|
|**max_sessions**|ssNoversion|ユーザーグループのセッション数の制限。|
|**max_memory_grant**|ssNoversion|ユーザーワークロード内の各クエリに対する最大メモリ許可 (%)。|
|**max_db_memory**|ssNoversion|ユーザー DB ワークロードの最大バッファープールメモリ上限|
|**govern_background_io**|bit|バックグラウンド書き込みがユーザーグループに対して課金されるかどうかを示します。|
|**min_db_max_size_in_mb**|BIGINT|最小データベースファイルの最大サイズ (MB 単位)。|
|**max_db_max_size_in_mb**|BIGINT|最大データベースファイルの最大サイズ (MB 単位)。|
|**default_db_max_size_in_mb**|BIGINT|既定の最大データベースファイルサイズ (MB 単位)。|
|**db_file_growth_in_mb**|BIGINT|Azure データベースファイルの既定の拡張 (MB 単位)。|
|**initial_db_file_size_in_mb**|BIGINT|既定のデータベースファイルのサイズ (MB 単位)。|
|**log_size_in_mb**|BIGINT|既定のログファイルのサイズ (MB 単位)。|
|**instance_cap_cpu**|ssNoversion|インスタンスレベルの CPU 上限。|
|**instance_max_log_rate**|BIGINT|インスタンスレベルでのログ生成率の上限 (バイト/秒)。|
|**instance_max_worker_threads**|ssNoversion|インスタンスレベルでのワーカースレッドの制限。|
|**replica_type**|ssNoversion|レプリカの種類。ここで、0はプライマリ、1はセカンダリです。|
|**max_transaction_size**|BIGINT|任意のトランザクションで使用される最大ログ領域 (KB 単位)。|
|**checkpoint_rate_mbps**|ssNoversion|チェックポイントの帯域幅 (Mbps)。|
|**checkpoint_rate_io**|ssNoversion|1秒あたりの IOs のチェックポイント IO 率。|
|**last_updated_date_utc**|DATETIME|最後に設定を変更または再構成した日付と時刻。|
|**primary_group_id**|ssNoversion|プライマリユーザーワークロードグループ ID。|
|**primary_group_max_workers**|ssNoversion|プライマリユーザーワークロードグループレベルでのワーカー制限。|
|**primary_min_log_rate**|BIGINT|プライマリユーザーワークロードグループレベルでの最小ログレート (1 秒あたりのバイト数)。|
|**primary_max_log_rate**|BIGINT|プライマリユーザーワークロードグループレベルでの最大ログ速度 (1 秒あたりのバイト数)。|
|**primary_group_min_io**|ssNoversion|プライマリユーザーワークロードグループレベルでの最小 IO。|
|**primary_group_max_io**|ssNoversion|プライマリユーザーワークロードグループレベルでの最大 IO 数。|
|**primary_group_min_cpu**|FLOAT|プライマリユーザーワークロードグループレベルの CPU の割合の最小値。|
|**primary_group_max_cpu**|FLOAT|プライマリユーザーワークロードグループレベルでの CPU の割合の最大値。|
|**primary_log_commit_fee**|ssNoversion|プライマリユーザーワークロードグループレベルでのログレートガバナンスコミット料金。|
|**primary_pool_max_workers**|ssNoversion|プライマリユーザープールレベルでのワーカー制限。
|**pool_max_io**|ssNoversion|プライマリユーザープールレベルでの最大 IO 制限。|
|**govern_db_memory_in_resource_pool**|bit|バッファープールの最大サイズがリソースプールレベルで管理されているかどうかを示します。 通常、エラスティックプール内のデータベースに対して設定します。|
|**volume_local_iops**|ssNoversion|ローカルボリュームの1秒あたりの Io 数 (例: C:、D:)。|
|**volume_managed_xstore_iops**|ssNoversion|リモートストレージアカウントの Io/秒の上限。|
|**volume_external_xstore_iops**|ssNoversion|Azure SQL DB のバックアップとテレメトリで使用されるリモートストレージアカウントの1秒あたりの Io 数。|
|**volume_type_local_iops**|ssNoversion|すべてのローカルボリュームの1秒あたりの Io 数の上限。|
|**volume_type_managed_xstore_iops**|ssNoversion|インスタンスによって使用されるすべてのリモートストレージアカウントの1秒あたりの Io 数。|
|**volume_type_external_xstore_iops**|ssNoversion|インスタンスの Azure SQL DB のバックアップとテレメトリによって使用されるすべてのリモートストレージアカウントの1秒あたりの Io 数。|
|**volume_pfs_iops**|ssNoversion|Premium file storage の Io/秒の上限。|
|**volume_type_pfs_iops**|ssNoversion|インスタンスによって使用されているすべての premium file ストレージの Io/秒の上限。|
|||

## <a name="permissions"></a>アクセス許可

このビューには、VIEW DATABASE STATE 権限が必要です。

## <a name="remarks"></a>コメント

ユーザーは、この動的管理ビューにアクセスして、Azure SQL Database データベースのリソースガバナンスの構成と容量の設定を確認できます。 

> [!IMPORTANT]
> この DMV によって提示されるデータのほとんどは、内部使用を目的としており、変更される可能性があります。

## <a name="examples"></a>使用例

次の例では、1つまたはプールされたデータベースのデータベースサーバー内のデータベース名によって並べ替えられた、インスタンスの最大ログレートデータを返します。

```
SELECT database_name,
       primary_max_log_rate
FROM sys.dm_user_db_resource_governance
ORDER BY database_name DESC;  
```

## <a name="see-also"></a>関連項目

- [トランザクションログレートガバナンス](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server#transaction-log-rate-governance)
- [単一データベースの DTU リソースの制限](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
- [単一データベースの仮想コアリソースの制限](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
