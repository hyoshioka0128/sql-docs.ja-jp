---
title: RevoScaleR rxDataStep を使用したデータの変換
description: SQL Server で R 言語を使用してデータを変換する方法に関するチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: c76bdf56febd06ecba6f2d9d11f1710eefdc23e6
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715511"
---
# <a name="transform-data-using-r-sql-server-and-revoscaler-tutorial"></a>R を使用したデータの変換 (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

このレッスンでは、分析のさまざまな段階でデータを変換するための**RevoScaleR**関数について説明します。

> [!div class="checklist"]
> * **RxDataStep**を使用してデータサブセットを作成および変換する
> * **RxImport**を使用して、インポート中に xdf ファイルまたはメモリ内のデータフレームとの間で転送中のデータを変換します。

データ移動専用ではありませんが、 **rxSummary**、 **rxCube**、 **rxLinMod**、 **rxLogit** の各関数もすべてデータ変換をサポートします。

## <a name="use-rxdatastep-to-transform-variables"></a>RxDataStep を使用して変数を変換する

[rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) 関数は一度に 1 つのチャンクのデータを処理し、1 つのデータ ソースからデータを読み取って別のデータ ソースに書き込みます。 変換する列や読み込む変換などを指定できます。

この例を興味深いものにするために、別の R パッケージの関数を使用してデータを変換してみましょう。 **boot** パッケージは "推奨" パッケージの 1 つであり、 **boot** は R のすべてのディストリビューションに含まれますが、起動時に自動的に読み込まれることはありません。 そのため、パッケージは、R 統合用に[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]構成されたインスタンスで既に使用可能である必要があります。

**ブート**パッケージから、関数**inv. logit**を使用します。これにより、logit の逆の計算が計算されます。 つまり、 **inv.logit** 関数はロジットを [0,1] のスケールで確率に変換します。

> [!TIP] 
> このスケールの予測を取得するもう 1 つの方法としては、 *rxPredict* の元の呼び出しで **type** パラメーターを **response**に設定します。

1. まず、テーブル`ccScoreOutput`に送信するデータを格納するデータソースを作成します。
  
    ```R
    sqlOutScoreDS <- RxSqlServerData( table =  "ccScoreOutput",  connectionString = sqlConnString, rowsPerRead = sqlRowsPerRead )
    ```
  
2. 別のデータソースを追加して、テーブル`ccScoreOutput2`のデータを保持します。
  
    ```R
    sqlOutScoreDS2 <- RxSqlServerData( table =  "ccScoreOutput2",  connectionString = sqlConnString, rowsPerRead = sqlRowsPerRead )
    ```
  
    新しいテーブルに、前`ccScoreOutput`の表のすべての変数と新しく作成した変数を格納します。
  
3. 計算コンテキストを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスに設定します。
  
    ```R
    rxSetComputeContext(sqlCompute)
    ```
  
4. 出力テーブル`ccScoreOutput2`が既に存在するかどうかを確認するには、関数**rxSqlServerTableExists**を使用します。その場合は、関数**rxSqlServerDropTable**を使用してテーブルを削除します。
  
    ```R
    if (rxSqlServerTableExists("ccScoreOutput2"))     rxSqlServerDropTable("ccScoreOutput2")
    ```
  
5. **rxDataStep** 関数を呼び出して、一覧で目的の変換を指定します。
  
    ```R
    rxDataStep(inData = sqlOutScoreDS,
        outFile = sqlOutScoreDS2,
        transforms = list(ccFraudProb = inv.logit(ccFraudLogitScore)),
        transformPackages = "boot",
        overwrite = TRUE)
    ```

    各列に適用される変換を定義するときは、変換の実行に必要な追加の R パッケージも指定できます。  実行できる変換の種類の詳細については、「 [RevoScaleR を使用してデータを変換およびサブセット化する方法](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-data-transform)」を参照してください。
  
6. **rxGetVarInfo** を呼び出して、新しいデータ セットの変数の概要を表示します。
  
```R
rxGetVarInfo(sqlOutScoreDS2)
```

**結果**

```R
Var 1: ccFraudLogitScore, Type: numeric
Var 2: state, Type: character
Var 3: gender, Type: character
Var 4: cardholder, Type: character
Var 5: balance, Type: integer
Var 6: numTrans, Type: integer
Var 7: numIntlTrans, Type: integer
Var 8: creditLine, Type: integer
Var 9: ccFraudProb, Type: numeric
```

元のロジット スコアは保持されますが、新しい列の *ccFraudProb*が追加されており、ロジット スコアが 0 ～ 1 の値として表示されます。

要素変数が文字データとしてテーブル`ccScoreOutput2`に書き込まれていることに注意してください。 以降の解析で因子として使用するには、 *colInfo* パラメーターを使用してレベルを指定します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [rxImport を使用してメモリにデータを読み込む](../../advanced-analytics/tutorials/deepdive-load-data-into-memory-using-rximport.md)