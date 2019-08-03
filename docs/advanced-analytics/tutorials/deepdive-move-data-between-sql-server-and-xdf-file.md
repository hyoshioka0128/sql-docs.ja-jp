---
title: RevoScaleR を使用して SQL Server と XDF ファイルの間でデータを移動する
description: SQL Server で XDF と R 言語を使用してデータを移動する方法に関するチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 7eb32a6ba915328a7f6a6baccdc948f534da1a09
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715548"
---
# <a name="move-data-between-sql-server-and-xdf-file-sql-server-and-revoscaler-tutorial"></a>SQL Server と XDF ファイルの間でのデータの移動 (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

この手順では、XDF ファイルを使用して、リモートとローカルの両方の計算コンテキストでデータを転送する方法について説明します。 XDF ファイルにデータを格納すると、データに対して変換を実行できます。

完了したら、ファイルのデータを使用して新しい[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]テーブルを作成します。 関数[rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep)は、データに変換を適用し、データフレームと xdf ファイル間の変換を実行できます。
  
## <a name="create-a-sql-server-table-from-an-xdf-file"></a>XDF ファイルから SQL Server テーブルを作成する

この演習では、クレジットカードの不正データを再度使用します。 このシナリオでは、カリフォルニア、オレゴン、ワシントンの各州のユーザーに対して追加の分析を実行するように求められています。 より効率的な方法として、ローカルコンピューターにのみこれらの状態のデータを格納し、性別、カード所有者、州、および残高の変数のみを使用することを決定しました。

1. 前の手順で`stateAbb`作成した変数を再利用して、含めるレベルを特定し、新しい`statesToKeep`変数に書き込みます。
  
    ```R
    statesToKeep <- sapply(c("CA", "OR", "WA"), grep, stateAbb)
    statesToKeep
    ```
    **結果**
    
    CA|スイッチまたは|WA
    ----|----|----
    5|38|48
    
2. [!INCLUDE[tsql](../../includes/tsql-md.md)]クエリを使用して、SQL Server から移動するデータを定義します。  後で、この変数を**rxImport**の*indata*引数として使用します。
  
    ```R
    importQuery <- paste("SELECT gender,cardholder,balance,state FROM",  sqlFraudTable,  "WHERE (state = 5 OR state = 38 OR state = 48)")
    ```
  
    クエリにライン フィードやタブなどの隠し文字がないことを確認します。
  
3. 次に、R でデータを操作するときに使用する列を定義します。たとえば、小さいデータセットでは、3つの要素レベルだけが必要です。これは、クエリが返すデータの状態が3つだけであるためです。  `statesToKeep`変数を適用して、含める適切なレベルを特定します。
  
    ```R
    importColInfo <- list(
        gender = list( type = "factor",  levels = c("1", "2"), newLevels = c("Male", "Female")),
        cardholder = list(  type = "factor",  levels = c("1", "2"), newLevels = c("Principal", "Secondary")),
        state = list(   type = "factor",  levels = as.character(statesToKeep), newLevels = names(statesToKeep))
            )
    ```
  
4. ローカルコンピューターですべてのデータを使用できるようにするため、コンピューティングコンテキストを**ローカル**に設定します。
  
    ```R
    rxSetComputeContext("local")
    ```
    
    [RxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqlserverdata)関数は、サポートされている任意のデータソースからローカル xdf ファイルにデータをインポートできます。 データのローカルコピーを使用すると、データに対してさまざまな分析を行う場合に便利ですが、同じクエリを何度も実行する必要がなくなります。

5. 以前に**RxSqlServerData**の引数として定義した変数を渡して、データソースオブジェクトを作成します。
  
    ```R
    sqlServerImportDS <- RxSqlServerData(
        connectionString = sqlConnString,
        sqlQuery = importQuery,
        colInfo = importColInfo)
    ```
  
6. **RxImport**を呼び出して、現在の作業ディレクトリ内`ccFraudSub.xdf`のという名前のファイルにデータを書き込みます。
  
    ```R
    localDS <- rxImport(inData = sqlServerImportDS,
        outFile = "ccFraudSub.xdf",
        overwrite = TRUE)
    ```
  
    RxImport 関数によって返される`ccFraud.xdf` オブジェクトは、ディスク上にローカルに格納されているデータファイルを表す軽量のrxxdfdataデータ`localDs`ソースオブジェクトです。
  
7. XDF ファイルで [rxGetVarInfo](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxgetvarinfoxdf) を呼び出し、データ スキーマが同じであることを確認します。
  
    ```R
    rxGetVarInfo(data = localDS)
    ```

    **結果**
    
    ```R
    rxGetVarInfo(data = localDS)
    Var 1: gender, Type: factor, no factor levels available
    Var 2: cardholder, Type: factor, no factor levels available
    Var 3: balance, Type: integer, Low/High: (0, 22463)
    Var 4: state, Type: factor, no factor levels available
    ```

8. これで、SQL Server のソースデータの場合と同様に、さまざまな R 関数を呼び出して**Localds**オブジェクトを分析できるようになりました。 たとえば、性別によって集計することができます。
  
    ```R
    rxSummary(~gender + cardholder + balance + state, data = localDS)
    ```

## <a name="next-steps"></a>次の手順

このレッスンでは、 **RevoScaleR**と SQL Server のマルチパートチュートリアルシリーズを終了します。 ここでは、さまざまなデータ関連および計算の概念を紹介し、独自のデータとプロジェクトの要件に移行するための基盤を提供しています。

**RevoScaleR**の知識を深めていただくために、R チュートリアルの一覧に戻って、見逃したと思われる演習を進めることができます。 または、目次にある操作方法に関する記事を参照して、一般的なタスクに関する情報を確認してください。

> [!div class="nextstepaction"]
> [SQL Server の R チュートリアル](sql-server-r-tutorials.md)