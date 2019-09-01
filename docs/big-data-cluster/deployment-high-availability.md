---
title: 高可用性を備えた SQL Server ビッグデータクラスターのデプロイ
titleSuffix: Deploy SQL Server Big Data Cluster with high availability
description: 高可用性でを[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]デプロイ (プレビュー) する方法について説明します。
author: mihaelablendea
ms.author: mihaelab
ms.reviewer: mikeray
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 2c0e5f5a5f194045b5d1b48a383f9d4dfd282649
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70158167"
---
# <a name="deploy-sql-server-big-data-cluster-with-high-availability"></a>高可用性を備えた SQL Server ビッグデータクラスターのデプロイ

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

ビッグデータクラスター (BDC) をデプロイするときに、可用性グループの構成にデプロイする SQL Server マスターを構成できます。 この構成では、Kubernetes インフラストラクチャが組み込みの正常性監視、障害検出、およびフェールオーバーメカニズムを使用して有効にすることで、SQL Server マスターの信頼性が向上します。 可用性グループ (AG) は、SQL Server インスタンスに冗長性を追加します。 この構成では、監視、障害検出、およびフェールオーバータスクは、ビッグデータクラスター管理サービス、つまり制御サービスによって管理されます。

さらに、データベースミラーリングエンドポイントの設定、可用性グループの作成、可用性グループへのデータベースの追加などの管理タスクは、ビッグデータクラスタープラットフォームによって提供されます。

可用性グループによって実現される機能の一部を次に示します。

1. 高可用性の設定が展開構成ファイルで指定されている場合は、と`containedag`いう名前の単一の可用性グループが作成されます。 既定では`containedag` 、にはプライマリを含む3つのレプリカがあります。 可用性グループのすべての CRUD 操作は、内部的に管理されます。
1. およびを`master` `msdb`含むすべてのデータベースが、可用性グループに自動的に追加されます。 Polybase 構成データベースには、各レプリカに固有のインスタンスレベルのメタデータが含まれているため、可用性グループには含まれません。
1. 外部エンドポイントは、AG データベースに接続するために自動的にプロビジョニングされます。 このエンド`master-svc-external`ポイントは、AG リスナーの役割を果たします。
1. セカンダリレプリカへの読み取り専用接続に対して、2番目の外部エンドポイントがプロビジョニングされます。 

# <a name="deploy"></a>配置

可用性グループに SQL Server マスターをデプロイするには:

1. `hadr`機能を有効にする
1. AG のレプリカの数を指定します (最小値は 3)
1. 読み取り専用セカンダリレプリカへの接続用に作成された2番目の外部エンドポイントの詳細を構成します。

次の手順は、これらの設定を含む修正プログラムファイルを作成する方法と、 `aks-dev-test`または`kubeadm-dev-test`構成プロファイルに適用する方法を示しています。 これらの手順では、HA 属性を追加する`aks-dev-test`ためにプロファイルに修正プログラムを適用する方法の例を紹介します。

1. ファイルの`ha-patch.json`作成

    ```json
    {
      "patch": [
        {
          "op": "replace",
          "path": "spec.resources.master.spec",
          "value": {
            "type": "Master",
            "replicas": 3,
            "endpoints": [
              {
                "name": "Master",
                "serviceType": "LoadBalancer",
                "port": 31433
              },
              {
                "name": "MasterSecondary",
                "serviceType": "LoadBalancer",
                "port": 31436
              }
            ],
            "settings": {
              "sql": {
                "hadr.enabled": "true"
              }
            }
          }
        }
      ]
    }
    ```

1. ターゲットプロファイルの複製

    ```bash
    azdata config init --source aks-dev-test --target custom-aks
    ```

1. カスタムプロファイルにパッチファイルを適用する

    ```bash
    azdata bdc config patch -c custom-aks/bdc.json --patch-file patch.json
    ```

## <a name="connect-to-sql-server-databases"></a>SQL Server データベースへの接続

SQL Server マスターに対して実行するワークロードの種類に応じて、読み取り/書き込みワークロードの場合はプライマリに、読み取り専用のワークロードの場合はセカンダリレプリカ内のデータベースに接続できます。 接続の種類ごとの概要を次に示します。

### <a name="connect-to-databases-on-the-primary-replica"></a>プライマリレプリカのデータベースに接続する

プライマリレプリカへの接続には、 `sql-server-master`エンドポイントを使用します。 このエンドポイントは、AG のリスナーでもあります。 すべての接続は、可用性グループのコンテキスト内にあります。 たとえば、このエンドポイントを使用する既定の接続では、SQL Server `master`インスタンス`master`データベースではなく、AG 内のデータベースに接続します。

```bash
azdata bdc endpoint list -e sql-server-master -o table
```

`Description                           Endpoint             Name               Protocol`
`------------------------------------  -------------------  -----------------  ----------`
`SQL Server Master Instance Front-End  13.64.235.192,31433  sql-server-master  tds`

### <a name="connect-to-databases-on-the-secondary-replicas"></a>セカンダリレプリカのデータベースに接続する

セカンダリレプリカのデータベースに対する読み取り専用接続の場合は、 `sql-server-master-readonly`エンドポイントを使用します。 このエンドポイントは、すべてのセカンダリレプリカのロードバランサーのように機能します。 接続文字列にユーザーデータベースコンテキストを指定します。

```bash
azdata bdc endpoint list -e sql-server-master-readonly -o table
```

`Description                                    Endpoint            Name                        Protocol`
`---------------------------------------------  ------------------  --------------------------  ----------`
`SQL Server Master Readable Secondary Replicas  13.64.238.24,31436  sql-server-master-readonly  tds`

### <a id="instance-connect"></a>SQL Server インスタンスに接続する

サーバーレベルの構成を設定する、または可用性グループにデータベースを手動で追加するなどの特定の操作 (データベースが復元ワークフローで作成された場合) には、インスタンスへの接続が必要です。 この接続を提供するには、外部エンドポイントを公開します。 このエンドポイントを公開してから、復元ワークフローで作成したデータベースを可用性グループに追加する方法の例を次に示します。

- `sql-server-master`エンドポイントに接続し、次のように実行して、プライマリレプリカをホストするポッドを特定します。

    ```sql
    SELECT @@SERVERNAME
   ```

- 新しい Kubernetes サービスを作成して外部エンドポイントを公開する

    Kubeadm クラスターの場合は、次のコマンドを実行します。 を`podName` 、前の`serviceName`手順で返されたサーバーの名前で、を、作成`namespaceName`した Kubernetes サービスの優先名に、* を BDC クラスターの名前に置き換えます。

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    Aks クラスターを実行する場合は、同じコマンドを実行し`LoadBalancer`ます。ただし、作成されるサービスの種類はになります。 以下に例を示します。 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    次に、このコマンドを aks に対して実行する例を示します。ここ`master-0`で、プライマリをホストしているポッドは次のようになります。

    ```bash
    kubectl -n mssql-cluster expose pod master-0 --port=1533  --name=master-sql-0 --type=LoadBalancer
    ```

    作成された Kubernetes サービスの IP を取得します。

    ```bash
    kubectl get services -n <namespaceName>
    ```

> [!IMPORTANT]
> ベストプラクティスとして、次のコマンドを実行して、上記で作成した Kubernetes サービスを削除してクリーンアップする必要があります。
>
>```bash
>kubectl delete svc master-sql-0 -n mssql-cluster
>```

- 可用性グループにデータベースを追加します。

    データベースを AG に追加するには、完全復旧モードで実行する必要があり、ログバックアップを実行する必要があります。 上で作成した Kubernetes サービスの IP を使用して SQL Server インスタンスに接続し、次に示すように TSQL ステートメントを実行します。

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    次の例では、インスタンス`sales`で復元されたという名前のデータベースを追加します。

    ```sql
    ALTER DATABASE sales SET RECOVERY FULL;
    BACKUP DATABASE sales TO DISK='/var/opt/mssql/data/sales.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE sales
    ```

## <a name="known-limitations"></a>既知の制限

ビッグデータクラスターの SQL Server マスターの可用性グループに関する既知の問題と制限事項を次に示します。

- 以外`CREATE DATABASE` のワークフロー`CREATE DATABASE FROM SNAPSHOT`の結果として作成されたデータベースは、可用性グループに自動的に追加されません。`RESTORE` [インスタンスに接続](#instance-connect)し、データベースを可用性グループに手動で追加します。
- でサーバーの構成設定を実行する`sp_configure`などの特定の操作では、master インスタンスに接続する必要があります。 対応するプライマリエンドポイントを使用することはできません。 [指示](#instance-connect)に従って SQL Server インスタンスに接続し、を`sp_configure`実行します。
- BDC をデプロイするときに、高可用性構成を作成する必要があります。 可用性グループの配置後に高可用性構成を有効にすることはできません。

## <a name="next-steps"></a>次の手順

- ビッグデータクラスターの展開における構成ファイルの使用の詳細については、「 [How to deploy [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] on Kubernetes](deployment-guidance.md#configfile)」を参照してください。
- SQL Server の可用性グループの機能の詳細については、「 [Always On 可用性グループの概要 (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)」を参照してください。
