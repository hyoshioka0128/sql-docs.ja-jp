---
title: ストアドプロシージャを使用したトレーニングと予測のための Python モデルのクイックスタート
description: 従来の虹彩データセットを使用して Python モデルを作成、トレーニング、使用するために SQL Server ストアドプロシージャに Python コードを埋め込みます。 トレーニング済みのモデルを SQL Server に保存し、それを使用して予測結果を生成します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: e23c025c018811e5ef84304d0cae6bf16491f91e
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715486"
---
# <a name="quickstart-create-train-and-use-a-python-model-with-stored-procedures-in-sql-server"></a>クイック スタート: ストアドプロシージャを使用した Python モデルの作成、トレーニング、および使用 SQL Server
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

Python を使用したこのクイックスタートでは、2つのストアドプロシージャを作成して実行します。 最初の例では、クラシック虹彩花のデータセットを使用して、花の特性に基づいて虹彩を予測するための、単純な Bayes モデルを生成します。 2番目の手順はスコアリング用です。 最初の手順で生成されたモデルを呼び出して、一連の予測を出力します。 ストアドプロシージャにコードを配置することで、操作が含まれ、再利用可能になり、他のストアドプロシージャやクライアントアプリケーションから呼び出すことができます。 

このクイックスタートを完了すると、次のことが学習されます。

> [!div class="checklist"]
> * ストアドプロシージャに Python コードを埋め込む方法
> * ストアドプロシージャの入力を使用してコードに入力を渡す方法
> * ストアドプロシージャを使用してモデルを運用化する方法

## <a name="prerequisites"></a>必須コンポーネント

前のクイックスタート「 [SQL Server に python が存在することを検証](quickstart-python-verify.md)する」では、このクイックスタートに必要な python 環境を設定するための情報とリンクを提供しています。

この演習で使用するサンプルデータは、 [**irissql**](demo-data-iris-in-sql.md)データベースです。

## <a name="create-a-stored-procedure-that-generates-models"></a>モデルを生成するストアドプロシージャを作成する

SQL Server 開発の一般的なパターンは、プログラミング可能な操作を個別のストアドプロシージャに整理することです。 この手順では、結果を予測するためのモデルを生成するストアドプロシージャを作成します。 

1. **Irissql**データベースに接続して Management Studio で新しいクエリウィンドウを開きます。 

    ```sql
    USE irissql
    GO
    ```

2. 次のコードをコピーして、新しいストアドプロシージャを作成します。 

   このプロシージャを実行すると、 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)が呼び出されて Python セッションが開始されます。 
   
   Python コードで必要な入力は、このストアドプロシージャの入力パラメーターとして渡されます。 機械学習アルゴリズムの Python **scikit-learn-** learning ライブラリに基づいて、出力はトレーニング済みのモデルになります。 

   このコードでは、 [**pickle**](https://docs.python.org/2/library/pickle.html)を使用してモデルをシリアル化します。 モデルは、 **iris_data**テーブルの列 0 ~ 4 のデータを使用してトレーニングされます。 
   
   プロシージャの2番目の部分に表示されるパラメーターは、データ入力とモデル出力を明確にします。 可能な限り、ストアドプロシージャで実行されている Python コードでは、実行時に渡されたストアドプロシージャの入力と出力にマップされる入力と出力を明確に定義する必要があります。 

    ```sql
    CREATE PROCEDURE generate_iris_model (@trained_model varbinary(max) OUTPUT)
    AS
    BEGIN
    EXEC sp_execute_external_script @language = N'Python',
    @script = N'
    import pickle
    from sklearn.naive_bayes import GaussianNB
    GNB = GaussianNB()
    trained_model = pickle.dumps(GNB.fit(iris_data[[0,1,2,3]], iris_data[[4]].values.ravel()))
    '
    , @input_data_1 = N'select "Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "SpeciesId" from iris_data'
    , @input_data_1_name = N'iris_data'
    , @params = N'@trained_model varbinary(max) OUTPUT'
    , @trained_model = @trained_model OUTPUT;
    END;
    GO
    ```

3. ストアドプロシージャが存在することを確認します。 

   前の手順の T-sql スクリプトがエラーなしで実行された場合は、 **generate_iris_model**という名前の新しいストアドプロシージャが作成され、 **irissql**データベースに追加されます。 ストアドプロシージャは、Management Studio の**オブジェクトエクスプローラー**の **[プログラミング]** で見つけることができます。

## <a name="execute-the-procedure-to-create-and-train-models"></a>手順を実行してモデルを作成およびトレーニングする

このステップでは、プロシージャを実行して埋め込みコードを実行し、トレーニング済みの、シリアル化されたモデルを出力として作成します。 SQL Server で再利用するために格納されているモデルは、バイトストリームとしてシリアル化され、データベーステーブルの VARBINARY (MAX) 列に格納されます。 モデルの作成、トレーニング、シリアル化、およびデータベースへの保存が完了すると、他のプロシージャ、またはスコアリングワークロードの[t-sql の予測](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql)関数で呼び出すことができます。

1. 次のコードをコピーして、プロシージャを実行します。 ストアドプロシージャを実行するための特定の`EXEC`ステートメントは、5行目にあります。

   この特定のスクリプトは、同じ名前の既存のモデル ("Naive Bayes") を削除して、同じプロシージャを再実行することによって作成された新しいものを確保します。 モデルを削除しないと、オブジェクトが既に存在することを示すエラーが発生します。 このモデルは、 **irissql**データベースを作成したときにプロビジョニングされた**iris_models**という名前のテーブルに格納されます。

    ```sql
    DECLARE @model varbinary(max);
    DECLARE @new_model_name varchar(50)
    SET @new_model_name = 'Naive Bayes'
    EXEC generate_iris_model @model OUTPUT;
    DELETE iris_models WHERE model_name = @new_model_name;
    INSERT INTO iris_models (model_name, model) values(@new_model_name, @model);
    GO
    ```

2. モデルの一覧を返すための別の方法が挿入されたことを確認します。

    ```sql
    SELECT * FROM dbo.iris_models
    ```

    **結果**

    | model_name  | model |
    |---|-----------------|
    | Naive Bayes | 0x800363736B6C6561726E2E6E616976655F62617965730A... | 

## <a name="create-and-execute-a-stored-procedure-for-generating-predictions"></a>予測を生成するためのストアドプロシージャを作成および実行する

モデルの作成、トレーニング、保存が完了したので、次の手順「予測を生成するストアドプロシージャの作成」に進みます。 これを行うには、sp_execute_external_script を呼び出して Python を起動してから、最後の練習で作成したシリアル化されたモデルを読み込む Python スクリプトを渡して、スコアを入力します。

1. 次のコードを実行して、スコアリングを実行するストアドプロシージャを作成します。 実行時に、このプロシージャはバイナリモデルを読み込み、入力と`[1,2,3,4]`して列を使用し`[0,5,6]`て、列を出力として指定します。

    ```sql
    CREATE PROCEDURE predict_species (@model varchar(100))
    AS
    BEGIN
    DECLARE @nb_model varbinary(max) = (SELECT model FROM iris_models WHERE model_name = @model);
    EXEC sp_execute_external_script @language = N'Python', 
    @script = N'
    import pickle
    irismodel = pickle.loads(nb_model)
    species_pred = irismodel.predict(iris_data[[1,2,3,4]])
    iris_data["PredictedSpecies"] = species_pred
    OutputDataSet = iris_data[[0,5,6]] 
    print(OutputDataSet)
    '
    , @input_data_1 = N'select id, "Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "SpeciesId" from iris_data'
    , @input_data_1_name = N'iris_data'
    , @params = N'@nb_model varbinary(max)'
    , @nb_model = @nb_model
    WITH RESULT SETS ( ("id" int, "SpeciesId" int, "SpeciesId.Predicted" int));
    END;
    GO
    ```

2. 使用するモデルをプロシージャが認識できるように、ストアドプロシージャを実行して、モデル名 "Naive Bayes" を指定します。 

    ```sql
    EXEC predict_species 'Naive Bayes';
    GO
    ```

    ストアドプロシージャを実行すると、Python データフレームが返されます。 T-sql の次の行は、返される結果`WITH RESULT SETS ( ("id" int, "SpeciesId" int, "SpeciesId.Predicted" int));`のスキーマを指定します。 結果を新しいテーブルに挿入するか、アプリケーションに返すことができます。

    ![実行中のストアドプロシージャからの結果セット](media/train-score-using-python-NB-model-results.png)

    結果は、フローラル特性を入力として使用している動物に関する150予測です。 観測の大部分については、予測された動物が実際の条件に一致します。

    この例は、トレーニングとスコアリングの両方に Python の虹彩データセットを使用することによって単純になっています。 より一般的な方法では、SQL クエリを実行して新しいデータを取得し、それを Python `InputDataSet`にとして渡します。 

## <a name="conclusion"></a>まとめ

この演習では、各ストアドプロシージャがシステムストアドプロシージャ[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)を使用して Python プロセスを開始する、さまざまなタスクに専用のストアドプロシージャを作成する方法について学習しました。 Python プロセスへの入力は、パラメーターとして sp_execute_external スクリプトに渡されます。 SQL Server データベースの Python スクリプト自体とデータ変数は両方とも入力として渡されます。

一般に、SSMS は洗練された Python コードと共に使用するか、行ベースの出力を返す単純な Python コードでのみ計画する必要があります。 ツールとして、SSMS は T-sql などのクエリ言語をサポートし、フラット化された行セットを返します。 コードで散布図やヒストグラムなどのビジュアル出力を生成する場合は、イメージをレンダリングできるツールまたはエンドユーザーアプリケーションが必要です。

さまざまな操作を処理するすべての包括的なスクリプトを記述するために使用される Python 開発者によっては、タスクを個別の手順で整理する必要がない場合があります。 しかし、トレーニングとスコアリングにはさまざまなユースケースがあります。 これらを分離することにより、各タスクを異なるスケジュールに設定し、アクセス許可を操作に適用できます。

同様に、並列処理やリソースガバナンスなどの SQL Server のリソース機能を活用したり、ストリーミングと並列実行をサポートする[revoscalepy](../python/ref-py-revoscalepy.md)または[microsoft ml](../python/ref-py-microsoftml.md)のアルゴリズムを使用するようにスクリプトを記述したりすることもできます。 トレーニングとスコア付けを分離することにより、特定のワークロードの最適化を対象にすることができます。

最終的な利点は、パラメーターを使用してプロセスを変更できることです。 この演習では、モデルを作成した Python コード (この例では "Naive Bayes" という名前) が、スコア付けプロセスでモデルを呼び出す2番目のストアドプロシージャに入力として渡されました。 この演習では1つのモデルのみを使用しますが、スコア付けタスクでモデルをパラメーター化することによってスクリプトの有用性が向上することを想像できます。

## <a name="next-steps"></a>次の手順

Python を初めて使用する場合は、ローカルセッションからリモート SQL Server インスタンスに実行をシフトする機能を使用して、Python コードをローカルで操作するための手順とツールを確認してください。

> [!div class="nextstepaction"]
> [Python クライアントワークステーションを設定](../python/setup-python-client-tools-sql.md)します。

