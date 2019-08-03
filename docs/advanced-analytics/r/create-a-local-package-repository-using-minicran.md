---
title: MiniCRAN を使用してローカル R パッケージリポジトリを作成する
description: MiniCran を使用して、R パッケージの依存関係を検出し、アセンブルし、1つの統合パッケージにインストールします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: a2324ad662cad2c91bc6e002fd652fed73d8ab3d
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715760"
---
# <a name="create-a-local-r-package-repository-using-minicran"></a>MiniCRAN を使用してローカル R パッケージリポジトリを作成する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

[miniCRAN](https://cran.r-project.org/web/packages/miniCRAN/index.html)パッケージは、 [Andre de vries](https://blog.revolutionanalytics.com/2016/05/minicran-sql-server.html)によって作成され、パッケージと依存関係を識別して1つのフォルダーにダウンロードします。このフォルダーは、オフラインの R パッケージインストール用に他のコンピューターにコピーできます。

入力として、1つまたは複数のパッケージを指定します。 **miniCRAN**は、これらのパッケージの依存関係ツリーを再帰的に読み取り、一覧表示されたパッケージとその依存関係のみを cran または類似のリポジトリからダウンロードします。

出力として、 **miniCRAN**は、選択したパッケージとすべての必要な依存関係から構成される内部一貫性リポジトリを作成します。 その後、このローカルリポジトリをサーバーに移動し、インターネット接続なしでパッケージのインストールを続行できます。

> [!NOTE]
> 経験豊富な R ユーザーは、多くの場合、ダウンロードしたパッケージの説明ファイルで依存パッケージの一覧を検索します。 ただし、**インポート**に記載されているパッケージには、第2レベルの依存関係がある場合があります。 このため、必要なパッケージの完全なコレクションをアセンブルするために**miniCRAN**を使用することをお勧めします。

## <a name="why-create-a-local-repository"></a>ローカルリポジトリを作成する理由

ローカルパッケージリポジトリを作成する目的は、サーバー管理者または組織内の他のユーザーがサーバーに新しい R パッケージをインストールするために使用できる1つの場所を提供することです (特に、インターネットにアクセスできない)。 リポジトリを作成した後は、新しいパッケージを追加するか、既存のパッケージのバージョンをアップグレードすることで変更できます。

パッケージリポジトリは、次のような場合に役立ちます。

- **セキュリティ**:多くの R ユーザーは、CRAN または1つのミラーサイトから、新しい R パッケージをダウンロードしてインストールすることに慣れています。 ただし、セキュリティ上の理由から、を[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]実行している運用サーバーは、通常、インターネットに接続していません。

- **より簡単なオフラインインストール**:オフラインサーバーにパッケージをインストールするには、すべてのパッケージの依存関係をダウンロードする必要があります。 miniCRAN を使用すると、すべての依存関係を正しい形式で取得しやすくなります。

    MiniCRAN を使用すると、 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)ステートメントを使用してインストールするパッケージを準備するときに、パッケージの依存関係エラーを回避できます。

- 改良された**バージョン管理**:マルチユーザー環境では、サーバーに複数のパッケージバージョンを無制限にインストールしないようにすることをお勧めします。 ローカルリポジトリを使用して、アナリストが使用するパッケージの一貫性のあるセットを提供します。 

> [!TIP]
> また、miniCRAN を使用して、Azure Machine Learning で使用するパッケージを準備することもできます。 詳細については、次のブログを参照してください。[Azure ML での miniCRAN の使用 (Michele Usuelli)](https://www.r-bloggers.com/using-minicran-in-azure-ml/) 

## <a name="install-minicran"></a>MiniCRAN のインストール

**MiniCRAN**パッケージ自体は、18個の他の cran パッケージに依存しています。その中には、 **devel**パッケージに対するシステムの依存関係を持つ**rcurl**パッケージがあります。 同様に、パッケージ**XML**は**libxml2 devel**に依存しています。 依存関係を解決するには、インターネットに完全にアクセスできるコンピューター上で、ローカルリポジトリを最初に作成することをお勧めします。 

基本の R、R ツール、およびインターネット接続があるコンピューターで、次のコマンドを実行します。 これは、SQL Server のコンピューターでは*ない*と想定されています。 次のコマンドは、 **miniCRAN**パッケージと必要な**igraph**パッケージをインストールします。 この例では、パッケージが既にインストールされているかどうかを確認しますが、if ステートメントをバイパスしてパッケージを直接インストールすることもできます。

```R
if(!require("miniCRAN")) install.packages("miniCRAN") 
if(!require("igraph")) install.packages("igraph") 
library("miniCRAN")
```

## <a name="set-the-cran-mirror-and-mran-snapshot"></a>CRAN mirror と MRAN スナップショットを設定する

パッケージの取得に使用するミラーサイトを指定します。 たとえば、必要なパッケージが含まれている地域で、MRAN サイトやその他のサイトを使用することができます。 ダウンロードに失敗した場合は、別のミラーサイトを試してください。

```R
CRAN_mirror <- c(CRAN = "https://mran.microsoft.com")
CRAN_mirror <- c(CRAN = "https://cran.cnr.berkeley.edu")
```

## <a name="create-a-local-folder"></a>ローカルフォルダーを作成する

収集したパッケージの保存先`C:\mylocalrepo`として、などのローカルフォルダーを作成します。 これを頻繁に繰り返す場合は、"miniCRANZooPackages" や "miniCRANMyRPackagev2" などのわかりやすい名前を使用することをお勧めします。

フォルダーをローカルリポジトリとして指定します。 R 構文では、パス名にスラッシュを使用します。これは、Windows の規則とは逆のものです。

```R
local_repo <- "C:/mylocalrepo"
```

## <a name="add-packages-to-the-local-repo"></a>ローカルリポジトリにパッケージを追加する

**MiniCRAN**のインストールと読み込みが完了したら、ダウンロードする追加パッケージを指定する一覧を作成します。

この初期リストに依存関係**を追加しない**でください。 **MiniCRAN**によって使用される**igraph**パッケージによって、依存関係の一覧が生成されます。 生成された依存関係グラフの使用方法の詳細については、「 [miniCRAN を使用したパッケージの依存関係の識別](https://cran.r-project.org/web/packages/miniCRAN/vignettes/miniCRAN-dependency-graph.html)」を参照してください。

1. ターゲットパッケージ "zoo" と "forecast" を変数に追加します。

    ```R
    pkgs_needed <- c("zoo", "forecast")
    ```

2. 必要に応じて、依存関係グラフをプロットしますが、有益である場合もあります。
    
    ```R
    plot(makeDepGraph(pkgs_needed))
    ```

3. ローカルリポジトリを作成します。 必要に応じて、SQL Server インスタンスにインストールされているバージョンに R バージョンを変更してください。 バージョン3.2.2 は SQL Server 2016、バージョン3.3 は SQL Server 2017 にあります。 コンポーネントのアップグレードを行った場合、バージョンは新しい可能性があります。 詳細については、「 [Get R And Python package information](../package-management/installed-package-information.md)」を参照してください。

    ```R
    pkgs_expanded <- pkgDep(pkgs_needed, repos = CRAN_mirror);
    makeRepo(pkgs_expanded, path = local_repo, repos = CRAN_mirror, type = "win.binary", Rversion = "3.3");
    ```

   この情報から、miniCRAN パッケージは、 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]後でパッケージをにコピーするために必要なフォルダー構造を作成します。

この時点で、必要なパッケージを含むフォルダーと、必要な追加のパッケージが必要です。 このパスは、次の例のようになります。C:\mylocalrepo\bin\windows\contrib\3.3 には、zip 形式のパッケージのコレクションが含まれている必要があります。 パッケージを解凍したり、ファイルの名前を変更したりしないでください。

必要に応じて、次のコードを実行して、ローカルの miniCRAN リポジトリに含まれているパッケージの一覧を表示します。

```R
pdb <- as.data.frame(pkgAvail(local_repo, type = "win.binary", Rversion = "3.3"), stringsAsFactors = FALSE);
head(pdb);
pdb$Package;
pdb[, c("Package", "Version", "License")]
```

## <a name="add-packages-to-the-instance-library"></a>インスタンスライブラリにパッケージを追加する

必要なパッケージを含むローカルリポジトリを作成したら、パッケージリポジトリを SQL Server コンピューターに移動します。 次の手順では、R ツールを使用してパッケージをインストールする方法について説明します。

1. MiniCRAN リポジトリが格納されているフォルダー全体を、パッケージのインストール先のサーバーにコピーします。 通常、このフォルダーには、miniCRAN root > bin > windows > contrib > version > すべてのパッケージが含まれます。 次の例では、ルートドライブからのフォルダーを想定しています。 

2. インスタンスに関連付けられている R ツールを開きます (たとえば、Rgui .exe を使用できます)。 **[管理者として実行]** を右クリックして、ツールがシステムを更新できるようにします。

    - SQL Server 2017 の場合、RGUI のファイルの場所`C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\bin\x64`はです。

    - SQL Server 2016 の場合、RGUI のファイルの場所`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES\bin\x64`はです。

3. インスタンスライブラリのパスを取得し、ライブラリパスの一覧に追加します。 SQL Server 2017 では、パスは次の例のようになります。

    ```R
    outputlib <- "C:/Program Files/Microsoft SQL Server/MSSQL14.MSSQLSERVER/R_SERVICES/library"
    ```

4. **MiniCRAN**リポジトリをコピーしたサーバー上の新しい場所をとして`server_repo`指定します。

    この例では、リポジトリをサーバーの一時フォルダーにコピーしたとします。

    ```R
    inputlib <- "C:/temp/mylocalrepo"
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

+ [パッケージ情報の取得](../package-management/installed-package-information.md)
+ [R チュートリアル](../tutorials/sql-server-r-tutorials.md)
