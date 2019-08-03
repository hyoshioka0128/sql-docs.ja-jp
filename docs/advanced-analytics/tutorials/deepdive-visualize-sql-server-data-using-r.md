---
title: RevoScaleR rxHistogram を使用して SQL Server データを視覚化する
description: SQL Server で R 言語を使用してデータを視覚化する方法に関するチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 8c7e837f9ed4392a8ecfc9e7c237b95f3fdde3d3
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714851"
---
#  <a name="visualize-sql-server-data-using-r-sql-server-and-revoscaler-tutorial"></a>R を使用した SQL Server データの視覚化 (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

このレッスンでは、R 関数を使用して、 *creditLine*列の値の分布を性別別に表示します。

> [!div class="checklist"]
> * ヒストグラム入力用の最小値変数の作成
> * **RevoScaleR**の**rxHistogram**を使用してヒストグラムのデータを視覚化する
> * 基本 R 分布に含まれる**lattice**からの**levelplot**を使用して、散布図で視覚化する

このレッスンでは、オープンソースと Microsoft 固有の関数を同じスクリプトで組み合わせることができます。

## <a name="add-maximum-and-minimum-values"></a>最大値と最小値の追加

前のレッスンで計算した概要統計に基づいて、データソースに挿入してさらに計算するために使用できるデータに関する有益な情報を見つけました。 たとえば、最小値と最大値を使用して、ヒストグラムを計算できます。 この演習では、 **RxSqlServerData**データソースに高値と安値を追加します。

1. まず、仮の変数をいくつか設定します。
  
    ```R
    sumDF <- sumOut$sDataFrame
    var <- sumDF$Name
    ```
  
2. 前のレッスンで作成した変数*Cccolinfo*を使用して、データソースの列を定義します。
  
   新しい計算列 (*Numtrans*、 *numintltrans*、および*creditLine*) を、元の定義をオーバーライドする列コレクションに追加します。 次のスクリプトでは、 **rxSummary**からのインメモリ出力を格納している sumout から取得した最小値と最大値に基づいて要素が追加されています。 
  
    ```R 
    ccColInfo <- list(
        gender = list(type = "factor",
          levels = c("1", "2"), 
          newLevels = c("Male", "Female")),
        cardholder = list(type = "factor",
          levels = c("1", "2"), 
          newLevels = c("Principal", "Secondary")), 
        state = list(type = "factor", 
          levels = as.character(1:51), 
          newLevels = stateAbb), 
        balance  = list(type = "numeric"),
        numTrans = list(type = "factor", 
          levels = as.character(sumDF[var == "numTrans", "Min"]:sumDF[var == "numTrans", "Max"])),
        numIntlTrans = list(type = "factor",  
            levels = as.character(sumDF[var == "numIntlTrans", "Min"]:sumDF[var =="numIntlTrans", "Max"])),
        creditLine = list(type = "numeric")
            )
    ```
  
3. 列コレクションを更新した後、次のステートメントを適用して、前[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]に定義したデータソースの更新バージョンを作成します。
  
    ```R
    sqlFraudDS <- RxSqlServerData(
        connectionString = sqlConnString,
        table = sqlFraudTable,
        colInfo = ccColInfo,
        rowsPerRead = sqlRowsPerRead)
    ```
  
    SqlFraudDS データソースに、 *Cccolinfo*を使用して追加された新しい列が含まれるようになりました。
  
この時点で、変更は R のデータソースオブジェクトにのみ影響します。新しいデータはデータベーステーブルにまだ書き込まれていません。 ただし、sumOut 変数にキャプチャされたデータを使用して、視覚化と概要を作成することができます。 

> [!TIP]
> 使用しているコンピューティングコンテキストを忘れた場合は、 **rxGetComputeContext ()** を実行します。 "RxLocalSeq Compute Context" という戻り値は、ローカルの計算コンテキストで実行されていることを示します。

## <a name="visualize-data-using-rxhistogram"></a>RxHistogram を使用してデータを表示する

1. 次の R コードを使用して [rxHistogram](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxhistogram) 関数を呼び出し、式とデータ ソースを渡します。 ローカルで最初にこの操作を実行することで、予想される結果と、所要時間を確認できます。
  
    ```R
    rxHistogram(~creditLine|gender, data = sqlFraudDS,  histType = "Percent")
    ```
 
    内部的には、 **rxHistogram** は、 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxcube) パッケージに含まれる **rxCube** 関数を呼び出します。 **rxCube**は、式で指定された各変数の1つの列を含む1つのリスト (またはデータフレーム) とカウント列を出力します。
    
2. 次に、コンピューティングコンテキストをリモート SQL Server コンピューターに設定し、 **rxHistogram**を再度実行します。
  
    ```R
    rxSetComputeContext(sqlCompute)
    rxHistogram(~creditLine|gender, data = sqlFraudDS,  histType = "Percent")
    ```
 
3. 同じデータソースを使用しているため、結果はまったく同じですが、2番目の手順では、計算がリモートサーバー上で実行されます。 結果は、表示のためにローカル ワークステーションに返されます。
   
  ![ヒストグラム結果](media/rsql-sue-histogramresults.jpg "ヒストグラム結果")


## <a name="visualize-with-scatter-plots"></a>散布図で視覚化する

散布図は、データ探索時に2つの変数間の関係を比較するためによく使用されます。 この目的では、 **RevoScaleR**関数によって提供される入力を使用して、組み込みの R パッケージを使用できます。

1. *Numtrans*と*Numintltrans*のすべての組み合わせについて、*対する fraudrisk*の平均を計算するには、 [rxCube](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxcrosstabs)関数を呼び出します。
  
    ```R
    cube1 <- rxCube(fraudRisk~F(numTrans):F(numIntlTrans),  data = sqlFraudDS)
    ```
  
    グループの平均の計算に使用するグループを指定するには、 `F()` 表記を使用します。 この例では`F(numTrans):F(numIntlTrans)` 、は、 `numIntlTrans`変数`numTrans`内の整数がカテゴリ変数として扱われることを示します。これは、各整数値のレベルを持ちます。
  
    **RxCube**の既定の戻り値は、クロス集計を表す*rxCube オブジェクト*です。 
  
2. [RxResultsDF](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxresultsdf)関数を呼び出して、R の標準プロット関数のいずれかで簡単に使用できるデータフレームに結果を変換します。
  
    ```R
    cubePlot <- rxResultsDF(cube1)
    ```
  
    **RxCube**関数には、結果をデータフレームに直接変換するために使用できる、省略可能な引数*returnDataFrame* = **TRUE**が含まれています。 例:
    
    `print(rxCube(fraudRisk~F(numTrans):F(numIntlTrans), data = sqlFraudDS, returnDataFrame = TRUE))`
       
    ただし、 **rxResultsDF**の出力はクリーナーで、ソース列の名前が保持されます。 の後に`head(cube1)`を`head(cubePlot)`実行すると、出力を比較できます。
  
3. **Lattice**パッケージの**levelplot**関数を使用して、すべての R ディストリビューションに含まれるヒートマップを作成します。
  
    ```R
    levelplot(fraudRisk~numTrans*numIntlTrans, data = cubePlot)
    ```
  
    **結果**
  
    ![散布図の結果](media/rsql-sue-scatterplotresults.jpg "散布図の結果")
  
このクイック分析から、トランザクション数と国際取引数の両方によって不正行為のリスクが増加していることがわかります。

**RxCube**関数とクロス集計表全般の詳細については、「 [RevoScaleR を使用したデータの概要](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-data-summaries)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [SQL Server データを使用して R モデルを作成する](../../advanced-analytics/tutorials/deepdive-create-models.md)