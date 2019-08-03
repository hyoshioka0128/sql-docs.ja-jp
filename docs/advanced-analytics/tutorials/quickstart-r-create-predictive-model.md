---
title: R を使用して予測モデルを作成するためのクイックスタート
description: このクイックスタートでは、予測をプロットするために SQL Server データを使用して、R でモデルを構築する方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: f902bd7325e84f07d50196c19338d9c05b3503d8
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715443"
---
# <a name="quickstart-create-a-predictive-model-using-r-in-sql-server"></a>クイック スタート: SQL Server で R を使用して予測モデルを作成する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このクイックスタートでは、R を使用してモデルをトレーニングし、SQL Server のテーブルにモデルを保存する方法について説明します。 モデルは、車両が手動で転送された確率を予測する単純な汎用線形モデル (GLM) です。 R に含まれる`mtcars`データセットを使用します。

## <a name="prerequisites"></a>必須コンポーネント

前のクイックスタート「 [SQL Server に r が存在することを確認](quickstart-r-verify.md)する」では、このクイックスタートに必要な r 環境を設定するための情報とリンクを提供しています。

## <a name="create-the-source-data"></a>ソース データを作成する

最初に、トレーニング データを保存するテーブルを作成します。

```sql
CREATE TABLE dbo.MTCars(
    mpg decimal(10, 1) NOT NULL,
    cyl int NOT NULL,
    disp decimal(10, 1) NOT NULL,
    hp int NOT NULL,
    drat decimal(10, 2) NOT NULL,
    wt decimal(10, 3) NOT NULL,
    qsec decimal(10, 2) NOT NULL,
    vs int NOT NULL,
    am int NOT NULL,
    gear int NOT NULL,
    carb int NOT NULL
);
```

次に、データセット`mtcars`にビルドのデータを挿入します。

```SQL
INSERT INTO dbo.MTCars
EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'MTCars <- mtcars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'MTCars';
```

+ 一時テーブルを使用する場合もありますが、一部の R クライアントはバッチ間でセッションを切断することに注意してください。

+ 小規模と大規模の多くのデータセットが R ランタイムに付属しています。 R と共にインストールされるデータセットの一覧を取得するには、R コマンド プロンプトから「`library(help="datasets")`」と入力します。

## <a name="create-a-model"></a>モデルを作成する

自動車速度データには、数値、馬力 (`hp`)、および weight (`wt`) の2つの列が含まれています。 このデータから、車両が手動で転送された確率を予測する汎用線形モデル (GLM) を作成します。

モデルを作成するには、R コード内に数式を定義し、入力パラメーターとしてそのデータを渡します。

```sql
DROP PROCEDURE IF EXISTS generate_GLM;
GO
CREATE PROCEDURE generate_GLM
AS
BEGIN
    EXEC sp_execute_external_script
    @language = N'R'
    , @script = N'carsModel <- glm(formula = am ~ hp + wt, data = MTCarsData, family = binomial);
        trained_model <- data.frame(payload = as.raw(serialize(carsModel, connection=NULL)));'
    , @input_data_1 = N'SELECT hp, wt, am FROM MTCars'
    , @input_data_1_name = N'MTCarsData'
    , @output_data_1_name = N'trained_model'
    WITH RESULT SETS ((model VARBINARY(max)));
END;
GO
```

+ の最初の引数`glm`は、に`hp + wt`依存するように`am`定義する*数式*パラメーターです。
+ 入力データは、SQL クエリによって設定される変数 `MTCarsData` に格納されます。 入力データに特定の名前を割り当てない場合、既定の変数名は "_InputDataSet_" になります。

## <a name="create-a-table-for-the-model"></a>モデルのテーブルを作成する

次に、モデルを再トレーニングまたは予測に使用できるように保存します。 モデルを作成する R パッケージの出力は、通常は**バイナリ オブジェクト**です。 そのため、モデルを格納するテーブルには、 **varbinary (max)** 型の列を指定する必要があります。

```sql
CREATE TABLE GLM_models (
    model_name varchar(30) not null default('default model') primary key,
    model varbinary(max) not null
);
```

## <a name="save-the-model"></a>モデルを保存する

モデルを保存するには、次の Transact-SQL ステートメントを実行してストアド プロシージャを呼び出し、モデルを生成し、テーブルに保存します。

```sql
INSERT INTO GLM_models(model)
EXEC generate_GLM;
```

このコードをもう一度実行すると、次のエラーが表示されることに注意してください。

```sql
Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
```

このエラーを避ける 1 つのオプションは、新しいモデルごとに名前を更新することです。 たとえば、わかりやすい名前に変更し、モデルの種類や作成日などを含めることができます。

```sql
UPDATE GLM_models
SET model_name = 'GLM_' + format(getdate(), 'yyyy.MM.HH.mm', 'en-gb')
WHERE model_name = 'default model'
```

## <a name="next-steps"></a>次の手順

モデルが完成したので、最後のクイックスタートでは、そこから予測を生成し、結果をプロットする方法を学習します。

> [!div class="nextstepaction"]
> [クイック スタート:モデルからの予測とプロット](quickstart-r-predict-from-model.md)