---
title: zypper を使用した azdata のインストール
titleSuffix: ''
description: zypper で azdata ツールをインストールする方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 01/07/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 8fdde8b6229bd2fc98005025e17efe97104d2fc1
ms.sourcegitcommit: d56f1eca807c55cf606a6316f3872585f014fec1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90914886"
---
# <a name="install-azdata-with-zypper"></a>zypper での `azdata` のインストール

`zypper` を使用した Linux ディストリビューションには、`azdata-cli` 用のパッケージが用意されています。 この CLI パッケージは、`zyper` を使用する Linux バージョンでテストされています。

- openSUSE 42.2 (leap) +
- SLES 12 SP 2 +

[!INCLUDE [azdata-package-installation-remove-pip-install](../../includes/azdata-package-installation-remove-pip-install.md)]

## <a name="install-with-zypper"></a>zypper でのインストール
>[!IMPORTANT]
>`azdata-cli` の RPM パッケージは、python3 のパッケージによって異なります。 これは、お使いのシステムでは *Python 3.6.x* の要件より前から存在する Python バージョンである可能性があります。 これが問題となる場合、代わりの python3 パッケージを見つけるか、[`pip`](../install/deploy-install-azdata-pip.md) を使用する手動でのインストール手順に従ってください。

1. `azdata-cli` のインストールに必要な依存関係をインストールします

   ```bash
   sudo zypper install -y curl
   ```

1. Microsoft リポジトリ キーをインポートします

   ```bash
   sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
   ```

1. ローカル リポジトリ情報を作成します

   ```bash
   sudo zypper addrepo -fc https://packages.microsoft.com/config/sles/12/prod.repo
   ```

1. インストール

   ```bash
   sudo zypper install --from packages-microsoft-com-mssql-server-2019 -y azdata-cli
   ```

## <a name="verify-install"></a>インストールの確認

   ```bash
   azdata
   azdata --version
   ```

## <a name="update"></a>更新

`zypper update` コマンドで `azdata-cli` を更新します。

   ```bash
   sudo zypper refresh
   sudo zypper update azdata-cli
   ```

## <a name="uninstall"></a>アンインストール

システムからのパッケージを削除します

```bash
   sudo zypper removerepo azdata-cli
```

## <a name="next-steps"></a>次のステップ

ビッグ データ クラスターの詳細については、「[[!INCLUDE[big-data-clusters-2019](../../includes/ssbigdataclusters-ver15.md)]とは](../../big-data-cluster/big-data-cluster-overview.md)」を参照してください。

[Azure Arc 対応データ サービス](/azure/azure-arc/data/)で azdata を使用する