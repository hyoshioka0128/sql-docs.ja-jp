---
title: RevoScaleR rxDataStep を使用したチャンク分析の実行
description: SQL Server で R 言語を使用して分散分析用にデータをチャンクする方法に関するチュートリアル。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: ed22020b162bfac9f35eb8328ea6409903191a4c
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714895"
---
# <a name="perform-chunking-analysis-using-rxdatastep-sql-server-and-revoscaler-tutorial"></a>RxDataStep を使用したチャンク分析の実行 (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

このレッスンでは、 **rxDataStep**関数を使用して、データセット全体をメモリに読み込んで、従来の R のように一度に処理することを要求するのではなく、データをチャンク単位で処理します。**RxDataStep**関数は、チャンク内のデータを読み取り、各データチャンクに R 関数を適用して、各チャンクの概要結果を共通[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]データソースに保存します。 すべてのデータが読み込まれると、結果が結合されます。

> [!TIP]
> このレッスンでは、R で**table**関数を使用してコンティンジェンシーテーブルを計算します。この例は、説明のみを目的としています。 
> 
> 実際のデータセットを図表化する必要がある場合は、この種の操作に最適化された**RevoScaleR**の**RxCrossTabs**または**rxCube**関数を使用することをお勧めします。

## <a name="partition-data-by-values"></a>値によるデータのパーティション分割

1. データの各チャンクに対して R **table**関数を呼び出し、新しい関数**processchunk**という名前を指定するカスタム r 関数を作成します。
  
    ```R
    ProcessChunk <- function( dataList) {
    # Convert the input list to a data frame and compute contingency table
    chunkTable <- table(as.data.frame(dataList))
  
    # Convert table output to a data frame with a single row
    varNames <- names(chunkTable)
    varValues <- as.vector(chunkTable)
    dim(varValues) <- c(1, length(varNames))
    chunkDF <- as.data.frame(varValues)
    names(chunkDF) <- varNames
  
    # Return the data frame, which has a single row
    return( chunkDF )
    }
    ```

2. コンピューティング コンテキストをサーバーに設定します。
  
    ```R
    rxSetComputeContext(sqlCompute)
    ```
  
3. 処理中のデータを保持する SQL Server データソースを定義します。 最初に SQL クエリを変数に代入します。 次に、新しい SQL Server データソースの*Sqlquery*引数でその変数を使用します。
  
    ```R
    dayQuery <-  "SELECT DayOfWeek FROM AirDemoSmallTest"
    inDataSource <- RxSqlServerData(sqlQuery = dayQuery,
        connectionString = sqlConnString,
        rowsPerRead = 50000,
        colInfo = list(DayOfWeek = list(type = "factor",
            levels = as.character(1:7))))
    ```

4. 必要に応じて、このデータソースに対して**rxGetVarInfo**を実行できます。 この時点で、1つの列が含まれています。*Var 1:DayOfWeek、種類: factor、利用可能なファクターレベルなし*
     
5. この因子変数をソース データに適用する前に、中間結果を保持するための別のテーブルを作成します。 ここでも、 **RxSqlServerData**関数を使用してデータを定義するだけで、同じ名前の既存のテーブルを確実に削除することができます。
  
    ```R
    iroDataSource = RxSqlServerData(table = "iroResults",   connectionString = sqlConnString)
    # Check whether the table already exists.
    if (rxSqlServerTableExists(table = "iroResults",  connectionString = sqlConnString))  { rxSqlServerDropTable( table = "iroResults", connectionString = sqlConnString) }
    ```
  
7.  カスタム関数**Processchunk**を呼び出して、データを読み取るときに変換します。これを**RxDataStep**関数の*transformfunc*引数として使用します。
  
    ```R
    rxDataStep( inData = inDataSource, outFile = iroDataSource, transformFunc = ProcessChunk, overwrite = TRUE)
    ```
  
8.  **Processchunk**の中間結果を表示するには、 **rxImport**の結果を変数に代入し、結果をコンソールに出力します。
  
    ```R
    iroResults <- rxImport(iroDataSource)
    iroResults
    ```

    **結果の一部**

    |      |    1  |   2   |  3   |  4   |  5  |   6   |  7 |
    | --- | ---  | --- | ---  |  ---  | ---  | ---  | --- |
    | 1 | 8228 | 8924 | 6916 | 6932 | 6944 | 5602 | 6454 |
    | 2  | 8321  | 5351 | 7329 | 7411 | 7409 | 6487 | 7692 |

9. チャンク全体の最終結果を計算するには、列を合計し、結果をコンソールに表示します。

    ```R
    finalResults <- colSums(iroResults)
    finalResults
    ```

    **結果**

    1  |   2  |   3  |   4  |   5  |   6  |   7
    ---  |   ---  |   ---  |   ---  |   ---  |   ---  |   ---
    97975 | 77725 | 78875 | 81304 | 82987 | 86159 | 94975 

10. 中間結果テーブルを削除するには、 **rxSqlServerDropTable**を呼び出します。
  
    ```R
    rxSqlServerDropTable( table = "iroResults", connectionString = sqlConnString)
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [SQL Server の R チュートリアル](sql-server-r-tutorials.md)