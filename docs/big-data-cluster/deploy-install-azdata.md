---
title: Pip を使用して azdata をインストールする
titleSuffix: SQL Server big data clusters
description: Pip を使用して、(プレビュー) をインストールし[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]て管理するための azdata ツールをインストールする方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: a72e2ab39a17adef6c330f1ae17dcdc8a5dd8ddf
ms.sourcegitcommit: 71fac5fee00e0eca57e555f44274dd7e08d47e1e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70160734"
---
# <a name="install-azdata-for-includebig-data-clusters-2019includesssbigdataclusters-ss-novermd-using-pip"></a>を`azdata`使用[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]するためのインストール`pip`

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

この記事では、を使用`azdata`して`pip`Windows または Linux にリリース候補用のツールをインストールする方法について説明します。

## <a id="prerequisites"></a> 前提条件

`azdata`は Python で記述されたコマンドラインユーティリティで、クラスター管理者は REST Api を使用してビッグデータクラスターをブートストラップおよび管理できます。 最低限必要な Python バージョンは v3.5 です。 また、ツールを`pip`ダウンロードしてインストール`azdata`するためにも使用する必要があります。 以下の手順は、Windows と Ubuntu の例です。 他のプラットフォームに Python をインストールする方法については、[Python のドキュメント](https://wiki.python.org/moin/BeginnersGuide/Download)を参照してください。
さらに、最新バージョンの *requests* Python パッケージをインストールして更新する必要もあります。
```bash
pip3 install -U requests
```

> [!IMPORTANT]
> 新しいバージョンのビッグデータクラスターをインストールする場合は、新しいリリースをアップグレード`azdata`してインストールする*前に*、データをバックアップし、古いクラスターを削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

## <a id="windows"></a>Windows `azdata`のインストール

1. Windows クライアントで、[https://www.python.org/downloads/](https://www.python.org/downloads/) から必要な Python パッケージをダウンロードします。 Python 3.5.3 以降では、Python をインストールするときに pip3 もインストールされます。 

   > [!TIP] 
   > Python3 をインストールするときに、**PATH** に Python を追加することを選択します。 そうしない場合は、後で pip3 が配置されている場所を見つけて、**PATH** に手動で追加することができます。

1. 新しい Windows PowerShell セッションを開き、Python で最新のパスを取得します。

1. 以前のリリースのツールがインストールされている場合 (CTP 3.2 より前では、このツールは**mssqlctl**と呼ばれていました)、の`azdata`最新バージョンをインストールする前に、最初にアンインストールすることが重要です。 次のコマンドは、 **mssqlctl**の CTP 3.1 バージョンを削除します。

   ```powershell
   pip3 uninstall -r https://private-repo.microsoft.com/python/ctp3.1/mssqlctl/requirements.txt
   ```

1. 以前のリリースの`azdata`がインストールされている場合は、最新バージョンをインストールする前に最初にアンインストールすることが重要です。

   CTP 3.2 の場合は、次のコマンドを実行します。

   ```powershell
   pip3 uninstall -r https://azdatacli.blob.core.windows.net/python/azdata/2019-ctp3.2/requirements.txt
   ```

1. 次`azdata`のコマンドを使用してをインストールします。

   ```powershell
   pip3 install -r https://aka.ms/azdata
   ```

## <a id="linux"></a>Linux `azdata`のインストール

Linux では、Python 3.5 をインストールしてから、pip をアップグレードする必要があります。 次の例は、Ubuntu で動作するコマンドを示しています。 その他の Linux プラットフォームについては、[Python のドキュメント](https://wiki.python.org/moin/BeginnersGuide/Download)を参照してください。

1. 必要な Python パッケージをインストールします。

   ```bash
   sudo apt-get update && \
   sudo apt-get install -y python3 && \
   sudo apt-get install -y python3-pip
   ```

1. pip3 をアップグレードする:

   ```bash
   sudo -H pip3 install --upgrade pip
   ```

1. 以前のリリースのツールがインストールされている場合 (CTP 3.2 より前では、このツールは**mssqlctl**と呼ばれていました)、の`azdata`最新バージョンをインストールする前に、最初にアンインストールすることが重要です。 次のコマンドは、 **mssqlctl**の CTP 3.1 バージョンを削除します。

   ```bash
   pip3 uninstall -r https://private-repo.microsoft.com/python/ctp3.1/mssqlctl/requirements.txt
   ```

1. 以前のリリースの`azdata`がインストールされている場合は、最新バージョンをインストールする前に最初にアンインストールすることが重要です。

   CTP 3.2 の場合は、次のコマンドを実行します。

   ```bash
   pip3 uninstall -r https://azdatacli.blob.core.windows.net/python/azdata/2019-ctp3.2/requirements.txt
   ```

1. 次`azdata`のコマンドを使用してをインストールします。

   ```bash
   pip3 install -r https://aka.ms/azdata --user
   ```

   > [!NOTE]
   > スイッチ`--user`が Python `azdata`ユーザーインストールディレクトリにインストールされます。 通常、Linux 上ではこれは `~/.local/bin` です。 このディレクトリをパスに追加するか、ユーザー インストール ディレクトリに移動して、そこから `./azdata` を実行します。

## <a name="next-steps"></a>次の手順

ビッグデータクラスターの詳細について[は[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]](big-data-cluster-overview.md)、「」を参照してください。
