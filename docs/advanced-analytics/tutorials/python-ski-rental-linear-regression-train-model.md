---
title: Python のチュートリアル:モデルのトレーニング (線形回帰)
description: このチュートリアルでは、SQL Server Machine Learning Services で Python と線形回帰を使用して、ski のレンタル数を予測します。 Python で線形回帰モデルをトレーニングします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/03/2019
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 30f390681dc63d6de9a95e805b6cc8f273b2b8d7
ms.sourcegitcommit: ecb19d0be87c38a283014dbc330adc2f1819a697
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242550"
---
# <a name="python-tutorial-train-a-linear-regression-model-in-sql-server-machine-learning-services"></a>Python のチュートリアル:SQL Server Machine Learning Services で線形回帰モデルをトレーニングする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この4部構成のチュートリアルシリーズのパート3では、Python で線形回帰モデルをトレーニングします。 このシリーズの次のパートでは、Machine Learning Services を使用して、このモデルを SQL Server データベースにデプロイします。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * 線形回帰モデルをトレーニングする
> * 線形回帰モデルを使用して予測を行う

[パート 1](python-ski-rental-linear-regression.md)では、サンプルデータベースを復元する方法を学習しました。

[パート 2](python-ski-rental-linear-regression-prepare-data.md)では、SQL Server から python データフレームにデータを読み込み、python でデータを準備する方法を学習しました。

[パート 4](python-ski-rental-linear-regression-deploy-model.md)では、モデルを SQL Server に格納する方法について学習した後、パート2と3で開発した Python スクリプトからストアドプロシージャを作成します。 ストアドプロシージャは、新しいデータに基づいて予測を行うために SQL Server で実行されます。

## <a name="prerequisites"></a>前提条件

* このチュートリアルのパート3では、[パート 1](python-ski-rental-linear-regression.md)とその前提条件を完了していることを前提としています。

## <a name="train-the-model"></a>モデルのトレーニング

予測するには、データセット内の変数間の依存関係を最もよく説明する関数 (モデル) を見つける必要があります。 これは、モデルのトレーニングと呼ばれます。 トレーニングデータセットは、このシリーズの第2部で作成した pandas のデータフレーム**df**のデータセット全体のサブセットになります。

モデル**lin_model**は線形回帰アルゴリズムを使用してトレーニングします。

```python
# Store the variable we'll be predicting on.
target = "RentalCount"

# Generate the training set.  Set random_state to be able to replicate results.
train = df.sample(frac=0.8, random_state=1)

# Select anything not in the training set and put it in the testing set.
test = df.loc[~df.index.isin(train.index)]

# Print the shapes of both sets.
print("Training set shape:", train.shape)
print("Testing set shape:", test.shape)

# Initialize the model class.
lin_model = LinearRegression()

# Fit the model to the training data.
lin_model.fit(train[columns], train[target])
```

次のような結果が表示されます。

```results
Training set shape: (362, 7)
Testing set shape: (91, 7)
```

## <a name="make-predictions"></a>予測の作成

モデル**lin_model**を使用してレンタルカウントを予測するには、predict 関数を使用します。

```python
# Generate our predictions for the test set.
lin_predictions = lin_model.predict(test[columns])
print("Predictions:", lin_predictions)
# Compute error between our test predictions and the actual values.
lin_mse = mean_squared_error(lin_predictions, test[target])
print("Computed error:", lin_mse)
```

```results
Predictions: [  40.   38.  240.   39.  514.   48.  297.   25.  507.   24.   30.   54.
   40.   26.   30.   34.   42.  390.  336.   37.   22.   35.   55.  350.
  252.  370.  499.   48.   37.  494.   46.   25.  312.  390.   35.   35.
  421.   39.  176.   21.   33.  452.   34.   28.   37.  260.   49.  577.
  312.   24.   24.  390.   34.   64.   26.   32.   33.  358.  348.   25.
   35.   48.   39.   44.   58.   24.  350.  651.   38.  468.   26.   42.
  310.  709.  155.   26.  648.  617.   26.  846.  729.   44.  432.   25.
   39.   28.  325.   46.   36.   50.   63.]
Computed error: 3.59831533436e-26
```

## <a name="next-steps"></a>次の手順

このチュートリアルシリーズの第3部では、次の手順を完了しました。

* 線形回帰モデルをトレーニングする
* 線形回帰モデルを使用して予測を行う

作成した機械学習モデルをデプロイするには、このチュートリアルシリーズの第4部に従います。

> [!div class="nextstepaction"]
> [Python のチュートリアル:Machine learning モデルをデプロイする](python-ski-rental-linear-regression-deploy-model.md)