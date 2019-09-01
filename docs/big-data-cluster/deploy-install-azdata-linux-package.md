---
title: インストーラーを使用して azdata を Linux にインストールする
titleSuffix: SQL Server big data clusters
description: インストーラー (Linux) を使用して、(プレビュー) [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]をインストールして管理するための azdata ツールをインストールする方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: e11e4851294ac8ffd8efa66e2156dcd47bce3aa0
ms.sourcegitcommit: 71fac5fee00e0eca57e555f44274dd7e08d47e1e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70160680"
---
# <a name="install-azdata-to-manage-includebig-data-clusters-2019includesssbigdataclusters-ss-novermd-on-linux"></a>Linux `azdata`で管理[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]するためのインストール

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

この記事では、Linux `azdata`で SQL Server 2019 ビッグデータクラスターリリース候補をインストールする方法について説明します。 これらのパッケージマネージャーを使用できるようにする`azdata`に`pip`は、必須のがインストールされている必要があります。

パッケージマネージャーは、さまざまなオペレーティングシステムおよびディストリビューション向けに設計されています。

- Linux (Ubuntu) の場合は、[と共に`azdata` `apt`インストール](#azdata-apt)します。

現時点では、他のオペレーティングシステムまたはディストリビューション`azdata`にインストールするパッケージマネージャーはありません。 これらのプラットフォームについては、「[パッケージマネージャーを`azdata`使用しないインストール](./deploy-install-azdata.md)」を参照してください。

## <a id="linux"></a>Linux `azdata`用のインストール

`azdata`の Ubuntu では、 `apt`インストールパッケージを使用できます。

### <a id="azdata-apt"></a>Apt `azdata`を使用したインストール (Ubuntu)

>[!NOTE]
>`azdata`パッケージは、システム python を使用せず、独自の python インタープリターをインストールします。

1. インストールプロセスに必要なパッケージを取得します。

    ```bash
    sudo apt-get update
    sudo apt-get install gnupg ca-certificates curl apt-transport-https lsb-release -y
    ```

2. 署名キーをダウンロードしてインストールします。

    ```bash
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    ```

3. リポジトリの`azdata`情報を追加します。

    ```bash
    sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/16.04/mssql-server-preview.list)"
    ```

4. リポジトリ情報を更新し`azdata`てインストールします。

    ```bash
    sudo apt-get update
    sudo apt-get install -y azdata-cli
    ```

5. インストールの確認:

    ```bash
    azdata --version
    ```

### <a name="update"></a>更新

アップグレード`azdata`のみ:

```bash
sudo apt-get update && sudo apt-get install --only-upgrade -y azdata-cli
```

### <a name="uninstall"></a>アンインストール

1. Apt を使用したアンインストール-削除:

    ```bash
    sudo apt-get remove -y azdata-cli
    ```

2. リポジトリの`azdata`情報を削除します。

    >[!NOTE]
    >今後のインストール`azdata`を予定している場合、この手順は必要ありません。

    ```bash
    sudo rm /etc/apt/sources.list.d/azdata-cli.list
    ```

3. 署名キーを削除します。

    ```bash
    sudo rm /etc/apt/trusted.gpg.d/dpgswdist.v1.asc.gpg
    ```

4. Azdata CLI を使用してインストールされた、不要な依存関係を削除します。

    ```bash
    sudo apt autoremove
    ```

## <a name="next-steps"></a>次の手順

ビッグデータクラスターの詳細について[は[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]](big-data-cluster-overview.md)、「」を参照してください。
