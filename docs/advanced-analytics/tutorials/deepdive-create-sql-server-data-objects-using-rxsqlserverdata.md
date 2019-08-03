---
title: RevoScaleR RxSqlServerData を使用して SQL Server データオブジェクトを作成する
description: SQL Server で R 言語を使用してデータオブジェクトを作成する方法に関するチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/26/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 403f71b4c04d4017094fb7de85ae5de36bcb2c68
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714900"
---
# <a name="create-sql-server-data-objects-using-rxsqlserverdata-sql-server-and-revoscaler-tutorial"></a>RxSqlServerData を使用して SQL Server データオブジェクトを作成する (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

レッスン2は、データベースの作成 (テーブルの追加とデータの読み込み) の継続です。 DBA がデータベースを作成し、[レッスン 1](deepdive-work-with-sql-server-data-using-r.md)でログインした場合、rstudio などの R IDE または**rstudio**などの組み込みツールを使用してテーブルを追加できます。

R から SQL Server に接続し、 **RevoScaleR** functions を使用して、次のタスクを実行します。

> [!div class="checklist"]
> * トレーニングデータと予測用のテーブルを作成する
> * ローカルの .csv ファイルのデータを使用してテーブルを読み込む

サンプルデータは、トレーニングデータセットとスコアリングデータセットにパーティション分割されたクレジットカード不正データ (ccFraud データセット) をシミュレートしたものです。 データファイルは**RevoScaleR**に含まれています。

これらの手順を完了するには、R IDE または**Rgui**を使用します。 次の場所にある R 実行可能ファイルを使用してください。C:\Program Files\Microsoft\R Client\R_SERVER\bin\x64 (そのツールを使用している場合は Rgui、C:\Program Files\Microsoft\R Client\r _s を指す R IDE)。 これらの実行可能ファイルを持つ[R クライアントワークステーション](../r/set-up-a-data-science-client.md)を使用することは、このチュートリアルの前提条件と見なされます。

## <a name="create-the-training-data-table"></a>トレーニングデータテーブルを作成する

1. データベース接続文字列を R 変数に格納します。 SQL Server の有効な ODBC 接続文字列の2つの例を次に示します。1つは SQL ログインを使用し、もう1つは Windows 統合認証です。 

   必要に応じて、サーバー名、ユーザー名、およびパスワードを変更してください。

    **SQL ログイン**
    ```R
    sqlConnString <- "Driver=SQL Server;Server=<server-name>; Database=RevoDeepDive;Uid=<user_name>;Pwd=<password>"
    ```

    **Windows 認証**
    ```R
    sqlConnString <- "Driver=SQL Server;Server=<server-name>;Database=RevoDeepDive;Trusted_Connection=True"
    ```

2. 作成するテーブルの名前を指定し、R 変数に保存します。
  
    ```R
    sqlFraudTable <- "ccFraudSmall"
    ```
  
    サーバーインスタンスとデータベース名は接続文字列の一部として既に指定されているので、2つの変数を組み合わせると、新しいテーブルの*完全修飾*名は*ccFraudSmall*になります。
  
3.  必要に応じて、[ *Rowsperread* ] を指定して、各バッチで読み取られるデータの行数を制御します。
  
    ```R
    sqlRowsPerRead = 5000
    ```
  
    このパラメーターは省略可能ですが、設定すると、より効率的な計算になる可能性があります。 **RevoScaleR**と**microsoft ml**の拡張分析関数の大部分は、データをチャンク単位で処理します。 *Rowsperread*パラメーターによって、各チャンク内の行の数が決まります。
  
    適切なバランスを見つけるために、この設定を試してみることが必要になる場合があります。 値が大きすぎる場合、そのサイズのチャンク単位でデータを処理するのに十分なメモリがないと、データアクセスの速度が低下する可能性があります。 逆に、一部のシステムでは、 *Rowsperread*の値が小さすぎる場合も、パフォーマンスが低下することがあります。
  
    初期値として、データベースエンジンインスタンスによって定義された既定のバッチ処理サイズを使用して、各チャンク内の行の数 (5000 行) を制御します。 *Sqlrowsperread*変数にその値を保存します。
  
4.  新しいデータソースオブジェクトの変数を定義し、以前に定義した引数を**RxSqlServerData**コンストラクターに渡します。 ここではデータ ソース オブジェクトが作成されるだけで、設定は行われないことに注意してください。 データの読み込みは別の手順です。
  
    ```R
    sqlFraudDS <- RxSqlServerData(connectionString = sqlConnString,
       table = sqlFraudTable,
       rowsPerRead = sqlRowsPerRead)
    ```

## <a name="create-the-scoring-data-table"></a>スコアリングデータテーブルを作成する

同じ手順を使用して、同じプロセスを使用してスコアリングデータを保持するテーブルを作成します。

1. スコア付けで使用するテーブルの名前を格納するための新しい R 変数 *sqlScoreTable*を作成します。
  
    ```R
    sqlScoreTable <- "ccFraudScoreSmall"
    ```
  
2. その変数を引数として **RxSqlServerData** 関数に渡して、2 つ目のデータ ソース オブジェクト *sqlScoreDS*を定義します。
  
    ```R
    sqlScoreDS <- RxSqlServerData(connectionString = sqlConnString,
       table = sqlScoreTable, rowsPerRead = sqlRowsPerRead)
    ```

接続文字列とその他のパラメーターは、R ワークスペースの変数として既に定義されているため、異なるテーブル、ビュー、またはクエリを表す新しいデータソースに再利用できます。

> [!NOTE]
> この関数では、クエリに基づくデータソースよりも、テーブル全体に基づいたデータソースの定義に異なる引数を使用します。 これは、SQL Server データベースエンジンがクエリを別々に準備する必要があるためです。 このチュートリアルの後半では、SQL クエリに基づいてデータソースオブジェクトを作成する方法について説明します。

## <a name="load-data-into-sql-tables-using-r"></a>R を使用して SQL テーブルにデータを読み込む

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルが作成されましたので、それらのテーブルには、適切な **Rx** 関数を使用してデータを読み込むことができます。

**RevoScaleR**パッケージには、データソースの種類に固有の関数が含まれています。 テキストデータの場合は、 [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata)を使用してデータソースオブジェクトを生成します。 その他に、Hadoop データや ODBC データなどからデータ ソース オブジェクトを作成するための関数もあります。

> [!NOTE]
> このセクションでは、データベースに対する**DDL の実行**アクセス許可が必要です。

### <a name="load-data-into-the-training-table"></a>トレーニングテーブルにデータを読み込む

1. R 変数*ccFraudCsv*を作成し、サンプルデータを含む CSV ファイルのファイルパスを変数に割り当てます。 このデータセットは、 **RevoScaleR**で提供されています。 "SampleDataDir" は、 **rxGetOption**関数のキーワードです。
  
    ```R
    ccFraudCsv <- file.path(rxGetOption("sampleDataDir"), "ccFraudSmall.csv")
    ```
  
    **RxGetOption**の呼び出しに注意してください。これは、 **RevoScaleR**の[rxOptions](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxoptions)に関連付けられている GET メソッドです。 このユーティリティを使用して、ローカルおよびリモートの計算コンテキスト (既定の共有ディレクトリなど)、または計算に使用するプロセッサ数 (コア) に関連するオプションを設定および一覧表示します。
    
    この特定の呼び出しでは、コードを実行している場所に関係なく、正しいライブラリからサンプルを取得します。 たとえば、SQL Server で関数を実行し、開発用コンピューターでパスがどのように違うかを確認してください。
  
2. 新しいデータを格納する変数を定義し、 **RxTextData** 関数を使用してテキスト データ ソースを指定します。
  
    ```R
    inTextData <- RxTextData(file = ccFraudCsv,      colClasses = c(
        "custID" = "integer", "gender" = "integer", "state" = "integer",
        "cardholder" = "integer", "balance" = "integer",
        "numTrans" = "integer",
        "numIntlTrans" = "integer", "creditLine" = "integer",
        "fraudRisk" = "integer"))
    ```
  
    引数 *colClasses* は重要です。 この引数は、テキスト ファイルから読み込まれたデータの各列に割り当てるデータ型を指定するために使用します。 この例では、すべての列がテキストとして扱われます。例外として、名前付きの列は整数として扱われます。
  
3. この時点で、しばらくしてから、で[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]データベースを表示することをお勧めします。 データベース内のテーブルの一覧を更新します。
  
    これは、R データオブジェクトがローカルワークスペースに作成されているにもかかわらず、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]データベースにテーブルが作成されていないことがわかります。 また、テキストファイルから R 変数にデータが読み込まれていません。
  
4. 関数[rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep)関数を呼び出してデータを挿入します。
  
    ```R
    rxDataStep(inData = inTextData, outFile = sqlFraudDS, overwrite = TRUE)
    ```
    
    接続文字列に問題がなければ、しばらくすると、次のような結果が表示されます。
  
    *書き込まれた合計行数:1万、合計時間:* 0.466*行読み取り:1万、処理された行数の合計:1万、合計チャンク時間:0.577 秒*
  
5. テーブルの一覧を最新の状態に更新します。 各変数のデータ型が正しいことを確認し、正常にインポートされたことを確認するには[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 、でテーブルを右クリックし、 **[上位1000行の選択]** を選択します。

### <a name="load-data-into-the-scoring-table"></a>スコアリングテーブルにデータを読み込む

1. 手順を繰り返して、スコアリングに使用されるデータセットをデータベースに読み込みます。
  
    まず、ソース ファイルへのパスを指定します。
  
    ```R
    ccScoreCsv <- file.path(rxGetOption("sampleDataDir"), "ccFraudScoreSmall.csv")
    ```
  
2. **RxTextData** 関数を使用してデータを取得し、それを変数 *inTextData*に保存します。
  
    ```R
    inTextData <- RxTextData(file = ccScoreCsv,      colClasses = c(
        "custID" = "integer", "gender" = "integer", "state" = "integer",
        "cardholder" = "integer", "balance" = "integer",
        "numTrans" = "integer",
        "numIntlTrans" = "integer", "creditLine" = "integer"))
    ```
  
3.  **rxDataStep** 関数を呼び出して、現在のテーブルを新しいスキーマとデータで上書きします。
  
    ```R
    rxDataStep(inData = inTextData, sqlScoreDS, overwrite = TRUE)
    ```
  
    - *inData* 引数では、使用するデータ ソースを定義します。
  
    - *OutFile* 引数では、データを保存する、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のテーブルを指定します。
  
    - テーブルが既に存在し、 *overwrite*オプションを使用しない場合、結果は切り捨てられずに挿入されます。
  
ここでも、接続に成功した場合は、完了を示すメッセージと、テーブルへのデータ書き込みに要した時間が表示されます。

*書き込まれた合計行数:1万、合計時間:0.384*
行読み取り *:1万、処理された行数の合計:1万、合計チャンク時間:0.456 秒*

## <a name="more-about-rxdatastep"></a>rxDataStep に関する詳細情報

[rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep)は、R データフレームに対して複数の変換を実行できる強力な関数です。 また、rxDataStep を使用して、変換先に必要な表現 (この場合は) [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]にデータを変換することもできます。

必要に応じて、 **rxDataStep**の引数に R 関数を使用して、データの変換を指定できます。 これらの操作の例については、このチュートリアルの後半で説明します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [SQL Server データに対するクエリおよび変更](../../advanced-analytics/tutorials/deepdive-query-and-modify-the-sql-server-data.md)