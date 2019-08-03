---
title: R 開発用のデータサイエンスクライアントをセットアップする
description: SQL Server にリモート接続するために、開発ワークステーションにローカルの R ライブラリとツールをインストールします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: e87770447c371f46ad384daffa3c7bc40b836904
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715602"
---
# <a name="set-up-a-data-science-client-for-r-development-on-sql-server"></a>SQL Server で R 開発用のデータサイエンスクライアントをセットアップする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

R 統合は、r 言語オプションを[SQL Server 2016 r Services](../install/sql-r-services-windows-install.md)または[SQL Server Machine Learning Services (データベース内)](../install/sql-machine-learning-services-windows-install.md)インストールに含める場合に SQL Server 2016 以降で使用できます。 

SQL Server 用の R ソリューションを開発してデプロイするには、開発ワークステーションに[Microsoft R Client](https://docs.microsoft.com/machine-learning-server/r-client/what-is-microsoft-r-client)をインストールして、 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)やその他の r ライブラリを取得します。 RevoScaleR ライブラリは、リモート SQL Server インスタンスにも必要であり、両方のシステム間でのコンピューティング要求を調整します。 

この記事では、R クライアント開発ワークステーションを構成して、machine learning と R の統合で有効になっているリモート SQL Server と対話できるようにする方法について説明します。 この記事の手順を完了すると、SQL Server にあるのと同じ R ライブラリが得られます。 また、SQL Server でローカル R セッションからリモート R セッションに計算をプッシュする方法についても説明します。

![クライアント/サーバーコンポーネント](media/sqlmls-r-client-revo.png "ローカルおよびリモートの R セッションとライブラリ")

インストールを検証するには、この記事で説明されている組み込みの**Rgui**ツールを使用するか、通常使用する rgui または他の IDE に[ライブラリをリンク](#install-ide)します。

> [!Note]
> クライアントライブラリのインストールの代わりに、[スタンドアロンサーバー](../install/sql-machine-learning-standalone-windows-install.md)をリッチクライアントとして使用することもできます。これにより、より高度なシナリオの作業に適しています。 スタンドアロンサーバーは SQL Server から完全に切り離されていますが、同じ R ライブラリがあるため、データベース内分析 SQL Server のクライアントとして使用できます。 また、他のデータプラットフォームからデータをインポートおよびモデル化する機能など、SQL に関連しない作業にも使用できます。 スタンドアロンサーバーをインストールする場合は、R 実行可能ファイルを次の場所`C:\Program Files\Microsoft SQL Server\140\R_SERVER`で見つけることができます。 インストールを検証するには、 [r コンソールアプリを開い](#R-tools)て、その場所にある r を使用してコマンドを実行します。

## <a name="commonly-used-tools"></a>一般的に使用されるツール

SQL を初めて使用する R 開発者でも、R とデータベース内の分析を初めて使用する SQL 開発者でも、データベース内のすべての機能を実行するには、R 開発ツールと、 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)などの t-sql クエリエディターの両方が必要になります。解析.

単純な R 開発シナリオでは、RGUI 実行可能ファイルを使用できます。これは、MRO および SQL Server の base R ディストリビューションにバンドルされています。 この記事では、ローカルとリモートの R セッションの両方で RGUI を使用する方法について説明します。 生産性を向上させるには、 [rstudio や Visual studio](#install-ide)などの機能豊富な IDE を使用する必要があります。

SSMS は、SQL Server でのストアドプロシージャの作成と実行に役立つ、R コードを含むストアドプロシージャを含む、個別のダウンロードです。 開発環境で記述するほとんどすべての R コードは、ストアドプロシージャに埋め込むことができます。 他のチュートリアルを実行して[、SSMS と埋め込み R](../tutorials/sqldev-in-database-r-for-sql-developers.md)について学習することができます。

## <a name="1---install-r-packages"></a>1-R パッケージをインストールする

Microsoft の R パッケージは、複数の製品とサービスで利用できます。 ローカルワークステーションでは、Microsoft R Client をインストールすることをお勧めします。 R Client には、 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、 [microsoft ml](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/microsoftml-package)、 [SQLRUtils](https://docs.microsoft.com/machine-learning-server/r-reference/sqlrutils/sqlrutils)、およびその他の r パッケージが用意されています。

1. [Microsoft R Client をダウンロード](https://aka.ms/rclient/download)します。

2. インストールウィザードで、既定のインストールパスを受け入れるか変更し、コンポーネントの一覧を受け入れるか変更して、Microsoft R Client ライセンス条項に同意します。

  インストールが完了すると、[ようこそ] 画面に製品とドキュメントが表示されます。

3. MKL_CBWR システム環境変数を作成して、Intel Math Kernel Library (MKL) 計算での一貫した出力を確認します。

  + コントロールパネルで、[**システムとセキュリティ** >  > ] [システム] [システム**設定** > ] **[環境変数]** をクリックします。
  + **[自動]** に設定された値を使用して、 **MKL_CBWR**という名前の新しいシステム変数を作成します。

## <a name="2---locate-executables"></a>2-実行可能ファイルの検索

インストールフォルダーの内容を見つけて一覧表示し、R .exe、RGUI、およびその他のパッケージがインストールされていることを確認します。 

1. エクスプローラーで、C:\Program Files\Microsoft\R Client\r Server\bin フォルダーを開き、Dfs-r の場所を確認します。

2. X64 サブフォルダーを開いて**Rgui**を確認します。 このツールは次の手順で使用します。

3. C:\Program Files\Microsoft\R Client\r _s ライブラリを開き、RevoScaleR、Microsoft Ml など、R Client と共にインストールされたパッケージの一覧を確認します。


<a name="R-tools"></a>
 
## <a name="3---start-rgui"></a>3-RGUI を開始する

SQL Server と共に R をインストールすると、RGui、Rgui などの R の基本インストールに標準となる同じ R ツールが得られます。 これらのツールは軽量で、パッケージとライブラリの情報をチェックしたり、アドホックコマンドやスクリプトを実行したり、チュートリアルをステップ実行したりするのに便利です。 これらのツールを使用して、R バージョン情報を取得し、接続を確認することができます。

1. C:\Program Files\Microsoft\R Client\R_SERVER\bin\x64 を開き、 **Rgui**をダブルクリックして r コマンドプロンプトで r セッションを開始します。

  Microsoft program フォルダーから R セッションを開始すると、RevoScaleR を含むいくつかのパッケージが自動的に読み込まれます。 

2. コマンド`print(Revo.version)`プロンプトで「」と入力して、RevoScaleR パッケージのバージョン情報を返します。 RevoScaleR にはバージョン9.2.1 または9.3.0 が必要です。

3. インストールされているパッケージの一覧を表示するには、R プロンプトで **「search ()** 」と入力します。

   ![R の読み込み時のバージョン情報](../install/media/rclient-rgui-r-prompt.png "R プロンプトを開く")


## <a name="4---get-sql-permissions"></a>4-SQL のアクセス許可を取得する

R クライアントでは、R 処理は2つのスレッドとメモリ内データで制限されています。 複数のコアと大規模なデータセットを使用したスケーラブルな処理では、リモート SQL Server インスタンスのデータセットと計算能力に対して実行 (*計算コンテキスト*と呼ばれます) をシフトできます。 これは、実稼働 SQL Server インスタンスとクライアントを統合するために推奨される方法です。この方法を使用するには、アクセス許可と接続情報が必要です。

スクリプトを実行してデータをアップロードするために SQL Server のインスタンスに接続するには、データベースサーバーで有効なログインが必要です。 SQL ログインまたは統合 Windows 認証を使用できます。 一般に、Windows 統合認証を使用することをお勧めしますが、スクリプトに外部データへの接続文字列が含まれている場合は特に、SQL ログインを使用する方が簡単です。

少なくとも、コードの実行に使用するアカウントには、使用しているデータベースから読み取るためのアクセス許可が必要です。さらに、特殊なアクセス許可では、外部スクリプトを実行します。 ほとんどの開発者は、ストアドプロシージャを作成し、トレーニングデータまたはスコア付けデータを含むテーブルにデータを書き込むためのアクセス許可も必要です。 

データベース管理者に問い合わせて、R を使用するデータベースで、[アカウントに対する次のアクセス許可を構成](../security/user-permission.md)します。

+ サーバーで R スクリプトを実行するには **、任意の外部スクリプトを実行**します。
+ モデルのトレーニングに使用するクエリを実行するための特権を**db_datareader**します。
+ トレーニングデータまたはスコア付けされたデータを書き込む**db_datawriter** 。
+ **db_owner** 。ストアドプロシージャ、テーブル、関数などのオブジェクトを作成します。 
  また、サンプルデータベースとテストデータベースを作成するには、 **db_owner**が必要です。 

SQL Server に既定でインストールされていないパッケージがコードに必要な場合は、データベース管理者に配置して、インスタンスと共にパッケージをインストールしてください。 SQL Server はセキュリティで保護された環境であり、パッケージをインストールできる場所に制限があります。 詳細については、「 [SQL Server に新しい R パッケージをインストール](install-additional-r-packages-on-sql-server.md)する」を参照してください。

## <a name="5---test-connections"></a>5-接続のテスト

 検証手順として、 **Rgui**と RevoScaleR を使用して、リモートサーバーへの接続を確認します。 [リモート接続](https://docs.microsoft.com/sql/database-engine/configure-windows/view-or-configure-remote-server-connection-options-sql-server)に対して SQL Server を有効にする必要があります。また、接続先のユーザーログインやデータベースなどのアクセス許可を持っている必要があります。 

次の手順では、デモデータベース、 [NYCTaxi_Sample](../tutorials/demo-data-nyctaxi-in-sql.md)、および Windows 認証を想定しています。

1. クライアントワークステーションで**Rgui**を開きます。 たとえば、を`~\Program Files\Microsoft SQL Server\140\R_SERVER\bin\x64`開き、 **rgui**をダブルクリックして起動します。

2. RevoScaleR は自動的に読み込まれます。 次のコマンドを実行して、RevoScaleR が動作可能であることを確認します。`print(Revo.version)`

3. リモートサーバーで実行するデモスクリプトを入力します。 リモート SQL Server インスタンスの有効な名前を含めるには、次のサンプルスクリプトを変更する必要があります。 このセッションはローカルセッションとして開始されますが、 **rxSummary**関数はリモート SQL Server インスタンスで実行されます。

  ```R
  # Define a connection. Replace server with a valid server name.
  connStr <- "Driver=SQL Server;Server=<your-server-name>;Database=NYCTaxi_Sample;Trusted_Connection=true"
  
  # Specify the input data in a SQL query.
  sampleQuery <-"SELECT DISTINCT TOP(100) tip_amount FROM [dbo].nyctaxi_sample ORDER BY tip_amount DESC;"
  
  # Define a remote compute context based on the remote server.
  cc <-RxInSqlServer(connectionString=connStr)

  # Execute the function using the remote compute context.
  rxSummary(formula = ~ ., data = RxSqlServerData(sqlQuery=sampleQuery, connectionString=connStr), computeContext=cc)
  ```

  **結果:**

  このスクリプトは、リモートサーバー上のデータベースに接続し、クエリを提供し、リモート`cc`コード実行用のコンピューティングコンテキスト命令を作成します。次に、RevoScaleR 関数**rxSummary**を提供してクエリの統計サマリーを返します。生じ.

  ```R
    Call:
  rxSummary(formula = ~., data = RxSqlServerData(sqlQuery = sampleQuery, 
      connectionString = connStr), computeContext = cc)

  Summary Statistics Results for: ~.
  Data: RxSqlServerData(sqlQuery = sampleQuery, connectionString = connStr) (RxSqlServerData Data Source)
  Number of valid observations: 100 
  
  Name       Mean   StdDev   Min Max ValidObs MissingObs
  tip_amount 63.245 31.61087 36  180 100      0     
  ```

4. コンピューティングコンテキストを取得して設定します。 コンピューティングコンテキストを設定すると、セッションの間は有効なままになります。 計算がローカルとリモートのどちらであるかわからない場合は、次のコマンドを実行して確認します。接続文字列を指定する結果は、リモートの計算コンテキストを示します。

  ```R
  # Return the current compute context.
  rxGetComputeContext()

  # Revert to a local compute context.
  rxSetComputeContext("local")
  rxGetComputeContext()

  # Switch back to remote.
  connStr <- "Driver=SQL Server;Server=<your-server-name>;Database=NYCTaxi_Sample;Trusted_Connection=true"
  cc <-RxInSqlServer(connectionString=connStr)
  rxSetComputeContext(cc)
  rxGetComputeContext()
  ```  

5. 名前と型を含む、データソース内の変数に関する情報を返します。

  ```R
  rxGetVarInfo(data = inDataSource)
  ```
  結果には23個の変数が含まれます。


6. 散布図を生成して、2つの変数間に依存関係があるかどうかを調べます。 

  ```R
  # Set the connection string. Substitute a valid server name for the placeholder.
  connStr <- "Driver=SQL Server;Server=<your database name>;Database=NYCTaxi_Sample;Trusted_Connection=true"

  # Specify a query on the nyctaxi_sample table.
  # For variables on each axis, remove nulls. Use a WHERE clause and <> to do this.
  sampleQuery <-"SELECT DISTINCT TOP 100 * from [dbo].[nyctaxi_sample] WHERE fare_amount <> '' AND  tip_amount <> ''"
  cc <-RxInSqlServer(connectionString=connStr)

  # Generate a scatter plot.
  rxLinePlot(fare_amount ~ tip_amount, data = RxSqlServerData(sqlQuery=sampleQuery, connectionString=connStr, computeContext=cc), type="p")
  ```

  次のスクリーンショットは、入力と散布図の出力を示しています。

   ![RGUI の散布図](media/rclient-setup-scatterplot.png "NYC タクシーのデモデータでの散布図")

<a name="install-ide"></a>

## <a name="6---link-tools-to-rexe"></a>6-ツールを R .exe にリンクする

継続的かつ本格的な開発プロジェクトの場合は、統合開発環境 (IDE) をインストールする必要があります。 SQL Server ツールと組み込みの R ツールは、大型の R 開発を行うための機能を備えていません。 コードを作成したら、それを SQL Server で実行するためのストアドプロシージャとして配置できます。

IDE でローカル R ライブラリをポイントします (base R、RevoScaleR など)。 リモート SQL Server でのワークロードの実行は、スクリプトの実行中、スクリプトが SQL Server でリモートの計算コンテキストを呼び出し、そのサーバー上のデータと操作にアクセスするときに発生します。

### <a name="rstudio"></a>RStudio

[Rstudio](https://www.rstudio.com/)を使用する場合は、リモート SQL Server に対応する R ライブラリと実行可能ファイルを使用するように環境を構成できます。

1. SQL Server にインストールされている R パッケージのバージョンを確認します。 詳細については、「 [Get R package information](../package-management/installed-package-information.md)」を参照してください。

1. Microsoft R Client またはスタンドアロンサーバーオプションの1つをインストールして、SQL Server インスタンスで使用される base R ディストリビューションなど、RevoScaleR およびその他の R パッケージを追加します。 同じレベル以下のバージョンを選択してください (パッケージは下位互換性があります)。サーバーと同じバージョンのパッケージを提供します。 バージョン情報については、この記事のバージョンマップを参照してください。[R および Python コンポーネントをアップグレード](../install/upgrade-r-and-python.md)します。

1. RStudio で、r[パスを更新して](https://support.rstudio.com/hc/articles/200486138-Using-Different-Versions-of-R)、RevoScaleR、Microsoft R Open、およびその他の microsoft パッケージを提供する r 環境をポイントします。 

  + R クライアントのインストールについては、C:\Program Files\Microsoft\R Client\R_SERVER\bin\x64 を参照してください。
  + スタンドアロンサーバーの場合は、C:\Program 140sql サーバーライブラリまたは C:\Program 130sql サーバーライブラリを検索します。

2. を閉じてから、RStudio を開きます。

RStudio を再度開くと、r クライアント (またはスタンドアロンサーバー) の r 実行可能ファイルが既定の R エンジンになります。


### <a name="r-tools-for-visual-studio-rtvs"></a>R Tools for Visual Studio (RTVS)

R に適した IDE がまだない場合は、 **R Tools for Visual Studio**をお勧めします。

+ [R Tools for Visual Studio のダウンロード (RTVS)](https://visualstudio.microsoft.com/vs/features/rtvs/)
+ [インストール手順](https://docs.microsoft.com/visualstudio/rtvs/installing-r-tools-for-visual-studio)-rtvs は、いくつかのバージョンの Visual Studio で使用できます。
+ [R Tools for Visual Studio を使ってみる](https://docs.microsoft.com/visualstudio/rtvs/getting-started-with-r)

### <a name="connect-to-sql-server-from-rtvs"></a>RTVS から SQL Server への接続

この例では、データサイエンスワークロードがインストールされた Visual Studio 2017 Community Edition を使用します。

1. **[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** を選択します。

2. 左側のウィンドウには、プレインストールされたテンプレートの一覧が表示されます。 **[R]** をクリックし、 **[r プロジェクト]** を選択します。 **[名前]** ボックスに「 `dbtest` 」と入力し、[ **OK]** をクリックします。 

  Visual Studio によって、新しいプロジェクトフォルダーと既定のスクリプト`Script.R`ファイルが作成されます。 

3. スクリプト`.libPaths()`ファイルの最初の行に「」と入力し、CTRL + enter キーを押します。

  現在の R ライブラリのパスは、 **[R インタラクティブ]** ウィンドウに表示されます。 

4. **[R Tools]** メニューをクリックし、 **[Windows]** を選択して、ワークスペースに表示できる他の r 固有のウィンドウの一覧を表示します。
 
  + CTRL キーを押しながら3キーを押して、現在のライブラリのパッケージに関するヘルプを表示します。
  + CTRL + 8 キーを押して、**変数エクスプローラー**の R 変数を参照してください。

## <a name="next-steps"></a>次のステップ

2つの異なるチュートリアルでは、計算コンテキストをローカルからリモートの SQL Server インスタンスに切り替える練習を行うことができます。

+ [チュートリアル: SQL Server データでの RevoScaleR R 関数の使用](../tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)
+ [データ サイエンスのエンド ツー エンド チュートリアル](../tutorials/walkthrough-data-science-end-to-end-walkthrough.md)