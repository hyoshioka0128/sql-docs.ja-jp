---
title: 'レッスン 1: Python と T-sql を使用したデータの探索と視覚化'
description: ストアドプロシージャと T-sql 関数 SQL Server に Python を埋め込む方法を示すチュートリアル
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/01/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 6ee82de1431a6bc21596505dc4b008b817b35830
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714702"
---
# <a name="explore-and-visualize-the-data"></a>データの探索と視覚化
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、 [SQL 開発者向けのデータベース内 Python analytics](sqldev-in-database-python-for-sql-developers.md)のチュートリアルの一部です。 

この手順では、サンプルデータを探索し、いくつかのプロットを生成します。 後で、Python でグラフィックスオブジェクトをシリアル化し、それらのオブジェクトを逆シリアル化してプロットする方法を学習します。

## <a name="review-the-data"></a>データを確認する

まず、NYC タクシーデータを簡単に使用できるように変更を加えたため、データスキーマを参照してください。

+ 元のデータセットでは、タクシーの識別子と旅行記録用に個別のファイルが使用されていました。 列_medallion_、 _hack_license_、 _pickup_datetime_の2つの元のデータセットを結合しました。  
+ 元のデータセットは多くのファイルにまたがっており、非常に大きくなっていました。 元のレコード数のうち 1% だけを取得することに downsampled しました。 現在のデータテーブルには、1703957行と23列が含まれています。

**タクシーの識別子**

_Medallion_列は、タクシーの一意の ID 番号を表します。

_Hack_license_列には、タクシードライバーのライセンス番号 (匿名化) が含まれています。

**乗車と料金の記録**

各乗車記録には、乗車場所、降車場所、走行距離が含まれています。

各料金記録には、支払の種類、支払の合計額、チップ額など、支払情報が含まれています。

最後の 3 つの列はさまざまな機械学習タスクに利用できます。  _tip_amount_ 列には連続する数値が含まれ、回帰分析用の **label** 列として利用できます。 _tipped_ 列には yes/no 値のみが含まれ、二項分類に利用されます。 _tip_class_ 列には複数の **クラス ラベル** が含まれ、複数クラスの分類タスク用のラベルとして利用できます。

ラベル列に使用される値はすべて、次の`tip_amount`ビジネスルールを使用して列に基づいています。

+ ラベル列`tipped`の値は0と1の可能性があります

    > `tip_amount` 0、= `tipped` 1 の場合は`tipped` 、それ以外の場合は0です。

+ ラベル列`tip_class`に使用可能なクラス値0-4

    クラス 0: `tip_amount` = $0

    クラス 1: `tip_amount` > $0 および`tip_amount` < = $5
    
    クラス 2: `tip_amount` > $5 および`tip_amount` < = $10
    
    クラス 3: `tip_amount` > $10 および`tip_amount` < = $20
    
    クラス 4: `tip_amount` > $20

## <a name="create-plots-using-python-in-t-sql"></a>T-sql で Python を使用してプロットを作成する

データ科学ソリューションの開発には、通常、集中的なデータの探索とデータの視覚化が伴います。 視覚エフェクトは、データの分布や外れ値を理解するための強力なツールであるため、Python にはデータを視覚化するためのパッケージが多数用意されています。 **Matplotlib**モジュールは、視覚化用のより一般的なライブラリの1つであり、ヒストグラム、散布図、箱ひげ図、その他のデータ探索グラフを作成するための多くの機能が含まれています。

このセクションでは、ストアドプロシージャを使用してプロットを操作する方法について説明します。 サーバーでイメージを開くのではなく、Python オブジェクト`plot`を**varbinary**データとして格納し、他の場所で共有または表示できるファイルに書き込みます。

### <a name="create-a-plot-as-varbinary-data"></a>Varbinary データとしてプロットを作成する

このストアドプロシージャは、 **varbinary**データ`figure`のストリームとしてシリアル化された Python オブジェクトを返します。 バイナリデータを直接表示することはできませんが、クライアントで Python コードを使用して、図を逆シリアル化して表示し、イメージファイルをクライアントコンピューターに保存することができます。

1. ストアドプロシージャ**PyPlotMatplotlib**を作成します (PowerShell スクリプトによってまだ作成されていない場合)。

    - 変数`@query`は、クエリテキスト`SELECT tipped FROM nyctaxi_sample`を定義し`@input_data_1`ます。これは、スクリプト入力変数の引数として Python コードブロックに渡されます。
    - Python スクリプトは非常に単純です。 **matplotlib** `figure`オブジェクトを使用してヒストグラムと散布図を作成し、これらのオブジェクトを`pickle`ライブラリを使用してシリアル化します。
    - Python グラフィックスオブジェクトは、出力用に **pandas** データフレームにシリアル化されます。
  
    ```sql
    DROP PROCEDURE IF EXISTS PyPlotMatplotlib;
    GO

    CREATE PROCEDURE [dbo].[PyPlotMatplotlib]
    AS
    BEGIN
        SET NOCOUNT ON;
        DECLARE @query nvarchar(max) =
        N'SELECT cast(tipped as int) as tipped, tip_amount, fare_amount FROM [dbo].[nyctaxi_sample]'
        EXECUTE sp_execute_external_script
        @language = N'Python',
        @script = N'
    import matplotlib
    matplotlib.use("Agg")
    import matplotlib.pyplot as plt
    import pandas as pd
    import pickle

    fig_handle = plt.figure()
    plt.hist(InputDataSet.tipped)
    plt.xlabel("Tipped")
    plt.ylabel("Counts")
    plt.title("Histogram, Tipped")
    plot0 = pd.DataFrame(data =[pickle.dumps(fig_handle)], columns =["plot"])
    plt.clf()

    plt.hist(InputDataSet.tip_amount)
    plt.xlabel("Tip amount ($)")
    plt.ylabel("Counts")
    plt.title("Histogram, Tip amount")
    plot1 = pd.DataFrame(data =[pickle.dumps(fig_handle)], columns =["plot"])
    plt.clf()

    plt.hist(InputDataSet.fare_amount)
    plt.xlabel("Fare amount ($)")
    plt.ylabel("Counts")
    plt.title("Histogram, Fare amount")
    plot2 = pd.DataFrame(data =[pickle.dumps(fig_handle)], columns =["plot"])
    plt.clf()

    plt.scatter( InputDataSet.fare_amount, InputDataSet.tip_amount)
    plt.xlabel("Fare Amount ($)")
    plt.ylabel("Tip Amount ($)")
    plt.title("Tip amount by Fare amount")
    plot3 = pd.DataFrame(data =[pickle.dumps(fig_handle)], columns =["plot"])
    plt.clf()

    OutputDataSet = plot0.append(plot1, ignore_index=True).append(plot2, ignore_index=True).append(plot3, ignore_index=True)
    ',
    @input_data_1 = @query
    WITH RESULT SETS ((plot varbinary(max)))
    END
    GO
    ```

2. 次に、引数を指定せずにストアドプロシージャを実行し、入力クエリとしてハードコーディングされたデータからプロットを生成します。

    ```sql
    EXEC [dbo].[PyPlotMatplotlib]
    ```

3. 結果は次のようになります。
  
    ```sql
    plot
    0xFFD8FFE000104A4649...
    0xFFD8FFE000104A4649...
    0xFFD8FFE000104A4649...
    0xFFD8FFE000104A4649...
    ```

  
4. [Python クライアント](../python/setup-python-client-tools-sql.md)から、バイナリプロットオブジェクトを生成した SQL Server インスタンスに接続し、プロットを表示できるようになりました。 

    これを行うには、次の Python コードを実行します。必要に応じて、サーバー名、データベース名、および資格情報を置き換えます。 クライアントとサーバーで Python のバージョンが同じであることを確認します。 また、クライアント上の Python ライブラリ (matplotlib など) が、サーバーにインストールされているライブラリと同じかそれ以降のバージョンであることを確認します。
  
    **SQL Server 認証を使用する:**
    
    ```python
    %matplotlib notebook
    import pyodbc
    import pickle
    import os
    cnxn = pyodbc.connect('DRIVER=SQL Server;SERVER={SERVER_NAME};DATABASE={DB_NAME};UID={USER_NAME};PWD={PASSWORD}')
    cursor = cnxn.cursor()
    cursor.execute("EXECUTE [dbo].[PyPlotMatplotlib]")
    tables = cursor.fetchall()
    for i in range(0, len(tables)):
        fig = pickle.loads(tables[i][0])
        fig.savefig(str(i)+'.png')
    print("The plots are saved in directory: ",os.getcwd())
    ```

    **Windows 認証を使用する場合:**

    ```python
    %matplotlib notebook
    import pyodbc
    import pickle
    import os
    cnxn = pyodbc.connect('DRIVER=SQL Server;SERVER={SERVER_NAME};DATABASE={DB_NAME};Trusted_Connection=True;')
    cursor = cnxn.cursor()
    cursor.execute("EXECUTE [dbo].[PyPlotMatplotlib]")
    tables = cursor.fetchall()
    for i in range(0, len(tables)):
        fig = pickle.loads(tables[i][0])
        fig.savefig(str(i)+'.png')
    print("The plots are saved in directory: ",os.getcwd())
    ```

5.  接続に成功すると、次のようなメッセージが表示されます。
  
    *プロットはディレクトリ: xxxx に保存されます。*
  
6.  出力ファイルは Python 作業ディレクトリに作成されます。 プロットを表示するには、Python 作業ディレクトリに移動し、ファイルを開きます。 次の図は、クライアントコンピューターに保存されているプロットを示しています。
  
    ![チップの金額と料金 amount](media/sqldev-python-sample-plot.png "チップの金額と料金 amount") 

## <a name="next-step"></a>次の手順

[T-SQL を使用してデータ機能を作成する](sqldev-py5-train-and-save-a-model-using-t-sql.md)

## <a name="previous-step"></a>前の手順

[NYC タクシーデータセットをダウンロードする](demo-data-nyctaxi-in-sql.md)

