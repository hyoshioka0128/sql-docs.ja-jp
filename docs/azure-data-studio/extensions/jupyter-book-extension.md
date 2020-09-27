---
title: Jupyter Book 拡張機能を作成する
description: 拡張機能ジェネレーターを使用して Jupyter Book を拡張機能にパッケージ化する方法について説明します
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: how-to
author: yualan
ms.author: alayu
ms.reviewer: alayu, maghan
ms.custom: ''
ms.date: 08/28/2020
ms.openlocfilehash: a781affee2db40dbd7636c25712ca622c881650e
ms.sourcegitcommit: 8f062015c2a033f5a0d805ee4adabbe15e7c8f94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91226839"
---
# <a name="create-a-jupyter-book-extension"></a>Jupyter Book 拡張機能を作成する

このチュートリアルでは、新しい Jupyter Book Azure Data Studio 拡張機能を作成する方法について説明します。 この拡張機能では、Azure Data Studio で開いて実行できる Jupyter Book のサンプルが送信されます。 

このチュートリアルでは、次の方法を学習します。
> [!div class="checklist"]
> - 拡張機能プロジェクトを作成する
> - 拡張機能ジェネレーターをインストールする
> - Jupyter Book 拡張機能を作成する
> - 拡張機能を実行する
> - 拡張機能をパッケージ化する
> - 拡張機能をマーケットプレースに公開する

## <a name="apis-used"></a>使用される API

- `bookTreeView.openBook`

### <a name="extension-use-cases"></a>拡張機能のユース ケース

Jupyter Book 拡張機能を作成する理由はいくつかあります。

- 編成されセクションに分けられた対話型ドキュメントを共有する
- 完全なブックを共有する (電子書籍に似ているが、Azure Data Studio を通じて配布される)
- Jupyter Book の更新プログラムをバージョン管理して追跡する

Jupyter Book 拡張機能とノートブック拡張機能の主な違いとして、Jupyter Book では編成が可能です。 ブックでは、数十のノートブックを異なるチャプターに分割できますが、ノートブック拡張機能は、少数の個別ノートブックを送信することを目的としています。

## <a name="prerequisites"></a>前提条件

Azure Data Studio は Visual Studio Code と同じフレームワーク上に構築されるため、Azure Data Studio の拡張機能は Visual Studio Code を使用して構築されます。 開始するには、次のコンポーネントが必要です。

- `$PATH` にインストールされ、利用できる [Node.js](https://nodejs.org)。 Node.js には、拡張機能ジェネレーターのインストールに使用される [npm](https://www.npmjs.com/) (Node.js パッケージ マネージャー) が含まれています。
- 拡張機能に変更を加えたり、拡張機能をデバッグしたりするための [Visual Studio Code](https://code.visualstudio.com)。
- 確実に `azuredatastudio` をパスに含めます。 Windows の場合、setup.exe で `Add to Path` オプションを選択します。 Mac または Linux の場合、*[PATH 内に 'azuredatastudio' コマンドをインストールします]* オプションを実行します。

## <a name="install-the-extension-generator"></a>拡張機能ジェネレーターをインストールする

拡張機能作成のプロセスを簡単にするため、Yeoman を利用して[拡張機能ジェネレーター](https://www.npmjs.com/package/generator-azuredatastudio)を構築しました。 これをインストールするには、コマンド プロンプトから次のコマンドを実行します。

```console
`npm install -g yo generator-azuredatastudio`
```

## <a name="create-your-extension"></a>拡張機能を作成する

拡張機能を作成するには: 

1. 次のコマンドを使用して拡張機能ジェネレーターを起動します。

   `yo azuredatastudio`

2. 拡張機能の種類の一覧から **[New Jupyter Book]\(新しい Jupyter Book\)** を選択します。

   :::image type="content" source="media/jupyter-book-extension/jupyter-book-extension-generator.png" alt-text="拡張機能ジェネレーター":::

3. 手順に従って、拡張機能名 (このチュートリアルでは **Test Book** を使用)、公開元名 (このチュートリアルでは **Microsoft** を使用) を入力し、説明を追加します。

既存の Jupyter Book を提供するか、提供されているサンプルを使用するか、または新しい Jupyter Book を作成するかを選択します。 以下に 3 つのすべてのオプションを示します。

### <a name="providing-an-existing-book"></a>既存のブックを提供する

既に作成してあるブックを送信する場合は、ブックの内容が含まれているフォルダーへの絶対ファイル パスを指定します。 その後、拡張機能について学習し、送信する準備が整います。

:::image type="content" source="media/jupyter-book-extension/jupyter-book-existing-book.png" alt-text="既存のブック":::

### <a name="using-the-sample-book"></a>サンプル ブックの使用

既存のブックまたはノートブックがない場合は、提供されているサンプルをジェネレーターで使用できます。

:::image type="content" source="media/jupyter-book-extension/jupyter-book-sample-path.png" alt-text="Jupyter Book のサンプル":::

このサンプル ブックは、単純な Jupyter Book の外観を示しています。 Jupyter Book のカスタマイズの詳細については、既存のノートブックを使用した新しいブックの作成に関する次のセクションを参照してください。

### <a name="creating-a-new-book"></a>新しいブックの作成

Jupyter Book にパッケージ化するノートブックがある場合は、パッケージ化を行うことができます。 ジェネレーターによって、ブック内にチャプターを設定するかどうかが確認され、設定する場合はその数とタイトルが求められます。 選択プロセスは次のように表示されます。 スペース バーを使用して、各チャプターに配置するノートブックを選択します。

:::image type="content" source="media/jupyter-book-extension/jupyter-book-create-book.png" alt-text="Jupyter Book を作成する":::

前の手順を完了すると、新しい Jupyter Book を含む新しいフォルダーが作成されます。 Visual Studio Code でフォルダーを開くと、Jupyter Book 拡張機能を送信する準備が整います。

## <a name="understanding-your-extension"></a>拡張機能の概要

この時点で、プロジェクトは次のように表示されます。

   :::image type="content" source="media/jupyter-book-extension/jupyter-book-file-structure-generator.png" alt-text="拡張機能ファイルの構造":::

`vsc-extension-quickstart.md` には、重要なファイルのリファレンスが用意されています。 `README.md` によって、新しい拡張機能のドキュメントが提供されます。 `package.json`、`jupyter-book.ts`、`content`、`toc.yml` のファイルに注目してください。 `content` フォルダーには、すべてのノートブック ファイルまたはマークダウン ファイルが格納されています。 `toc.yml` によって、Jupyter Book が構造化されます。これは、拡張機能ジェネレーターを使用してカスタム Jupyter Book を作成することを選択した場合に自動生成されます。

ジェネレーターを使用してブックを作成し、ブック内にチャプターを設定することを選択した場合、フォルダー構造は少し異なります。 `content` フォルダーには、マークダウン ファイルと Jupyter Notebook ファイルの代わりに、チャプターに対して選択したタイトルに対応するサブフォルダーが含まれています。 

公開しないファイルまたはフォルダーがある場合は、その名前を `.vscodeignore` ファイルに含めることができます。

`jupyter-book.ts` を確認して、新しく作成した拡張機能の動作を見てみましょう。

```javascript
// This function is called when you run the command `Launch Book: Test Book` from
// command palette in Azure Data Studio. If you would like any additional functionality
// to occur when you launch the book, add to the activate function.
export function activate(context: vscode.ExtensionContext) {
    context.subscriptions.push(vscode.commands.registerCommand('launchBook.test-book', () => {
        processNotebooks();
    }));

    // Add other code here if you want to register another command
}
```

`activate` 関数は、拡張機能の主要なアクションです。 登録するコマンドは、`launchBook.test-book` コマンドと同様に `activate` 関数内に表示される必要があります。 `processNotebooks` 関数の内部で、Jupyter Book が格納されている拡張機能フォルダーを見つけ、拡張機能フォルダーをパラメーターとして使用して `bookTreeView.openBook` 呼び出します。 

また、`package.json` ファイルは、拡張機能のコマンドを登録するときに重要な役割を果たします。

```json
"activationEvents": [
        "onCommand:launchBook.test-book"
    ],
    "main": "./out/notebook.js",
    "contributes": {
        "commands": [
            {
                "command": "launchBook.test-book",
                "title": "Launch Book: Test Book"
            }
        ]
    }
```

アクティブ化イベント `onCommand` によって、コマンドを呼び出すときに登録した関数がトリガーされます。 他にも追加のカスタマイズが可能なアクティブ化イベントがいくつかあります。 詳細については、「[アクティブ化イベント](https://code.visualstudio.com/api/references/activation-events)」を参照してください。

## <a name="package-your-extension"></a>拡張機能をパッケージ化する

他者と共有するには、拡張機能を 1 つのファイルにパッケージ化する必要があります。 ファイルは Azure Data Studio 拡張機能マーケットプレースに公開するか、チームやコミュニティで共有できます。 それを行うには、別の npm パッケージをコマンド ラインからインストールする必要があります。

```console
`npm install -g vsce`
```

`README.md` を好みに応じて編集し、次に拡張機能のベース ディレクトリに移動して、`vsce package` を実行します。 必要に応じてリポジトリを拡張機能とリンクすることも、それなしで続行することもできます。 1 つ追加するには、`package.json` ファイルに同様の行を追加します。

```json
"repository": {
    "type": "git",
    "url": "https://github.com/laurajjiang/testbook.git"
}
```

これらの行が追加されると、test-book-0.0.1.vsix ファイルが作成され、Azure Data Studio にインストールできるようになります。

## <a name="run-your-extension"></a>拡張機能を実行する

拡張機能を実行してテストするには、Azure Data Studio を開き、コマンド パレット (`Ctrl + Shift + P`) を開きます。 コマンド **[Extensions: Install from VSIX]\(拡張機能: VSIX からのインストール\)** を見つけて、新しい拡張機能が格納されているフォルダーに移動します。 この時点で、Azure Data Studio の拡張機能パネルに表示されます。

   :::image type="content" source="media/jupyter-book-extension/install-vsix.png" alt-text="VSIX のインストール":::

コマンド パレットをもう一度開き、登録したコマンド **[Launch Book: Test Notebook]\(ブックの起動: ノートブックのテスト\)** を見つけます。 実行すると、拡張機能とともにパッケージ化した Jupyter Book が開きます。

   :::image type="content" source="media/jupyter-book-extension/jupyter-book-launch-ads.png" alt-text="Notebook コマンド":::

おめでとうございます! これで、最初の Jupyter Book 拡張機能がビルドされ、送信できるようになりました。 Jupyter Book の詳細については、「[Books with Jupyter](https://jupyterbook.org/intro.html)」(Jupyter で作る本) を参照してください。

## <a name="publish-your-extension-to-the-marketplace"></a>拡張機能をマーケットプレースに公開する

Azure Data Studio 拡張機能マーケットプレースはまだ完全には実装されていません。 公開するには、拡張機能の VSIX をいずれかの場所 (たとえば、GitHub リリース ページ) でホストし、[この JSON ファイル](https://github.com/Microsoft/azuredatastudio/blob/release/extensions/extensionsGallery.json)を拡張機能の情報で更新する PR を送信します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。
> [!div class="checklist"]
> - 拡張機能プロジェクトを作成する
> - 拡張機能ジェネレーターをインストールする
> - Jupyter Book 拡張機能を作成する
> - 拡張機能をパッケージ化する
> - 拡張機能をマーケットプレースに公開する

この記事を読むと、Azure Data Studio コミュニティに公開する Jupyter Book に関するアイデアが得られます。 

アイデアはあるが、どのように始めたらいいのかわからない場合は、チーム宛に問題提起またはツイートしてください ([azuredatastudio](https://twitter.com/azuredatastudio))。

[Visual Studio Code 拡張ガイド](https://code.visualstudio.com/docs/extensions/overview)では、既存の API やパターンがすべて取り上げられているので、いつでも参考にできます。
