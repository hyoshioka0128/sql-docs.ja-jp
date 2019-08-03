---
title: R 関数を示すクイックスタート-SQL Server Machine Learning
description: このクイックスタートでは、高度な統計計算用に R 関数を記述する方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 0f81b4f755441a5129892ed321d74bb8c6427ee4
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714846"
---
# <a name="quickstart-using-r-functions"></a>クイック スタート: R 関数の使用
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

前のクイックスタートを完了している場合は、基本的な操作について理解し、統計関数など、より複雑な作業を行うことができます。 T-sql で実装するのが複雑な高度な統計関数は、1行のコードだけを使用して R で実行できます。

このクイックスタートでは、R の数学関数とユーティリティ関数を SQL Server ストアドプロシージャに埋め込みます。

## <a name="prerequisites"></a>必須コンポーネント

前のクイックスタート「 [SQL Server に r が存在することを確認](quickstart-r-verify.md)する」では、このクイックスタートに必要な r 環境を設定するための情報とリンクを提供しています。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>乱数を生成するストアド プロシージャを作成する

わかりやすくするために、SQL Server で`stats` r 機能サポートをインストールするときに既定でインストールされ、読み込まれる r パッケージを使用してみましょう。 パッケージには、一般的な統計タスク用の数百の関数が含まれますが、その中の `rnorm` 関数は、指定された標準偏差と平均に対し、正規分布を使用して、指定された個数の乱数を生成します。

たとえば、次の R コードは、指定された 3 の標準偏差で、平均 50 の 100 個の数値を返します。

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

この R の行を T-SQL から呼び出すには、次のように sp_execute_external_script を実行し、R スクリプト パラメーターで R 関数を追加します。

```sql
EXEC sp_execute_external_script
      @language = N'R'
    , @script = N'
         OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));'
    , @input_data_1 = N'   ;'
      WITH RESULT SETS (([Density] float NOT NULL));
```

別の乱数のセットをもっと簡単に生成するにはどうすればよいでしょうか。

これは、SQL Server と組み合わせて簡単に行うことができます。ユーザーから引数を取得するストアドプロシージャを定義します。 次に、R スクリプトにそれらの引数を変数として渡します。

```sql
CREATE PROCEDURE MyRNorm (@param1 int, @param2 int, @param3 int)
AS
    EXEC sp_execute_external_script
      @language = N'R'
    , @script = N'
         OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
    WITH RESULT SETS (([Density] float NOT NULL));
```

+ 最初の行では、ストアド プロシージャが実行されるときに必要な各 SQL 入力パラメーターを定義します。

+ `@params` で始まる行は、R コードで使用されるすべての変数と、対応する SQL データ型を定義します。

+ それに続く行は、SQL パラメーター名を対応する R 変数名にマップします。

ストアド プロシージャで R 関数をラップしたので、次のように関数を簡単に呼び出し、別の値を渡すことができます。

```sql
EXEC MyRNorm @param1 = 100,@param2 = 50, @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>トラブルシューティングのための R ユーティリティ関数の使用

既定では、r のインストールには`utils` 、現在の r 環境を調査するためのさまざまなユーティリティ機能を提供するパッケージが含まれています。 これは、SQL Server と外部環境で R コードが実行する方法に不一致が見つかった場合に役立つ可能性があります。

たとえば、R `memory.limit()` 関数を使用して、現在の R 環境のメモリを取得できます。 `utils` パッケージはインストールされていますが既定で読み込まれていないため、最初に `library()` 関数を使用して読み込む必要があります。

```sql
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
        library(utils);
        mymemory <- memory.limit();
        OutputDataSet <- as.data.frame(mymemory);'
    , @input_data_1 = N' ;'
WITH RESULT SETS (([Col1] int not null));
```

`system.time` や`proc.time`などのシステムタイミング関数を使用して、r プロセスによって使用される時間をキャプチャし、パフォーマンスの問題を分析する多くのユーザー。

例については、次のチュートリアルを参照してください。[データ機能を作成](../tutorials/walkthrough-create-data-features.md)します。 このチュートリアルでは、R タイミング関数をソリューションに埋め込んで、データから機能を作成する2つの方法のパフォーマンスを比較します。R 関数とT-sql 関数。

## <a name="next-steps"></a>次の手順

次に、SQL Server で R を使用して予測モデルを作成します。

> [!div class="nextstepaction"]
> [クイック スタート:予測モデルを作成する](quickstart-r-create-predictive-model.md)
