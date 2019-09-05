---
title: Python でデータ構造を操作するためのクイックスタート
description: SQL Server での Python スクリプトのこのクイックスタートでは、sp_execute_external_script システムストアドプロシージャでのデータ構造の使用方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 13fb37bee355ce1d379d8348734293baaeb481d8
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714800"
---
# <a name="quickstart-python-data-structures-in-sql-server"></a>クイック スタート: SQL Server の Python データ構造
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このクイックスタートでは、SQL Server Machine Learning Services で Python を使用するときのデータ構造の使用方法を示します。

SQL Server は、表形式のデータを操作するのに最適な Python **pandas** パッケージに依存しています。 ただし、スカラーを Python から SQL Server に渡すことはできません。 このクイックスタートでは、いくつかの基本的なデータ型の定義を確認し、Python と SQL Server の間で表形式のデータを渡すときに発生する可能性のある追加の問題を準備します。

+ データフレームは、_複数_の列を含むテーブルです。
+ データフレームの1つの列は、系列と呼ばれるリストのようなオブジェクトです。
+ 1つの値はデータフレームのセルであり、インデックスによって呼び出す必要があります。

データフレームにテーブル構造が必要な場合、計算の1つの結果をデータフレームとして公開するにはどうすればよいでしょうか。 1つの答えは、単一のスカラー値を系列として表すことです。これは、データフレームに簡単に変換できます。 

## <a name="prerequisites"></a>必須コンポーネント

前のクイックスタート「 [SQL Server に python が存在することを検証](quickstart-python-verify.md)する」では、このクイックスタートに必要な python 環境を設定するための情報とリンクを提供しています。

## <a name="scalar-value-as-a-series"></a>系列としてのスカラー値

この例では、単純な数値演算を行い、スカラーを系列に変換します。

1. 系列にはインデックスが必要です。このインデックスは、次に示すように手動で割り当てることも、プログラムで割り当てることもできます。

    ```sql
    execute sp_execute_external_script 
    @language = N'Python', 
    @script = N'
    a = 1
    b = 2
    c = a/b
    print(c)
    s = pandas.Series(c, index =["simple math example 1"])
    print(s)
    '
    ```

2. 系列はデータフレームに変換されていないため、[メッセージ] ウィンドウに値が返されますが、結果がより表形式になっていることがわかります。

    **結果**

    ```text
    STDOUT message(s) from external script: 
    0.5
    simple math example 1    0.5
    dtype: float64
    ```

3. 系列の長さを増やすには、配列を使用して新しい値を追加します。 

    ```sql
    execute sp_execute_external_script 
    @language = N'Python', 
    @script = N'
    a = 1
    b = 2
    c = a/b
    d = a*b
    s = pandas.Series([c,d])
    print(s)
    '
    ```

    インデックスを指定しない場合は、0から始まり、配列の長さで終わる値を持つインデックスが生成されます。

    **結果**

    ```text
    STDOUT message(s) from external script: 
    0    0.5
    1    2.0
    dtype: float64
    ```

4. **インデックス**値の数を増やしても、新しい**データ**値を追加しない場合、データ値は系列に合わせて繰り返されます。

    ```sql
    execute sp_execute_external_script 
    @language = N'Python', 
    @script = N'
    a = 1
    b = 2
    c = a/b
    s = pandas.Series(c, index =["simple math example 1", "simple math example 2"])
    print(s)
    '
    ```

    **結果**

    ```text
    STDOUT message(s) from external script: 
    0.5
    simple math example 1    0.5
    simple math example 2    0.5
    dtype: float64
    ```

## <a name="convert-series-to-data-frame"></a>系列をデータフレームに変換します

スカラー数値演算の結果を表形式構造に変換した後も、SQL Server が処理できる形式に変換する必要があります。 

1. 系列をデータフレームに変換するには、pandas の[データフレーム](https://pandas.pydata.org/pandas-docs/stable/dsintro.html#dataframe)メソッドを呼び出します。

    ```sql
    execute sp_execute_external_script 
    @language = N'Python', 
    @script = N'
    import pandas as pd
    a = 1
    b = 2
    c = a/b
    d = a*b
    s = pandas.Series([c,d])
    print(s)
    df = pd.DataFrame(s)
    OutputDataSet = df
    '
    WITH RESULT SETS (( ResultValue float ))
    ```

2. 結果は次のようになります。 インデックスを使用してデータフレームから特定の値を取得する場合でも、インデックス値は出力の一部ではありません。

    **結果**

    |ResultValue|
    |------|
    |0.5|
    |2|

## <a name="output-values-into-dataframe"></a>値をデータフレームに出力します。

データへの変換方法を見てみましょう。フレームは、単純な算術演算の結果を含む2つの系列と連携して動作します。 最初のには、Python によって生成される連続する値のインデックスがあります。 2番目の例では、文字列値の任意のインデックスを使用します。

1. この例では、整数インデックスを使用する系列から値を取得します。

    ```sql
    EXECUTE sp_execute_external_script 
    @language = N'Python', 
    @script = N'
    import pandas as pd
    a = 1
    b = 2
    c = a/b
    d = a*b
    s = pandas.Series([c,d])
    print(s)
    df = pd.DataFrame(s, index=[1])
    OutputDataSet = df
    '
    WITH RESULT SETS (( ResultValue float ))
    ```

    自動生成されたインデックスは0から始まることに注意してください。 範囲外のインデックス値を使用して、何が起こるかを確認してください。

2. 次に、文字列インデックスを持つ他のデータフレームから1つの値を取得します。 

    ```sql
    EXECUTE sp_execute_external_script 
    @language = N'Python', 
    @script = N'
    import pandas as pd
    a = 1
    b = 2
    c = a/b
    s = pandas.Series(c, index =["simple math example 1", "simple math example 2"])
    print(s)
    df = pd.DataFrame(s, index=["simple math example 1"])
    OutputDataSet = df
    '
    WITH RESULT SETS (( ResultValue float ))
    ```

    **結果**

    |ResultValue|
    |------|
    |0.5|

    数値インデックスを使用してこの系列から値を取得しようとすると、エラーが発生します。

## <a name="next-steps"></a>次の手順

次に、SQL Server で Python を使用して予測モデルを作成します。

> [!div class="nextstepaction"]
> [ストアドプロシージャを使用した Python モデルの作成、トレーニング、および使用 SQL Server](quickstart-python-train-score-in-tsql.md)