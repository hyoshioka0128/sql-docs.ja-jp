---
title: T-sql での "Hello World" 基本的な R コードの実行のクイックスタート
description: SQL Server での R スクリプトのクイックスタート。 Hello world の演習で sp_execute_external_script システムストアドプロシージャを使用して R スクリプトを呼び出す方法の基本について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: ac9537e3e13313e6ca0094a75b0e72c7c8ee80c2
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714754"
---
# <a name="quickstart-hello-world-r-script-in-sql-server"></a>クイック スタート: SQL Server の "Hello world" R スクリプト 
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このクイックスタートでは、 **sp_execute_external_script**システムストアドプロシージャの概要と共に、"Hello World" R スクリプトを実行して、主要な概念について説明します。 

## <a name="prerequisites"></a>必須コンポーネント

前のクイックスタート「 [SQL Server に r が存在することを確認](quickstart-r-verify.md)する」では、このクイックスタートに必要な r 環境を設定するための情報とリンクを提供しています。

## <a name="basic-r-interaction"></a>基本的な R の相互作用

SQL Database で R コードを実行するには、次の2つの方法があります。

+ システムストアドプロシージャ[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)の引数として R スクリプトを追加します。
+ [リモート R クライアント](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client)から SQL database に接続し、SQL Database を計算コンテキストとして使用してコードを実行します。

次の演習では、最初の相互作用モデル (R コードをストアドプロシージャに渡す方法) に重点を置いています。

1. 単純なスクリプトを実行して、SQL データベースで R スクリプトがどのように実行されるかを確認します。

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

2. すべてが正しく設定されていることを前提として、正しい結果`print`が計算され、R 関数によって結果が**Messages**ウィンドウに返されます。

    **結果**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    コードをテストするときに**stdout**メッセージを取得すると便利ですが、多くの場合、アプリケーションで使用したり、テーブルに書き込んだりできるように、結果を表形式で返す必要があります。 クイック[スタートを参照:詳細については、SQL Server](rtsql-working-with-inputs-and-outputs.md)の R を使用した入力と出力の処理」を参照してください。

引数内のすべてが`@script`有効な R コードである必要があることに注意してください。

## <a name="run-a-hello-world-script"></a>Hello World スクリプトを実行する

次の演習では、別の単純な R スクリプトを実行します。

```sql
EXEC sp_execute_external_script
  @language =N'R',
  @script=N'OutputDataSet<-InputDataSet',
  @input_data_1 =N'SELECT 1 AS hello'
  WITH RESULT SETS (([Hello World] int));
GO
```

このストアドプロシージャへの入力は次のとおりです。

+ *@language* パラメーターでは、呼び出す言語拡張機能 (この場合は R) を定義します。
+ *@script* パラメーターでは、R ランタイムに渡されるコマンドを定義します。 Unicode テキストとして、この引数で R スクリプト全体を囲む必要があります。 **nvarchar** 型の変数にテキストを追加して、その変数を呼び出すこともできます。
+ *@input_data_1* クエリによって返されるデータです。 R ランタイムに渡され、データフレームとして SQL Server するデータが返されます。
+ WITH RESULT SETS 句では、SQL Server に対して返されるデータテーブルのスキーマを定義し、列名として "Hello World" を追加し、データ型に**int**を追加します。

**結果**

| Hello World |
|-------------|
| 1 |

## <a name="next-steps"></a>次の手順

これで、いくつかの単純な R スクリプトを実行できました。次は、入力と出力の構成について詳しく見ていきます。

> [!div class="nextstepaction"]
> [クイック スタート:入力と出力の処理](quickstart-r-inputs-and-outputs.md)
