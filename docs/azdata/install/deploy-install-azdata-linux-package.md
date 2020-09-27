---
title: インストーラーを使用して azdata を Linux にインストールする
titleSuffix: ''
description: インストーラーで azdata ツールをインストールする方法について説明します (Linux)。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 01/07/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 2dc1c3d58ee5f7b6ea032a2e41f7c18431229881
ms.sourcegitcommit: d56f1eca807c55cf606a6316f3872585f014fec1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90914963"
---
# <a name="install-azdata-with-apt"></a>apt での `azdata` のインストール

[!INCLUDE[SQL Server 2019](../../includes/applies-to-version/azdata.md)]

この記事では、Linux に `azdata` をインストールする方法について説明します。 これらのパッケージ マネージャーが使用可能になる前は、`azdata` をインストールするために `pip` が必要でした。

[!INCLUDE [azdata-package-installation-remove-pip-install](../../includes/azdata-package-installation-remove-pip-install.md)]

## <a name="install-azdata-for-linux"></a><a id="linux"></a>Linux 用の `azdata` をインストールする

Ubuntu で使用可能な `azdata` インストール パッケージは `apt` を使用して入手できます。

### <a name="install-azdata-with-apt-ubuntu"></a><a id="azdata-apt"></a>apt (Ubuntu) を使用して `azdata` をインストールする

>[!NOTE]
>`azdata` パッケージでは、システム Python は使用されず、独自の Python インタープリターがインストールされます。

1. インストール プロセスに必要なパッケージを取得します。

    ```bash
    sudo apt-get update
    sudo apt-get install gnupg ca-certificates curl wget software-properties-common apt-transport-https lsb-release -y
    ```

2. 署名キーをダウンロードし、インストールします。

    ```bash
    curl -sL https://packages.microsoft.com/keys/microsoft.asc |
    gpg --dearmor |
    sudo tee /etc/apt/trusted.gpg.d/microsoft.asc.gpg > /dev/null
    ```

3. `azdata` リポジトリ情報を追加します。

   Ubuntu 16.04 クライアントには次を実行します。
    ```bash
    sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/16.04/prod.list)"
    ```

   Ubuntu 18.04 クライアントには次を実行します。
    ```bash
    sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/18.04/prod.list)"
    ```

4. リポジトリ情報を更新し、`azdata` をインストールします。

    ```bash
    sudo apt-get update
    sudo apt-get install -y azdata-cli
    ```

5. インストールを確認します。

    ```bash
    azdata --version
    ```

### <a name="update"></a>更新

`azdata` のみをアップグレードします。

```bash
sudo apt-get update && sudo apt-get install --only-upgrade -y azdata-cli
```

### <a name="uninstall"></a>アンインストール

1. apt-get remove を使用してアンインストールします。

    ```bash
    sudo apt-get remove -y azdata-cli
    ```

2. `azdata` リポジトリ情報を削除します。

    >[!NOTE]
    >今後 `azdata` をインストールする予定がある場合、この手順は必要ありません

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

## <a name="next-steps"></a>次のステップ

ビッグ データ クラスターの詳細については、「[[!INCLUDE[big-data-clusters-2019](../../includes/ssbigdataclusters-ver15.md)]とは](../../big-data-cluster/big-data-cluster-overview.md)」を参照してください。

[Azure Arc 対応データ サービス](/azure/azure-arc/data/)で azdata を使用する