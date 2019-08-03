---
title: R と SQL のデータ型およびオブジェクトに関するクイックスタート
description: このクイックスタートでは、R および SQL Server でデータ型とデータオブジェクトを操作する方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: eb007525834c312952f9eb02809edadebaefa305
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715431"
---
# <a name="quickstart-handle-data-types-and-objects-using-r-in-sql-server"></a>クイック スタート: SQL Server で R を使用してデータ型とオブジェクトを処理する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このクイックスタートでは、R と SQL Server 間でデータを移動するときに発生する一般的な問題について、実践的な概要を説明します。 この演習を通じて得られる経験により、独自のスクリプトでデータを操作するときに必要な背景情報を得ることができます。

主な問題は、次のとおりです。

+ データ型が一致しない場合がある
+ 暗黙的な変換が行われる可能性があります
+ キャスト操作と変換操作が必要な場合がある
+ R と SQL が異なるデータ オブジェクトを使用する

## <a name="prerequisites"></a>前提条件

前のクイックスタート「 [SQL Server に r が存在することを確認](quickstart-r-verify.md)する」では、このクイックスタートに必要な r 環境を設定するための情報とリンクを提供しています。

## <a name="always-return-a-data-frame"></a>常にデータフレームを返す

スクリプトが R から SQL Server に結果を返す場合は、データを **data.frame** として返す必要があります。 スクリプトで生成するその他の種類のオブジェクト (リスト、係数、ベクター、バイナリデータなど) は、ストアドプロシージャの結果の一部として出力する場合は、データフレームに変換する必要があります。 他のオブジェクトをデータ フレームに変更する処理をサポートする R 関数は複数あります。 バイナリ モデルをシリアル化して、データ フレームに返すこともできます。この処理については、このチュートリアルで後述します。

まず、いくつかの R 基本的な R オブジェクト (ベクター、マトリックス、およびリスト) を試し、データフレームへの変換によって SQL Server に渡される出力を変更する方法を見てみましょう。

これらの2つの "Hello World" スクリプトを R で比較します。スクリプトはほぼ同じように見えますが、最初の列は3つの値の1つの列を返し、2番目の列はそれぞれ1つの値を持つ3つの列を返します。

**例 1**

```sql
EXECUTE sp_execute_external_script
       @language = N'R'
     , @script = N' mytextvariable <- c("hello", " ", "world");
       OutputDataSet <- as.data.frame(mytextvariable);'
     , @input_data_1 = N' ';
```

**例 2**

```sql
EXECUTE sp_execute_external_script
        @language = N'R'
      , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
      , @input_data_1 = N'  ';
```

## <a name="identify-schema-and-data-types"></a>スキーマとデータ型の識別

結果が異なる理由は何でしょうか。 

通常、その答えは、R の `str()` コマンドを使用するとわかります。 R スクリプトのいずれかの場所に関数 `str(object_name)` を追加し、指定された R オブジェクトのデータ スキーマを情報メッセージとして返します。 メッセージを確認するには、Visual Studio Code の **[メッセージ]** ウィンドウまたは SSMS の **[メッセージ]** タブを参照してください。

例 1 と例 2 の結果が異なる理由を理解するには、次に示すように、各ステートメントの _@script_ 変数定義の末尾に `str(OutputDataSet)` という行を挿入します。

**Str 関数が追加された例1**

```sql
EXECUTE sp_execute_external_script
        @language = N'R'
      , @script = N' mytextvariable <- c("hello", " ", "world");
      OutputDataSet <- as.data.frame(mytextvariable);
      str(OutputDataSet);'
      , @input_data_1 = N'  '
;
```

**Str 関数が追加された例2**

```sql
EXECUTE sp_execute_external_script
  @language = N'R', 
  @script = N' OutputDataSet <- data.frame(c("hello"), " ", c("world"));
    str(OutputDataSet);' , 
  @input_data_1 = N'  ';
```

これで、 **[メッセージ]** のテキストを確認すると、出力が異なる理由がわかります。

**結果 - 例 1**

```sql
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**結果 - 例 2**

```sql
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

R 構文のわずかな変更が結果のスキーマに大きな影響をもたらしていることがわかります。 ここではその理由については説明しませんが、R データ型の違いについては、 [「Advanced R」の「Hadley Wickham](http://adv-r.had.co.nz)」の「*データ構造*」セクションで詳しく説明されています。

ここでは、R オブジェクトをデータ フレームに変換する際に予想される結果を確認する必要があることに留意してください。

> [!TIP]
> また、、などの R id 関数`is.matrix` `is.vector`を使用して、内部データ構造に関する情報を返すこともできます。

## <a name="implicit-conversion-of-data-objects"></a>データ オブジェクトの暗黙的な変換

R の各データ オブジェクトには、2 つのデータ オブジェクトの次元数が同じ場合や、いずれかのデータ オブジェクトに異種データ型が格納されている場合に他のデータ オブジェクトと結合する際の値の処理方法に関する固有のルールがあります。

たとえば、次のステートメントを実行して、R を使用して行列乗算を実行するとします。4つの値を持つ配列で、1列の行列に3つの値を乗算し、結果として 4x3 matrix を想定します。

```sql
EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'
        x <- as.matrix(InputDataSet);
        y <- array(12:15);
    OutputDataSet <- as.data.frame(x %*% y);'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
    WITH RESULT SETS (([Col1] int, [Col2] int, [Col3] int, Col4 int));
```

この場合、3 つの値で構成される列が 1 列の行列に変換されます。 R では行列は特殊なケースの配列であるため、2 つの引数が一致するように、配列 `y` が暗黙的に 1 列の行列に変換されます。

**結果**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

ただし、配列`y`のサイズを変更するとどうなるかに注意してください。

```sql
execute sp_execute_external_script
   @language = N'R'
   , @script = N'
        x <- as.matrix(InputDataSet);
        y <- array(12:14);
   OutputDataSet <- as.data.frame(y %*% x);'
   , @input_data_1 = N' SELECT [Col1]  from RTestData;'
   WITH RESULT SETS (([Col1] int ));
```

これで、R は結果として 1 つの値を返します。

**結果**
    
|Col1|
|---|
|1542|

なぜでしょうか。 この場合、2つの引数を同じ長さのベクターとして処理できるため、R は内部製品をマトリックスとして返します。  これは、線形代数のルールに従った想定どおりの動作です。ただし、ダウンストリーム アプリケーションで出力スキーマが変更されないと想定されている場合に問題が発生する可能性があります。

> [!TIP]
> 
> エラーの取得 これらの例では、 **RTestData**テーブルが必要です。 テストデータテーブルを作成していない場合は、このトピックに戻ってテーブルを作成します。[入力と出力を処理](../tutorials/rtsql-working-with-inputs-and-outputs.md)します。
> 
> テーブルを作成した後もエラーが発生する場合は、 **master**データベースや他のデータベースではなく、テーブルが格納されているデータベースのコンテキストでストアドプロシージャを実行していることを確認してください。
> 
> また、これらの例では一時テーブルを使用しないことをお勧めします。 一部の R クライアントは、バッチ間の接続を終了し、一時テーブルを削除します。

## <a name="merge-or-multiply-columns-of-different-length"></a>異なる長さの列の結合または乗算

R は、さまざまなサイズのベクターを操作したり、これらの列のような構造をデータフレームに結合したりするための優れた柔軟性を提供します。 ベクトルのリストはテーブルのような外見ですが、データベース テーブルを管理するすべてのルールに従うわけではありません。

たとえば、次のスクリプトでは、長さが 6 の数値配列を定義し、R 変数 `df1` に格納します。 次に、数値配列が RTestData テーブルの整数と結合され`df2`ます。このテーブルには3つの値が含まれ、新しいデータフレームが作成されます。

```sql
EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'
               df1 <- as.data.frame( array(1:6) );
               df2 <- as.data.frame( c( InputDataSet , df1 ));
               OutputDataSet <- df2'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
    WITH RESULT SETS (( [Col2] int not null, [Col3] int not null ));
```

データ フレームに入力するために、R は RTestData から取得した要素を必要な数だけ繰り返し、配列 `df1` 内の要素の数と一致させます。

**結果**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

データ フレームはテーブルのような外見ですが、実際はベクトルのリストであることを思い出してください。

## <a name="cast-or-convert-sql-server-data"></a>SQL Server データのキャストまたは変換

R と SQL Server は同じデータ型を使用しないため、データを取得して R ランタイムに渡すためのクエリを SQL Server で実行する際は、通常何らかの暗黙的な変換を行います。 R から SQL Server にデータを返す場合は、別の変換を行います。

- SQL Server は、スタートパッドサービスによって管理される R プロセスにクエリのデータをプッシュし、効率を向上させるために内部表現に変換します。
- R ランタイムはデータを data.frame 変数に読み込み、データに対する操作を実行します。
- データベース エンジンは、セキュリティで保護された内部接続を使用する SQL Server にデータを返し、SQL Server のデータ型の観点からデータを提供します。
- データを取得するには、SQL クエリを発行して表形式データ セットを処理できるクライアントまたはネットワーク ライブラリを使用して SQL Server に接続します。 このクライアント アプリケーションは、他の方法でデータに影響を及ぼす可能性があります。

これがどのように動作するかを確認するには、 [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)データウェアハウスでクエリを実行します。 このビューは、予測の作成に使用する売上データを返します。

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = 'M200 Europe'
           ORDER BY ReportingDate ASC
```

> [!NOTE]
> 
> 任意のバージョンの AdventureWorks を使用することも、独自のデータベースを使用して別のクエリを作成することもできます。 ここでは、text、datetime、および numeric 値を含むデータを処理しようとします。

ここで、ストアドプロシージャへの入力としてこのクエリを貼り付けてみてください。

```sql
EXECUTE sp_execute_external_script
       @language = N'R'
      , @script = N' str(InputDataSet);
      OutputDataSet <- InputDataSet;'
      , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

エラーが発生した場合は、クエリ テキストの一部を編集しなければならない可能性があります。 たとえば、WHERE 句に predicate という文字列を含める場合、単一引用符で囲む必要があります。

クエリを動作させたら、`str` 関数の結果を確認し、R で入力データがどのように処理されるかを把握します。

**結果**

```sql
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

+ 日時列は R データ型 **POSIXct** を使用して処理されました。
+ テキスト列 "ProductSeries" は、カテゴリ変数として識別されています。 文字列値は、既定では因子として処理されます。 R に渡した文字列は、内部で使用するために整数に変換され、出力時に再度文字列にマップされます。

### <a name="summary"></a>まとめ

これらの短い例からも、SQL クエリを入力として渡すときに、データ変換の影響を確認する必要があることを確認できます。 一部の SQL Server データ型は R でサポートされていないため、エラーを回避するには、次の方法を検討してください。

+ データを事前にテストし、R コードに渡されたときに問題となる可能性があるスキーマの列または値を確認します。
+ `SELECT *` を使用するのではなく、入力データ ソースに列を個別に指定し、各列の処理方法を把握します。
+ 予期しない動作を回避するために、入力データを準備する際に、必要に応じて明示的なキャストを実行します。
+ エラーが発生し、モデリングには役に立たない、データの列 (GUID や rowguids など) を渡さないようにします。

サポートされるデータ型とサポートされないデータ型の詳細については、「 [R ライブラリとデータ型](../r/r-libraries-and-data-types.md)」を参照してください。

文字列から数値要素への実行時の変換によるパフォーマンスへの影響の詳細については、「 [SQL Server R Services パフォーマンスチューニング](../r/sql-server-r-services-performance-tuning.md)」を参照してください。

## <a name="next-step"></a>次の手順

次のクイックスタートでは、SQL Server データに R 関数を適用する方法について説明します。

> [!div class="nextstepaction"]
> [クイック スタート:SQL Server データでの R 関数の使用](quickstart-r-functions.md)
