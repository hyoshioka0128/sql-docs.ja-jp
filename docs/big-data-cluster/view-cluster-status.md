---
title: ビッグ データ クラスター (BDC) の管理リソース
titleSuffix: SQL Server
description: この記事では、Azure Data Studio、ノートブック、および Azure Data CLI (azdata) コマンドを使用して、ビッグ データ クラスターの状態を表示する方法について説明します。
author: cloudmelon
ms.author: melqin
ms.reviewer: mikeray
ms.date: 04/15/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: b41666e4f0474d0e0843e8a1b78d83fcfab474be
ms.sourcegitcommit: a177a1e17200400a70f1d61b737481c83249e9a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107584033"
---
# <a name="administration-resources-for-big-data-clusters-bdc"></a>ビッグ データ クラスター (BDC) の管理リソース

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を外部から管理する方法について説明します。

## <a name="know-your-architecture"></a>アーキテクチャを理解する

SQL Server 2019 (15.x) 以降、[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]を使用すると、Kubernetes 上で動作する拡張可能な SQL Server、Spark、および HDFS コンテナーのクラスターを展開できます。 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]の概要についは、「[SQL Server ビッグ データ クラスターとは](big-data-cluster-overview.md)」を参照してください。

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]では、明確で一貫性のある承認と認証が提供されます。 ビッグ データ クラスターのセキュリティの概要については、「[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] ビッグ データ クラスターのセキュリティの概念](concept-security.md)」を参照してください。

## <a name="manage-and-operate-with-tools"></a>ツールを使用して管理および操作する

次の記事は、ビッグ データ クラスターを次の方法で管理および操作する方法について説明しています。 

- [Azure Data Studio を使用して SQL Server ビッグ データ クラスターに接続する](connect-to-big-data-cluster.md)
- [SQL Server コントローラー ダッシュボードのビッグ データ クラスターを管理する](manage-with-controller-dashboard.md)
- [Azure Data Studio ノートブックを使用して SQL Server ビッグ データ クラスターを管理する](notebooks-manage-bdc.md)
- [ノートブックを使用してクラスター上でビッグ データ クラスター (BDC) を管理する](cluster-manage-notebooks.md)
- [Spark を使用したサンプル ノートブックの実行](notebooks-tutorial-spark.md)

## <a name="monitor-with-tools"></a>ツールを使用して監視する

次の記事は、ビッグ データ クラスターを次の方法で監視する方法について説明しています。 

- [Azure Data Studio を使用して BDC クラスターを監視する](cluster-monitor-ads.md)
- [Azdata ユーティリティを使用して BDC クラスターを監視する](cluster-monitor-cmdlet.md)
- [Grafana ダッシュボードを使用して BDC クラスターを監視する](cluster-monitor-grafana.md)
- [Jupyter ノートブックと Azure Data Studio を使用して BDC クラスターを監視する](cluster-monitor-notebooks.md)

## <a name="monitor-and-inspect-logs-with-notebooks"></a>ノートブックを使用してログを監視および検査する

次の記事には、Azure Data Studio で使用できる Jupyter ノートブックの多くが一覧表示されています。

- [ノートブックを使用したクラスターの監視](cluster-monitor-notebooks.md)
- [ノートブックを使用したクラスター内のログの収集と分析](cluster-logging-notebooks.md)

## <a name="big-data-clusters-troubleshooting-resources"></a>ビッグ データ クラスターのトラブルシューティングに関するリソース

次の記事では、ビッグ データ クラスターのトラブルシューティングを行う方法が説明されています。

- [kubectl ユーティリティを使用した BDC クラスターのトラブルシューティング](cluster-troubleshooting-commands.md) 
- [Pyspark ノートブックのトラブルシューティング](troubleshoot-pyspark-notebook.md)
- [Jupyter ノートブックと Azure Data Studio (ADS) を使用した BDC クラスターのトラブルシューティング](cluster-troubleshooter-notebooks.md)
- [HDFS のアクセス許可の復元](troubleshoot-hdfs-restore-admin.md)

次の記事では、Active Directory モードで展開されたビッグ データ クラスターのトラブルシューティングを行う方法が説明されています。
- [Active Directory モードでの BDC クラスターのトラブルシューティング](troubleshoot-active-directory.md) 
- [AD モード ログイン失敗のトラブルシューティング](troubleshoot-ad-login-failed-untrusted-domain.md)
- [BDC AD モードのデプロイが停止した場合のトラブルシューティング](troubleshoot-ad-reverse-lookup-zone.md)


## <a name="where-to-find-big-data-clusters-2019-administration-notebooks"></a>管理者向け [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] ノートブックの場所 

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]では、Jupyter Notebook を使用した包括的な管理エクスペリエンスを提供します。 この提供されているノートブックでは、クラスターの操作、管理、監視、ログ記録、およびトラブルシューティングを行えます。 


### <a name="access-to-local-notebooks"></a>ローカルのノートブックの場所 

Azure Data Studio (ADS) を使用して BDC クラスターに接続できたら、[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]] タブに移動し、次のように、ローカルのすべてのノートブックへの直接のリンクを表示できます。 

![BDC のローカル上のガイド](media/view-cluster-status/bdc-local-guides.png)

ノートブックには、Azure Data Studio (ADS) から直接アクセスすることも可能です。 Ctrl + Shift + P のキーボード ショートカットを使用するか、[ビュー]、[コマンド パレット] を選択し、'Jupyter Books: Get localized SQL Server 2019 guide' のオプションを見つけます。 


### <a name="add-remote-notebooks"></a>リモートのノートブックを追加する

ノートブックはリモートから取得するため、自分で選択したノートブック バージョンを取得できます。 Azure Data Studio (ADS) からリモートのノートブックを追加するには、Ctrl + Shift + P のキーボード ショートカットを使用するか、次のように [ビュー]、[コマンド パレット] を選択します。

![ADS のパレット](media/view-cluster-status/bdc-ads-palette.png)

'Jupyter Books: Add Remote Book' のオプションが表示され、自分で選択したノートブック バージョンを選択するパネルが表示されます。

![BDC のリモートのガイド](media/view-cluster-status/bdc-remote-guides.png)

[追加] をクリックすると、次のように Azure Data Studio の [ノートブック] タブから、自分で選択したバージョンのすべてのノートブックにアクセスできます。 

![BDC ADS ガイド](media/view-cluster-status/bdc-ads-guides.png)


### <a name="how-to-use-these-notebooks"></a>これらのノートブックの使用方法 

これらのノートブックの使用方法は、以下の記事で説明しています。

- [Jupyter ノートブックと Azure Data Studio を使用して BDC クラスターを監視する](cluster-monitor-notebooks.md)
- [ノートブックを使用したクラスター内のログの収集と分析](cluster-logging-notebooks.md)
- [Pyspark ノートブックのトラブルシューティング](troubleshoot-pyspark-notebook.md)
- [Jupyter ノートブックと Azure Data Studio (ADS) を使用した BDC クラスターのトラブルシューティング](cluster-troubleshooter-notebooks.md)

## <a name="next-steps"></a>次のステップ

ビッグ データ クラスターの詳細については、[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] とは](big-data-cluster-overview.md)の概要に関するページを参照してください。