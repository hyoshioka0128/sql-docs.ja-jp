---
title: SQL と R の関数を使用したグラフとプロットの作成-SQL Server Machine Learning
description: SQL Server で R 言語関数を使用してグラフやプロットを作成する方法を示すチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 3e8293fecf351176ac2b1e88176395f6c2b34d20
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715311"
---
# <a name="create-graphs-and-plots-using-sql-and-r-walkthrough"></a>SQL と R を使用したグラフとプロットの作成 (チュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このチュートリアルのパートでは、R を使用して SQL Server データでプロットとマップを生成する方法について説明します。 単純なヒストグラムを作成し、より複雑なマッププロットを開発します。

## <a name="prerequisites"></a>前提条件

この手順では、このチュートリアルの前の手順に基づいて、R セッションが継続的に実行されることを前提としています。 ここでは、これらの手順で作成した接続文字列とデータソースオブジェクトを使用します。 スクリプトの実行には、次のツールとパッケージが使用されます。

+ R コマンドを実行するための rgui .exe
+ T-sql の実行 Management Studio
+ googMap
+ ggmap パッケージ
+ mapproj パッケージ

## <a name="create-a-histogram"></a>ヒストグラムを作成する

1. [rxHistogram](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxdatasource) 関数を利用し、最初のプロットを生成します。  RxHistogram 関数は、オープンソース R パッケージの場合と同様の機能を提供しますが、リモート実行コンテキストで実行できます。

    ```R
    # Plot fare amount on SQL Server and return the plot
    start.time <- proc.time()
    rxHistogram(~fare_amount, data = inDataSource, title = "Fare Amount Histogram")
    used.time <- proc.time() - start.time
    print(paste("It takes CPU Time=", round(used.time[1]+used.time[2],2), " seconds, Elapsed Time=", round(used.time[3],2), " seconds to generate plot.", sep=""))
    ```

2. この画像はお使いの開発環境の R グラフィックス デバイスで返されたものです。  たとえば、RStudio で、 **プロット** ウィンドウをクリックします。  [!INCLUDE[rsql_rtvs](../../includes/rsql-rtvs-md.md)]で、別個のグラフィックス ウィンドウが開きます。

    ![rxHistogram を使用した料金の金額のプロット](media/rsql-e2e-rxhistogramresult.png "rxHistogram を使用した料金の金額のプロット")

    > [!NOTE]
    > グラフの外観が異なるか。
    >  
    > これは、 _Indatasource_では上位の1000行のみを使用するためです。 ORDER BY 句がない場合、TOP を使用した行の順序は非決定的であるため、データと結果のグラフが異なる可能性があります。
    > この画像は約 10,000 行のデータを利用して生成されました。 行数を変えて、実験してみることをお勧めします。お使いの環境で結果を得るためにかかった時間をメモしてください。

## <a name="create-a-map-plot"></a>マッププロットを作成する

通常、データベースサーバーはインターネットアクセスをブロックします。 これは、マップまたはその他のイメージをダウンロードしてプロットを生成する必要がある R パッケージを使用する場合に、不便な場合があります。 ただし、独自のアプリケーションを開発する場合に役立つ回避策があります。 基本的に、クライアントでマップ表現を生成し、SQL Server テーブルに属性として格納されているポイントをマップ上でオーバーレイします。

1. R プロットオブジェクトを作成する関数を定義します。 カスタム関数*Mライド ot*は、タクシーの集配地点を使用する散布図を作成し、各場所から開始された職務の数をプロットします。 **Ggplot2**パッケージと**ggmap**パッケージを使用します。これらのパッケージは既にインストールさ[れ、読み込ま](walkthrough-data-science-end-to-end-walkthrough.md#add-packages)れています。

    ```R
    mapPlot <- function(inDataSource, googMap){
        library(ggmap)
        library(mapproj)
        ds <- rxImport(inDataSource)
        p <- ggmap(googMap)+
        geom_point(aes(x = pickup_longitude, y =pickup_latitude ), data=ds, alpha =.5,
    color="darkred", size = 1.5)
        return(list(myplot=p))
    }
    ```

    + *MRxSqlServerData*関数は、2つの引数を受け取ります。これは、以前にを使用して定義した既存のデータオブジェクトと、クライアントから渡されたマップ表現です。
    + *Ds*変数で始まる行では、rxImport を使用して、以前に作成したデータソース ( *indatasource*) からメモリデータに読み込むことができます。 (このデータソースには1000行しか含まれていません。より多くのデータポイントを含むマップを作成する場合は、別のデータソースに置き換えることができます)。
    + オープンソースの R 関数を使用する場合は常に、データをローカルメモリ内のデータフレームに読み込む必要があります。 ただし、 [rxImport](https://docs.microsoft.com/r-server/r-reference/revoscaler/rximport)関数を呼び出すことによって、リモートの計算コンテキストのメモリ内でを実行できます。

2. コンピューティングコンテキストをローカルに変更し、マップの作成に必要なライブラリを読み込みます。

    ```R
    rxSetComputeContext("local")
    library(ggmap)
    library(mapproj)
    gc <- geocode("Times Square", source = "google")
    googMap <- get_googlemap(center = as.numeric(gc), zoom = 12, maptype = 'roadmap', color = 'color');
    ```

    + `gc` 変数は、ニューヨークにあるタイムズ スクウェアの一組の座標を保存します。

    + `googmap` で始まる行により、指定した座標を中心に据えるマップが生成されます。

3. 次に示すように、 [rxExec](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxexec)でプロット関数をラップして、SQL Server 計算コンテキストに切り替え、結果を表示します。 RxExec 関数は、 **RevoScaleR**パッケージの一部であり、リモートの計算コンテキストで任意の R 関数の実行をサポートします。

    ```R
    rxSetComputeContext(sqlcc)
    myplots <- rxExec(mapPlot, inDataSource, googMap, timesToRun = 1)
    plot(myplots[[1]][["myplot"]]);
    ````

    + の`googMap`マップデータは、リモートで実行される関数*mot*に引数として渡されます。 マップはローカル環境で生成されたため、SQL Server のコンテキストでプロットを作成するためには、関数に渡す必要があります。

    + で`plot`始まる行を実行すると、レンダリングされたデータはローカルの r 環境にシリアル化され、r クライアントで表示できるようになります。

    > [!NOTE]
    > Azure 仮想マシンで SQL Server を使用している場合は、この時点でエラーが発生する可能性があります。 Azure の既定のファイアウォール規則によって R コードによるネットワークアクセスがブロックされると、エラーが発生します。 このエラーを修正する方法の詳細については、「 [AZURE VM に Machine Learning (R) サービスをインストール](../install/sql-machine-learning-azure-virtual-machine.md)する」を参照してください。

4. 出力プロットは次の図のようになります。 乗車場所が赤い点でマップ上に追加されます。 使用したデータソースに格納されている場所の数によっては、イメージが異なる場合があります。

    ![カスタムの R 関数を使用したタクシーの乗車のプロット](media/rsql-e2e-mapplot.png "カスタムの R 関数を使用したタクシーの乗車のプロット")

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [R と SQL を使用したデータ機能の作成](walkthrough-create-data-features.md)
