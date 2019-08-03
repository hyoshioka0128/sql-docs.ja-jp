---
title: RevoScaleR rxImport を使用したメモリへのデータの読み込み
description: SQL Server で R 言語を使用してデータを読み込む方法についてのチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 0e498e2aff0f6c21d11e4c34439301f36119257f
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714924"
---
# <a name="load-data-into-memory-using-rximport-sql-server-and-revoscaler-tutorial"></a>RxImport を使用したメモリへのデータの読み込み (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

[RxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport)関数を使用すると、データソースのデータを、セッションメモリ内のデータフレームまたはディスク上の xdf ファイルに移動できます。 移動先としてファイルを指定しない場合、データはデータ フレームとしてメモリに格納されます。

この手順では、から[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]データを取得し、 **rxImport**関数を使用して、関心のあるデータをローカルファイルに格納する方法について説明します。 この方法を利用すると、データベースに対して再クエリすることなく、ローカルの計算コンテキストでデータを繰り返し分析できます。

## <a name="extract-a-subset-of-data-from-sql-server-to-local-memory"></a>SQL Server からローカルメモリにデータのサブセットを抽出する

リスクの高い個人だけを調べることをお勧めします。 の[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]ソーステーブルは大きくなるため、危険度の高い顧客に関する情報のみを取得する必要があります。 その後、そのデータをローカルワークステーションのメモリ内のデータフレームに読み込みます。

1. 計算コンテキストをローカル ワークステーションにリセットします。

    ```R
    rxSetComputeContext("local")
    ```

2. 新しい SQL Server データ ソース オブジェクトを作成し、 *sqlQuery* パラメーターに有効な SQL ステートメントを指定します。 この例では、リスク スコアが最上位になっている観察のサブセットが取得されます。 この方法なら、本当に必要なデータのみがローカル メモリに格納されます。

    ```R
    sqlServerProbDS \<- RxSqlServerData(
        sqlQuery = paste("SELECT * FROM ccScoreOutput2",
        "WHERE (ccFraudProb > .99)"),
        connectionString = sqlConnString)
    ```

3. 関数[rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport)を呼び出して、ローカル R セッションのデータフレームにデータを読み込みます。

    ```R
    highRisk <- rxImport(sqlServerProbDS)
    ```

    操作が成功した場合は、次のようなステータスメッセージが表示されます。"読み取られる行:35、処理された行数の合計:35、合計チャンク時間:0.036 秒 "

4. これで、危険度の高い観測がインメモリデータフレームに含まれるようになったので、さまざまな R 関数を使用してデータフレームを操作できます。 たとえば、リスクスコアで顧客を注文し、リスクの高い顧客の一覧を印刷することができます。

    ```R
    orderedHighRisk <- highRisk[order(-highRisk$ccFraudProb),]
    row.names(orderedHighRisk) <- NULL
    head(orderedHighRisk)
    ```

**結果**

```R
ccFraudLogitScore   state gender cardholder balance numTrans numIntlTrans creditLine ccFraudProb1
9.786345    SD   Male  Principal   23456       25            5 75   0.99994382
9.433040    FL Female  Principal   20629       24           28 75   0.99992003
8.556785    NY Female  Principal   19064       82           53 43   0.99980784
8.188668    AZ Female  Principal   19948       29            0 75   0.99972235
7.551699    NY Female  Principal   11051       95            0 75   0.99947516
7.335080    NV   Male  Principal   21566        4            6  75   0.9993482
```

## <a name="more-about-rximport"></a>rxImport の詳細

**rxImport** は、データの移動だけでなく、読み取りプロセスでデータを変換する場合にも使用できます。 たとえば、幅が固定された列の文字数を指定する、変数の説明を指定する、要素列のレベルを設定する、さらにはインポート後に使用する新しいレベルを作成することもできます。

**RxImport**関数は、インポート処理中に変数名を列に割り当てますが、 *colinfo*パラメーターを使用して新しい変数名を指定することも、 *colclasses*パラメーターを使用してデータ型を変更することもできます。

*transforms* パラメーターで追加の操作を指定すると、読み取り対象の各データ群に対して基本的な処理を実行できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [rxDataStep を使用した新しい SQL Server テーブルの作成](../../advanced-analytics/tutorials/deepdive-move-data-between-sql-server-and-xdf-file.md)