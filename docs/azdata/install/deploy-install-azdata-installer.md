---
title: Windows インストーラーで azdata をインストールする
titleSuffix: ''
description: インストーラーで azdata ツールをインストールする方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 11/04/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: a33e43386c44ec2ab60166ef57a502fc592c8d73
ms.sourcegitcommit: d56f1eca807c55cf606a6316f3872585f014fec1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90914958"
---
# <a name="install-azdata-to-manage-big-data-clusters-2019-with-windows-installer"></a>Windows インストーラーで [!INCLUDE[big-data-clusters-2019](../../includes/ssbigdataclusters-ss-nover.md)]を管理するための `azdata` をインストールする

[!INCLUDE[SQL Server 2019](../../includes/applies-to-version/azdata.md)]

この記事では、Windows に `azdata` をインストールする方法について説明します。 Windows インストールを利用できるようになる前は、`azdata` をインストールするには `pip` が必要でした。

>Linux (Ubuntu) の場合は、[インストーラーでの `azdata` のインストール](./deploy-install-azdata-linux-package.md)に関する記事を参照してください。

現時点では、他のオペレーティング システムまたはディストリビューションに `azdata` をインストールするためのパッケージ マネージャーはありません。 これらのプラットフォームについては、[パッケージ マネージャーを使用せずに `azdata` をインストールする方法](./deploy-install-azdata.md)に関する記事を参照してください。

## <a name="install-azdata-with-the-microsoft-windows-installer"></a>Microsoft Windows インストーラーで `azdata` をインストールする

Microsoft Windows インストーラーで `azdata` をインストールするには、次のようにします。

1. `pip` を使用してインストールされた `azdata` がある場合は削除します。 Windows インストーラーを使用して `azdata` がインストールされている場合は、次のステップに進みます。
1. Windows インストーラーを使用して `azdata` をインストールします。

### <a name="uninstall-if-previous-installation-done-with-pip"></a>以前に `pip` で行われたインストールがある場合はアンインストールする

以前のリリースの `azdata` がインストールされている場合は、まずそれをアンインストールしてから最新バージョンをインストールすることが重要です。

   `azdata` のリリース候補バージョンを削除するには、次のコマンドを実行します。

   ```bash
   pip3 uninstall -r https://azdatacli.blob.core.windows.net/python/azdata/2019-rc1/requirements.txt
   ```

削除されたら、[Windows に `azdata` をインストールします](#install-azdata-windows)。

>[!NOTE]
>以前のインストールが MSI を使用して行われた場合、MSI インストーラーを使用する前に、現在のバージョンをアンインストールする必要はありません。

### <a name="install-with-windows-installer"></a><a id="install-azdata-windows"></a>Windows インストーラーでインストールする

Windows 上の `azdata` を、Windows インストーラーを使用してインストールまたは更新します。

[`azdata` Windows インストーラーをダウンロードします](https://aka.ms/azdata-msi)。

インストーラーでコンピューターを変更できるかどうか確認するメッセージが表示されたら、[`Yes`] をクリックします。

### <a name="uninstall-azdata-with-windows-installer"></a>Windows インストーラーで `azdata` をアンインストールする

Windows インストーラーで `azdata` をアンインストールするには、該当するオペレーティング システムの指示に従います。

| プラットフォーム      | Instructions                                           |
| ------------- |--------------------------------------------------------|
| Windows 10| [スタート] > [設定] > [アプリ]                                |
| Windows 8     | [スタート] > [コントロール パネル] > [プログラム] > [プログラムのアンインストール] |

アンインストールするプログラムは `Azdata CLI` という名前です。 このアプリケーションを選択し、`Uninstall` ボタンをクリックします。

## <a name="next-steps"></a>次のステップ

ビッグ データ クラスターの詳細については、「[[!INCLUDE[big-data-clusters-2019](../../includes/ssbigdataclusters-ver15.md)]とは](../../big-data-cluster/big-data-cluster-overview.md)」を参照してください。

[Azure Arc 対応データ サービス](/azure/azure-arc/data/)で azdata を使用する
