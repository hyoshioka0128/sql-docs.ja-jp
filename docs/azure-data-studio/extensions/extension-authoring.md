---
title: 拡張機能の作成
description: 拡張機能を利用して Azure Data Studio に機能を追加できます。 それを作成し、拡張機能ギャラリーに公開する方法について説明します。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: conceptual
author: markingmyname
ms.author: maghan
ms.reviewer: alayu
ms.custom: ''
ms.date: 08/26/2020
ms.openlocfilehash: 9dbb60dab5d2b1a5dc3a95189a93d6617ed83b6b
ms.sourcegitcommit: cc23d8646041336d119b74bf239a6ac305ff3d31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91123429"
---
# <a name="extend-the-functionality-by-creating-azure-data-studio-extensions"></a>Azure Data Studio 拡張機能を作成することで機能を拡張する

Azure Data Studio の拡張機能により、Azure Data Studio の基本インストールにさらに機能を追加する簡単な方法が提供されます。

拡張機能は、Azure Data Studio チーム (Microsoft) と、サード パーティのコミュニティ (お客様) によって提供されます。

## <a name="author-an-extension"></a>拡張機能を作成する

Azure Data Studio の拡張に関心がある場合は、独自の拡張機能を作成し、拡張機能ギャラリーに公開することができます。

### <a name="writing-an-extension"></a>拡張機能を作成する

#### <a name="prerequisites"></a>前提条件

拡張機能を開発するには、[Node.js](https://nodejs.org/) をインストールし、自分の `$PATH` で利用できるようにする必要があります。 Node.js には、拡張機能ジェネレーターのインストールに使用される npm (Node.js パッケージ マネージャー) が含まれています。

新しい拡張機能を作成するには、Azure Data Studio 拡張機能ジェネレーターを使用できます。 この Yeoman [拡張機能ジェネレーター](https://www.npmjs.com/package/generator-azuredatastudio)は、拡張機能プロジェクトの使用を開始するうえで便利です。 ジェネレーターを起動するには、コマンド プロンプトで次のように入力します。

```console
npm install -g yo generator-azuredatastudio # Install the generator
yo azuredatastudio
```

拡張機能テンプレートの使用を開始する方法に関する詳細なガイドについては、[キーマップ拡張機能](keymap-extension.md)に関する記事を参照してください。この記事では、拡張機能の作成について説明しています。

### <a name="extensibility-references"></a>機能拡張の参照

Azure Data Studio 機能拡張の詳細については、[拡張機能の概要](../extensibility.md)に関するページを参照してください。 また、既存の[サンプル](https://github.com/Microsoft/azuredatastudio/tree/main/samples)で API を使用する方法の例もご覧いただけます。

## <a name="debug-an-extension"></a>拡張機能をデバッグする

Visual Studio Code 拡張機能の [Azure Data Studio Debug](https://github.com/kevcunnane/sqlops-debug) を使用して、新しい拡張機能をデバッグできます。

手順:

1. [Visual Studio Code](https://code.visualstudio.com/) で拡張機能を開きます。
2. Azure Data Studio Debug 拡張機能をインストールします。
3. **F5** を押すか、[デバッグ] アイコンを選択し、 **[開始]** を選択します。
4. Azure Data Studio の新しいインスタンスが特別なモード (拡張機能開発ホスト) で起動し、この新しいインスタンスによってご利用の拡張機能が認識されています。

## <a name="create-an-extension-package"></a>拡張機能パッケージを作成する

拡張機能を作成した後は、Azure Data Studio にインストールされる VSIX パッケージを作成する必要があります。 [vscode-vsce](https://github.com/Microsoft/vscode-vsce) (Visual Studio Code Extensions) を使用して、VSIX パッケージを作成できます。

```console
npm install -g vsce
cd myExtensionName
vsce package
# The myExtensionName.vsix file has now been generated
```

VSIX パッケージを使用すると、`.vsix` ファイルを共有し、コマンド パレットからコマンド **Extensions: Install From VSIX File** を使用して拡張機能を Azure Data Studio にインストールすることで、その拡張機能をローカルおよびプライベートに共有することができます。

## <a name="publish-an-extension"></a>拡張機能を公開する

Azure Data Studio に新しい拡張機能を公開するには、次の操作を行います。

1. 拡張機能を https://github.com/Microsoft/azuredatastudio/blob/release/extensions/extensionsGallery.json に追加する
2. 現在、サード パーティの拡張機能をホストすることはサポートされていないため、拡張機能をダウンロードする代わりに、Azure Data Studio にはダウンロード ページを参照するオプションがあります。 拡張機能のダウンロード ページを設定するには、アセット "Microsoft.AzureDataStudio.DownloadPage" の値を設定します。
3. リリース/拡張機能ブランチに対して PR を作成します。
4. 校閲リクエストをチームに送信します。

拡張機能が確認され、拡張機能ギャラリーに追加されます。

### <a name="publishing-extension-updates"></a>拡張機能の更新プログラムの公開

更新プログラムを公開するプロセスは、拡張機能の公開と同様です。 バージョンが package.json で更新されていることを確認します。

## <a name="next-steps"></a>次の手順

開始方法について段階的な指示が必要な場合、次のいずれかの拡張機能作成チュートリアルを参照してください。

- [キーマップ拡張機能チュートリアル](keymap-extension.md)
- [ダッシュボード拡張機能チュートリアル](dashboard-extension.md)
- [ノートブック拡張機能チュートリアル](notebook-extension.md)
- [Jupyter ブック拡張機能チュートリアル](jupyter-book-extension.md)
- [ウィザード拡張機能チュートリアル](wizard-extension.md)