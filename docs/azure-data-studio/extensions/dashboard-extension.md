---
title: ダッシュボード拡張機能を作成する
description: このチュートリアルでは、カスタム機能を Azure Data Studio に追加するダッシュボード拡張機能を作成する方法について説明します。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: how-to
author: yualan
ms.author: alayu
ms.reviewer: alayu, maghan
ms.custom: ''
ms.date: 08/28/2020
ms.openlocfilehash: 835b1fd4687dc5fd86afe300d7c6ed56dc2ed6ea
ms.sourcegitcommit: cc23d8646041336d119b74bf239a6ac305ff3d31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91111646"
---
# <a name="create-an-azure-data-studio-dashboard-extension"></a>Azure Data Studio ダッシュボード拡張機能を作成する

このチュートリアルでは、新しい **Azure Data Studio ダッシュボード拡張機能**を作成する方法について説明します。 この拡張機能は Azure Data Studio 接続ダッシュボードで役立つので、Azure Data Studio の機能をユーザーが見やすいように拡張することができます。

このチュートリアルでは、次の方法を学習します。
> [!div class="checklist"]
> - 拡張機能ジェネレーターをインストールする
> - 拡張機能を作成する
> - 拡張機能のダッシュボードに役立つようにする
> - 拡張機能をテストする
> - 拡張機能をパッケージ化する
> - 拡張機能をマーケットプレースに公開する

## <a name="prerequisites"></a>前提条件

Azure Data Studio は Visual Studio Code と同じフレームワーク上に構築されるため、Azure Data Studio の拡張機能は Visual Studio Code を使用して構築されます。 開始するには、次のコンポーネントが必要です。

- `$PATH` にインストールされ、利用できる [Node.js](https://nodejs.org)。 Node.js には、拡張機能ジェネレーターのインストールに使用される [npm](https://www.npmjs.com/) (Node.js パッケージ マネージャー) が含まれています。
- 拡張機能をデバッグするための [Visual Studio Code](https://code.visualstudio.com)。
- The Azure Data Studio [Debug 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-mssql.sqlops-debug) (任意)。 これにより、パッケージ化して Azure Data Studio にインストールしなくても拡張機能をテストできます。
- 確実に `azuredatastudio` をパスに含めます。 Windows の場合、setup.exe で `Add to Path` オプションを選択します。 Mac または Linux の場合、*[PATH 内に 'azuredatastudio' コマンドをインストールします]* オプションを実行します。

## <a name="install-the-extension-generator"></a>拡張機能ジェネレーターをインストールする

拡張機能作成のプロセスを簡単にするため、Yeoman を利用して[拡張機能ジェネレーター](https://code.visualstudio.com/docs/extensions/yocode)を構築しました。 これをインストールするには、コマンド プロンプトから次を実行します。

```console
`npm install -g yo generator-azuredatastudio`
```

## <a name="create-your-dashboard-extension"></a>ダッシュボード拡張機能を作成する

### <a name="introduction-to-the-dashboard"></a>ダッシュボードの概要

Azure Data Studio 接続ダッシュボードとは、ユーザーの接続を要約すると共に分析情報を提供する強力なツールです。

ダッシュボードには、サーバー全体を要約する 'サーバー ダッシュボード' と、個々のデータベースを要約する 'データベース ダッシュボード' の 2 つのバリエーションがあります。 アクセスするには、Azure Data Studio の Connections viewlet 内でサーバーまたはデータベースのいずれかを右クリックし、 **[管理]** をクリックします。

:::image type="content" source="media/dashboard-extension/dashboard-summary.gif" alt-text="ダッシュボードの概要":::

ダッシュボードに機能を追加するための拡張機能には、次の 3 つの主要なコントリビューション ポイントがあります。

1. **完全なダッシュボード タブ**: 拡張機能のダッシュボードにある独立したタブ。 サーバーまたはデータベースのダッシュボードに追加できます。 ウィジェット、ツールバー、およびナビゲーション セクションでカスタマイズできます。
2. **ホームページの操作**: 接続ツールバーの上部にあるアクション ボタン。
3. **ウィジェット**: ご利用の SQL Server に対して実行されるグラフ。

:::image type="content" source="media/dashboard-extension/dashboard-contrib-points.png" alt-text="コントリビューション ポイント":::

### <a name="run-the-extension-generator"></a>拡張機能ジェネレーターを実行する

拡張機能を作成するには: 

1. 次のコマンドを使用して拡張機能ジェネレーターを起動します。

   `yo azuredatastudio`

2. 拡張機能の種類の一覧から **[新しいダッシュボード]** を選択します。

3. 次に示すようにプロンプトに入力します。 これにより、サーバー ダッシュボードにタブを提供する拡張機能が作成されます。

:::image type="content" source="media/dashboard-extension/dashboard-generator.png" alt-text="拡張機能ジェネレーター":::

多くのプロンプトがあります。そのため、各質問の意味についてもう少し詳しく説明します。

:::image type="content" source="media/dashboard-extension/dashboard-flowchart.png" alt-text="ダッシュボードのフローチャート":::

前の手順を完了すると、新しいフォルダーが作成されます。 Visual Studio Code でフォルダーを開きます。これで独自のダッシュボード拡張機能を作成する準備ができました。

### <a name="run-the-extension"></a>拡張機能を実行する

拡張機能を実行して、ダッシュボード テンプレートが提供する内容を見てみましょう。 実行する前に、**Azure Data Studio Debug 拡張機能**を、確実に Visual Studio Code にインストールします。

VS Code で **F5** を選択します。すると、Azure Data Studio がデバッグ モードで起動し、拡張機能が実行されます。 これで、この既定のテンプレートがダッシュボードにどのように役立つのかを確認できます。

次に、この既定のダッシュボードを変更する方法について説明します。

### <a name="develop-the-dashboard"></a>ダッシュボードを開発する

拡張機能の開発を始める上で最も重要なファイルは、`package.json` です。 これは、ダッシュボードのコントリビューションが登録されているマニフェストファイルです。 `dashboard.tabs`、`dashboard.insights`、`dashboard.containers` の各セクションに注目してください。

ここで、いくつかの変更を試してみます。

- 'bar'、'horizontalBar'、'timeSeries' などの分析情報の種類を試してみます
- ご利用の SQL Server 接続に対して実行する独自のクエリを記述します
- 具体的な分析情報チュートリアルについては、[サンプル分析情報チュートリアル](../tutorial-qds-sql-server.md)または[こちら](../tutorial-table-space-sql-server.md)を参照してください。

## <a name="package-your-extension"></a>拡張機能をパッケージ化する

他者と共有するには、拡張機能を 1 つのファイルにパッケージ化する必要があります。 ファイルは Azure Data Studio 拡張機能マーケットプレースに公開するか、チームやコミュニティで共有できます。 それを行うには、別の npm パッケージをコマンド ラインからインストールする必要があります。

```console
`npm install -g vsce`
```

`README.md` を好みに応じて編集し、次に拡張機能のベース ディレクトリに移動して、`vsce package` を実行します。 必要に応じてリポジトリを拡張機能とリンクすることも、それなしで続行することもできます。 1 つ追加するには、`package.json` ファイルに同様の行を追加します。

```json
"repository": {
    "type": "git",
    "url": "https://github.com/anjalia/my-test-extension.git"
}
```

これらの行が追加されると、my-test-extension-0.0.1.vsix ファイルが作成され、Azure Data Studio にインストールできるようになります。

:::image type="content" source="media/dashboard-extension/install-vsix.png" alt-text="vsix のインストール":::

## <a name="publish-your-extension-to-the-marketplace"></a>拡張機能をマーケットプレースに公開する

Azure Data Studio 拡張機能マーケットプレースはまだ完全には実装されておらず、現行のプロセスは、拡張機能 VSIX をどこか (GitHub リリース ページなど) でホストし、その後、PR を送信し、[この JSON ファイル](https://github.com/Microsoft/azuredatastudio/blob/release/extensions/extensionsGallery.json)を自分の拡張機能情報で更新するというものです。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。
> [!div class="checklist"]
> - 拡張機能ジェネレーターをインストールする
> - 拡張機能を作成する
> - 拡張機能のダッシュボードに役立つようにする
> - 拡張機能をテストする
> - 拡張機能をパッケージ化する
> - 拡張機能をマーケットプレースに公開する

読了後、Azure Data Studio の拡張機能を自分でも作るべく奮起していただけたら幸いです。 Dashboard Insights (SQL Server に対して実行される美麗なグラフ)、さまざまな SQL 固有 API、Visual Studio Code から継承された大量の既存の拡張ポイント セットをサポートしています。

アイデアはあるが、どこから始めたらいいのかわからない場合、チーム宛に問題提起またはツイートしてください ([azuredatastudio](https://twitter.com/azuredatastudio))。

[Visual Studio Code 拡張ガイド](https://code.visualstudio.com/docs/extensions/overview)では、既存の API やパターンがすべて取り上げられているので、いつでも参考にできます。

Azure Data Studio で T-SQL を使用する方法については、T-SQL エディターのチュートリアルを完了してください。

> [!div class="nextstepaction"]
> [Transact-SQL エディターを使用し、データベース オブジェクトを作成する](../tutorial-sql-editor.md)。
