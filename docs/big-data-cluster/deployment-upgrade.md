---
title: 新しいリリースにアップグレードする
titleSuffix: SQL Server big data clusters
description: 新しいリリースにアップグレード[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)] (プレビュー) する方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: e3fa24998e4c48dad568f926dca2bba4359fe691
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70155337"
---
# <a name="how-to-upgrade-includebig-data-clusters-2019includesssbigdataclusters-ss-novermd"></a>アップグレードする方法[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

この記事では、SQL Server ビッグ データ クラスターを新しいリリースにアップグレードする方法に関するガイダンスを提供します。 この記事の手順は、具体的にはプレビュー リリース間でアップグレードする方法に適用されます。

## <a name="backup-and-delete-the-old-cluster"></a>以前のクラスターをバックアップして削除する

現時点では、ビッグ データ クラスターを新しいリリースにアップグレードする唯一の方法は、クラスターを手動で削除して再作成することです。 各リリースには、以前の`azdata`バージョンと互換性のないの固有のバージョンがあります。 また、古いクラスターで新しいノードにイメージをダウンロードする必要があった場合は、最新のイメージがクラスター上の古いイメージと互換性がない可能性があります。 最新のリリースにアップグレードするには、次の手順に従います。

1. 以前のクラスターを削除する前に、SQL Server マスター インスタンス上と HDFS 上のデータをバックアップします。 SQL Server マスター インスタンスの場合は、[SQL Server のバックアップと復元](data-ingestion-restore-database.md)を使用できます。 HDFS の場合、[を使用して`curl`データをコピーでき](data-ingestion-curl.md)ます。

1. `azdata delete cluster` コマンドを使用して、古いクラスターを削除します。

   ```bash
    azdata bdc delete --name <old-cluster-name>
   ```

   > [!Important]
   > クラスターに一致する`azdata`バージョンのを使用します。 新しいバージョンの`azdata`では、古いクラスターを削除しないでください。

1. CTP 3.2 より前で`azdata`は、 `mssqlctl`が呼び出されました。 以前のリリースの`mssqlctl`または`azdata`がインストールされている場合は、の`azdata`最新バージョンをインストールする前に、まずをアンインストールすることが重要です。

   CTP 2.3 以降の場合は、次のコマンドを実行します。 コマンド`ctp3.1`内のを、アンインストールするの`mssqlctl`バージョンに置き換えます。 バージョンが CTP 3.1 より前の場合は、バージョン番号の前にダッシュ (例: `ctp-2.5`) を追加します。

   ```powershell
   pip3 uninstall -r https://aka.ms/azdata
   ```

1. の`azdata`最新バージョンをインストールします。 次のコマンドは`azdata` 、リリース候補としてインストールされます。

   **Windows:**

   ```powershell
   pip3 install -r https://aka.ms/azdata
   ```

   **Linux:**

   ```bash
   pip3 install -r https://aka.ms/azdata --user
   ```

   > [!IMPORTANT]
   > リリースごとに、パス`azdata`が変更されます。 以前にまたは`azdata` `mssqlctl`をインストールしていた場合でも、新しいクラスターを作成する前に、最新のパスからを再インストールする必要があります。

## <a id="azdataversion"></a> azdata のバージョンを確認する

新しいビッグデータクラスターをデプロイする前に、最新バージョン`azdata`のを`--version`パラメーターと共に使用していることを確認します。

```bash
azdata --version
```

## <a name="install-the-new-release"></a>新しいリリースをインストールする

前のビッグデータクラスターを削除し、最新`azdata`のをインストールしたら、現在の展開手順に従って、新しいビッグデータクラスターをデプロイします。 詳細については、「 [How [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] to deploy on Kubernetes](deployment-guidance.md)」を参照してください。 次に、必要なデータベースまたはファイルを復元します。

## <a name="next-steps"></a>次の手順

ビッグデータクラスターの詳細について[は[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] ](big-data-cluster-overview.md)、「」を参照してください。
