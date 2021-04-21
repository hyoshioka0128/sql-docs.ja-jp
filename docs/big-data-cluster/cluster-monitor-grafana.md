---
title: Grafana ダッシュボードを使用したクラスターの監視
titleSuffix: SQL Server Big Data Clusters
description: SQL Server 2019 ビッグ データ クラスター上で Grafana ダッシュボードを使用したクラスターの監視。
author: cloudmelon
ms.author: melqin
ms.reviewer: mikeray
ms.metadata: seo-lt-2019
ms.date: 04/16/2021
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: cb1a8acdf83f324dc4295869172b74bcdb737537
ms.sourcegitcommit: 708b3131db2e542b1d461d17dec30d673fd5f0fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107729082"
---
# <a name="monitor-cluster-with-azdata-and-grafana-dashboard"></a>azdata と Grafana ダッシュボードを使用してクラスターを監視する

この記事では、[!INCLUDE[ssbigdataclusters-ss-nover](../includes/ssbigdataclusters-ss-nover.md)]内でアプリケーションを監視する方法について説明します。 SQL Server ビッグ データ クラスターが監視用の Grafana ダッシュボードを公開し、それらのメトリックは influxDB に格納されます。 そのようなメトリックは、次のいずれかに分類されます。 
- メトリックの収集、処理、集計、および書き込みのためのエージェントである Telegraf によって収集された Kubernetes ホスト関連のメトリック。
- ワークロード関連のメトリック: そのようなメトリックは SQL Server に関連し、Spark および HDFS は CollectID ごとに収集されます (SQL Server DMV メトリックや [SQL Server 拡張イベント (XEvent)](../relational-databases/extended-events/extended-events.md) が含まれます) 

## <a name="available-metrics"></a>使用可能なメトリック 

[!INCLUDE[ssbigdataclusters-ss-nover](../includes/ssbigdataclusters-ss-nover.md)] では、次のメトリックを使用できます。

|Categories |Description | metrics |
|---|---|---|
|ホストされているノードのメトリック|Kubernetes ホストに関連するメトリック | CPU、RAM 使用率、ディスク IOPS、負荷平均など   |
|ポッドとコンテナーのメトリック|Kubernetes ポッドおよびコンテナーに関連するメトリック。Grafana を使用すると、ポッド、さらには特定のコンテナーごとにそのようなメトリックをフィルター処理できます。 | CPU、RAM、ディスク、およびネットワークの使用率。   |
|SQL Server のメトリック|SQL Server に関連するメトリック | トランザクション/秒、バッチ要求/秒、データベース アクティビティ、SQL Server アクティビティなど。特に、ContainerAG が有効になっている場合は、ここから AlwaysOn を監視することもできます。   |
|Spark のメトリック |Spark アプリに関連するメトリック。 | Executor の hdfs の書き込み、JVM の GC 時間、JVM ヒープの使用率など。   |
|アプリのメトリック|[!INCLUDE[ssbigdataclusters-ss-nover](../includes/ssbigdataclusters-ss-nover.md)] で[展開されているアプリ](concept-application-deployment.md)に関連するメトリック。Grafana が特定のアプリとアプリのバージョン別にそのようなメトリックをフィルター処理できるようになります。 | CPU、RAM、および HTTP 要求の状態。   |

## <a name="prerequisites"></a>前提条件

- [SQL Server 2019 ビッグ データ クラスター](deployment-guidance.md)
- [azdata コマンドライン ユーティリティ](../azdata/install/deploy-install-azdata.md)

## <a name="capabilities"></a>機能

SQL Server 2019 では、アプリケーションの作成、削除、説明、初期化、一覧表示、実行、更新を行うことができます。 次の表では、**azdata** で使用できるアプリケーションの展開コマンドについて説明します。

|コマンド |説明 |
|:---|:---|
|`azdata bdc endpoint list` | ビッグ データ クラスターのエンドポイントを一覧表示します。 |


次の例を利用し、**Grafana ダッシュボード** のエンドポイントをリストアップできます。

```bash
azdata bdc endpoint list --endpoint-name metricsui 
```

表示されたエンドポイントには、クラスターのユーザー名とパスワードを使用してログインできます。 

![Grafana ダッシュボード](media/big-data-cluster-monitor-apps/grafana-dashboard-endpoint.png)

`nodeMetricsUrl` と `sqlMetricsUrl` の値は、Kubernetes ノードのメトリックとビッグ データ クラスターのサービス メトリックを監視するための Grafana ダッシュボードにリンクされています。

![grafana ダッシュボード](./media/view-cluster-status/grafana-dashboard.png)

![SQL](./media/view-cluster-status/grafana-sql-status.png)



## <a name="next-steps"></a>次のステップ

[!INCLUDE[ssbigdataclusters-ss-nover](../includes/ssbigdataclusters-ss-nover.md)]の詳細については、「[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]とは](big-data-cluster-overview.md)」を参照してください。