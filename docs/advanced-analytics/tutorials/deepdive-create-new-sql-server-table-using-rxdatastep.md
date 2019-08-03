---
title: RevoScaleR rxDataStep を使用して新しい SQL Server テーブルを作成する
description: SQL Server で R 言語を使用して SQL Server テーブルを作成する方法についてのチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: b18f2bd42070746551d21ff7508e7fce58b49037
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714908"
---
# <a name="create-new-sql-server-table-using-rxdatastep-sql-server-and-revoscaler-tutorial"></a>RxDataStep を使用して新しい SQL Server テーブルを作成する (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

このレッスンでは、インメモリデータフレーム、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]コンテキスト、およびローカルファイルの間でデータを移動する方法について説明します。

> [!NOTE]
> このレッスンでは、別のデータセットを使用します。 航空会社の遅延データセットは、機械学習の実験に広く使用されているパブリックデータセットです。 この例で使用されるデータファイルは、他の製品サンプルと同じディレクトリにあります。

## <a name="load-data-from-a-local-xdf-file"></a>ローカル XDF ファイルからのデータの読み込み

このチュートリアルの前半では、 **RxTextData**関数を使用してテキストファイルから R にデータをインポートし、 **RxDataStep**関数を使用してデータをに[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]移動しています。

このレッスンでは、さまざまなアプローチを採用し、 [Xdf 形式](https://en.wikipedia.org/wiki/Extensible_Data_Format)で保存されたファイルのデータを使用します。 Xdf ファイルを使用してデータの軽量な変換を行った後、変換されたデータ[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]を新しいテーブルに保存します。

**XDF とは**

XDF 形式は、高次元データ用に開発された XML 標準であり、 [Machine Learning Server](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-xdf)によって使用されるネイティブファイル形式です。 XDF は、R インターフェイスを使用したバイナリ ファイル形式です。行と列の処理と分析を最適化しています。  XDF 形式は、データの移動や、分析に有用なデータのサブセットを格納するために使用できます。

1. 計算コンテキストをローカル ワークステーションに設定します。 **この手順には DDL のアクセス許可が必要です。**

    ```R
    rxSetComputeContext("local")
    ```
  
2. **RxXdfData** 関数を使用して新しいデータ ソース オブジェクトを定義します。 XDF データソースを定義するには、データファイルへのパスを指定します。  

    テキスト変数を使用して、ファイルへのパスを指定できます。 ただし、この場合は便利なショートカットがあります。これは、 **rxGetOption**関数を使用し、サンプルデータディレクトリからファイル (放映 Linedemosmall. xdf) を取得するためのものです。
  
    ```R
    xdfAirDemo <- RxXdfData(file.path(rxGetOption("sampleDataDir"),  "AirlineDemoSmall.xdf"))
    ```

3. インメモリ データに対して [rxGetVarInfo](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxgetvarinfoxdf) を呼び出して、データセットの概要を表示します。
  
    ```R
    rxGetVarInfo(xdfAirDemo)
    ```

**結果**

```R
Var 1: ArrDelay, Type: integer, Low/High: (-86, 1490)
Var 2: CRSDepTime, Type: numeric, Storage: float32, Low/High: (0.0167, 23.9833)
Var 3: DayOfWeek 7 factor levels: Monday Tuesday Wednesday Thursday Friday Saturday Sunday
```

> [!NOTE]
> 
> ご覧のように、データを XDF ファイルに読み込むために他の関数を呼び出す必要がなく、データに対して **rxGetVarInfo** をすぐに呼び出すことができます。 これは、XDF が**RevoScaleR**の既定の中間ストレージメソッドであるためです。 XDF ファイルに加えて、 **rxGetVarInfo**関数では複数のソースの種類がサポートされるようになりました。

## <a name="move-contents-to-sql-server"></a>コンテンツを SQL Server に移動します

ローカル R セッションで XDF データソースが作成されたので、このデータをデータベーステーブルに移動し、 *DayOfWeek*を 1 ~ 7 の値を持つ整数として格納できるようになりました。

1. データを格納するテーブルとリモートサーバーへの接続を指定して、SQL Server データソースオブジェクトを定義します。
  
    ```R
    sqlServerAirDemo <- RxSqlServerData(table = "AirDemoSmallTest", connectionString = sqlConnString)
    ```
  
2. 念のため、同じ名前のテーブルが既に存在するかどうかを確認する手順を含め、テーブルが存在する場合は削除します。 同じ名前の既存のテーブルでは、新しいテーブルを作成できません。
  
    ```R
    if (rxSqlServerTableExists("AirDemoSmallTest",  connectionString = sqlConnString))  rxSqlServerDropTable("AirDemoSmallTest",  connectionString = sqlConnString)
    ```
  
3. **RxDataStep**を使用してテーブルにデータを読み込みます。 この関数は、既に定義されている2つのデータソース間でデータを移動し、必要に応じてデータを変換できます。
  
    ```R
    rxDataStep(inData = xdfAirDemo, outFile = sqlServerAirDemo,
        transforms = list( DayOfWeek = as.integer(DayOfWeek),
        rowNum = .rxStartRow : (.rxStartRow + .rxNumRows - 1) ),
        overwrite = TRUE )
    ```
  
    これは非常に大きなテーブルであるため、次のような最終的なステータスメッセージが表示されるまで待ちます。*読み取られる行:20万、処理された行数の合計:60万*。
     
## <a name="load-data-from-a-sql-table"></a>SQL テーブルからのデータの読み込み

テーブルにデータが存在する場合は、単純な SQL クエリを使用してデータを読み込むことができます。 

1. 新しい SQL Server データソースを作成します。 この入力は、先ほど作成してデータと共に読み込まれた新しいテーブルに対するクエリです。 この定義では、 **RxSqlServerData**に*colinfo*引数を使用して、 *DayOfWeek*列の係数レベルを追加します。
  
    ```R
    sqlServerAirDemo2 <- RxSqlServerData(
        sqlQuery = "SELECT * FROM AirDemoSmallTest",
        connectionString = sqlConnString,
        rowsPerRead = 50000,
        colInfo = list(DayOfWeek = list(type = "factor",  levels = as.character(1:7))))
    ```
  
2. **RxSummary**をもう一度呼び出して、クエリ内のデータの概要を確認します。
  
    ```R
    rxSummary(~., data = sqlServerAirDemo2)
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [rxDataStep を使用したチャンク分析の実行](../../advanced-analytics/tutorials/deepdive-perform-chunking-analysis-using-rxdatastep.md)