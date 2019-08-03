---
title: 事前トレーニング済みの機械学習モデルをインストールする
description: センチメント analysis および image 特性付けの事前トレーニング済みのモデルを SQL Server Machine Learning Services (R または Python) または SQL Server R Services に追加します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 87f75b8ef8f9f151eb548787da4c9791eb1437b9
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715160"
---
# <a name="install-pre-trained-machine-learning-models-on-sql-server"></a>事前トレーニング済みの機械学習モデルを SQL Server にインストールする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、Powershell を使用して、*センチメント分析*用の無料の事前トレーニング済みの機械学習モデルと、R または Python を統合した SQL Server インスタンスに*イメージ特性付け*を追加する方法について説明します。 事前トレーニング済みのモデルは、インストール後のタスクとしてインスタンスに追加された、Microsoft によって構築され、すぐに使用できるようになりました。 これらのモデルの詳細については、この記事の「[リソース](#bkmk_resources)」セクションを参照してください。

インストールが完了すると、事前トレーニング済みのモデルは、Microsoft Ml (R) ライブラリと microsoft ml (Python) ライブラリで特定の機能を備えた実装の詳細と見なされます。 モデルを表示、カスタマイズ、または再トレーニングすることはできません。また、これらをカスタムコードの独立したリソースとして扱うことや、他の関数と組み合わせて使用することもできません。 

事前トレーニング済みのモデルを使用するには、次の表に示す関数を呼び出します。

| R 関数 (Microsoft Ml) | Python 関数 (microsoft ml) | 使用方法 |
|--------------------------|-------------------------------|-------|
| [getSentiment](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/getsentiment) | [get_sentiment](https://docs.microsoft.com//machine-learning-server/python-reference/microsoftml/get-sentiment) | テキスト入力に対して正の負のセンチメントスコアを生成します。 |
| [featurizeImage](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/featurizeimage) | [featurize_image](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/featurize-image) | 画像ファイル入力からテキスト情報を抽出します。 |

## <a name="prerequisites"></a>前提条件

機械学習アルゴリズムは、計算を集中的に行います。 すべてのサンプルデータを使用したチュートリアルチュートリアルの完了など、低 ~ 中レベルのワークロードには 16 GB の RAM を使用することをお勧めします。

コンピューターに対する管理者権限と、事前トレーニング済みのモデルを追加するための SQL Server が必要です。

外部スクリプトを有効にし、SQL Server スタートパッドサービスが実行されている必要があります。 インストール手順では、これらの機能を有効にして確認する手順について説明します。 

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
[Microsoft Ml R パッケージ](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/microsoftml-package)または[microsoft ml Python パッケージ](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)には、事前トレーニング済みのモデルが含まれています。

[SQL Server Machine Learning Services](sql-machine-learning-services-windows-install.md)には、Machine Learning ライブラリの言語バージョンが両方とも含まれているので、この前提条件は、これ以上の操作は必要ありません。 ライブラリが存在するため、この記事で説明されている PowerShell スクリプトを使用して、事前トレーニング済みのモデルをこれらのライブラリに追加できます。
::: moniker-end

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
[Microsoft Ml R パッケージ](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/microsoftml-package)には、事前トレーニング済みのモデルが含まれています。

[SQL Server R Services](sql-r-services-windows-install.md)(R のみ) には、すぐに使用できる[microsoft ml パッケージ](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/microsoftml-package)が含まれていません。 Microsoft Ml を追加するには、[コンポーネントのアップグレード](../install/upgrade-r-and-python.md)を行う必要があります。 コンポーネントのアップグレードの利点の1つは、事前トレーニング済みのモデルを同時に追加できることです。これにより、PowerShell スクリプトの実行が不要になります。 ただし、既にアップグレードしていて、事前トレーニング済みのモデルを初めて追加しなかった場合は、この記事の説明に従って PowerShell スクリプトを実行できます。 SQL Server の両方のバージョンに対して機能します。 その前に、Microsoft Ml ライブラリがに`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES\library`存在することを確認します。
::: moniker-end

<a name="file-location"></a>

## <a name="check-whether-pre-trained-models-are-installed"></a>事前トレーニング済みのモデルがインストールされているかどうかを確認する

R モデルと Python モデルのインストールパスは次のとおりです。

+ R の場合:`C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library\MicrosoftML\mxLibs\x64`

+ Python の場合:`C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\Lib\site-packages\microsoftml\mxLibs`

モデルファイル名を次に示します。

+ AlexNet\_が更新されました。モデル
+ ImageNet1K\_mean.xml
+ pretrained.model
+ Resnet\_101\_が更新されました。モデル
+ Resnet\_18\_が更新されました。モデル
+ Resnet\_50\_が更新されました。モデル

モデルが既にインストールされている場合は、[検証手順](#verify)に進んで、可用性を確認します。

## <a name="download-the-installation-script"></a>インストールスクリプトをダウンロードする

Install-MLModels [https://aka.ms/mlm4sql](https://aka.ms/mlm4sql)ファイルをダウンロードする場合にクリックします。

## <a name="execute-with-elevated-privileges"></a>昇格された特権で実行する

1. PowerShell を起動します。 タスクバーで、PowerShell プログラムアイコンを右クリックし、 **[管理者として実行]** を選択します。
2. インストールスクリプトファイルへの完全修飾パスを入力し、インスタンス名を含めます。 ダウンロードフォルダーと既定のインスタンスの場合、コマンドは次のようになります。

   ```powershell
   PS C:\WINDOWS\system32> C:\Users\<user-name>\Downloads\Install-MLModels.ps1 MSSQLSERVER
   ```

**出力**

インターネットに接続された SQL Server Machine Learning Services R と Python を使用した既定のインスタンスの場合、次のようなメッセージが表示されます。

   ```powershell
   MSSQL14.MSSQLSERVER
        Verifying R models [9.2.0.24]
        Downloading R models [C:\Users\<user-name>\AppData\Local\Temp]
        Installing R models [C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\]
        Verifying Python models [9.2.0.24]
        Installing Python models [C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\]
   PS C:\WINDOWS\system32>
   ```

<a name="verify"> </a>

## <a name="verify-installation"></a>インストールの確認

まず、 [mxlibs フォルダー](#file-location)に新しいファイルがあるかどうかを確認します。 次に、デモコードを実行して、モデルがインストールされ、機能していることを確認します。 

### <a name="r-verification-steps"></a>R 検証手順

1. **Rgui を起動します。EXE** (C:\Program SERVER\MSSQL14. SQL)MSSQLSERVER\R_SERVICES\bin\x64.

2. コマンドプロンプトで、次の R スクリプトを貼り付けます。

    ```R
    # Create the data
    CustomerReviews <- data.frame(Review = c(
    "I really did not like the taste of it",
    "It was surprisingly quite good!",
    "I will never ever ever go to that place again!!"),
    stringsAsFactors = FALSE)

    # Get the sentiment scores
    sentimentScores <- rxFeaturize(data = CustomerReviews, 
                                    mlTransforms = getSentiment(vars = list(SentimentScore = "Review")))

    # Let's translate the score to something more meaningful
    sentimentScores$PredictedRating <- ifelse(sentimentScores$SentimentScore > 0.6, 
                                            "AWESOMENESS", "BLAH")

    # Let's look at the results
    sentimentScores
    ```

3. Enter キーを押して、センチメントスコアを表示します。 出力は次のようになります。

    ```R
    > sentimentScores
                                            Review SentimentScore
    1           I really did not like the taste of it      0.4617899
    2                 It was surprisingly quite good!      0.9601924
    3 I will never ever ever go to that place again!!      0.3103435
    PredictedRating
    1            BLAH
    2     AWESOMENESS
    3            BLAH
    ```

### <a name="python-verification-steps"></a>Python の検証手順

1. C:\Program Server\MSSQL14. SQL の**Python .exe を起動します。** MSSQLSERVER\PYTHON_SERVICES.

2. コマンドプロンプトで次の Python スクリプトを貼り付けます。

    ```python
    import numpy
    import pandas
    from microsoftml import rx_logistic_regression, rx_featurize, rx_predict, get_sentiment

    # Create the data
    customer_reviews = pandas.DataFrame(data=dict(review=[
                "I really did not like the taste of it",
                "It was surprisingly quite good!",
                "I will never ever ever go to that place again!!"]))
                
    # Get the sentiment scores
    sentiment_scores = rx_featurize(
        data=customer_reviews,
        ml_transforms=[get_sentiment(cols=dict(scores="review"))])
        
    # Let's translate the score to something more meaningful
    sentiment_scores["eval"] = sentiment_scores.scores.apply(
                lambda score: "AWESOMENESS" if score > 0.6 else "BLAH")
    print(sentiment_scores)
    ```

3. スコアを印刷するには、Enter キーを押します。 出力は次のようになります。

    ```python
    >>> print(sentiment_scores)
                                                review    scores         eval
    0            I really did not like the taste of it  0.461790         BLAH
    1                  It was surprisingly quite good!  0.960192  AWESOMENESS
    2  I will never ever ever go to that place again!!  0.310344         BLAH
    >>>
    ```

> [!NOTE]
> デモスクリプトが失敗した場合は、最初にファイルの場所を確認してください。 SQL Server の複数のインスタンスがあるシステム、またはスタンドアロンバージョンとサイドバイサイドで実行されているインスタンスの場合、インストールスクリプトによって環境が誤って読み取られ、ファイルが間違った場所に配置される可能性があります。 通常、正しい mxlib フォルダーに手動でファイルをコピーすると、問題が解決されます。

## <a name="examples-using-pre-trained-models"></a>事前トレーニング済みのモデルを使用した例

次のリンクには、事前トレーニング済みのモデルを呼び出すコード例が含まれています。

+ [コードサンプル:テキスト Featurizer を使用した感情分析](https://github.com/Microsoft/microsoft-r/tree/master/microsoft-ml/Samples/101/BinaryClassification/SimpleSentimentAnalysis)

<a name="bkmk_resources"></a> 

## <a name="research-and-resources"></a>研究とリソース

現在使用できるモデルは、センチメント分析とイメージ分類のためのディープニューラルネットワーク (DNN) モデルです。 事前トレーニング済みのすべてのモデルは、Microsoft の[コンピュテーション Network Toolkit](https://cntk.ai/Features/Index.html)または**cntk**を使用してトレーニングされています。

各ネットワークの構成は、次の参照実装に基づいています。

+ ResNet-18
+ ResNet-50
+ ResNet-101
+ AlexNet

これらのディープラーニングモデルで使用されるアルゴリズムの詳細と、CNTK を使用した実装方法とトレーニング方法については、次の記事を参照してください。

+ [Microsoft の研究者のアルゴリズム設定 ImageNet チャレンジマイルストーン](https://www.microsoft.com/research/blog/microsoft-researchers-algorithm-sets-imagenet-challenge-milestone/)

+ [Microsoft のコンピューティングネットワークツールキットでは、最も効率的に分散されたディープラーニングの計算パフォーマンスが提供されます](https://www.microsoft.com/research/blog/microsoft-computational-network-toolkit-offers-most-efficient-distributed-deep-learning-computational-performance/)

## <a name="see-also"></a>関連項目

+ [SQL Server Machine Learning Services](sql-machine-learning-services-windows-install.md)
+ [SQL Server インスタンスの R および Python コンポーネントをアップグレードする](../install/upgrade-r-and-python.md)
+ [R 用の Microsoft Ml パッケージ](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/microsoftml-package)
+ [Python 用 microsoft ml パッケージ](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)
