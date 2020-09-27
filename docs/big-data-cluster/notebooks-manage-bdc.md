---
title: 管理:Azure Data Studio ノートブック
titleSuffix: SQL Server Big Data Clusters
description: Azure Data Studio のノートブックを使用して、SQL Server ビッグ データ クラスターの管理とトラブルシューティングを行います。
author: yualan
ms.author: alanyu
ms.reviewer: mikeray
ms.metadata: seo-lt-2019
ms.date: 03/30/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 372520b7bc4d5c80f67e6206194d8e02e2562e7b
ms.sourcegitcommit: 7345e4f05d6c06e1bcd73747a4a47873b3f3251f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88778531"
---
# <a name="manage-sql-server-big-data-clusters-with-azure-data-studio-notebooks"></a>Azure Data Studio ノートブックを使用して SQL Server ビッグ データ クラスターを管理する

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] は、ノートブックを含む Azure Data Studio の拡張機能を提供します。 ノートブックには、SQL Server 2019 ビッグ データ クラスターを管理するために Azure Data Studio で使用できるドキュメントとコードが用意されています。

もともとは、オープン ソース プロジェクトとして実装され、[ノートブック](../azure-data-studio/notebooks-guidance.md)は [Azure Data Studio](../azure-data-studio/download-azure-data-studio.md?view=sql-server-ver15) 内に組み込まれていました。 テキスト セル内のテキストに対してはマークダウンを、また、コード セル内にコードを記述するには利用可能なカーネルの 1 つを使用できます。

ノートブックを使用して、[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] に対してビッグ データ クラスターを展開できます。

ノートブックに加え、ユーザーは Jupyter Book と呼ばれるノートブックのコレクションを表示できます。 Jupyter Book には、ノートブックのコレクション内を移動できる目次が用意されています。これにより、必要なノートブックを見つけたり、SQL Server のトラブルシューティングを行ったり、クラスターの状態を表示したりすることができます。

## <a name="prerequisites"></a>前提条件

ノートブックを開くには、次のコンポーネントが必要です。

* [Azure Data Studio Insiders ビルド](https://aka.ms/azuredatastudio-rc)の最新バージョン
* [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 拡張機能が Azure Data Studio にインストールされている

これらのコンポーネントに加えて、SQL Server 2019 ビッグ データ クラスターを展開するには、次のコンポーネントも必要です。

* [azdata](deploy-install-azdata.md)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-native-package-management)
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="access-troubleshooting-notebooks"></a>ノートブックのトラブルシューティングへのアクセス

ノートブックのトラブルシューティングにアクセスするには、次の 3 つの方法があります。

### <a name="command-palette"></a>コマンド パレット

1. **[ビュー]**  >  **[コマンド パレット]** を選択します。

2. 「**Jupyter Books:SQL Server 2019 Guide**」と入力します。

SQL Server ビッグ データ クラスターに関連するトラブルシューティング ノートブックを含む Jupyter Book が含まれている Jupyter Books Viewlet が開きます。

### <a name="sql-master-dashboard"></a>SQL マスター ダッシュボード

1. Azure Data Studio Insider をインストールした後、SQL Server ビッグ データ クラスター インスタンスに接続します。

2. インスタンスに接続した後、 **[接続]** の下のサーバー名を右クリックし、 **[管理]** を選択します。

3. ダッシュボードで、 **[SQL Server ビッグ データ クラスター]** をクリックします。 **[SQL Server 2019 ガイド]** を選択して、必要なノートブックを含む Jupyter Book を開きます。
    ![ダッシュボードの Jupyter ノートブック](media/manage-notebooks/jupyter-book-button.png)

4. 完了する必要があるタスクのノートブックを選択します。

### <a name="controller-dashboard"></a>コントローラー ダッシュボード

1. **[接続]** ビューで、 **[SQL Server ビッグ データ クラスター]** を展開します。

2. コントローラーのエンドポイントの詳細を追加します。

3. コントローラーに接続した後、エンドポイントを右クリックし、 **[管理]** を選択します。

4. ダッシュボードが読み込まれたら、 **[トラブルシューティング]** を選択して Jupyter Book トラブルシューティング ガイドを開きます。

## <a name="use-troubleshooting-notebooks"></a>ノートブックのトラブルシューティングの使用

1. Jupyter Book の目次で必要なトラブルシューティング ガイドを見つけます。

2. ノートブックは最適化されているので、 **[セルの実行]** を選択する必要があるだけです。 この操作により、ノートブックが完了するまで、ノートブック内の各セルが個別に実行されます。

3. エラーが検出された場合、Jupyter Book は、エラーを修正するために実行できるノートブックを提案します。 推奨される手順に従って、もう一度ノートブックを実行します。

## <a name="change-the-big-data-cluster"></a>ビッグ データ クラスターを変更する

ノートブック用の SQL Server ビッグ データ クラスターを変更するには

1. ノートブックのツール バーから **[アタッチ先]** メニューをクリックします。

   ![ノートブックのツール バーから [アタッチ先] メニューをクリックする](./media/notebooks-how-to-manage/select-attach-to-1.png)

2. **[アタッチ先]** メニューからサーバーをクリックします。

   ![[アタッチ先] メニューからサーバーを選択する](./media/notebooks-how-to-manage/select-attach-to-2.png)

## <a name="next-steps"></a>次のステップ

Azure Data Studio のノードブックの詳細については、[SQL Server でノートブックを使用する方法](../azure-data-studio/notebooks-guidance.md)に関するページを参照してください。