---
title: R での入力と出力を操作するためのクイックスタート
description: SQL Server の R スクリプトのこのクイックスタートでは、sp_execute_external_script システムストアドプロシージャに入力と出力を構成する方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 75de63ef960c205545db5229f22a437c70b350b9
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714797"
---
# <a name="quickstart-handle-inputs-and-outputs-using-r-in-sql-server"></a>クイック スタート: SQL Server で R を使用して入力と出力を処理する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このクイックスタートでは、SQL Server Machine Learning Services または R Services で R を使用するときに入力と出力を処理する方法を示します。

SQL Server で R コードを実行する場合は、ストアドプロシージャで R スクリプトをラップする必要があります。 1つを作成するか、R スクリプトを[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)に渡すことができます。 このシステムストアドプロシージャは、r にデータを渡し、R ユーザーセッションを安全に管理し、結果をクライアントに返す、SQL Server のコンテキストで R ランタイムを開始するために使用されます。

既定では、 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)は単一の入力データセットを受け入れます。通常は、有効な SQL クエリの形式で指定します。 その他の種類の入力は、SQL 変数として渡すことができます。

このストアドプロシージャは、出力として1つの R データフレームを返しますが、スカラーおよびモデルを変数として出力することもできます。 たとえば、トレーニング済みのモデルをバイナリ変数として出力し、それを T-sql INSERT ステートメントに渡して、そのモデルをテーブルに書き込むことができます。 また、プロット (バイナリ形式) またはスカラー (日付と時刻、モデルのトレーニングに要した時間など) を生成することもできます。

## <a name="prerequisites"></a>必須コンポーネント

前のクイックスタート「 [SQL Server に r が存在することを確認](quickstart-r-verify.md)する」では、このクイックスタートに必要な r 環境を設定するための情報とリンクを提供しています。

## <a name="create-the-source-data"></a>ソース データを作成する

次の T-sql ステートメントを実行して、テストデータの小さなテーブルを作成します。

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)
INSERT INTO RTestData VALUES (1);
INSERT INTO RTestData VALUES (10);
INSERT INTO RTestData VALUES (100);
GO
```

テーブルが作成されたら、次のステートメントを使用してテーブルを照会します。
  
```sql
SELECT * FROM RTestData
```

**結果**

![RTestData テーブルの内容](./media/select-rtestdata.png)

## <a name="inputs-and-outputs"></a>[スクリプト変換エディター]

Sp_execute_external_script `InputDataSet`の既定の入力変数と出力変数、およびを`OutputDataSet`見てみましょう。

1. R スクリプトへの入力として、テーブルからデータを取得できます。 次のステートメントを実行します。 このメソッドは、テーブルからデータを取得し、R ランタイムを介してラウンドトリップを行い、列名*NewColName*を持つ値を返します。

    クエリによって返されるデータは R ランタイムに渡され、データフレームとして SQL Database するデータが返されます。 WITH RESULT SETS 句では、SQL Database に対して返されるデータテーブルのスキーマを定義します。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **結果**

    ![テーブルからデータを返す R スクリプトからの出力](./media/r-output-rtestdata.png)

2. 入力変数または出力変数の名前を変更してみましょう。 上記のスクリプトでは、入力と出力の既定の変数名である_inputdataset_と_outputdataset_が使用されていました。 _Inputdatset_に関連付けられている入力データを定義 *@input_data_1* するには、変数を使用します。

    このスクリプトでは、ストアドプロシージャの出力変数と入力変数の名前が*SQL_out*および*SQL_in*に変更されています。

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N'SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    R では大文字と小文字が区別されるため、および`@input_data_1_name` `@output_data_1_name`の入力変数と出力変数の大文字と小文字の区別`@script`は、の r コード内の変数と一致している必要があります。 

    パラメーターとして渡すことができるのは、1 つの入力データセットのみです。また、1 つのデータセットのみを返すことができます。 ただし、R コード内から他のデータセットを呼び出し、データセットに加えて他の型の出力を返すことができます。 また、OUTPUT キーワードを任意のパラメーターに追加して、その結果を受け取ることもできます。 

    `WITH RESULT SETS`ステートメントでは、SQL Server で使用されるデータのスキーマを定義します。 R から返される各列に対して、SQL と互換性のあるデータ型を指定する必要があります。R データフレームの列名を使用する必要がないため、スキーマ定義を使用して新しい列名を指定することもできます。

3. R スクリプトを使用して値を生成し、入力クエリ文字列を空白 _@input_data_1_ のままにすることもできます。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **結果**

    ![入力とし@scriptてを使用したクエリ結果](./media/r-data-generated-output.png)

## <a name="next-steps"></a>次の手順

R と SQL の間のテーブルデータの暗黙的な変換や相違など、R と SQL Server 間でデータを渡すときに発生する可能性のある問題の一部を確認します。

> [!div class="nextstepaction"]
> [クイック スタート:データ型とオブジェクトの処理](quickstart-r-data-types-and-objects.md)