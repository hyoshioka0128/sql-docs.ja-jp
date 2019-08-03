---
title: R 関数を使用した SQL Server データの表示と集計
description: SQL Server でのデータベース内分析に R 関数を使用して、統計の概要を視覚化および生成する方法を示すチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/26/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 47850bebcc20fdd357b2336a9597da067cd479ca
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715360"
---
# <a name="view-and-summarize-sql-server-data-using-r-walkthrough"></a>R を使用した SQL Server データの表示と集計 (チュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンでは、 **RevoScaleR**パッケージの関数について説明し、次のタスクについて説明します。

> [!div class="checklist"]
> * SQL Server への接続
> * 必要なデータを含むクエリを定義するか、テーブルまたはビューを指定します。
> * R コードの実行時に使用する計算コンテキストを 1 つまたは複数定義します。
> * 必要に応じて、ソースからデータを読み取っているときに、データソースに適用される変換を定義します。

## <a name="define-a-sql-server-compute-context"></a>SQL Server の計算コンテキストを定義する

クライアントワークステーションの R 環境で次の R ステートメントを実行します。 このセクションでは、 [Microsoft R Client のデータサイエンスワークステーション](../r/set-up-a-data-science-client.md)を前提としています。これには、すべての RevoScaleR パッケージが含まれており、R ツールの基本的で軽量なセットが含まれているためです。 たとえば、Rgui を使用して、このセクションで R スクリプトを実行できます。

1. **RevoScaleR**パッケージがまだ読み込まれていない場合は、次の R コード行を実行します。

    ```R
    library("RevoScaleR")
    ```

     引用符は省略可能ですが、この場合は推奨されます。
     
     エラーが発生した場合は、R 開発環境で、RevoScaleR パッケージを含むライブラリが使用されていることを確認してください。 などのコマンドを使用`.libPaths()`して、現在のライブラリパスを表示します。

2. SQL Server の接続文字列を作成し、R 変数 ( *Connstr*) に保存します。

   プレースホルダー "サーバー名" は、有効な SQL Server インスタンス名に変更する必要があります。 サーバー名については、インスタンス名のみを使用できます。また、ネットワークによっては、名前を完全修飾する必要がある場合もあります。
    
   SQL Server 認証の場合、接続構文は次のようになります。

    ```R
    connStr <- "Driver=SQL Server;Server=your_server_name;Database=nyctaxi_sample;Uid=your-sql-login;Pwd=your-login-password"
    ```

    Windows 認証の場合、構文は少し異なります。
    
    ```R
    connStr <- "Driver=SQL Server;Server=your_server_name;Database=nyctaxi_sample;Trusted_Connection=True"
    ```

    一般に、R コードにパスワードが保存されないようにするには、可能な限り Windows 認証を使用することをお勧めします。

3. 新しい*計算コンテキスト*の作成に使用する変数を定義します。 計算コンテキストオブジェクトを作成したら、それを使用して SQL Server インスタンスで R コードを実行できます。

    ```R
    sqlShareDir <- paste("C:\\AllShare\\",Sys.getenv("USERNAME"),sep="")
    sqlWait <- TRUE
    sqlConsoleOutput <- FALSE
    ```

    - ワークステーションと [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンピューター間で双方向に R オブジェクトをシリアル化する際、R では、一時ディレクトリが使用されます。 *sqlShareDir*として使用されるローカル ディレクトリを指定するか、既定のものをそのまま利用できます。
  
    - *Sqlwait*を使用して、R でサーバーからの結果を待機するかどうかを指定します。  待機中のジョブと待機中でないジョブの詳細については、 [Microsoft R での RevoScaleR を使用した分散コンピューティングと並列コンピューティング](https://docs.microsoft.com/r-server/r/how-to-revoscaler-distributed-computing)に関する記事をご覧ください。
  
    - *Sqlconsoleoutput*引数を使用して、R コンソールからの出力を表示しないことを指定します。

4. [RxInSqlServer](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxinsqlserver)コンストラクターを呼び出して、既に定義されている変数と接続文字列を使用して計算コンテキストオブジェクトを作成し、R 変数*sqlcc*に新しいオブジェクトを保存します。
  
    ```R
    sqlcc <- RxInSqlServer(connectionString = connStr, shareDir = sqlShareDir, wait = sqlWait, consoleOutput = sqlConsoleOutput)
    ```

5. 既定では、計算コンテキストはローカルであるため、*アクティブな*計算コンテキストを明示的に設定する必要があります。

    ```R
    rxSetComputeContext(sqlcc)
    ```

    + [rxSetComputeContext](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsetcomputecontext)は、以前にアクティブなコンピューティングコンテキストを非表示にして、使用できるようにします。
    + [rxGetComputeContext](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsetcomputecontext)はアクティブなコンピューティングコンテキストを返します
    
    コンピューティングコンテキストの設定は、 **RevoScaleR**パッケージ内の関数を使用する操作にのみ影響することに注意してください。コンピューティングコンテキストは、オープンソースの R 操作の実行方法には影響しません。

## <a name="create-a-data-source-using-rxsqlserver"></a>RxSqlServer を使用してデータソースを作成する

RevoScaleR などの Microsoft R ライブラリを使用する場合、*データソース*は RevoScaleR 関数を使用して作成するオブジェクトです。 データソースオブジェクトは、モデルのトレーニングや特徴の抽出など、タスクに使用するデータのセットを指定します。 SQL Server を含むさまざまなソースからデータを取得できます。 現在サポートされているソースの一覧については、「 [RxDataSource](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxdatasource)」を参照してください。

前に、接続文字列を定義し、その情報を R 変数に保存しました。 この接続情報を再利用して、取得するデータを指定できます。

1. SQL クエリを文字列変数として保存します。 このクエリでは、モデルをトレーニングするためのデータを定義します。

    ```R
    sampleDataQuery <- "SELECT TOP 1000 tipped, fare_amount, passenger_count,trip_time_in_secs,trip_distance, pickup_datetime, dropoff_datetime, pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude FROM nyctaxi_sample"
    ```

    ここでは、実行速度を向上させるために TOP 句を使用していましたが、クエリによって返される実際の行は、順序によって異なる場合があります。 そのため、概要の結果が以下に示すものと異なる場合もあります。 TOP 句を削除してもかまいません。

2. [RxSqlServerData](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxsqlserverdata) 関数にクエリの定義を引数として渡します。

    ```R
    inDataSource <- RxSqlServerData(
      sqlQuery = sampleDataQuery,
      connectionString = connStr,
      colClasses = c(pickup_longitude = "numeric", pickup_latitude = "numeric",
      dropoff_longitude = "numeric", dropoff_latitude = "numeric"),
      rowsPerRead=500
      )
    ```
    
    + 引数  *colClasses* により、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と R の間でデータを移動するときに使用する列の型が指定されます。これが重要なのは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が R と異なるデータ型とその他のデータ型を使用するためです。 詳細については、「 [R ライブラリとデータ型](../r/r-libraries-and-data-types.md)」を参照してください。
  
    + 引数*Rowsperread*は、メモリ使用量と効率的な計算を管理するために重要です。  [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)] 内の拡張分析関数の大部分は、データをチャンク単位で処理し、中間結果を蓄積していきます。すべてのデータを読み取ったら、最終的な計算結果を返します。  *Rowsperread*パラメーターを追加することによって、処理のために各チャンクに読み込まれるデータの行数を制御できます。  このパラメーターの値が大きすぎると、このような大量のデータを効率的に処理するのに十分なメモリがないため、データアクセスの速度が低下する可能性があります。  一部のシステムでは、 *Rowsperread*を極端に小さい値に設定すると、パフォーマンスが低下することがあります。

3. この時点では、 *Indatasource*オブジェクトを作成しましたが、データは含まれていません。 [RxImport](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxdatastep)や[rxSummary](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxsummary)などの関数を実行するまで、データは SQL クエリからローカル環境にプルされません。

    ただし、データオブジェクトを定義したので、それを他の関数の引数として使用することができます。

## <a name="use-the-sql-server-data-in-r-summaries"></a>R の概要で SQL Server データを使用する

このセクションでは、リモートコンピューティングコンテキストをサポートするで[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]提供されている関数をいくつか試してみます。 データソースに R 関数を適用すると、データの[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]探索、要約、およびグラフ化を行うことができます。

1. 関数[rxGetVarInfo](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxgetvarinfo)を呼び出して、データソース内の変数とそのデータ型の一覧を取得します。

    **rxGetVarInfo**は便利な関数です。任意のデータフレームまたはリモートデータオブジェクト内のデータセットに対してこのメソッドを呼び出すことにより、最大値、最小値、データ型、係数列のレベル数などの情報を取得できます。
    
    あらゆる種類のデータ入力、機能変換、または機能エンジニアリングの後にこの関数を実行することを検討してください。 これにより、モデルで使用するすべての機能が予期されるデータ型であることを確認し、エラーを回避できます。
  
    ```R
    rxGetVarInfo(data = inDataSource)
    ```

    **結果**
    
    ```R
    Var 1: tipped, Type: integer
    Var 2: fare_amount, Type: numeric
    Var 3: passenger_count, Type: integer
    Var 4: trip_time_in_secs, Type: numeric, Storage: int64
    Var 5: trip_distance, Type: numeric
    Var 6: pickup_datetime, Type: character
    Var 7: dropoff_datetime, Type: character
    Var 8: pickup_longitude, Type: numeric
    Var 9: pickup_latitude, Type: numeric
    Var 10: dropoff_longitude, Type: numeric
    ```

2. 次に、RevoScaleR 関数[rxSummary](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxsummary)を呼び出して、個々の変数に関する詳細な統計情報を取得します。

    rxSummary は R `summary`関数に基づいていますが、いくつかの追加機能と利点があります。 rxSummary は複数のコンピューティングコンテキストで機能し、チャンキングをサポートしています。  また、rxSummary を使用して値を変換したり、係数レベルに基づいて集計したりすることもできます。
    
    この例では、乗客の数に基づいて料金 amount を集計します。
    
    ```R
    start.time <- proc.time()
    rxSummary(~fare_amount:F(passenger_count,1,6), data = inDataSource)
    used.time <- proc.time() - start.time
    print(paste("It takes CPU Time=", round(used.time[1]+used.time[2],2)," seconds,
      Elapsed Time=", round(used.time[3],2),
      " seconds to summarize the inDataSource.", sep=""))
    ```
    + RxSummary の最初の引数は、集計する数式または用語を指定します。 ここでは、 _\__ 関数を使用して、乗客数の値を集計前に`F()`係数に変換します。 また、_乗客\_のカウント_係数変数の最小値 (1) と最大値 (6) を指定する必要があります。
    + 出力する統計情報を指定しなかった場合、既定では rxSummary は平均、StDev、最小値、最大値、および有効な観測値および不足している観測の数を出力します。
    + この例には、パフォーマンスを比較できるように、関数の開始時間と終了時間を追跡するコードも含まれています。
  
    **結果**

    RxSummary 関数が正常に実行された場合は、次のような結果が表示され、その後にカテゴリ別の統計情報の一覧が表示されます。 

    ```R
    rxSummary(formula = ~fare_amount:F(passenger_count, 1,6), data = inDataSource)
    Data: inDataSource (RxSqlServerData Data Source)
    Number of valid observations: 1000
    ```

### <a name="bonus-exercise-on-big-data"></a>ビッグデータに対するボーナスの行使

すべての行を含む新しいクエリ文字列を定義してみてください。 この実験用に新しいデータソースオブジェクトを設定することをお勧めします。 また、 *rowsToRead*パラメーターを変更して、スループットにどのように影響するかを確認することもできます。

```R
bigDataQuery  <- "SELECT tipped, fare_amount, passenger_count,trip_time_in_secs,trip_distance, pickup_datetime, dropoff_datetime, pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude FROM nyctaxi_sample"

bigDataSource <- RxSqlServerData(
      sqlQuery = bigDataQuery,
      connectionString = connStr,
      colClasses = c(pickup_longitude = "numeric", pickup_latitude = "numeric",
      dropoff_longitude = "numeric", dropoff_latitude = "numeric"),
      rowsPerRead=500
      )

start.time <- proc.time()
rxSummary(~fare_amount:F(passenger_count,1,6), data = bigDataSource)
used.time <- proc.time() - start.time
print(paste("It takes CPU Time=", round(used.time[1]+used.time[2],2)," seconds,
  Elapsed Time=", round(used.time[3],2),
  " seconds to summarize the inDataSource.", sep=""))
```

> [!TIP]
> これが実行されている間に、[プロセスエクスプローラー](https://technet.microsoft.com/sysinternals/processexplorer.aspx)や SQL Profiler などのツールを使用して接続がどのように行われ、R コードが SQL Server services を使用して実行されるかを確認できます。
> 
> もう1つのオプションは、これらの[カスタムレポート](../r/monitor-r-services-using-custom-reports-in-management-studio.md)を使用して SQL Server で実行されている R ジョブを監視することです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [R を利用してグラフやプロットを作成する](walkthrough-create-graphs-and-plots-using-r.md)