---
title: R を使用したデータベース内分析のチュートリアル
description: SQL Server ストアドプロシージャおよび T-sql 関数に R プログラミング言語コードを埋め込む方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: e15e56dfb4a27f0a99262ff1f105ceb0a1fbc294
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715384"
---
# <a name="tutorial-r-data-analytics-for-sql-developers"></a>チュートリアル:SQL 開発者向け R Data Analytics
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL プログラマー向けのこのチュートリアルでは、SQL Server 上の[NYCTaxi_sample](demo-data-nyctaxi-in-sql.md)データベースを使用して r ベースの機械学習ソリューションを構築してデプロイすることで、r の統合について学習します。 T-sql、SQL Server Management Studio、およびデータベースエンジンインスタンスを [Machine Learning Services] ([Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)および R 言語サポートと共に使用します。

このチュートリアルでは、データモデリングワークフローで使用される R 関数について説明します。 手順には、データの探索、二項分類モデルの構築とトレーニング、モデルのデプロイが含まれます。 ビルドするモデルは、旅行が、時間、距離旅行、集荷位置に基づいてヒントを生成する可能性があるかどうかを予測します。 

このチュートリアルで使用するすべての R コードは、Management Studio で作成して実行するストアドプロシージャにラップされています。

## <a name="background-for-sql-developers"></a>SQL 開発者のための背景

機械学習ソリューションを構築するプロセスは、複数のツールに関連する可能性がある複雑なソリューションであり、複数の段階にわたって複数の分野の専門家を調整します。

+ データの取得とクリーニング
+ データの調査とモデリングに役立つ機能の構築
+ モデルのトレーニングとチューニング
+ 運用環境への配置

実際のコードの開発とテストは、専用の R 開発環境を使用して実行することをお勧めします。 ただし、スクリプトが完全にテストされた後は、の[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]使い慣れた[!INCLUDE[tsql](../../includes/tsql-md.md)]環境でストアドプロシージャを使用して簡単に配置できます。

このマルチパートチュートリアルの目的は、"完成した R コード" を SQL Server に移行するための一般的なワークフローの概要です。 

- [レッスン 1:ストアドプロシージャで R 関数を呼び出して、データの形状と分布を調べ、視覚化する](../tutorials/sqldev-explore-and-visualize-the-data.md)

- [レッスン 2:T-sql 関数で R を使用してデータ機能を作成する](sqldev-create-data-features-using-t-sql.md)
  
- [レッスン 3:関数とストアドプロシージャを使用した R モデルのトレーニングと保存](sqldev-train-and-save-a-model-using-t-sql.md)
  
- [レッスン 4:ストアドプロシージャで R モデルを使用して潜在的な結果を予測する](../tutorials/sqldev-operationalize-the-model.md)

モデルがデータベースに保存された後、ストアドプロシージャを使用して[!INCLUDE[tsql](../../includes/tsql-md.md)] 、からの予測のためにモデルを呼び出します。

## <a name="prerequisites"></a>必須コンポーネント

すべてのタスクは、の[!INCLUDE[tsql](../../includes/tsql-md.md)] [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]ストアドプロシージャを使用して実行できます。

このチュートリアルでは、データベースとテーブルの作成、データのインポート、SQL クエリの作成などの基本的なデータベース操作について理解していることを前提としています。 R がわかっていることを前提としていません。そのため、すべての R コードが提供されます。 

+ [R が有効になっている](../install/sql-machine-learning-services-windows-install.md#verify-installation) [SQL Server 2016 r Services](../install/sql-r-services-windows-install.md#verify-installation)または SQL Server Machine Learning Services

+ [R ライブラリ](../package-management/installed-package-information.md)

+ [権限](../security/user-permission.md)

+ [NYC タクシーデモデータベース](demo-data-nyctaxi-in-sql.md)


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ストアドプロシージャで R 関数を使用してデータの探索と視覚化を行う](../tutorials/sqldev-explore-and-visualize-the-data.md)
