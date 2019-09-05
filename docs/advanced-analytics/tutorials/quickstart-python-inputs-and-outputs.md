---
title: Python での入力と出力を操作するためのクイックスタート
description: SQL Server での Python スクリプトのこのクイックスタートでは、sp_execute_external_script システムストアドプロシージャに入力と出力を構成する方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 10c62f78ff2ac23e33ad251a07ed8f3689f79843
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715466"
---
# <a name="quickstart-handle-inputs-and-outputs-using-python-in-sql-server"></a>クイック スタート: SQL Server での Python を使用した入力と出力の処理
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このクイックスタートでは、SQL Server Machine Learning Services で Python を使用するときに入力と出力を処理する方法を示します。

既定では、 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)は単一の入力データセットを受け入れます。通常は、有効な SQL クエリの形式で指定します。

その他の種類の入力は SQL 変数として渡すことができます。たとえば、 [pickle](https://docs.python.org/3.0/library/pickle.html)や[rx_serialize_model](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-serialize-model)などのシリアル化関数を使用して、モデルをバイナリ形式で書き込むことで、トレーニング済みのモデルを変数として渡すことができます。

このストアドプロシージャでは、出力として1つの Python [pandas](https://pandas.pydata.org/pandas-docs/stable/index.html) データフレームが返されますが、スカラーおよびモデルを変数として出力することもできます。 たとえば、トレーニング済みのモデルをバイナリ変数として出力し、それを T-sql INSERT ステートメントに渡して、そのモデルをテーブルに書き込むことができます。 また、プロット (バイナリ形式) またはスカラー (日付と時刻、モデルのトレーニングに要した時間など) を生成することもできます。

## <a name="prerequisites"></a>必須コンポーネント

前のクイックスタート「 [SQL Server に python が存在することを検証](quickstart-python-verify.md)する」では、このクイックスタートに必要な python 環境を設定するための情報とリンクを提供しています。

## <a name="create-the-source-data"></a>ソース データを作成する

次の T-sql ステートメントを実行して、テストデータの小さなテーブルを作成します。

```sql
CREATE TABLE PythonTestData (col1 INT NOT NULL)
INSERT INTO PythonTestData VALUES (1);
INSERT INTO PythonTestData VALUES (10);
INSERT INTO PythonTestData VALUES (100);
GO
```

テーブルが作成されたら、次のステートメントを使用してテーブルを照会します。
  
```sql
SELECT * FROM PythonTestData
```

**結果**

![PythonTestData テーブルの内容](./media/select-pythontestdata.png)

## <a name="inputs-and-outputs"></a>[スクリプト変換エディター]

Sp_execute_external_script `InputDataSet`の既定の入力変数と出力変数、およびを`OutputDataSet`見てみましょう。

1. Python スクリプトへの入力として、テーブルからデータを取得できます。 次のステートメントを実行します。 このメソッドは、テーブルからデータを取得し、Python ランタイムを介してラウンドトリップを行い、列名*NewColName*を持つ値を返します。

    クエリによって返されるデータは、データフレームとして SQL Database するデータを返す Python ランタイムに渡されます。 WITH RESULT SETS 句では、SQL Database に対して返されるデータテーブルのスキーマを定義します。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'Python'
        , @script = N'OutputDataSet = InputDataSet;'
        , @input_data_1 = N'SELECT * FROM PythonTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **結果**

    ![テーブルからデータを返す Python スクリプトからの出力](./media/python-output-pythontestdata.png)

2. 入力変数または出力変数の名前を変更してみましょう。 上記のスクリプトでは、入力と出力の既定の変数名である_inputdataset_と_outputdataset_が使用されていました。 _Inputdataset_に関連付けられている入力データを定義 *@input_data_1* するには、変数を使用します。

    このスクリプトでは、ストアドプロシージャの出力変数と入力変数の名前が*SQL_out*および*SQL_in*に変更されています。

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'Python'
      , @script = N'SQL_out = SQL_in'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Python では大文字と小文字が`@input_data_1_name`区別`@output_data_1_name`されるので、とでの入力変数と出力変数`@script`の大文字と小文字の区別は、の python コードの大文字と小文字の区別に一致する必要があります。

    パラメーターとして渡すことができるのは、1 つの入力データセットのみです。また、1 つのデータセットのみを返すことができます。 ただし、Python コード内から他のデータセットを呼び出すことができます。また、データセットに加えて他の型の出力を返すこともできます。 また、OUTPUT キーワードを任意のパラメーターに追加して、その結果を受け取ることもできます。 

    `WITH RESULT SETS`ステートメントでは、SQL Server で使用されるデータのスキーマを定義します。 Python から返される各列に対して、SQL と互換性のあるデータ型を指定する必要があります。 スキーマ定義を使用すると、Python data. frame の列名を使用する必要がないため、新しい列名を指定することもできます。

3. Python スクリプトを使用して値を生成し、入力クエリ文字列を空白 _@input_data_1_ のままにすることもできます。

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'Python'
    , @script = N'
    import pandas as pd
    mytextvariable = pandas.Series(["hello", " ", "world"]);
    OutputDataSet = pd.DataFrame(mytextvariable);'
    , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **結果**

    ![入力とし@scriptてを使用したクエリ結果](./media/python-data-generated-output.png)

## <a name="next-steps"></a>次の手順

Python と SQL Server 間で表形式のデータを渡すときに発生する可能性のある問題をいくつか確認します。

> [!div class="nextstepaction"]
> [クイック スタート:SQL Server の Python データ構造](quickstart-python-data-structures.md)
