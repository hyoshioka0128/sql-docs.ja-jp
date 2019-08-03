---
title: T-sql での "Hello World" 基本的な Python コード実行のクイックスタート
description: SQL Server での Python スクリプトのクイックスタート。 Hello world の演習で sp_execute_external_script システムストアドプロシージャを使用した Python スクリプトの呼び出しの基本について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/10/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: a170bd2ee3e893a83ebb9d3201ee117321e7562b
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714823"
---
# <a name="quickstart-hello-world-python-script-in-sql-server"></a>クイック スタート: SQL Server の "Hello world" Python スクリプト 
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このクイックスタートでは、sp_execute_external_script システムストアドプロシージャの概要と共に、"Hello World" Python スクリプトのを実行して、主要な概念を学習します。 

## <a name="prerequisites"></a>前提条件

前のクイックスタート「 [SQL Server に python が存在することを検証](quickstart-python-verify.md)する」では、このクイックスタートに必要な python 環境を設定するための情報とリンクを提供しています。

## <a name="basic-python-interaction"></a>基本的な Python の相互作用

SQL Server で Python コードを実行するには、次の2つの方法があります。

+ Python スクリプトを、システムストアドプロシージャ[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)の引数として追加します。

+ [リモート Python クライアント](../python/setup-python-client-tools-sql.md)から SQL Server に接続し、SQL Server を計算コンテキストとして使用してコードを実行します。 これには[revoscalepy](../python/ref-py-revoscalepy.md)が必要です。

次の演習では、最初の相互作用モデルに焦点を絞って説明します。 Python コードをストアドプロシージャに渡す方法について説明します。

1. いくつかの単純なコードを実行して、SQL Server と Python の間でデータがどのようにやり取りされるかを確認します。

    ```sql
    execute sp_execute_external_script 
    @language = N'Python', 
    @script = N'
    a = 1
    b = 2
    c = a/b
    d = a*b
    print(c, d)
    '
    ```

2. すべてが正しく設定されており、python と SQL Server が相互に通信していることを前提として、正しい結果`print`が計算され、python 関数によって結果が**メッセージ**ウィンドウに返されます。

    **結果**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    コードをテストするときに**stdout**メッセージを取得するのは便利ですが、アプリケーションで使用したり、テーブルに書き込んだりするために、結果を表形式で返すことが必要になることがあります。

ここでは、次の規則を忘れないでください。

+ 引数内の`@script`すべては、有効な Python コードである必要があります。 
+ コードは、インデント、変数名などに関するすべての Python 規則に従う必要があります。 エラーが発生した場合は、空白と大文字小文字の区別を確認してください。
+ プログラミング言語の中で、Python は、単一引用符と二重引用符に関して最も柔軟性の高いものの1つです。これらは非常に交換可能です。 ただし、t-sql では特定の項目に対してのみ単一引用符を`@script`使用し、引数は単一引用符を使用して Python コードを Unicode 文字列として囲みます。 そのため、Python コードを確認し、1つの引用符を二重引用符に変更することが必要になる場合があります。
+ 既定で読み込まれていないライブラリを使用している場合は、スクリプトの先頭で import ステートメントを使用して読み込む必要があります。 SQL Server 製品固有のライブラリがいくつか追加されます。 詳細については、「 [Python ライブラリ](../python/python-libraries-and-data-types.md)」を参照してください。
+ ライブラリがまだインストールされていない場合は、次の説明に従って、SQL Server の外部にある Python パッケージを停止してインストールします。[SQL Server に新しい Python パッケージをインストールする](../python/install-additional-python-packages-on-sql-server.md)

## <a name="run-a-hello-world-script"></a>Hello World スクリプトを実行する

次の演習では、別の単純な Python スクリプトを実行します。

```sql
EXEC sp_execute_external_script
@language =N'Python',
@script=N'OutputDataSet = InputDataSet',
@input_data_1 =N'SELECT 1 AS hello'
WITH RESULT SETS (([Hello World] int));
GO
```

このストアドプロシージャへの入力は次のとおりです。

+ *@language* パラメーターでは、呼び出す言語拡張機能 (この場合は Python) を定義します。
+ *@script* パラメーターは、Python ランタイムに渡されるコマンドを定義します。 Python スクリプト全体を Unicode テキストとしてこの引数で囲む必要があります。 **nvarchar** 型の変数にテキストを追加して、その変数を呼び出すこともできます。
+ *@input_data_1* データは、データフレームとして SQL Server するデータを返す Python ランタイムに渡される、クエリによって返されるデータです。
+ WITH RESULT SETS 句では、SQL Server に対して返されるデータテーブルのスキーマを定義し、列名として "Hello World" を追加し、データ型に**int**を追加します。

**結果**

| Hello World |
|-------------|
| 1 |

## <a name="next-steps"></a>次のステップ

これで、いくつかの単純な Python スクリプトを実行できました。次は、入力と出力の構成について詳しく見ていきます。

> [!div class="nextstepaction"]
> [クイック スタート:入力と出力の処理](quickstart-python-inputs-and-outputs.md)
