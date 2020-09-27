---
title: 記憶域プールとは
titleSuffix: SQL Server big data clusters
description: SQL Server 2019 ビッグ データ クラスターの SQL Server ストレージ プールのロールと、SQL ストレージ プールのアーキテクチャと機能について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/21/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: fd7a38d555cbf6e2f64743f0907fbfbbdec4d41f
ms.sourcegitcommit: 6f49804b863fed44968ea5829e2c26edc5988468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87806471"
---
# <a name="what-is-the-storage-pool-big-data-clusters-2019"></a>記憶域プールとは ([!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)])

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]での *SQL Server 記憶域プール*の役割について説明します。 以下のセクションでは、SQL 記憶域プールのアーキテクチャと機能について説明します。

## <a name="storage-pool-architecture"></a>記憶域プールのアーキテクチャ

記憶域プールは、SQL Server on Linux、Spark、および HDFS で構成される記憶域ノードで構成されます。 SQL ビッグ データ クラスター内のすべての記憶域ノードは、HDFS クラスターのメンバーです。

![記憶域プールのアーキテクチャ](media/concept-storage-pool/scale-big-data-on-demand.png)

## <a name="responsibilities"></a>役割

記憶域ノードの役割は次のとおりです。

- Spark を使用したデータ インジェスト。
- HDFS のデータ ストレージ (Parquet および区切りテキスト形式)。 HDFS では、HDFS データが SQL ビッグ データ クラスター内のすべての記憶域ノードに分散されるため、データの永続性も提供されます。
- HDFS と SQL Server エンドポイントを使用したデータ アクセス。

## <a name="next-steps"></a>次のステップ

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] の詳細については、次のリソースを参照してください。

- [[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]とは](big-data-cluster-overview.md)
- [ワークショップ: Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] アーキテクチャ](https://github.com/Microsoft/sqlworkshops/tree/master/sqlserver2019bigdataclusters)
