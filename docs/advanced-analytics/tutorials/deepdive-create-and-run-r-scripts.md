---
title: Compute summary statistics RevoScaleR のチュートリアル
description: SQL Server で R 言語を使用して統計概要統計を計算する方法に関するチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 90a674057845427fe60fd3c62268bf0fb3d18688
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715572"
---
# <a name="compute-summary-statistics-in-r-sql-server-and-revoscaler-tutorial"></a>R の計算概要統計 (SQL Server および RevoScaleR チュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

前のレッスンで作成した、確立されたデータソースと計算コンテキストを使用して、高電力の R スクリプトを実行します。 このレッスンでは、次のタスクのために、ローカルとリモートのサーバーコンピューティングコンテキストを使用します。

> [!div class="checklist"]
> * コンピューティングコンテキストを SQL Server に切り替えます
> * リモートデータオブジェクトの概要統計情報の取得
> * ローカルの概要を計算する

前のレッスンを完了している場合は、sqlCompute および sqlComputeTrace というリモート計算コンテキストが必要です。 今後のレッスンでは、sqlCompute とローカルコンピューティングコンテキストを使用します。

このレッスンでは、r IDE または**Rgui**を使用して r スクリプトを実行します。

## <a name="compute-summary-statistics-on-remote-data"></a>リモートデータの計算概要統計

R コードをリモートで実行するには、リモートの計算コンテキストを指定する必要があります。 後続のすべての計算は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] *sqlcompute*パラメーターに指定されたコンピューター上で行われます。

コンピューティングコンテキストは、変更するまでアクティブなままです。 ただし、リモートサーバーコンテキストで実行*できない*すべての R スクリプトは、自動的にローカルで実行されます。

コンピューティングコンテキストがどのように動作するかを確認するには、リモート SQL Server の sqlFraudDS データソースに関する概要統計情報を生成します。 このデータソースオブジェクトは[レッスン 2](deepdive-create-sql-server-data-objects-using-rxsqlserverdata.md)で作成され、RevoDeepDive データベースの ccFraudSmall テーブルを表しています。 

1. 前のレッスンで作成した sqlCompute にコンピューティングコンテキストを切り替えます。
  
    ```R
    rxSetComputeContext(sqlCompute)
    ```

2. [RxSummary](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsummary)関数を呼び出し、式やデータソースなどの必須の引数を渡して、結果を変数`sumOut`に代入します。
  
    ```R
    sumOut <- rxSummary(formula = ~gender + balance + numTrans + numIntlTrans + creditLine, data = sqlFraudDS)
    ```
  
    R 言語には多くの概要関数が用意されていますが、 **RevoScaleR**の**rxSummary**は[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]、などのさまざまなリモート計算コンテキストでの実行をサポートしています。 同様の関数の詳細については、「 [RevoScaleR を使用したデータの概要](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-data-summaries)」を参照してください。
  
3. SumOut の内容をコンソールに出力します。
  
    ```R
    sumOut
    ```
    > [!NOTE]
    > エラーが発生した場合は、実行が完了するまで数分待ってから、コマンドを再試行してください。

**結果**

```R
Summary Statistics Results for: ~gender + balance + numTrans + numIntlTrans + creditLine
Data: sqlFraudDS (RxSqlServerData Data Source)
Number of valid observations: 10000

 Name  Mean    StdDev  Min Max ValidObs    MissingObs
 balance       4075.0318 3926.558714            0   25626 100000
 numTrans        29.1061   26.619923 0     100 10000    0           100000
 numIntlTrans     4.0868    8.726757 0      60 10000    0           100000
 creditLine       9.1856    9.870364 1      75 10000    0          100000
 
 Category Counts for gender
 Number of categories: 2
 Number of valid observations: 10000
 Number of missing observations: 0

 gender Counts
  Male   6154
  Female 3846
```

## <a name="create-a-local-summary"></a>ローカルの概要を作成する

1. すべての作業をローカルに行うように計算コンテキストを変更します。
  
    ```R
    rxSetComputeContext ("local")
    ```
  
2. SQL Server からデータを抽出するときに、多くの場合、読み取りごとに抽出される行の数を増やすことで、パフォーマンスを向上させることができます。これは、増加したブロックサイズをメモリ内に確保できることを前提としています。 次のコマンドを実行して、データソースの*Rowsperread*パラメーターの値を増やします。 これまでは、 *rowsPerRead* の値は 5000 に設定されていました。
  
    ```R
    sqlServerDS1 <- RxSqlServerData(
       connectionString = sqlConnString,
       table = sqlFraudTable,
       colInfo = ccColInfo,
       rowsPerRead = 10000)
    ```

3. 新しいデータソースで**rxSummary**を呼び出します。
  
    ```R
    rxSummary(formula = ~gender + balance + numTrans + numIntlTrans + creditLine, data = sqlServerDS1)
    ```
  
   実際の結果は、 **コンピューターのコンテキストで** rxSummary [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を実行した場合と同じになるはずです。 ただし、処理は速くなる場合と遅くなる場合があります。 データは分析のためのローカル コンピューターに転送されるので、処理速度はデータベースへの接続に左右されます。

4. 次のいくつかのレッスンでは、リモートコンピューティングコンテキストに戻ります。

    ```R
    rxSetComputeContext(sqlCompute)
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [R を使用して SQL Server のデータを表示する](../../advanced-analytics/tutorials/deepdive-visualize-sql-server-data-using-r.md)