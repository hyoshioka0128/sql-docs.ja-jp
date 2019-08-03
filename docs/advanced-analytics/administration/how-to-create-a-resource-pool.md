---
title: R および Python 用のリソースプールを作成する方法
description: SQL Server データベースエンジンインスタンス上の R または Python プロセスの SQL Server リソースプールを定義します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 882b9b15fbba567f30172d625af3867b27ae387e
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715903"
---
# <a name="how-to-create-a-resource-pool-for-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services のリソースプールを作成する方法
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

この記事では、SQL Server で R および Python machine learning ワークロードの管理専用のリソースプールを作成して使用する方法について説明します。 Machine learning の機能が既にインストールされ、有効になっていることを前提としています。また、R や Python などの外部プロセスで使用されるリソースの詳細な管理をサポートするようにインスタンスを再構成する必要があります。

このプロセスには、次の複数の手順が含まれます。

1.  既存のリソースプールの状態を確認します。 既存のリソースを使用しているサービスを理解することが重要です。
2.  サーバーリソースプールを変更します。
3.  外部プロセス用の新しいリソースプールを作成します。
4.  外部スクリプト要求を識別する分類関数を作成します。
5.  新しい外部リソースプールが、指定されたクライアントまたはアカウントから R ジョブまたは Python ジョブをキャプチャしていることを確認します。

##  <a name="bkmk_ReviewStatus"></a>既存のリソース プールの状態を確認する
  
1.  次のようなステートメントを使用して、サーバーの既定のプールに割り当てられているリソースを確認します。
  
    ```sql
    SELECT * FROM sys.resource_governor_resource_pools WHERE name = 'default'
    ```

    **サンプルの結果**

    |pool_id|NAME|min_cpu_percent|max_cpu_percent|min_memory_percent|max_memory_percent|cap_cpu_percent|min_iops_per_volume|max_iops_per_volume|
    |-|-|-|-|-|-|-|-|-|
    |2|既定値 (default)|0|100|0|100|100|0|0|

2.  既定の**外部**リソース プールに割り当てられているリソースを確認します。
  
    ```sql
    SELECT * FROM sys.resource_governor_external_resource_pools WHERE name = 'default'
    ```

    **サンプルの結果**

    |external_pool_id|NAME|max_cpu_percent|max_memory_percent|max_processes|version|
    |-|-|-|-|-|-|
    |2|既定値 (default)|100|20|0|2|
 
3.  これらのサーバーの既定の設定では、ほとんどのタスクを完了するために、外部ランタイムのリソースが不足している可能性があります。 これを変更するには、サーバーのリソース使用率を次のように変更する必要があります。
  
    -   データベースエンジンで使用できる最大コンピューターメモリを減らします。
  
    -   外部プロセスで使用できる最大コンピューターメモリを増やします。

## <a name="modify-server-resource-usage"></a>サーバーのリソース使用率を変更する

1.  [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] で、次のステートメントを実行して、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のメモリ使用量を [最大サーバー メモリ] の値の **60%** に制限します。

    ```sql
    ALTER RESOURCE POOL "default" WITH (max_memory_percent = 60);
    ```
  
2.  同様に、次のステートメントを実行して、外部プロセスによるメモリの使用量をコンピューター リソース全体の **40%** に制限します。
  
    ```sql
    ALTER EXTERNAL RESOURCE POOL "default" WITH (max_memory_percent = 40);
    ```
  
3.  これらの変更を適用するには、リソース ガバナーを次のように再構成して再起動する必要があります。
  
    ```sql
    ALTER RESOURCE GOVERNOR RECONFIGURE;
    ```
  
    > [!NOTE]
    >  これらは、最初は推奨される設定にすぎません。他のサーバープロセスの中で機械学習タスクを評価して、環境とワークロードの適切なバランスを判断する必要があります。

## <a name="create-a-user-defined-external-resource-pool"></a>ユーザー定義の外部リソース プールを作成する
  
1.  リソース ガバナーの構成に対する変更はサーバー全体に適用され、サーバーの既定のプールを使用するワークロードだけではなく、外部プールを使用するワークロードにも影響します。
  
     そのため、優先するワークロードを細かく制御するために、新しいユーザー定義の外部リソース プールを作成できます。 さらに、分類子関数を定義して、外部リソース プールに割り当てる必要があります。 **EXTERNAL**キーワードは new です。
  
     新しい "*ユーザー定義の外部リソース プール*" の作成から始めます。 次の例では、プールに **ds_ep** という名前を付けています。
  
    ```sql
    CREATE EXTERNAL RESOURCE POOL ds_ep WITH (max_memory_percent = 40);
    ```

2.  セッション要求の管理で使用する `ds_wg` という名前のワークロード グループを作成します。 SQL クエリでは既定のプールを使用し、すべての外部プロセスのクエリでは `ds_ep` プールを使用します。
  
    ```sql
    CREATE WORKLOAD GROUP ds_wg WITH (importance = medium) USING "default", EXTERNAL "ds_ep";
    ```
  
     要求は、要求を分類することができない場合、またはその他の分類エラーが発生した場合は、既定のグループに割り当てられます。
  
     詳細については、「[Resource Governor Workload Group (リソース ガバナー ワークロード グループ)](../../relational-databases/resource-governor/resource-governor-workload-group.md)」と「[CREATE WORKLOAD GROUP &#40;Transact-SQL&#41; (ワークロード グループの作成 (TRANSACT-SQL))](../../t-sql/statements/create-workload-group-transact-sql.md)」を参照してください。
  
## <a name="create-a-classification-function-for-machine-learning"></a>Machine learning の分類関数を作成する
  
分類子関数は、受信タスクを検査して、タスクが現在のリソース プールを使用して実行できるかどうかを決定します。 分類子関数の条件を満たしていないタスクは、サーバーの既定のリソース プールに割り当てられます。
  
1. まず、Resource Governor が分類子関数を使用してリソースプールを決定するように指定します。 分類子関数のプレースホルダーとして**null**を割り当てることができます。
  
    ```sql
    ALTER RESOURCE GOVERNOR WITH (classifier_function = NULL);
    ALTER RESOURCE GOVERNOR RECONFIGURE;
    ```
  
     詳細については、「[ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md)」を参照してください。
  
2.  各リソースプールの分類子関数で、リソースプールに割り当てる必要があるステートメントまたは受信要求の種類を定義します。
  
     たとえば、次の関数は、要求を送信したアプリケーションが 'Microsoft R Host' または 'RStudio' の場合は、ユーザー定義の外部リソース プールに割り当てられたスキーマの名前を返します。それ以外の場合は既定のリソース プールを返します。
  
    ```sql
    USE master
    GO
    CREATE FUNCTION is_ds_apps()
    RETURNS sysname
    WITH schemabinding
    AS
    BEGIN
        IF program_name() in ('Microsoft R Host', 'RStudio') RETURN 'ds_wg';
        RETURN 'default'
        END;
    GO
    ```
  
3.  関数を作成したら、リソース グループを再定義して、新しい分類子関数が先ほど定義した外部リソース グループに割り当てられるようにします。
  
    ```sql
    ALTER RESOURCE GOVERNOR WITH  (classifier_function = dbo.is_ds_apps);
    ALTER RESOURCE GOVERNOR WITH reconfigure;
    GO
    ```

## <a name="verify-new-resource-pools-and-affinity"></a>新しいリソース プールとアフィニティを確認する

変更が加えられたことを確認するには、これらのインスタンスリソースプールに関連付けられている各ワークロードグループについて、サーバーのメモリと CPU の構成を確認する必要があります。

+ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]サーバーの既定のプール
+ 外部プロセスの既定のリソースプール
+ 外部プロセス用のユーザー定義プール

1. すべてのワークロードグループを表示するには、次のステートメントを実行します。

    ```sql
    SELECT * FROM sys.resource_governor_workload_groups;
    ```

    **サンプルの結果**

    |group_id|NAME|importance|request_max_memory_grant_percent|request_max_cpu_time_sec|request_memory_grant_timeout_sec|max_dop|group_max_requests pool_id|pool_idd|external_pool_id|
    |-|-|-|-|-|-|-|-|-|-|
    |1|internal|Medium|25|0|0|0|0|1|2|
    |2|既定値 (default)|Medium|25|0|0|0|0|2|2|
    |256|ds_wg|Medium|25|0|0|0|0|2|256|
  
2.  新しいカタログビュー [resource_governor_external_resource_pools &#40;&#41;](../../relational-databases/system-catalog-views/sys-resource-governor-external-resource-pools-transact-sql.md)を使用して、すべての外部リソースプールを表示します。
  
    ```sql
    SELECT * FROM sys.resource_governor_external_resource_pools;
    ```

    **サンプルの結果**
    
    |external_pool_id|NAME|max_cpu_percent|max_memory_percent|max_processes|version|
    |-|-|-|-|-|-|
    |2|既定値 (default)|100|20|0|2|
    |256|ds_ep|100|40|0|1|
  
     詳細については、「[Resource Governor Catalog Views &#40;Transact-SQL&#41; (リソース ガバナーのカタログ ビュー (Transact-SQL))](../../relational-databases/system-catalog-views/resource-governor-catalog-views-transact-sql.md)」を参照してください。
  
3.  次のステートメントを実行して、外部リソースプールに関連付けられているコンピューターリソースに関する情報を返します (該当する場合)。
  
    ```sql
    SELECT * FROM sys.resource_governor_external_resource_pool_affinity;
    ```
  
     ここでは、プールは、AUTO のアフィニティで作成されたため、情報は表示されません。 詳細については、「 [sys.dm_resource_governor_resource_pool_affinity &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pool-affinity-transact-sql.md)」を参照してください。

## <a name="next-steps"></a>次の手順

サーバーリソースの管理の詳細については、以下を参照してください。

+  [リソース ガバナー](../../relational-databases/resource-governor/resource-governor.md) 
+ [関連する動的管理ビュー &#40;の Resource Governor transact-sql&#41;](../../relational-databases/system-dynamic-management-views/resource-governor-related-dynamic-management-views-transact-sql.md)

Machine learning のリソースガバナンスの概要については、次を参照してください。

+  [Machine Learning Services のリソースガバナンス](../../advanced-analytics/r/resource-governance-for-r-services.md)
