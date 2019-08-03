---
title: R モデルを構築して SQL Server に保存する (チュートリアル)
description: データベース内分析 SQL Server に使用される R 言語モデルを構築する方法を示すチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/26/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: af2b1bf8f619800737863ff955011b011f4819d0
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715388"
---
# <a name="build-an-r-model-and-save-to-sql-server-walkthrough"></a>R モデルを構築して SQL Server に保存する (チュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この手順では、機械学習モデルを構築し、モデルをに[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]保存する方法について説明します。 モデルを保存することにより、システムストアドプロシージャ[!INCLUDE[tsql](../../includes/tsql-md.md)] 、 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 、または[PREDICT (t-sql) 関数](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql)を使用して、コードから直接呼び出すことができます。

## <a name="prerequisites"></a>必須コンポーネント

この手順では、このチュートリアルの前の手順に基づいて、R セッションが継続的に実行されることを前提としています。 ここでは、これらの手順で作成した接続文字列とデータソースオブジェクトを使用します。 スクリプトの実行には、次のツールとパッケージが使用されます。

+ R コマンドを実行するための rgui .exe
+ T-sql の実行 Management Studio
+ ROCR パッケージ
+ RODBC パッケージ

### <a name="create-a-stored-procedure-to-save-models"></a>モデルを保存するストアドプロシージャを作成する

この手順では、ストアドプロシージャを使用して、トレーニング済みのモデルを SQL Server に保存します。 この操作を実行するストアドプロシージャを作成すると、タスクが簡単になります。

Management Studio のクエリウィンドウで次の T-sql コードを実行して、ストアドプロシージャを作成します。

```sql
USE [NYCTaxi_Sample]
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'PersistModel')
  DROP PROCEDURE PersistModel
GO

CREATE PROCEDURE [dbo].[PersistModel] @m nvarchar(max)
AS
BEGIN
    -- SET NOCOUNT ON added to prevent extra result sets from
    -- interfering with SELECT statements.
    SET NOCOUNT ON;
    insert into nyc_taxi_models (model) values (convert(varbinary(max),@m,2))
END
GO
```

> [!NOTE]
> エラーが発生した場合は、ログインにオブジェクトを作成する権限があることを確認してください。 オブジェクトを作成するための明示的なアクセス許可を付与するには、次`exec sp_addrolemember 'db_owner', '<user_name>'`のような t-sql ステートメントを実行します。

## <a name="create-a-classification-model-using-rxlogit"></a>RxLogit を使用して分類モデルを作成する

このモデルは、タクシードライバーが特定の乗り物でチップを取得する可能性が高いかどうかを予測するバイナリ分類器です。 前のレッスンで作成したデータソースを使用して、ロジスティック回帰を使用して tip 分類子をトレーニングします。

1. [RevoScaleR](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxlogit) パッケージに含まれる **rxLogit** 関数を呼び出し、ロジスティック回帰モデルを作成します。 

    ```R
    system.time(logitObj <- rxLogit(tipped ~ passenger_count + trip_distance + trip_time_in_secs + direct_distance, data = featureDataSource));
    ```

    モデルを構築する呼び出しは、system.time 関数で囲まれます。 これにより、モデルの構築に必要な時間を取得できます。

2. モデルを作成した後、 `summary`関数を使用してモデルを検査し、係数を表示できます。

    ```R
    summary(logitObj);
    ```

    **結果**
    
    ```R
     *Logistic Regression Results for: tipped ~ passenger_count + trip_distance + trip_time_in_secs +*
     direct_distance* 
     *Data: featureDataSource (RxSqlServerData Data Source)*
     *Dependent variable(s): tipped*
     *Total independent variables: 5*
     *Number of valid observations: 17068*
     *Number of missing observations: 0*
     *-2\*LogLikelihood: 23540.0602 (Residual deviance on 17063 degrees of freedom)*
     *Coefficients:*
     *Estimate Std. Error z value Pr(>|z|)*
     *(Intercept)       -2.509e-03  3.223e-02  -0.078  0.93793*
     *passenger_count   -5.753e-02  1.088e-02  -5.289 1.23e-07 \*\*\**
     *trip_distance     -3.896e-02  1.466e-02  -2.658  0.00786 \*\**
     *trip_time_in_secs  2.115e-04  4.336e-05   4.878 1.07e-06 \*\*\**
     *direct_distance    6.156e-02  2.076e-02   2.966  0.00302 \*\**
     *---*
     *Signif. codes:  0 ‘\*\*\*’ 0.001 ‘\*\*’ 0.01 ‘\*’ 0.05 ‘.’ 0.1 ‘ ’ 1*
     *Condition number of final variance-covariance matrix: 48.3933*
     *Number of iterations: 4*
   ```

## <a name="use-the-logistic-regression-model-for-scoring"></a>スコアリングにロジスティック回帰モデルを使用する

モデルが構築されたので、そのモデルを使用して運転手が特定のドライブでチップを受け取る可能性が高いかどうかを予測できます。

1. 最初に、 [RxSqlServerData](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxsqlserverdata)関数を使用して、スコアリング結果を格納するためのデータソースオブジェクトを定義します。

    ```R
    scoredOutput <- RxSqlServerData(
      connectionString = connStr,
      table = "taxiScoreOutput"  )
    ```

    + この例を簡単にするために、ロジスティック回帰モデルへの入力は、モデルのトレーニング`sql_feature_ds`に使用したのと同じ特徴データソース () です。  多くの場合、スコアリングには新しいデータを使用したり、テスト用とトレーニング用のデータを別に確保したりします。
  
    + 予測結果は、 _taxiscoreoutput という_テーブルに保存されます。 RxSqlServerData を使用して作成した場合、このテーブルのスキーマは定義されていないことに注意してください。 スキーマは、rxPredict 出力から取得されます。
  
    + 予測値を格納するテーブルを作成するには、rxSqlServer データ関数を実行している SQL ログインにデータベースの DDL 権限が必要です。 ログインでテーブルを作成できない場合、ステートメントは失敗します。

2. [rxPredict](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxpredict) 関数を呼び出して結果を生成します。

    ```R
    rxPredict(modelObject = logitObj,
        data = featureDataSource,
        outData = scoredOutput,
        predVarNames = "Score",
        type = "response",
        writeModelVars = TRUE, overwrite = TRUE)
    ```
    
    ステートメントが成功した場合は、実行に時間がかかることがあります。 完了したら、SQL Server Management Studio を開いて、テーブルが作成されたこと、およびそのテーブルにスコア列およびその他の予想される出力が含まれていることを確認できます。

## <a name="plot-model-accuracy"></a>プロットモデルの精度

モデルの精度を把握するには、 [rxRoc](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxroc)関数を使用して、受信側の操作曲線をプロットします。 RxRoc は、リモートコンピューティングコンテキストをサポートする RevoScaleR パッケージによって提供される新しい関数の1つであるため、次の2つのオプションがあります。

+ RxRoc 関数を使用して、リモート計算コンテキストでプロットを実行し、プロットをローカルクライアントに返すことができます。

+ また、R クライアント コンピューターにデータをインポートして、他の R プロット関数を使用してパフォーマンス グラフを作成することもできます。

このセクションでは、両方の手法を試してみます。

### <a name="execute-a-plot-in-the-remote-sql-server-compute-context"></a>リモート (SQL Server) コンピューティング コンテキストでプロットを実行する

1. 関数 rxRoc を呼び出し、前の手順で定義したデータを入力として指定します。

    ```R
    scoredOutput = rxImport(scoredOutput);
    rxRoc(actualVarName= "tipped", predVarNames = "Score", scoredOutput);
    ```

    この呼び出しは、ROC チャートの計算に使用される値を返します。 [ラベル] 列には、予測しようとしている実際の結果が_表示され_、[_スコア_] 列には予測があります。

2. グラフを実際にプロットするには、ROC オブジェクトを保存し、プロット関数を使用して描画します。 グラフは、リモートの計算コンテキストで作成され、R 環境に返されます。

    ```R
    scoredOutput = rxImport(scoredOutput);
    rocObjectOut <- rxRoc(actualVarName= "tipped", predVarNames = "Score", scoredOutput);
    plot(rocObjectOut);
    ```

    グラフを表示するには、R グラフィックスデバイスを開くか、RStudio の **[プロット]** ウィンドウをクリックします。

    ![モデルの ROC プロット](media/rsql-e2e-rocplot.png "モデルの ROC プロット")

### <a name="create-the-plots-in-the-local-compute-context-using-data-from-sql-server"></a>SQL Server のデータを使用して、ローカル コンピューティング コンテキストでプロットを作成する

コマンドプロンプトでを実行`rxGetComputeContext()`することで、計算コンテキストがローカルであることを確認できます。 戻り値は "RxLocalSeq Compute Context" にする必要があります。

1. ローカルコンピューティングコンテキストでは、プロセスはほぼ同じです。 [RxImport](https://docs.microsoft.com/r-server/r-reference/revoscaler/rximport)関数を使用して、指定されたデータをローカルの R 環境に移動します。

    ```R
    scoredOutput = rxImport(scoredOutput)
    ```

2. ローカルメモリ内のデータを使用して、 **Rocr**パッケージを読み込み、そのパッケージの予測関数を使用して新しい予測を作成します。

    ```R
    library('ROCR');
    pred <- prediction(scoredOutput$Score, scoredOutput$tipped);
    ```

3. 出力変数`pred`に格納されている値に基づいて、ローカルプロットを生成します。

    ```R
    acc.perf = performance(pred, measure = 'acc');
    plot(acc.perf);
    ind = which.max( slot(acc.perf, 'y.values')[[1]] );
    acc = slot(acc.perf, 'y.values')[[1]][ind];
    cutoff = slot(acc.perf, 'x.values')[[1]][ind];
    ```

    ![R を使用したモデルのパフォーマンスのプロット](media/rsql-e2e-performanceplot.png "R を使用したモデルのパフォーマンスのプロット")

> [!NOTE]
> 使用するデータポイントの数によっては、グラフの外観が異なる場合があります。

## <a name="deploy-the-model"></a>モデルの配置

モデルを構築し、それが正常に実行されていることを確かめるしたら、おそらく、組織内のユーザーまたはユーザーがモデルを利用できるサイトに配置したり、定期的にモデルを再トレーニングしたり再調整したりすることができます。 このプロセスは、モデルの*運用*と呼ばれることもあります。 SQL Server では、ストアドプロシージャに R コードを埋め込むことによって運用化が実現されます。 コードはプロシージャ内に存在するので、SQL Server に接続できる任意のアプリケーションから呼び出すことができます。

外部アプリケーションからモデルを呼び出すには、実稼働環境で使用されるデータベースにモデルを保存する必要があります。 トレーニング済みのモデルは、 **varbinary (max)** 型の1つの列にバイナリ形式で格納されます。

一般的な配置ワークフローは、次の手順で構成されています。

1. モデルを16進数文字列にシリアル化する
2. シリアル化されたオブジェクトをデータベースに転送します。
3. Varbinary (max) 列にモデルを保存する

このセクションでは、ストアドプロシージャを使用してモデルを永続化し、予測に使用できるようにする方法について説明します。 このセクションで使用するストアドプロシージャは PersistModel です。 PersistModel の定義は[前提条件](#prerequisites)です。

1. まだ使用していない場合は、ローカルの R 環境に戻り、モデルをシリアル化して、変数に保存します。

    ```R
    rxSetComputeContext("local");
    modelbin <- serialize(logitObj, NULL);
    modelbinstr=paste(modelbin, collapse="");
    ```

2. **RODBC**を使用して ODBC 接続を開きます。 パッケージが既に読み込まれている場合は、RODBC への呼び出しを省略できます。

    ```R
    library(RODBC);
    conn <- odbcDriverConnect(connStr);
    ```

3. SQL Server に対して PersistModel ストアドプロシージャを呼び出して、シリアル化されたオブジェクトをデータベースに transmite し、モデルのバイナリ表現を列に格納します。 

    ```R
    q <- paste("EXEC PersistModel @m='", modelbinstr,"'", sep="");
    sqlQuery (conn, q);
    ```

4. Management Studio を使用して、モデルが存在することを確認します。 オブジェクトエクスプローラーで、 **nyc_taxi_models**テーブルを右クリックし、 **[上位1000行の選択]** をクリックします。 結果として、 **[モデル]** 列にバイナリ表現が表示されます。

モデルをテーブルへの保存に必要なステートメントは、INSERT のみです。 ただし、多くの場合、 *Persistmodel*などのストアドプロシージャにラップすると、より簡単になります。

## <a name="next-steps"></a>次の手順

次の最後のレッスンでは、を使用して、保存された[!INCLUDE[tsql](../../includes/tsql-md.md)]モデルに対してスコア付けを実行する方法について説明します。

> [!div class="nextstepaction"]
> [R モデルをデプロイして SQL で使用する](walkthrough-deploy-and-use-the-model.md)
