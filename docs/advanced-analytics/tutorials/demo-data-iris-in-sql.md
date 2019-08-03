---
title: Python および R 用の虹彩のデモデータセットのチュートリアル
Description: 虹彩データセットを含むデータベースと、モデルを格納するためのテーブルを作成します。 このデータセットは、R 言語または Python コードを SQL Server ストアドプロシージャにラップする方法を示す演習で使用されます。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/19/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 78acdd0ef2eed808d8f974c38899282dc823436d
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715500"
---
#  <a name="iris-demo-data-for-python-and-r-tutorials-in-sql-server"></a>SQL Server での Python および R チュートリアル用の虹彩のデモデータ 
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この演習では、同じデータに基づいて、[虹彩フラワーデータセット](https://en.wikipedia.org/wiki/Iris_flower_data_set)とモデルのデータを格納する SQL Server データベースを作成します。 虹彩データは SQL Server によってインストールされた R と Python の両方のディストリビューションに含まれており、SQL Server の機械学習のチュートリアルで使用されます。 

この演習を完了するには、 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)または t-sql クエリを実行できる別のツールが必要です。

このデータセットを使用したチュートリアルとクイックスタートには、次のものがあります。

+  [クイック スタート:ストアドプロシージャを使用した Python モデルの作成、トレーニング、および使用 SQL Server](quickstart-python-train-score-in-tsql.md)

## <a name="create-the-database"></a>データベースの作成

1. SQL Server Management Studio を開始し、新しい**クエリ**ウィンドウを開きます。  

2. このプロジェクトの新しいデータベースを作成し、新しいデータベースを使用するように**クエリ**ウィンドウのコンテキストを変更します。

    ```sql
    CREATE DATABASE irissql
    GO
    USE irissql
    GO
    ```

    > [!TIP] 
    > SQL Server に慣れていない場合、または所有しているサーバーで作業している場合は、通常、ログインして、 **master**データベース内にあることを確認せずに作業を開始します。 正しいデータベースを使用していることを確認するには、常に`USE <database name>`ステートメント (など`use irissql`) を使用してコンテキストを指定します。

3. 空のテーブルを追加します。1つはデータを格納するテーブル、もう1つはトレーニング済みのモデルを格納するテーブルです。 **Iris_models**テーブルは、他の演習で生成されるシリアル化されたモデルを格納するために使用されます。

    次のコードでは、トレーニングデータ用のテーブルを作成します。

    ```sql
    DROP TABLE IF EXISTS iris_data;
    GO
    CREATE TABLE iris_data (
      id INT NOT NULL IDENTITY PRIMARY KEY
      , "Sepal.Length" FLOAT NOT NULL, "Sepal.Width" FLOAT NOT NULL
      , "Petal.Length" FLOAT NOT NULL, "Petal.Width" FLOAT NOT NULL
      , "Species" VARCHAR(100) NOT NULL, "SpeciesId" INT NOT NULL
    );
    ```

    > [!TIP] 
    > T-sql を初めて使用する場合は、 `DROP...IF`ステートメントを覚えることになります。 テーブルを作成しようとしたときに、テーブルが既に存在している場合、SQL Server はエラーを返します。"データベースに ' iris_data ' という名前のオブジェクトが既に存在します。" このようなエラーを回避する方法の1つは、既存のテーブルやその他のオブジェクトをコードの一部として削除することです。

4. 次のコードを実行して、トレーニング済みのモデルを格納するために使用するテーブルを作成します。 SQL Server で Python (または R) モデルを保存するには、 **varbinary (max)** 型の列にシリアル化して格納する必要があります。 

    ```sql
    DROP TABLE IF EXISTS iris_models;
    GO
    
    CREATE TABLE iris_models (
      model_name VARCHAR(50) NOT NULL DEFAULT('default model') PRIMARY KEY,
      model VARBINARY(MAX) NOT NULL
    );
    GO
    ```

    モデルの内容に加えて、通常は、モデルの名前、トレーニングされた日付、ソースアルゴリズムとパラメーター、ソースデータなど、その他の便利なメタデータの列も追加します。 ここでは、単純化し、モデル名だけを使用します。

## <a name="populate-the-table"></a>テーブルを設定する

組み込みの虹彩データは、R または Python から取得できます。 Python または R を使用してデータをデータフレームに読み込んでから、データベース内のテーブルに挿入することができます。 外部セッションから SQL Server テーブルにトレーニングデータを移動するには、次の手順を実行します。

+ 必要なデータを取得するストアドプロシージャを設計します。
+ 実際にデータを取得するには、ストアドプロシージャを実行します。
+ 取得したデータを保存する場所を指定する INSERT ステートメントを構築します。

1. Python 統合を使用するシステムでは、Python コードを使用してデータを読み込む、次のストアドプロシージャを作成します。

    ```sql
    CREATE PROCEDURE get_iris_dataset
    AS
    BEGIN
    EXEC sp_execute_external_script @language = N'Python', 
    @script = N'
    from sklearn import datasets
    iris = datasets.load_iris()
    iris_data = pandas.DataFrame(iris.data)
    iris_data["Species"] = pandas.Categorical.from_codes(iris.target, iris.target_names)
    iris_data["SpeciesId"] = iris.target
    ', 
    @input_data_1 = N'', 
    @output_data_1_name = N'iris_data'
    WITH RESULT SETS (("Sepal.Length" float not null, "Sepal.Width" float not null, "Petal.Length" float not null, "Petal.Width" float not null, "Species" varchar(100) not null, "SpeciesId" int not null));
    END;
    GO
    ```

    このコードを実行すると、"コマンドは正常に完了しました。" というメッセージが表示されます。 これは、ストアドプロシージャが仕様に従って作成されていることを意味します。

2. または、R を統合するシステムでは、代わりに R を使用するプロシージャを作成します。

    ```sql
    CREATE PROCEDURE get_iris_dataset
    AS
    BEGIN
    EXEC sp_execute_external_script @language = N'R', 
    @script = N'
    library(RevoScaleR)
    data(iris)
    iris$SpeciesID <- c(unclass(iris$Species))
    iris_data <- iris
    ', 
    @input_data_1 = N'', 
    @output_data_1_name = N'iris_data'
    WITH RESULT SETS (("Sepal.Length" float not null, "Sepal.Width" float not null, "Petal.Length" float not null, "Petal.Width" float not null, "Species" varchar(100) not null, "SpeciesId" int not null));
    END;
    GO
    ```

3. テーブルに実際にデータを設定するには、ストアドプロシージャを実行し、データの書き込み先のテーブルを指定します。 実行すると、ストアドプロシージャは Python または R コードを実行します。このコードでは、組み込みの虹彩データセットが読み込まれ、 **iris_data**テーブルにデータが挿入されます。

    ```sql
    INSERT INTO iris_data ("Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "Species", "SpeciesId")
    EXEC dbo.get_iris_dataset;
    ```

    T-sql を初めて使用する場合は、INSERT ステートメントで新しいデータのみを追加することに注意してください。既存のデータをチェックしたり、テーブルを削除および再構築したりすることはありません。 テーブル内の同じデータの複数のコピーが取得さ`TRUNCATE TABLE iris_data`れないようにするには、最初にステートメントを実行します。 T-sql [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql)ステートメントでは、既存のデータが削除されますが、テーブルの構造はそのまま維持されます。

    > [!TIP]
    > 後でストアドプロシージャを変更するには、ストアドプロシージャを削除して再作成する必要はありません。 [ALTER PROCEDURE](https://docs.microsoft.com/sql/t-sql/statements/alter-procedure-transact-sql)ステートメントを使用します。 


## <a name="query-the-data"></a>データのクエリ

検証手順として、クエリを実行してデータがアップロードされたことを確認します。

1. オブジェクトエクスプローラーの [データベース] で、 **irissql**データベースを右クリックし、新しいクエリを開始します。

2. いくつかの単純なクエリを実行します。

    ```sql
    SELECT TOP(10) * FROM iris_data;
    SELECT COUNT(*) FROM iris_data;
    ```

## <a name="next-steps"></a>次の手順

次のクイックスタートでは、機械学習モデルを作成してテーブルに保存し、モデルを使用して予測結果を生成します。

+ [クイック スタート:ストアドプロシージャを使用した Python モデルの作成、トレーニング、および使用 SQL Server](quickstart-python-train-score-in-tsql.md)
