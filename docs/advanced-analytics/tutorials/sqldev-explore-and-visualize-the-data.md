---
title: 'レッスン 1: R と T-sql を使用したデータの探索と視覚化'
description: R 関数を使用して SQL Server データを探索および視覚化する方法を示すチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/29/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 2c204e06edd830d8036b6d0119ce1aff1a9c6833
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715368"
---
# <a name="lesson-1-explore-and-visualize-the-data"></a>レッスン 1:データの探索と視覚化
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、SQL Server で R を使用する方法に関する SQL 開発者向けのチュートリアルの一部です。

この手順では、サンプルデータを確認し、 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)の[RxHistogram](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxhistogram)と Base R の汎用[Hist](https://www.rdocumentation.org/packages/graphics/versions/3.5.0/topics/hist)関数を使用して、いくつかのプロットを生成します。これらの R 関数は、既[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]にに含まれています。

このレッスンの主な目的は、ストアドプロシージャ[!INCLUDE[tsql](../../includes/tsql-md.md)]で R 関数を呼び出し、結果をアプリケーションファイル形式で保存する方法を示すことです。

+ **RxHistogram**を使用して、データとして R プロットを生成するストアドプロシージャを作成します。 **Bcp**を使用して、バイナリストリームをイメージファイルにエクスポートします。
+ **Hist**を使用してプロットを生成し、結果を JPG および PDF 出力として保存するストアドプロシージャを作成します。

> [!NOTE]
> 視覚エフェクトはデータの形状と分布を理解するための強力なツールであるため、R には、ヒストグラム、散布図、箱ひげ図、その他のデータ探索グラフを生成するためのさまざまな関数とパッケージが用意されています。 R は、通常、グラフィック出力用の R デバイスを使用してイメージを作成します。これをキャプチャして、アプリケーションに表示するための**varbinary**データ型として格納することができます。 また、サポートファイル形式 () に画像を保存することもできます。JPG、。PDF など)。

## <a name="review-the-data"></a>データを確認する

データ科学ソリューションの開発には、通常、集中的なデータの探索とデータの視覚化が伴います。 まず、サンプルデータを確認してください (まだ作成していない場合)。

元のパブリックデータセットでは、タクシーの識別子と旅行レコードが個別のファイルで提供されていました。 ただし、サンプルデータの使用を容易にするために、2つの元のデータセットが_medallion_、_ハッキング\_ライセンス_、および_集配\__ の各列に結合されています。  レコードもサンプリングされており、元のレコード数の 1% だけが取得されています。 結果的にダウンサンプリングされたデータセットは 1,703,957 行と 23 列です。

**タクシーの識別子**
  
-   _Medallion_列は、タクシーの一意の id 番号を表します。
  
-   [_ハッキング\_ライセンス_] 列には、タクシードライバーのライセンス番号 (匿名化) が含まれています。
  
**乗車と料金の記録**
  
-   各乗車記録には、乗車場所、降車場所、走行距離が含まれています。
  
-   各料金記録には、支払の種類、支払の合計額、チップ額など、支払情報が含まれています。
  
-   最後の 3 つの列はさまざまな機械学習タスクに利用できます。 [ _Tip\_amount_ ] 列には連続する数値が含まれており、回帰分析の**ラベル**列として使用できます。 _tipped_ 列には yes/no 値のみが含まれ、二項分類に利用されます。 _Tip\_class_列には複数の**クラスラベル**があるため、多クラス分類タスクのラベルとして使用できます。
  
    このチュートリアルでは、二項分類タスクのみを紹介します。他の 2 つの機械学習タスク (回帰と複数クラスの分類) でもモデルの構築をお試しください。
  
-   ラベル列に使用される値はすべて、次のビジネスルールを使用して、[ _tip\_amount_ ] 列に基づいています。
  
    |派生列名|Rule|
    |-|-|
     |tipped|tip_amount > 0 の場合、tipped = 1、それ以外では tipped = 0|
    |tip_class|クラス 0: tip_amount = $0<br /><br />クラス 1: tip_amount > $0 および tip_amount <= $5<br /><br />クラス 2: tip_amount > $5 および tip_amount <= $10<br /><br />クラス 3: tip_amount > $10 および tip_amount <= $20<br /><br />クラス 4: tip_amount > $20|

## <a name="create-a-stored-procedure-using-rxhistogram-to-plot-the-data"></a>RxHistogram を使用してデータをプロットするストアドプロシージャを作成する

プロットを作成するには、 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)で提供されている拡張 R 関数の1つである[rxHistogram](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxhistogram)を使用します。 この手順では、 [!INCLUDE[tsql](../../includes/tsql-md.md)]クエリのデータに基づいてヒストグラムをプロットします。 この関数は、ストアドプロシージャである "保存" にラップすることができ**ます。**

1. のオブジェクトエクスプローラーで、 **NYCTaxi_Sample**データベースを右クリックし、 **[新しいクエリ]** をクリックします。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]

2. 次のスクリプトを貼り付けて、ヒストグラムをプロットするストアドプロシージャを作成します。 この例の名前は、* rの "r" という名前です。

    ```sql
    CREATE PROCEDURE [dbo].[RxPlotHistogram]
    AS
    BEGIN
      SET NOCOUNT ON;
      DECLARE @query nvarchar(max) =  
      N'SELECT tipped FROM nyctaxi_sample'  
      EXECUTE sp_execute_external_script @language = N'R',  
                                         @script = N'  
       image_file = tempfile();  
       jpeg(filename = image_file);  
       #Plot histogram  
       rxHistogram(~tipped, data=InputDataSet, col=''lightgreen'',   
       title = ''Tip Histogram'', xlab =''Tipped or not'', ylab =''Counts'');  
       dev.off();  
       OutputDataSet <- data.frame(data=readBin(file(image_file, "rb"), what=raw(), n=1e6));  
       ',  
       @input_data_1 = @query  
       WITH RESULT SETS ((plot varbinary(max)));  
    END
    GO
    ```

このスクリプトで理解する重要な点は、次のとおりです。 
  
+ 変数 `@query` によりクエリ テキスト (`'SELECT tipped FROM nyctaxi_sample'`) が定義され、スクリプト入力変数 `@input_data_1`の引数として R スクリプトに渡されます。 外部プロセスとして実行される R スクリプトの場合、スクリプトへの入力と、SQL Server で R セッションを開始する[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)システムストアドプロシージャへの入力の間に1対1のマッピングが必要です。
  
+ R スクリプト内では、イメージを`image_file`格納するための変数 () が定義されています。 

+ RevoScaleR ライブラリの**rxHistogram**関数が、プロットを生成するために呼び出されます。
  
+ このコマンドを SQL Server の外部スクリプトとして実行しているため、R デバイスは**off**に設定されています。 通常、R では、高レベルのプロットコマンドを発行すると、R は*デバイス*と呼ばれるグラフィックスウィンドウを開きます。 ファイルに書き込む場合や、出力を他の方法で処理する場合は、デバイスの電源をオフにすることができます。
  
+ R グラフィックス オブジェクトは、出力のために R data.frame にシリアル化されます。

### <a name="execute-the-stored-procedure-and-use-bcp-to-export-binary-data-to-an-image-file"></a>ストアドプロシージャを実行し、bcp を使用してバイナリデータをイメージファイルにエクスポートする

ストアド プロシージャは varbinary データのストリームとして画像を返し、直接見ることはできません。 ただし、 **bcp** ユーティリティを利用すれば、varbinary データを取得し、クライアント コンピューターに画像ファイルとして保存できます。
  
1. [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]で、次のステートメントを実行します。
  
    ```sql
    EXEC [dbo].[RxPlotHistogram]
    ```
  
    **結果**
    
    *プロット* *0xFFD8FFE000104A4649...*
  
2. PowerShell コマンドプロンプトを開き、次のコマンドを実行して、適切なインスタンス名、データベース名、ユーザー名、資格情報を引数として指定します。 Windows id を使用する場合は、- **U**と- **P**を **-T**に置き換えることができます。
  
    ```powershell
    bcp "exec RxPlotHistogram" queryout "plot.jpg" -S <SQL Server instance name> -d  NYCTaxi_Sample  -U <user name> -P <password> -T
    ```

    > [!NOTE]
    > Bcp のコマンドスイッチでは大文字と小文字が区別されます。
  
3. 接続に成功した場合は、グラフィック ファイル形式に関する詳細情報を入力するように求められます。 

   プロンプトのたびに ENTER キーを押し、初期設定を適用します。ただし、次については変更します。
    
   + **フィールド プロットのプレフィックス長**に「0」を入力します。
  
   + 後で再利用するために出力パラメーターを保存する場合、「 **Y** 」を入力します。
  
    ```powershell
    Enter the file storage type of field plot [varbinary(max)]: 
    Enter prefix-length of field plot [8]: 0
    Enter length of field plot [0]:
    Enter field terminator [none]:
  
    Do you want to save this format information in a file? [Y/n]
    Host filename [bcp.fmt]:
    ```
  
    **結果**
    
    ```powershell
    Starting copy...
    1 rows copied.
    Network packet size (bytes): 4096
    Clock Time (ms.) Total     : 3922   Average : (0.25 rows per sec.)
    ```

    > [!TIP]
    > フォーマット情報をファイル (bcp.fmt) に保存する場合、 **bcp** ユーティリティによりフォーマット定義が生成されます。それを今後、同様のコマンドに適用できます。グラフィック ファイル フォーマットの選択を求められません。 フォーマット ファイルを使用するには、 `-f bcp.fmt` をコマンド行の終わりに追加します。パスワード引数の後です。
  
4.  出力ファイルは、PowerShell コマンドを実行した同じディレクトリに作成されます。 プロットを表示するには、ファイル plot.jpg を開きます。
  
    ![チップありとチップなしのタクシー乗車](media/rsql-devtut-tippedornot.jpg "チップありとチップなしのタクシー乗車")  
  
## <a name="create-a-stored-procedure-using-hist-and-multiple-output-formats"></a>Hist と複数の出力形式を使用したストアドプロシージャの作成

通常、データ科学者は、さまざまな観点からデータを洞察するために、複数のデータビジュアライゼーションを生成します。 この例では、scatterplots やその他の R グラフィックスをに書き込む**r Thist**というストアドプロシージャを作成します。JPG および。PDF 形式。

このストアドプロシージャは、 **Hist**関数を使用してヒストグラムを作成し、などの一般的な形式にバイナリデータをエクスポートします。JPG、。PDF、および.PNG. 

1. のオブジェクトエクスプローラーで、 **NYCTaxi_Sample**データベースを右クリックし、 **[新しいクエリ]** をクリックします。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]

2. 次のスクリプトを貼り付けて、ヒストグラムをプロットするストアドプロシージャを作成します。 この例の名前は、r"r" になります。
  
    ```sql
    CREATE PROCEDURE [dbo].[RPlotHist]  
    AS  
    BEGIN  
      SET NOCOUNT ON;  
      DECLARE @query nvarchar(max) =  
      N'SELECT cast(tipped as int) as tipped, tip_amount, fare_amount FROM [dbo].[nyctaxi_sample]'  
      EXECUTE sp_execute_external_script @language = N'R',  
      @script = N'  
       # Set output directory for files and check for existing files with same names   
        mainDir <- ''C:\\temp\\plots''  
        dir.create(mainDir, recursive = TRUE, showWarnings = FALSE)  
        setwd(mainDir);  
        print("Creating output plot files:", quote=FALSE)
        
        # Open a jpeg file and output histogram of tipped variable in that file.  
        dest_filename = tempfile(pattern = ''rHistogram_Tipped_'', tmpdir = mainDir)  
        dest_filename = paste(dest_filename, ''.jpg'',sep="")  
        print(dest_filename, quote=FALSE);  
        jpeg(filename=dest_filename);  
        hist(InputDataSet$tipped, col = ''lightgreen'', xlab=''Tipped'',   
            ylab = ''Counts'', main = ''Histogram, Tipped'');  
         dev.off();  

        # Open a pdf file and output histograms of tip amount and fare amount.   
        # Outputs two plots in one row  
        dest_filename = tempfile(pattern = ''rHistograms_Tip_and_Fare_Amount_'', tmpdir = mainDir)  
        dest_filename = paste(dest_filename, ''.pdf'',sep="")  
        print(dest_filename, quote=FALSE);  
        pdf(file=dest_filename, height=4, width=7);  
        par(mfrow=c(1,2));  
        hist(InputDataSet$tip_amount, col = ''lightgreen'',   
            xlab=''Tip amount ($)'',   
            ylab = ''Counts'',   
            main = ''Histogram, Tip amount'', xlim = c(0,40), 100);  
        hist(InputDataSet$fare_amount, col = ''lightgreen'',   
            xlab=''Fare amount ($)'',   
            ylab = ''Counts'',   
            main = ''Histogram,   
            Fare amount'',   
            xlim = c(0,100), 100);  
       dev.off();  
  
        # Open a pdf file and output an xyplot of tip amount vs. fare amount using lattice;  
        # Only 10,000 sampled observations are plotted here, otherwise file is large.  
        dest_filename = tempfile(pattern = ''rXYPlots_Tip_vs_Fare_Amount_'', tmpdir = mainDir)  
        dest_filename = paste(dest_filename, ''.pdf'',sep="")  
        print(dest_filename, quote=FALSE);  
        pdf(file=dest_filename, height=4, width=4);  
        plot(tip_amount ~ fare_amount,   
            data = InputDataSet[sample(nrow(InputDataSet), 10000), ],   
            ylim = c(0,50),   
            xlim = c(0,150),   
            cex=.5,   
            pch=19,   
            col=''darkgreen'',    
            main = ''Tip amount by Fare amount'',   
            xlab=''Fare Amount ($)'',   
            ylab = ''Tip Amount ($)'');   
        dev.off();',  
     @input_data_1 = @query  
     END
    ```
  
+ ストアド プロシージャ内の SELECT クエリの出力が既定の R データ フレーム `InputDataSet`に保存されます。 さまざまな R プロット関数を呼び出し、実際のグラフィックス ファイルを生成できます。 埋め込み R スクリプトのほとんどがそのようなグラフィックス関数のオプションを表します。 `plot` や `hist`などです。
  
+ すべてのファイルはローカルフォルダー C:\temp\Plots. に保存されます。 宛先フォルダーはストアド プロシージャの一部として R スクリプトに渡された引数により定義されます。  変数 `mainDir`の値を変更して宛先フォルダーを変更できます。

+ ファイルを別のフォルダーに出力するには、ストアド プロシージャに埋め込まれている R スクリプトの `mainDir` 変数の値を変更します。 スクリプトを変更し、さまざまなフォーマットを出力したり、出力するファイルを増やしたりできます。

### <a name="execute-the-stored-procedure"></a>ストアド プロシージャを実行する

次のステートメントを実行して、バイナリプロットデータを JPEG および PDF ファイル形式にエクスポートします。

```sql
EXEC RPlotHist
```

**結果**
    
```sql
STDOUT message(s) from external script:
[1] Creating output plot files:[1] C:\temp\plots\rHistogram_Tipped_18887f6265d4.jpg[1] 

C:\temp\plots\rHistograms_Tip_and_Fare_Amount_1888441e542c.pdf[1]

C:\temp\plots\rXYPlots_Tip_vs_Fare_Amount_18887c9d517b.pdf
```

ファイル名の数値はランダムに生成され、既存のファイルに書き込もうとしてもエラーが発生しないようにします。

### <a name="view-output"></a>出力の表示 

プロットを表示するには、変換先フォルダーを開き、ストアドプロシージャの R コードによって作成されたファイルを確認します。

1. STDOUT メッセージに示されているフォルダーにアクセスします (例では、これは C:\temp\plots です。\)

2. を`rHistogram_Tipped.jpg`開くと、ヒントが表示されたトリップの数と、先端のないトリップの数が表示されます。 (このヒストグラムは、前の手順で生成したものとよく似ています)。

3. を`rHistograms_Tip_and_Fare_Amount.pdf`開くと、料金の金額に対してプロットされた、チップの金額の分布が表示されます。
    
  ![tip_amount と fare_amount を示すヒストグラム](media/rsql-devtut-tipamtfareamt.PNG "tip_amount と fare_amount を示すヒストグラム")

4. を`rXYPlots_Tip_vs_Fare_Amount.pdf`開くと、x 軸の料金 amount と y 軸のチップの金額を含む散布図が表示されます。

   ![料金 amount をプロット]したチップの金額(media/rsql-devtut-tipamtbyfareamt.PNG "料金 amount をプロット")したチップの金額

## <a name="next-lesson"></a>次のレッスン

[レッスン 2:T-sql を使用してデータ機能を作成する](sqldev-create-data-features-using-t-sql.md)

## <a name="previous-lesson"></a>前のレッスン

[NYC タクシーのデモデータを設定する](demo-data-nyctaxi-in-sql.md)
