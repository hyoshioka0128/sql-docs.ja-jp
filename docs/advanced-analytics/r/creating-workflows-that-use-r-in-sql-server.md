---
title: R での SSIS と SSRS のワークフローを作成する
description: SQL Server Machine Learning Services と R Services、Reporting Services (SSRS)、および SQL Server Integration Services (SSIS) を組み合わせた統合シナリオ。
ms.prod: sql
ms.technology: machine-learning
ms.date: 03/17/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 2b8d55e95991437e4d76911fd26afb5b1bc9c550
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715172"
---
# <a name="create-ssis-and-ssrs-workflows-with-r-on-sql-server"></a>SQL Server で R を使用して SSIS および SSRS ワークフローを作成する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server Machine Learning Services の言語およびデータサイエンス機能を使用して、埋め込み R と Python スクリプトを使用する方法について、次の2つの重要な SQL Server 機能を使用して説明します。SQL Server Integration Services (SSIS) と SQL Server Reporting Services SSRS。 SQL Server の R および Python ライブラリには、統計関数と予測関数が用意されています。 SSIS と SSRS は、それぞれ、連携した ETL 変換と視覚化を提供します。 この記事では、これらのすべての機能をこのワークフローパターンにまとめる方法について説明します。

> [!div class="checklist"]
> * 実行可能 R または Python を含むストアドプロシージャを作成する
> * SSIS または SSRS からのストアドプロシージャの実行

この記事の例は、ほとんどが R と SSIS に関するものですが、これらの概念と手順は Python にも同様に適用されます。 2番目のセクションでは、SSRS 視覚エフェクトのガイダンスとリンクを示します。

<a name="bkmk_ssis"></a> 

## <a name="use-ssis-for-automation"></a>オートメーションに SSIS を使用する

データ サイエンス ワークフローは何度も繰り返され、スケーリング、集計、確率計算、および属性の名前変更とマージなど、多くのデータ変換を含みます。 データ サイエンティストはこのようなタスクの多くを R、Python または他の言語で実行することに慣れていますが、これらのワークフローをエンタープライズ データで実行するには、ETL ツールとプロセスのシームレスな統合が必要になります。

を[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]使用すると、transact-sql とストアドプロシージャを使用して R で複雑な操作を実行できるため、データサイエンスタスクを既存の ETL プロセスと統合できます。 メモリを集中的に使用するタスクのチェーンを実行するのではなく、や[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] [!INCLUDE[tsql](../../includes/tsql-md.md)]など、最も効率的なツールを使用してデータの準備を最適化することができます。 

次に、を使用して[!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)]データ処理とモデリングパイプラインを自動化する方法に関するいくつかのヒントを示します。

+ オンプレミスまたはクラウドのソースからデータを抽出してトレーニングデータを構築する 
+ データ統合ワークフローの一部として R または Python モデルを構築して実行する
+ 定期的 (スケジュール) に従ってモデルを再トレーニングする
+ R または Python スクリプトの結果を、Excel、Power BI、Oracle、Teradata などの他の変換先に読み込み、いくつかの名前を付けます。
+ SSIS タスクを使用して SQL データベースにデータ機能を作成する
+ 条件分岐を使用して R ジョブと Python ジョブの計算コンテキストを切り替える

## <a name="ssis-example"></a>SSIS の例

次の例は、この URL で Jimmy Wong によって作成された、提供終了になった MSDN ブログ投稿を基にしています。`https://blogs.msdn.microsoft.com/ssis/2016/01/11/operationalize-your-machine-learning-project-using-sql-server-2016-ssis-and-r-services/`

この例では、SSIS を使用してタスクを自動化する方法を示します。 SQL Server Management Studio を使用して R を埋め込んだストアドプロシージャを作成し、それらのストアドプロシージャを SSIS パッケージの[t-sql タスクを実行](https://docs.microsoft.com/sql/integration-services/control-flow/execute-t-sql-statement-task)して実行します。

この例を実行するには、Management Studio、SSIS、SSIS デザイナー、パッケージのデザイン、T-sql について理解している必要があります。 SSIS パッケージでは、トレーニングデータをテーブルに挿入し、データをモデル化し、データをスコア付けして予測出力を取得する、3つの[T-sql 実行タスク](https://docs.microsoft.com/sql/integration-services/control-flow/execute-t-sql-statement-task)を使用します。

### <a name="load-training-data"></a>トレーニングデータの読み込み

データを格納するテーブルを作成するには、SQL Server Management Studio で次のスクリプトを実行します。 この演習では、テストデータベースを作成して使用する必要があります。 

```T-SQL
Use test-db
GO

Create table ssis_iris (
    id int not null identity primary key
    , "Sepal.Length" float not null, "Sepal.Width" float not null
    , "Petal.Length" float not null, "Petal.Width" float not null
    , "Species" varchar(100) null
);
GO
```

データフレームにトレーニングデータを読み込むストアドプロシージャを作成します。 この例では、組み込みの虹彩データセットを使用しています。 

```T-SQL
Create procedure load_iris
as
begin
    execute sp_execute_external_script
        @language = N'R'
        , @script = N'iris_df <- iris;'
        , @input_data_1 = N''
        , @output_data_1_name = N'iris_df'
    with result sets (("Sepal.Length" float not null, "Sepal.Width" float not null, "Petal.Length" float not null, "Petal.Width" float not null, "Species" varchar(100)));
end;
```

SSIS デザイナーで、先ほど定義したストアドプロシージャを実行する[SQL 実行タスク](https://docs.microsoft.com/sql/integration-services/control-flow/execute-sql-task)を作成します。 **SQLStatement**のスクリプトは、既存のデータを削除し、挿入するデータを指定してから、ストアドプロシージャを呼び出してデータを提供します。

```T-SQL
truncate table ssis_iris;
insert into ssis_iris("Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "Species")
exec dbo.load_iris;
```

![データの挿入](../media/create-workflows-using-r-in-sql-server/ssis-exec-sql-insert-data.png "データの挿入")

### <a name="generate-a-model"></a>モデルの生成

SQL Server Management Studio で次のスクリプトを実行して、モデルを格納するテーブルを作成します。 

```T-SQL
Use test-db
GO

Create table ssis_iris_models (
    model_name varchar(30) not null default('default model') primary key,
    model varbinary(max) not null
);
GO
```

[RxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod)を使用して線形モデルを生成するストアドプロシージャを作成します。 RevoScaleR と revoscalepy のライブラリは、R および Python セッションで SQL Server で自動的に使用できるため、ライブラリをインポートする必要はありません。

```T-SQL
Create procedure generate_iris_rx_model
as
begin
    execute sp_execute_external_script
        @language = N'R'
        , @script = N'
          irisLinMod <- rxLinMod(Sepal.Length ~ Sepal.Width + Petal.Length + Petal.Width + Species, data = ssis_iris);
          trained_model <- data.frame(payload = as.raw(serialize(irisLinMod, connection=NULL)));'
        , @input_data_1 = N'select "Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "Species" from ssis_iris'
        , @input_data_1_name = N'ssis_iris'
        , @output_data_1_name = N'trained_model'
    with result sets ((model varbinary(max)));
end;
GO
```

SSIS デザイナーで、 **generate_iris_rx_model**ストアドプロシージャを実行する[SQL 実行タスク](https://docs.microsoft.com/sql/integration-services/control-flow/execute-sql-task)を作成します。 モデルはシリアル化され、ssis_iris_models テーブルに保存されます。 **SQLStatement**のスクリプトは次のとおりです。

```T-SQL
insert into ssis_iris_models (model)
exec generate_iris_rx_model;
update ssis_iris_models set model_name = 'rxLinMod' where model_name = 'default model';
```

![線形モデルを生成し]ます(../media/create-workflows-using-r-in-sql-server/ssis-exec-rxlinmod.png "線形モデルを生成し")ます

チェックポイントとして、このタスクが完了したら、ssis_iris_models に対してクエリを実行し、1つのバイナリモデルが含まれていることを確認できます。

### <a name="predict-score-outcomes-using-the-trained-model"></a>"トレーニング済み" モデルを使用して結果を予測 (スコア) する

これで、トレーニングデータを読み込み、モデルを生成するコードが完成しました。残りの手順は、モデルを使用して予測を生成することだけです。 

これを行うには、SQL クエリに R スクリプトを配置して、ssis_iris_model で[rxPredict](https://docs.microsoft.com//machine-learning-server/r-reference/revoscaler/rxpredict)組み込み r 関数をトリガーします。 **Predict_species_length**というストアドプロシージャは、このタスクを実現します。

```T-SQL
Create procedure predict_species_length (@model varchar(100))
as
begin
    declare @rx_model varbinary(max) = (select model from ssis_iris_models where model_name = @model);
    -- Predict based on the specified model:
    exec sp_execute_external_script
        @language = N'R'
        , @script = N'
irismodel <-unserialize(rx_model);
irispred <- rxPredict(irismodel, ssis_iris[,2:6]);
OutputDataSet <- cbind(ssis_iris[1], irispred$Sepal.Length_Pred, ssis_iris[2]);
colnames(OutputDataSet) <- c("id", "Sepal.Length.Actual", "Sepal.Length.Expected");
#OutputDataSet <- subset(OutputDataSet, Species.Length.Actual != Species.Expected);
'
    , @input_data_1 = N'
    select id, "Sepal.Length", "Sepal.Width", "Petal.Length", "Petal.Width", "Species" from ssis_iris
    '
    , @input_data_1_name = N'ssis_iris'
    , @params = N'@rx_model varbinary(max)'
    , @rx_model = @rx_model
    with result sets ( ("id" int, "Species.Length.Actual" float, "Species.Length.Expected" float)
        );
end;
```

SSIS デザイナーで、予測される花弁長を生成するために**predict_species_length**ストアドプロシージャを実行する[SQL 実行タスク](https://docs.microsoft.com/sql/integration-services/control-flow/execute-sql-task)を作成します。

```T-SQL
exec predict_species_length 'rxLinMod';
```

![予測の生成](../media/create-workflows-using-r-in-sql-server/ssis-exec-predictions.png "予測の生成")

### <a name="run-the-solution"></a>ソリューションを実行する

SSIS デザイナーで、F5 キーを押してパッケージを実行します。 次のスクリーンショットのような結果が表示されます。

![F5 キーを押してデバッグモードで実行する](../media/create-workflows-using-r-in-sql-server/ssis-exec-F5-run.png "F5 キーを押してデバッグモードで実行する")

<a name="bkmk_ssrs"></a> 

## <a name="use-ssrs-for-visualizations"></a>SSRS を視覚エフェクトに使用する

R はグラフや興味深い視覚エフェクトを作成できますが、外部データソースとは適切に統合されていません。つまり、各グラフまたはグラフを個別に生成する必要があります。 共有も困難な可能性があります。

を使用[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]すると、 [!INCLUDE[tsql](../../includes/tsql-md.md)]ストアドプロシージャを使用して R で複雑な操作を実行できます。ストアドプロシージャは、や Power BI を[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]含むさまざまなエンタープライズレポートツールで簡単に使用できます。

### <a name="ssrs-example"></a>SSRS の例

[R Graphics Device for Microsoft Reporting Services (SSRS)](https://rgraphicsdevice.codeplex.com/) (Microsoft Reporting Services (SSRS) 用の R グラフィックス デバイス)

この CodePlex プロジェクトでは、R のグラフィックス出力をレポートで[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)]使用できるイメージとしてレンダリングするカスタムレポートアイテムを作成するためのコードを提供しています。  カスタム レポート アイテムを使用すると、次の操作を実行できます。

+ R グラフィックス デバイスを使用して作成したグラフとプロットを [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] ダッシュボードに公開する

+ [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] パラメーターを R プロットに渡す

> [!NOTE]
> このサンプルでは、Reporting Services 用の R グラフィックスデバイスをサポートするコードが、Reporting Services サーバー、および Visual Studio にインストールされている必要があります。 手動によるコンパイルと構成も必要です。

## <a name="next-steps"></a>次のステップ

この記事の SSIS と SSRS の例では、埋め込みの R または Python スクリプトを含むストアドプロシージャを実行する2つのケースを示します。 重要な通じ重要は、ストアドプロシージャに対して実行要求を送信できるアプリケーションやツールで R または Python スクリプトを使用できるようにすることです。 SSIS の追加通じ重要として、データの取得、クレンジング、操作など、さまざまな操作を自動化してスケジュール設定するパッケージを作成できます。このパッケージは、操作チェーンに含まれる R または Python データサイエンス機能を使用して行います。 詳細とアイデアについては、 [SQL Server Machine Learning Services の「ストアドプロシージャを使用した R コードの運用化](operationalizing-your-r-code.md)」を参照してください。