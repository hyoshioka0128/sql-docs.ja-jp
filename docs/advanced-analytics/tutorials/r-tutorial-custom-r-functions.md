---
title: RevoScaleR rxExec を使用して SQL Server でカスタム R 関数を実行する
description: RevoScaleR functions を使用して SQL Server でカスタム R スクリプトを実行する方法に関するチュートリアルのチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 439b21bce4e081025db1db53ab44498415ca44af
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715415"
---
# <a name="run-custom-r-functions-on-sql-server-using-rxexec"></a>RxExec を使用して SQL Server でカスタム R 関数を実行する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

[RxExec](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxexec)を使用して関数を渡すことによって、カスタム r 関数を SQL Server のコンテキストで実行できます。スクリプトで必要なライブラリがサーバーにもインストールされており、これらのライブラリが R の基本ディストリビューションと互換性があることを前提としています。 

**RevoScaleR**の**rxExec**関数は、必要な任意の R スクリプトを実行するためのメカニズムを提供します。 さらに、 **rxExec**では、1台のサーバーの複数のコアに対して作業を明示的に分散することができ、それ以外の場合はネイティブ R エンジンのリソース制約に限定されたスクリプトにスケールを追加します。

このチュートリアルでは、シミュレートされたデータを使用して、リモートサーバーで実行されるカスタム R 関数の実行を示します。

## <a name="prerequisites"></a>必須コンポーネント

+ [SQL Server Machine Learning Services (r)](../install/sql-machine-learning-services-windows-install.md)または[SQL Server 2016 R Services (データベース内)](../install/sql-r-services-windows-install.md)
  
+ [データベース権限](../security/user-permission.md)と SQL Server データベースユーザーログイン

+ [RevoScaleR ライブラリを使用した開発ワークステーション](../r/set-up-a-data-science-client.md)

クライアントワークステーションの R ディストリビューションには、このチュートリアルで R スクリプトを実行するために使用できる組み込みの**Rgui**ツールが用意されています。 RStudio や R Tools for Visual Studio などの IDE を使用することもできます。

## <a name="create-the-remote-compute-context"></a>リモート計算コンテキストを作成する

クライアントワークステーションで次の R コマンドを実行します。 たとえば、 **Rgui**を使用している場合は、次の場所から開始します。C:\Program Files\Microsoft\R Client\R_SERVER\bin\x64\.

1. 計算を実行する SQL Server インスタンスの接続文字列を指定します。 サーバーは、R 統合用に構成されている必要があります。 この演習ではデータベース名が使用されていませんが、接続文字列には1つ必要です。 テストデータベースまたはサンプルデータベースがある場合は、それを使用できます。

    **SQL ログインを使用する**

    ```R
    sqlConnString <- "Driver=SQL Server;Server=<SQL-Server-instance-name>; Database=<database-name>;Uid=<SQL-user-name>;Pwd=<password>"
    ```

    **Windows 認証を使用する**

    ```R
    sqlConnString <- "Driver=SQL Server;Server=<SQL-Server-instance-name>;Database=<database-name>;Trusted_Connection=True"
    ```

2. 接続文字列で参照されている SQL Server インスタンスへのリモート計算コンテキストを作成します。

    ```R
    sqlCompute <- RxInSqlServer(connectionString = sqlConnString)
    ```

3. コンピューティングコンテキストをアクティブ化し、確認手順としてオブジェクト定義を返します。 コンピューティングコンテキストオブジェクトのプロパティが表示されます。

    ```R
    rxSetComputeContext(sqlCompute)
    rxGetComputeContext()
    ```

## <a name="create-the-custom-function"></a>カスタム関数を作成する

この演習では、ダイスのペアのローリングで構成される共通のカジノをシミュレートするカスタム R 関数を作成します。 ゲームのルールにより、勝利または損失の結果が決定されます。

+ 最初のロールで7または11をロールすると、勝ちです。
+ ロール2、3、または12は失われます。
+ 4、5、6、8、9、または10をロールします。その番号はポイントになり、ポイントを再びロールするか (勝ち)、または7をロールする (この場合は) ことになります。

このゲームは R で簡単にシミュレートできます。カスタム関数を作成し、それを何度も実行します。

1.  次の R コードを使用して、カスタム関数を作成します。
  
    ```R
    rollDice <- function()
    {
        result <- NULL
        point <- NULL
        count <- 1
            while (is.null(result))
            {
                roll <- sum(sample(6, 2, replace=TRUE))
  
                if (is.null(point))
                { point <- roll }
                if (count == 1 && (roll == 7 || roll == 11))
                {  result <- "Win" }
                else if (count == 1 && (roll == 2 || roll == 3 || roll == 12))
                { result <- "Loss" }
                else if (count > 1 && roll == 7 )
                { result <- "Loss" }
                else if (count > 1 && point == roll)
                { result <- "Win" }
                else { count <- count + 1 }
            }
            result
    }
    ```
  
2.  関数を実行して、1試合のダイスをシミュレートします。
  
    ```R
    rollDice()
    ```
  
    あなたの勝ちでしたか。それとも負けでしたか。
  
これで、操作スクリプトが完成しました。次は、 **rxExec**を使用して関数を複数回実行して、勝利の確率を判断するためのシミュレーションを作成する方法を見てみましょう。

## <a name="pass-rolldice-in-rxexec"></a>RxExec で rollDice () を渡す

リモート SQL Server のコンテキストで任意の関数を実行するには、 **rxExec**関数を呼び出します。

1. **RxExec**の引数としてカスタム関数を呼び出し、シミュレーションを変更する他のパラメーターと共に呼び出します。
  
    ```R
    sqlServerExec <- rxExec(rollDice, timesToRun=20, RNGseed="auto")
    length(sqlServerExec)
    ```
  
    + *timesToRun* 引数を使用すれば、関数を実行する回数を指示できます。  この場合、20 回サイコロを振ります。
  
    + *RNGseed* 引数と *RNGkind* 引数を利用し、乱数の生成を制御できます。 *RNGseed* が **auto**に設定されると、各ワーカーで並行乱数ストリームが初期化されます。
  
2. **rxExec** 関数は実行のたびに 1 つの要素で一覧を作成します。ただし、一覧が完了するまで内容はわかりません。 すべてのイテレーションが完了すると、 **length**で始まる行によって値が返されます。
  
    次の手順に進むと、勝敗記録のまとめを取得できます。
  
3. R の **unlist** 関数を使用して、返された一覧をベクトルに変換し、 **table** 関数を使用して結果をまとめます。
  
    ```R
    table(unlist(sqlServerExec))
    ```
  
    結果は次のようになります。
  
     *損失獲得* *12 8*

## <a name="conclusion"></a>まとめ

この演習は単純ですが、SQL Server で実行されている R スクリプトで任意の R 関数を統合するための重要なメカニズムを示しています。 この手法を可能にする主なポイントをまとめるには、次のようにします。

+ SQL Server は、machine learning と R の統合用に構成する必要があります。R 機能を使用して[Machine Learning Services を SQL Server](../install/sql-machine-learning-services-windows-install.md)するか、または[2016 r Services (データベース内) を SQL Server](../install/sql-r-services-windows-install.md)します。

+ 依存関係も含め、関数で使用されているオープンソースまたはサードパーティのライブラリを SQL Server にインストールする必要があります。 詳細については、「 [Install New R packages](../r/install-additional-r-packages-on-sql-server.md)」を参照してください。

+ 開発環境から、強化された運用環境にスクリプトを移動すると、ファイアウォールとネットワークの制限が生じる可能性があります。 慎重にテストして、スクリプトが期待どおりに実行できることを確認します。

## <a name="next-steps"></a>次のステップ

**RxExec**を使用するより複雑な例については、次の記事を参照してください。[Foreach と rxExec による粒度の粗い並列処理](https://blog.revolutionanalytics.com/2015/04/coarse-grain-parallelism-with-foreach-and-rxexec.html)
