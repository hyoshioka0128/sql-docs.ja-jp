---
title: SQL 開発者向けのデータベース内 Python analytics のチュートリアル
description: ストアドプロシージャと T-sql 関数 SQL Server に Python コードを埋め込む方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/29/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 9d3905ef9434bf4d3f887130c6f67ad68c6a6e36
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714766"
---
# <a name="tutorial-python-data-analytics-for-sql-developers"></a>チュートリアル:SQL 開発者向けの Python Data Analytics
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL プログラマー向けのこのチュートリアルでは、SQL Server 上の[NYCTaxi_sample](demo-data-nyctaxi-in-sql.md)データベースを使用して python ベースの機械学習ソリューションを構築してデプロイすることで、python 統合について説明します。 T-sql、SQL Server Management Studio、および[Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)と Python 言語サポートを備えたデータベースエンジンインスタンスを使用します。

このチュートリアルでは、データモデリングワークフローで使用される Python 関数について説明します。 手順には、データの探索、二項分類モデルの構築とトレーニング、モデルのデプロイが含まれます。 ニューヨーク市のタクシーと Limosine の歩合のサンプルデータを使用します。作成するモデルでは、旅行の結果として、1日の時間、距離のしかも、および集荷場所に基づくヒントが生じる可能性があるかどうかを予測します。 

このチュートリアルで使用するすべての Python コードは、Management Studio で作成して実行するストアドプロシージャにラップされています。

> [!NOTE]
> このチュートリアルは、R と Python の両方で使用できます。 R バージョンについては、「 [r 開発者向けのデータベース内分析](sqldev-in-database-r-for-sql-developers.md)」を参照してください。

## <a name="overview"></a>概要

機械学習ソリューションを構築するプロセスは、複数のツールに関連する可能性がある複雑なソリューションであり、複数の段階にわたって複数の分野の専門家を調整します。

+ データの取得とクリーニング
+ データの調査とモデリングに役立つ機能の構築
+ モデルのトレーニングとチューニング
+ 運用環境への配置

実際のコードの開発とテストは、専用の開発環境を使用して実行することをお勧めします。 ただし、スクリプトが完全にテストされた後は、の[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]使い慣れた[!INCLUDE[tsql](../../includes/tsql-md.md)]環境でストアドプロシージャを使用して簡単に配置できます。 SQL Server でコードを運用するには、ストアドプロシージャで外部コードをラップすることが主要なメカニズムです。

Python を初めて使用する SQL プログラマでも、SQL を初めて使用する Python 開発者でも、このマルチパートチュートリアルでは、Python と SQL Server でデータベース内分析を行うための一般的なワークフローを紹介します。 

+ [レッスン 1:Python を使用したデータの探索と視覚化](sqldev-py3-explore-and-visualize-the-data.md)

+ [レッスン 2:カスタム SQL 関数を使用したデータ機能の作成](sqldev-py4-create-data-features-using-t-sql.md)

+ [レッスン 3:T-sql を使用した Python モデルのトレーニングと保存](sqldev-py5-train-and-save-a-model-using-t-sql.md)

+ [レッスン 4:ストアドプロシージャで Python モデルを使用して潜在的な結果を予測する](sqldev-py6-operationalize-the-model.md)

モデルがデータベースに保存された後は、ストアドプロシージャを使用して、 [!INCLUDE[tsql](../../includes/tsql-md.md)]からモデルを呼び出すことができます。

## <a name="prerequisites"></a>必須コンポーネント

+ [Python での Machine Learning Services の SQL Server](../install/sql-machine-learning-services-windows-install.md#verify-installation)

+ [権限](../security/user-permission.md)

+ [NYC タクシーデモデータベース](demo-data-nyctaxi-in-sql.md)

すべてのタスクは、の[!INCLUDE[tsql](../../includes/tsql-md.md)] [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]ストアドプロシージャを使用して実行できます。

このチュートリアルでは、データベースとテーブルの作成、データのインポート、SQL クエリの作成などの基本的なデータベース操作について理解していることを前提としています。 Python がわかっていることを前提としていません。 そのため、すべての Python コードが用意されています。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Python を使用したデータの探索と視覚化](sqldev-py3-explore-and-visualize-the-data.md)
