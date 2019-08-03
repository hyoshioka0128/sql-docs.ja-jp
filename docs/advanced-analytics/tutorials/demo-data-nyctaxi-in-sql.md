---
title: 埋め込み R および Python 用の NYC タクシーデモデータとスクリプトをダウンロードする
description: ニューヨーク市のタクシーサンプルデータをダウンロードし、データベースを作成するための手順です。 データは SQL Server Python および R 言語のチュートリアルで使用され、SQL Server ストアドプロシージャおよび T-sql 関数にスクリプトを埋め込む方法を示しています。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/31/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: a5b60397bb3581ba2d210a6b4469184306145c8a
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714866"
---
# <a name="nyc-taxi-demo-data-for-sql-server-python-and-r-tutorials"></a>SQL Server Python および R チュートリアル用の NYC タクシーデモデータ
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、[ニューヨーク市のタクシーと & リムジンの歩合](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml)からのパブリックデータで構成されるサンプルデータベースを設定する方法について説明します。 このデータは、SQL Server でのデータベース内分析のために、いくつかの R および Python のチュートリアルで使用されています。 サンプルコードの実行速度を高めるために、データの代表的な 1% サンプリングを作成しました。 システムでは、データベースバックアップファイルが 90 MB をわずかに上回り、プライマリデータテーブルに170万行が提供されます。

この演習を完了するには、 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)または別のツールを使用して、データベースバックアップファイルを復元して t-sql クエリを実行する必要があります。

このデータセットを使用したチュートリアルとクイックスタートには、次のものがあります。

+ [SQL Server で R を使用したデータベース内分析について学習する](sqldev-in-database-r-for-sql-developers.md)
+ [SQL Server での Python を使用したデータベース内分析の学習](sqldev-in-database-python-for-sql-developers.md)

## <a name="download-files"></a>ファイルのダウンロード

サンプルデータベースは、Microsoft によってホストされる SQL Server 2016 BAK ファイルです。 SQL Server 2016 以降で復元できます。 ファイルのダウンロードは、リンクをクリックするとすぐに開始されます。 

ファイルサイズは約 90 MB です。

1. [ [NYCTaxi_Sample](https://sqlmldoccontent.blob.core.windows.net/sqlml/NYCTaxi_Sample.bak) ] をクリックして、データベースのバックアップファイルをダウンロードします。

2. ファイルを C:\Program Server\MSSQL-instance-name\MSSQL\Backup フォルダーにコピーします。

3. Management Studio で、 **[データベース]** を右クリックし、 **[ファイルとファイルグループの復元]** を選択します。

4. データベース名として「 *NYCTaxi_Sample* 」と入力します。

5. **デバイスから** をクリックし、ファイルの選択 ページを開いてバックアップファイルを選択します。 **[追加]** をクリックして NYCTaxi_Sample を選択します。

6. **[復元]** チェックボックスをオンにし、 **[OK]** をクリックしてデータベースを復元します。

## <a name="review-database-objects"></a>データベースオブジェクトの確認
   
を使用して[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、インスタンスにデータベースオブジェクトが存在することを確認します。 データベース、テーブル、関数、およびストアドプロシージャが表示されます。
  
   ![rsql_devtut_BrowseTables](media/rsql-devtut-browsetables.png "rsql_devtut_BrowseTables")

### <a name="objects-in-nyctaxisample-database"></a>NYCTaxi_Sample データベース内のオブジェクト

次の表は、NYC タクシーデモデータベースで作成されたオブジェクトをまとめたものです。

|**オブジェクト名です。**|**オブジェクトの種類**|**[説明]**|
|----------|------------------------|---------------|
|**NYCTaxi_Sample** | [データベース] | データベースと 2 つのテーブルを作成します。<br /><br />nyctaxi_sample テーブル:メインの NYC タクシーデータセットを格納します。 ストレージとクエリのパフォーマンスを向上させるために、クラスター化列ストア インデックスをテーブルに追加します。 NYC タクシーデータセットの 1% のサンプルがこのテーブルに挿入されます。<br /><br />nyc _taxi_models テーブル:トレーニング済みの高度な分析モデルを永続化するために使用されます。|
|**fnCalculateDistance** |スカラー値関数 (scalar-valued function) | Pickup と降車の場所の間の直接距離を計算します。 この関数は、[データ機能の作成](sqldev-create-data-features-using-t-sql.md)、[モデルのトレーニングと保存](sqldev-train-and-save-a-model-using-t-sql.md)、 [R モデルの運用化](sqldev-operationalize-the-model.md)に使用されます。|
|**fnEngineerFeatures** |テーブル値関数 (table-valued function) | モデルトレーニング用の新しいデータ機能を作成します。 この関数は、[データの作成機能](sqldev-create-data-features-using-t-sql.md)と[R モデルの運用化](sqldev-operationalize-the-model.md)に使用されます。|


ストアドプロシージャは、さまざまなチュートリアルに記載されている R および Python スクリプトを使用して作成されます。 次の表は、さまざまなレッスンからスクリプトを実行するときに、必要に応じて NYC タクシーデモデータベースに追加できるストアドプロシージャをまとめたものです。

|**ストアドプロシージャ**|**言語**|**[説明]**|
|-------------------------|------------|---------------|
|**RxPlotHistogram** |R | RevoScaleR rxHistogram 関数を呼び出して変数のヒストグラムをプロットし、そのプロットをバイナリオブジェクトとして返します。 このストアドプロシージャは[、データの探索と視覚化](sqldev-explore-and-visualize-the-data.md)で使用されます。|
|**RPlotRHist** |R| Hist 関数を使用してグラフィックを作成し、ローカル PDF ファイルとして出力を保存します。 このストアドプロシージャは[、データの探索と視覚化](sqldev-explore-and-visualize-the-data.md)で使用されます。|
|**RxTrainLogitModel**  |R| R パッケージを呼び出して、ロジスティック回帰モデルをトレーニングします。 モデルは、tipped 列の値を予測し、ランダムに選択した 70% のデータを使用してトレーニングされます。 ストアド プロシージャの出力は、テーブル nyc_taxi_models に保存されているトレーニング済みのモデルです。 このストアドプロシージャは[、モデルのトレーニングと保存](sqldev-train-and-save-a-model-using-t-sql.md)に使用されます。|
|**RxPredictBatchOutput**  |R | モデルを使用して予測を作成するために、トレーニング済みのモデルを呼び出します。 ストアド プロシージャは、その入力パラメーターとしてクエリを受け取り、入力行のスコアを格納する数値の列を返します。 このストアドプロシージャは、[潜在的な結果を予測](sqldev-operationalize-the-model.md)するために使用されます。|
|**RxPredictSingleRow**  |R| モデルを使用して予測を作成するために、トレーニング済みのモデルを呼び出します。 このストアド プロシージャは、インライン パラメーターとして渡された個々の機能の値と共に、入力として新しい監視を受け取り、新しい監視の結果を予測する値を返します。 このストアドプロシージャは、[潜在的な結果を予測](sqldev-operationalize-the-model.md)するために使用されます。|

## <a name="query-the-data"></a>データのクエリ

検証手順として、クエリを実行してデータがアップロードされたことを確認します。

1. オブジェクトエクスプローラーの [データベース] で、 **NYCTaxi_Sample**データベースを右クリックし、新しいクエリを開始します。

2. いくつかの単純なクエリを実行します。

    ```sql
    SELECT TOP(10) * FROM dbo.nyctaxi_sample;
    SELECT COUNT(*) FROM dbo.nyctaxi_sample;
    ```
データベースには170万行が含まれています。

3. データベース内には、データセットを含む**nyctaxi_sample**テーブルがあります。 テーブルは、[列ストアインデックス](../../relational-databases/indexes/columnstore-indexes-overview.md)を追加することによって、セットベースの計算用に最適化されています。 このステートメントを実行すると、テーブルに簡単な概要が生成されます。

    ```sql
    SELECT DISTINCT [passenger_count]
        , ROUND (SUM ([fare_amount]),0) as TotalFares
        , ROUND (AVG ([fare_amount]),0) as AvgFares
    FROM [dbo].[nyctaxi_sample]
    GROUP BY [passenger_count]
    ORDER BY  AvgFares DESC
    ````
結果は、次のスクリーンショットに示すようになります。

  ![テーブルの概要情報](media/nyctaxidatatablesummary.png "クエリ結果")

## <a name="next-steps"></a>次の手順

NYC タクシーサンプルデータをハンズオンラーニングで使用できるようになりました。

+ [SQL Server で R を使用したデータベース内分析について学習する](sqldev-in-database-r-for-sql-developers.md)
+ [SQL Server での Python を使用したデータベース内分析の学習](sqldev-in-database-python-for-sql-developers.md)