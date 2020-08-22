---
title: ソース管理
description: Azure Data Studio を使用すると、ソース コントロール管理 (SCM) 用に Git をサポートできます。 既存の Git リポジトリを開く方法と、新しい Git リポジトリを初期化する方法について説明します。
ms.prod: azure-data-studio
ms.technology: ''
ms.topic: conceptual
author: markingmyname
ms.author: maghan
ms.reviewer: alayu, sstein
ms.custom: seodec18
ms.date: 09/24/2018
ms.openlocfilehash: c8b3ad59ac518eebefa9fbb073544fdb7791a419
ms.sourcegitcommit: 7035d9471876c70b99c58bf9b46af5cce6e9c66c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87522506"
---
# <a name="source-control-in-azure-data-studio"></a>Azure Data Studio のソース管理

Azure Data Studio は、バージョン管理/ソース管理用の Git をサポートします。

## <a name="git-support-in-azure-data-studio"></a>Azure Data Studio の Git サポート

Azure Data Studio には、Git ソース管理マネージャー (SCM) が付属していますが、これらの機能を使用できるようにする前に [Git (バージョン 2.0.0 以降) をインストールする](https://git-scm.com/download)必要があります。 

## <a name="open-an-existing-git-repository"></a>既存の Git リポジトリを開く

1. **[ファイル]** メニューで、 **[フォルダーを開く]** を選択します。
2. Git で追跡されるファイルが含まれているフォルダーを参照して、 **[フォルダーの選択]** をクリックします。 ここで、ローカル リポジトリ内のサブフォルダーを選択できます。

## <a name="initialize-a-new-git-repository"></a>新しい Git リポジトリを初期化する

1. **[ソース管理]** を選択して、Git アイコンを選択します。

   ![ソース管理 Git アイコン](media/source-control/source-control.png)

1. Git リポジトリとして初期化するフォルダーへのパスを入力して、**Enter** キーを押します。

   ![Git リポジトリを初期化する](media/source-control/initialize-git-repository.png)

## <a name="working-with-git-repositories"></a>Git リポジトリの操作

Azure Data Studio では、Git 実装が VS Code から継承されていますが、追加の SCM プロバイダーは現在サポートされていません。 リポジトリを開くか、初期化した後に Git を操作する方法の詳細については、[VS Code での Git のサポート](https://code.visualstudio.com/docs/editor/versioncontrol#_git-support)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [Git のドキュメント](https://git-scm.com/documentation)
