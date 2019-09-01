---
title: コントローラーダッシュボードを使用して SQL Server ビッグデータクラスターを管理する
titleSuffix: Manage SQL Server big data cluster with controller dashboard
description: Azure Data Studio のノートブックを使用して、ビッグ データ クラスターの管理とトラブルシューティングを行います。
author: yualan
ms.author: alanyu
ms.reviewer: mikeray
ms.date: 08/29/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 5fadb9b069e6f70cb780e6dc69295f63330a683a
ms.sourcegitcommit: 71fac5fee00e0eca57e555f44274dd7e08d47e1e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70160720"
---
# <a name="manage-big-data-clusters-for-sql-server-controller-dashboard"></a>SQL Server controller ダッシュボードのビッグデータクラスターの管理

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

**Azdata**と cluster status notebook に加えて、SQL Server ビッグデータクラスターの状態を表示する別の方法もあります。 **接続**viewlet を使用して SQL Server ビッグデータクラスターコントローラーを追加できるようになりました。 これにより、ダッシュボードを使用してクラスターの正常性を表示できます。

![ダッシュボード](media/manage-with-controller-dashboard/controller-dashboard.png)
## <a name="prerequisites"></a>前提条件

Notebook を起動するには、次の前提条件が必要です。

* [Azure Data Studio Insiders ビルド](https://docs.microsoft.com/sql/big-data-cluster/deploy-big-data-tools?view=sqlallproducts-download-and-install-azure-data-studio-sql-server-2019-release-candidate-rc)の最新バージョン
* [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 拡張機能が Azure Data Studio にインストールされている

上記に加えて、SQL Server 2019 ビッグデータクラスターにも次のものが必要です。

* **azdata**
    - [Linux パッケージマネージャー](deploy-install-azdata-linux-package.md)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-native-package-management)
* [Azure CLI](/cli/azure/install-azure-cli)


## <a name="add-sql-server-big-data-cluster-controller"></a>ビッグデータクラスターコントローラーの追加 SQL Server

1. 左側のウィンドウで、[接続] ビューをクリックします。
2. 接続 ビューの下部にある  **SQL Server ビッグデータクラスター** をクリックして展開します。
3. **[Big data cluster controller SQL Server 追加]** をクリックすると、新しいダイアログが表示されます。

## <a name="add-new-controller"></a>新しいコントローラーの追加

1. クラスター名を追加します。
2. クラスター管理サービスの URL を追加します。 これは、BDC ダッシュボードのサービスエンドポイントテーブルにあります。または、クラスター管理者に問い合わせてください。
3. ユーザー名とパスワードを追加します。

## <a name="launch-controller-dashboard"></a>コントローラーダッシュボードの起動

1. コントローラーが正常に追加されると、[SQL Server ビッグデータクラスター] の下に表示されます。
2. ダッシュボードを起動するには、コントローラーを右クリックし、 **[管理]** をクリックします。

## <a name="controller-dashboard"></a>コントローラーダッシュボード

1. コントローラーダッシュボードには、次の3つの主要なコンポーネントがあります。

    - ダッシュボードのアクションを含む上部の**ツールバー** 。
    - 左側の**ナビゲーションウィンドウ**では、ダッシュボード上のさまざまなビューが変更されます。
    - ページの大部分をカバーする**ビュー** 。

2. **ビッグデータクラスターの概要**ページには、次の情報が表示されます。

    - クラスターの**プロパティ**を使用して、クラスターに関する情報を表示します。
    - クラスターの**概要**では、すべてのクラスターコンポーネントと異常なコンポーネントの概要を確認します。
    - SQL Server ビッグデータクラスター内のさまざまなサービスにコピーしたり、アクセスしたりするための**サービスエンドポイント**。

3. **ナビゲーションウィンドウ**で、サービスの一覧を表示し、1つをクリックすると、追加のクラスターの詳細を表示できます。 これは、 **[クラスターの概要]** でサービスをクリックしたときに表示されるビューと同じです。

4. **クラスターの詳細**の下にある各ビューは、同じ UI コンポーネントで構成されています。

    - コンポーネントの状態と正常性の状態を共有する**正常性状態の詳細**。
    - Grafana と Kibana を通じて追加のメトリックとログを表示するための**メトリックとログ**。

1. 問題のあるコンポーネントを表示する場合は、ツールバーの **[トラブルシューティング]** をクリックして、問題の診断に役立つノートブックを含む Jupyter ブックを起動します。

## <a name="next-steps"></a>次の手順

コントローラーの詳細については、[コントローラーのドキュメント](concept-controller.md)を参照してください。