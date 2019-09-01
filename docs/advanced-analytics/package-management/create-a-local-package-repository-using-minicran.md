---
title: MiniCRAN を使用してローカル R パッケージリポジトリを作成する
description: MiniCRAN パッケージを使用して R パッケージをオフラインでインストールし、パッケージと依存関係のローカルリポジトリを作成する方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 08/15/2019
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 68f86d673b944a029c7bd0f74c9594692bd579f4
ms.sourcegitcommit: 00350f6ffb73c2c0d99beeded61c5b9baa63d171
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196335"
---
# <a name="create-a-local-r-package-repository-using-minicran"></a>MiniCRAN を使用してローカル R パッケージリポジトリを作成する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、 [miniCRAN](https://cran.r-project.org/web/packages/miniCRAN/index.html)を使用して R パッケージをオフラインでインストールし、パッケージと依存関係のローカルリポジトリを作成する方法について説明します。 **miniCRAN**は、パッケージと依存関係を識別して、オフラインの R パッケージインストール用に他のコンピューターにコピーする1つのフォルダーにダウンロードします。

1つまたは複数のパッケージを指定できます。 **miniCRAN**は、これらのパッケージの依存関係ツリーを再帰的に読み取ります。 次に、CRAN または類似のリポジトリから、一覧表示されたパッケージとその依存関係のみをダウンロードします。

この処理が完了すると、 **miniCRAN**は、選択したパッケージとすべての必要な依存関係から構成される、内部的に一貫性のあるリポジトリを作成します。 このローカルリポジトリをサーバーに移動し、インターネットに接続せずにパッケージのインストールを続行できます。

経験豊富な R ユーザーは、多くの場合、ダウンロードしたパッケージの説明ファイルで依存パッケージの一覧を検索します。 ただし、**インポート**に記載されているパッケージには、第2レベルの依存関係がある場合があります。 このため、必要なパッケージの完全なコレクションをアセンブルするために**miniCRAN**を使用することをお勧めします。

## <a name="why-create-a-local-repository"></a>ローカルリポジトリを作成する理由

ローカルパッケージリポジトリを作成する目的は、サーバー管理者または組織内の他のユーザーがサーバーに新しい R パッケージをインストールするために使用できる1つの場所を提供することです (特に、インターネットにアクセスできない)。 リポジトリを作成した後は、新しいパッケージを追加するか、既存のパッケージのバージョンをアップグレードすることで変更できます。

パッケージリポジトリは、次のような場合に役立ちます。

- **セキュリティ**:多くの R ユーザーは、CRAN または1つのミラーサイトから、新しい R パッケージをダウンロードしてインストールすることに慣れています。 ただし、セキュリティ上の理由から、を[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]実行している運用サーバーは、通常、インターネットに接続していません。

- **より簡単なオフラインインストール**:オフラインサーバーにパッケージをインストールするには、すべてのパッケージの依存関係もダウンロードする必要があります。 MiniCRAN を使用すると、すべての依存関係を正しい形式で取得しやすくなります。 MiniCRAN を使用すると、 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)ステートメントを使用してインストールするパッケージを準備するときに、パッケージの依存関係エラーを回避できます。

- 改良された**バージョン管理**:マルチユーザー環境では、サーバーに複数のパッケージバージョンを無制限にインストールしないようにすることをお勧めします。 ローカルリポジトリを使用して、ユーザーに対して一貫したパッケージのセットを提供します。

## <a name="install-minicran"></a>MiniCRAN のインストール

**MiniCRAN**パッケージ自体は、18個の他の cran パッケージに依存しています。その中には、 **devel**パッケージに対するシステムの依存関係を持つ**rcurl**パッケージがあります。 同様に、パッケージ**XML**は**libxml2 devel**に依存しています。 依存関係を解決するには、インターネットに完全にアクセスできるコンピューター上で、ローカルリポジトリを最初に作成することをお勧めします。

基本の R、R ツール、およびインターネット接続があるコンピューターで、次のコマンドを実行します。 これは、SQL Server のコンピューターではないと想定されています。 次のコマンドは、 **miniCRAN**パッケージと**igraph**パッケージをインストールします。 この例では、パッケージが既にインストールされているか`if`どうかを確認しますが、ステートメントをバイパスしてパッケージを直接インストールすることもできます。

```R
if(!require("miniCRAN")) install.packages("miniCRAN") 
if(!require("igraph")) install.packages("igraph") 
library("miniCRAN")
```

## <a name="set-the-cran-mirror-and-mran-snapshot"></a>CRAN mirror と MRAN スナップショットを設定する

パッケージの取得に使用するミラーサイトを指定します。 たとえば、必要なパッケージが含まれている地域で、MRAN サイトやその他のサイトを使用することができます。 ダウンロードに失敗した場合は、別のミラーサイトを試してください。

```R
CRAN_mirror <- c(CRAN = "https://cran.cnr.berkeley.edu")
```

## <a name="create-a-local-folder"></a>ローカルフォルダーを作成する

収集したパッケージを格納するローカルフォルダーを作成します。 これを頻繁に繰り返す場合は、"miniCRANZooPackages" や "miniCRANMyRPackageV2" などのわかりやすい名前を使用することをお勧めします。

フォルダーをローカルリポジトリとして指定します。 R 構文では、パス名にスラッシュを使用します。これは、Windows の規則とは逆のものです。

```R
local_repo <- "C:/miniCRANZooPackages"
```

## <a name="add-packages-to-the-local-repo"></a>ローカルリポジトリにパッケージを追加する

**MiniCRAN**のインストールと読み込みが完了したら、ダウンロードする追加パッケージを指定する一覧を作成します。

この初期リストに依存関係を追加しないでください。 **MiniCRAN**によって使用される**igraph**パッケージは、依存関係の一覧を自動的に生成します。 生成された依存関係グラフの使用方法の詳細については、「 [miniCRAN を使用したパッケージの依存関係の識別](https://cran.r-project.org/web/packages/miniCRAN/vignettes/miniCRAN-dependency-graph.html)」を参照してください。

1. ターゲットパッケージ "zoo" と "forecast" を変数に追加します。

    ```R
    pkgs_needed <- c("zoo", "forecast")
    ```

2. 必要に応じて、依存関係グラフをプロットします。 これは必須ではありませんが、有益である可能性があります。

    ```R
    plot(makeDepGraph(pkgs_needed))
    ```

3. ローカルリポジトリを作成します。 必要に応じて、SQL Server インスタンスにインストールされているバージョンに R バージョンを変更してください。 コンポーネントのアップグレードを行った場合、バージョンは元のバージョンよりも新しい可能性があります。 詳細については、「 [Get R package information](../package-management/r-package-information.md)」を参照してください。

    ```R
    pkgs_expanded <- pkgDep(pkgs_needed, repos = CRAN_mirror);
    makeRepo(pkgs_expanded, path = local_repo, repos = CRAN_mirror, type = "win.binary", Rversion = "3.3");
    ```

   この情報から、miniCRAN パッケージは、 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]後でパッケージをにコピーするために必要なフォルダー構造を作成します。

この時点で、必要なパッケージと、必要な追加パッケージを含むフォルダーが作成されます。 フォルダーには、圧縮されたパッケージのコレクションが含まれている必要があります。 パッケージを解凍したり、ファイルの名前を変更したりしないでください。

必要に応じて、次のコードを実行して、ローカルの miniCRAN リポジトリに含まれているパッケージの一覧を表示します。

```R
pdb <- as.data.frame(pkgAvail(local_repo, type = "win.binary", Rversion = "3.3"), stringsAsFactors = FALSE);
head(pdb);
pdb$Package;
pdb[, c("Package", "Version", "License")]
```

## <a name="add-packages-to-the-instance-library"></a>インスタンスライブラリにパッケージを追加する

必要なパッケージを含むローカルリポジトリを作成したら、パッケージリポジトリを SQL Server コンピューターに移動します。 次の手順では、R ツールを使用してパッケージをインストールする方法について説明します。

1. MiniCRAN リポジトリが格納されているフォルダー全体を、パッケージのインストール先のサーバーにコピーします。 通常、このフォルダーには次の構造があります。 

   `<miniCRAN root>/bin/windows/contrib/version/<all packages>`

   この手順では、ルートドライブからのフォルダーを想定しています。

2. インスタンスに関連付けられている R ツールを開きます (たとえば、Rgui .exe を使用できます)。 を右クリックし、 **[管理者として実行]** を選択して、ツールがシステムを更新できるようにします。

   ::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
   - たとえば、RGUI の既定のファイルの場所は`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES\bin\x64`です。
   ::: moniker-end

   ::: moniker range="=sql-server-2017||=sqlallproducts-allversions"
   - たとえば、RGUI のファイルの場所は`C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\bin\x64`です。
   ::: moniker-end

   ::: moniker range=">sql-server-2017||=sqlallproducts-allversions"
   - たとえば、RGUI のファイルの場所は`C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\R_SERVICES\bin\x64`です。
   ::: moniker-end

3. インスタンスライブラリのパスを取得し、ライブラリパスの一覧に追加します。

   ::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
   例を次に示します。

   ```R
   outputlib <- "C:/Program Files/Microsoft SQL Server/MSSQL13.MSSQLSERVER/R_SERVICES/library"
   ```

   ::: moniker-end

   ::: moniker range="=sql-server-2017||=sqlallproducts-allversions"
   例を次に示します。

   ```R
   outputlib <- "C:/Program Files/Microsoft SQL Server/MSSQL14.MSSQLSERVER/R_SERVICES/library"
   ```

   ::: moniker-end

   ::: moniker range=">sql-server-2017||=sqlallproducts-allversions"
   例を次に示します。

   ```R
   outputlib <- "C:/Program Files/Microsoft SQL Server/MSSQL15.MSSQLSERVER/R_SERVICES/library"
   ```

   ::: moniker-end

4. **MiniCRAN**リポジトリをコピーしたサーバー上の新しい場所をとし`server_repo`て指定します。

    この例では、リポジトリをサーバーの一時フォルダーにコピーしたとします。

    ```R
    inputlib <- "C:/miniCRANZooPackages"
    ```

5. サーバーの新しい R ワークスペースで作業しているため、インストールするパッケージの一覧も指定する必要があります。

    ```R
    mypackages <- c("zoo", "forecast")
    ```

6. MiniCRAN リポジトリのローカルコピーへのパスを指定して、パッケージをインストールします。

    ```R
    install.packages(mypackages, repos = file.path("file://", normalizePath(inputlib, winslash = "/")), lib = outputlib, type = "win.binary", dependencies = TRUE);
    ```

7. インスタンスライブラリからは、次のようなコマンドを使用して、インストールされているパッケージを表示できます。

    ```R
    installed.packages()
    ```

## <a name="see-also"></a>関連項目

+ [R パッケージ情報の取得](../package-management/r-package-information.md)
+ [R チュートリアル](../tutorials/sql-server-r-tutorials.md)
