---
title: RevoScaleR コンピューティングコンテキストを定義して使用する
description: SQL Server で R 言語を使用して計算コンテキストを定義する方法に関するチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: c5fb28af293c549a9f5494ab08c6c01ebf5d2a20
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715545"
---
# <a name="define-and-use-compute-contexts-sql-server-and-revoscaler-tutorial"></a>コンピューティングコンテキストの定義と使用 (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

前のレッスンでは、 **RevoScaleR**関数を使用してデータオブジェクトを検査しています。 このレッスンでは、 [RxInSqlServer](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinsqlserver)関数を導入します。これにより、リモート SQL Server の計算コンテキストを定義できます。 リモートの計算コンテキストを使用すると、R の実行をローカルセッションからサーバー上のリモートセッションにシフトできます。 

> [!div class="checklist"]
> * リモート SQL Server のコンピューティングコンテキストの要素について説明します
> * コンピューティングコンテキストオブジェクトでトレースを有効にする

**RevoScaleR**では、複数のコンピューティングコンテキストがサポートされています。Hadoop、HDFS 上の Spark、およびデータベース内の SQL Server。 SQL Server の場合、 **RxInSqlServer**関数はサーバー接続に使用され、ローカルコンピューターとリモート実行コンテキストの間でオブジェクトを渡します。

## <a name="create-and-set-a-compute-context"></a>コンピューティングコンテキストを作成および設定する

SQL Server の計算コンテキストを作成する**RxInSqlServer**関数は、次の情報を使用します。

+ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスの接続文字列
+ 出力の処理方法の指定
+ 共有データディレクトリの指定 (省略可能)
+ トレースを有効にするかトレースレベルを指定する省略可能な引数

このセクションでは、それぞれの手順について説明します。

1. 計算を実行するインスタンスの接続文字列を指定します。 前の手順で作成した接続文字列を再利用できます。

    **SQL ログインを使用する**

    ```R
    sqlConnString <- "Driver=SQL Server;Server=<SQL Server instance name>; Database=<database name>;Uid=<SQL user nme>;Pwd=<password>"
      ```

    **Windows 認証を使用する**

    ```R
    sqlConnString <- "Driver=SQL Server;Server=instance_name;Database=RevoDeepDive;Trusted_Connection=True"
    ```
    
2. 出力の処理方法を指定します。 次の操作を処理する前に、サーバー上で R ジョブの結果を待機するようにローカル R セッションに指示するスクリプトを次に示します。 また、リモート計算からの出力がローカルセッションに表示されないようにします。
  
    ```R
    sqlWait <- TRUE
    sqlConsoleOutput <- FALSE
    ```
  
    *RxInSqlServer* に渡す **wait** 引数は、次のオプションをサポートします。
  
    -   **TRUE**。 ジョブはブロックとして構成され、完了するか失敗するまで戻りません。  詳細については、「 [Machine Learning Server での分散コンピューティングと並列コンピューティング](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing)」を参照してください。
  
    -   **FALSE**。 ジョブは非ブロッキングとして構成され、直ちに返されます。これにより、他の R コードの実行を続けることができます。 ただし、非ブロック モードであっても、ジョブの実行中は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] とのクライアント接続を維持する必要があります。

3. 必要に応じて、ローカルの R セッションおよびリモート[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]コンピューターとそのアカウントによって共有されるローカルディレクトリの場所を指定します。

    ```R
    sqlShareDir <- paste("c:\\AllShare\\", Sys.getenv("USERNAME"), sep="")
    ```
    
   共有用に特定のディレクトリを手動で作成する場合は、次のような行を追加します。

    ```R
    dir.create(sqlShareDir, recursive = TRUE)
    ```

4. **RxInSqlServer**コンストラクターに引数を渡して、*計算コンテキストオブジェクト*を作成します。

    ```R
    sqlCompute <- RxInSqlServer(  
         connectionString = sqlConnString,
         wait = sqlWait,
         consoleOutput = sqlConsoleOutput)
    ```
    
    **RxInSqlServer**の構文は、前にデータソースを定義するために使用した**RxSqlServerData**関数とほぼ同じです。 しかし、次に示すいくつかの重要な相違点があります。
      
    - [RxSqlServerData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqlserverdata)関数を使用して定義するデータ ソース オブジェクトは、データの保存場所を指定します。
    
    - これに対し、関数[RxInSqlServer](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinsqlserver)を使用して定義された計算コンテキストは、集計やその他の計算が行われる場所を示します。
    
    計算コンテキストを定義しても、ワークステーションで実行されるその他の一般的な R 計算に影響なく、データのソースは変わりません。 たとえば、データ ソースとしてローカル テキスト ファイルを定義できますが、計算コンテキストを SQL Server に変更し、すべての読み取りと集計を SQL Server コンピューター上のデータに対して行うことができます。

5. リモートコンピューティングコンテキストをアクティブにします。

    ```R
    rxSetComputeContext(sqlCompute)
    ```

6. プロパティを含む、コンピューティングコンテキストに関する情報を返します。

    ```R
    rxGetComputeContext()
    ```

7. "Local" キーワードを指定して、計算コンテキストをローカルコンピューターに戻します (次のレッスンでは、リモートコンピューティングコンテキストの使用方法を示します)。

    ```R
    rxSetComputeContext("local")
    ```

> [!Tip]
> この関数でサポートされているその他のキーワードの一覧が必要な場合、R コマンド ラインに「 `help("rxSetComputeContext")` 」と入力してください。

## <a name="enable-tracing"></a>トレースを有効にする

リモート計算コンテキストで実行すると、ローカル コンテキストで動作する操作で問題が発生することがあります。 問題を分析したりパフォーマンスを監視したりする場合は、実行時のトラブルシューティングをサポートするために、コンピューティングコンテキストでトレースを有効にすることができます。

1. 同じ接続文字列を使用する新しい計算コンテキストを作成しますが、引数*Traceenabled*と*traceLevel*を**RxInSqlServer**コンストラクターに追加します。

    ```R
    sqlComputeTrace <- RxInSqlServer(
        connectionString = sqlConnString,
        #shareDir = sqlShareDir,
        wait = sqlWait,
        consoleOutput = sqlConsoleOutput,
        traceEnabled = TRUE,
        traceLevel = 7)
    ```
  
   この例では、*traceLevel* プロパティを 7 に設定してあります。これは、"すべてのトレース情報を表示する" ことを意味します。

2. トレースが有効な計算コンテキストを名前で指定するには、 [rxSetComputeContext](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsetcomputecontext)関数を使用します。

    ```R
    rxSetComputeContext(sqlComputeTrace)
    ```

## <a name="next-steps"></a>次のステップ

サーバーまたはローカルで R コードを実行するように計算コンテキストを切り替える方法について説明します。

> [!div class="nextstepaction"]
> [ローカルとリモートの計算コンテキストで概要統計を計算する](../../advanced-analytics/tutorials/deepdive-create-and-run-r-scripts.md)