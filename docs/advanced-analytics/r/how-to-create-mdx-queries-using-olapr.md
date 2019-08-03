---
title: OlapR を使用して R で MDX クエリを作成する方法
description: SQL Server の olapR パッケージライブラリを使用して、R 言語スクリプトで MDX クエリを記述します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 05/22/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: db9afc06a02825b8db449492d8cec6ee6d67801d
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715122"
---
# <a name="how-to-create-mdx-queries-in-r-using-olapr"></a>OlapR を使用して R で MDX クエリを作成する方法
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

[Olapr](https://docs.microsoft.com/machine-learning-server/r-reference/olapr/olapr)パッケージは、SQL Server Analysis Services でホストされているキューブに対する MDX クエリをサポートしています。 既存のキューブに対してクエリを作成したり、ディメンションなどのキューブオブジェクトを調べたり、既存の MDX クエリを貼り付けてデータを取得したりできます。

この記事では、 **Olapr**パッケージの主な2つの用途について説明します。

+ [OlapR パッケージに用意されているコンストラクターを使用して、R から MDX クエリを作成する](#buildMDX)
+ [OlapR および OLAP プロバイダーを使用して、既存の有効な MDX クエリを実行する](#executeMDX)

次の操作はサポートされていません。

+ テーブルモデルに対する DAX クエリ
+ 新しい OLAP オブジェクトの作成
+ メジャーまたは合計を含むパーティションへの書き戻し

## <a name="buildMDX"></a>R から MDX クエリを作成する

1. OLAP データ ソース (SSAS インスタンス) および MSOLAP プロバイダーを指定する接続文字列を定義します。

2. `OlapConnection(connectionString)` 関数を使って MDX クエリのハンドルを作成し、接続文字列を渡します。

3. `Query()` コンストラクターを使って、クエリ オブジェクトをインスタンス化します。

4. 以下のヘルパー関数を使って、MDX クエリに含めるディメンションとメジャーについての詳細を提供します。

     + `cube()` : SSAS データベースの名前を指定します。 名前付きインスタンスに接続する場合は、コンピューター名とインスタンス名を指定します。 
     + `columns()`**ON COLUMNS**引数で使用するメジャーの名前を指定します。
     + `rows()`**ON ROWS**引数で使用するメジャーの名前を指定します。
     + `slicers()` : スライサーとして使うフィールドまたはメンバーを指定します。 スライサーとは、すべての MDX クエリ データに適用されるフィルターのようなものです。
     
     + `axis()` : クエリで使う追加の軸の名前を指定します。 
     
         OLAP キューブは、最大で 128 個のクエリ軸を含むことができます。 一般に、最初の4つの軸は、**列**、**行**、**ページ**、および**チャプター**と呼ばれます。 
         
         クエリが比較的単純な場合は、 `columns`、 `rows`などの関数を使ってクエリを作成できます。 ただし、 `axis()` 関数を使って 0 以外のインデックス値を指定し、多くの修飾子を持つ MDX クエリを作成したり、修飾子として余分なディメンションを追加したりすることもできます。

5. 結果の形に応じて、ハンドルおよび完成した MDX クエリを、次のいずれかの関数に渡します。 

  + `executeMD` : 多次元配列を返します
  + `execute2D` : 2 次元 (表形式) のデータ フレームを返します

## <a name="executeMDX"></a>R から有効な MDX クエリを実行する

1. OLAP データ ソース (SSAS インスタンス) および MSOLAP プロバイダーを指定する接続文字列を定義します。

2. `OlapConnection(connectionString)` 関数を使って MDX クエリのハンドルを作成し、接続文字列を渡します。

3. MDX クエリのテキストを格納する R 変数を定義します。

4. 結果の形に応じて、ハンドルおよび MDX クエリを含む変数を `executeMD` または `execute2D`関数に渡します。

    + `executeMD` : 多次元配列を返します
    + `execute2D` : 2 次元 (表形式) のデータ フレームを返します

## <a name="examples"></a>使用例

次の例は、AdventureWorks データマートとキューブプロジェクトに基づいています。これは、Analysis Services に簡単に復元できるバックアップファイルを含め、複数のバージョンのプロジェクトが広く利用可能であるためです。 既存のキューブがない場合は、次のいずれかのオプションを使用してサンプルキューブを取得します。

+ レッスン4までの Analysis Services チュートリアルに従って、これらの例で使用するキューブを作成します。[OLAP キューブの作成](../../analysis-services/multidimensional-tutorial/multidimensional-modeling-adventure-works-tutorial.md)

+ 既存のキューブをバックアップとしてダウンロードし、Analysis Services のインスタンスに復元します。 たとえば、このサイトは完全に処理されたキューブを zip 形式で提供します。[Adventure Works 多次元モデル SQL 2014](https://msftdbprodsamples.codeplex.com/downloads/get/882334)。 ファイルを抽出し、SSAS インスタンスに復元します。 詳細については、「 [Backup and restore](../../analysis-services/multidimensional-models/backup-and-restore-of-analysis-services-databases.md)」または「 [Restore-Asdatabase コマンドレット](../../analysis-services/powershell/restore-asdatabase-cmdlet.md)」を参照してください。

### <a name="1-basic-mdx-with-slicer"></a>1.スライサーを使う基本的な MDX

この MDX クエリは、インターネットの販売数と販売金額の数と金額の_メジャー_を選択し、列軸に設定します。 SalesTerritory ディメンションのメンバーを *スライサー*として追加し、オーストラリアからの販売のみが計算で使われるようにクエリをフィルターします。

```MDX
SELECT {[Measures].[Internet Sales Count], [Measures].[InternetSales-Sales Amount]} ON COLUMNS, 
{[Product].[Product Line].[Product Line].MEMBERS} ON ROWS 
FROM [Analysis Services Tutorial] 
WHERE [Sales Territory].[Sales Territory Country].[Australia]
```

+ 列では、コンマ区切りの文字列の要素として複数のメジャーを指定できます。
+ 行軸では、"Product Line" ディメンションのすべての可能な値 (すべての MEMBERS) を使います。 
+ このクエリは、すべての国のインターネット販売の_ロールアップ_の概要を含む3つの列を含むテーブルを返します。
+ WHERE 句は_スライサー軸_を指定します。 この例では、スライサーは**SalesTerritory**ディメンションのメンバーを使用してクエリをフィルター処理し、オーストラリアからの売上のみを計算に使用します。

#### <a name="to-build-this-query-using-the-functions-provided-in-olapr"></a>olapR で提供される関数を使ってこのクエリを作成するには

```R
cnnstr <- "Data Source=localhost; Provider=MSOLAP; initial catalog=Analysis Services Tutorial"
ocs <- OlapConnection(cnnstr)

qry <- Query()
cube(qry) <- "[Analysis Services Tutorial]"
columns(qry) <- c("[Measures].[Internet Sales Count]", "[Measures].[Internet Sales-Sales Amount]")
rows(qry) <- c("[Product].[Product Line].[Product Line].MEMBERS") 
slicers(qry) <- c("[Sales Territory].[Sales Territory Country].[Australia]")

result1 <- executeMD(ocs, qry)

```

名前付きインスタンスの場合は、R の制御文字と見なすことができる文字を必ずエスケープしてください。 たとえば、次の接続文字列は、ContosoHQ という名前のサーバー上のインスタンス OLAP01 を参照します。

```R
cnnstr <- "Data Source=ContosoHQ\\OLAP01; Provider=MSOLAP; initial catalog=Analysis Services Tutorial"
```

#### <a name="to-run-this-query-as-a-predefined-mdx-string"></a>事前定義された MDX 文字列としてこのクエリを実行するには

```R
cnnstr <- "Data Source=localhost; Provider=MSOLAP; initial catalog=Analysis Services Tutorial"
ocs <- OlapConnection(cnnstr)

mdx <- "SELECT {[Measures].[Internet Sales Count], [Measures].[InternetSales-Sales Amount]} ON COLUMNS, {[Product].[Product Line].[Product Line].MEMBERS} ON ROWS FROM [Analysis Services Tutorial] WHERE [Sales Territory].[Sales Territory Country].[Australia]"

result2 <- execute2D(ocs, mdx)
```

SQL Server Management Studio の MDX ビルダーを使用してクエリを定義し、MDX 文字列を保存すると、次に示すように、0から始まる軸の番号が付けられます。 

```MDX
SELECT {[Measures].[Internet Sales Count], [Measures].[Internet Sales-Sales Amount]} ON AXIS(0), 
   {[Product].[Product Line].[Product Line].MEMBERS} ON AXIS(1) 
   FROM [Analysis Services Tutorial] 
   WHERE [Sales Territory].[Sales Territory Countr,y].[Australia]
```

それでも、事前定義された MDX 文字列としてこのクエリを実行できます。 ただし、 `axis()`関数を使用して R を使用して同じクエリを作成するには、1から始まる軸の番号を指定する必要があります。

### <a name="2-explore-cubes-and-their-fields-on-an-ssas-instance"></a>2.SSAS インスタンス上のキューブとそのフィールドを調べる

`explore` 関数を使ってキューブ、ディメンション、またはメンバーのリストを取得し、クエリの作成で使うことができます。 これは、他の OLAP 参照ツールにアクセスできない場合、またはプログラムで MDX クエリを操作または作成する場合に便利です。

#### <a name="to-list-the-cubes-available-on-the-specified-connection"></a>指定した接続で使用可能なキューブのリストを取得するには

表示権限のあるインスタンス上のすべてのキューブまたはパースペクティブを表示するには、 `explore`への引数としてハンドルを渡します。

> [!IMPORTANT]
> 最終的な結果はキューブでは**ありません**。TRUE は、メタデータ操作が成功したことを示します。 無効な引数を渡すとエラーがスローされます。

```R
cnnstr <- "Data Source=localhost; Provider=MSOLAP; initial catalog=Analysis Services Tutorial"
ocs <- OlapConnection(cnnstr)
explore(ocs)
```

| [結果]  |
| ----|
| _Analysis Services Tutorial_|
|_Internet Sales_|
|_Reseller Sales_|
|_Sales Summary_|
|_[1] TRUE_|

#### <a name="to-get-a-list-of-cube-dimensions"></a>キューブ ディメンションのリストを取得するには

キューブまたはパースペクティブのすべてのディメンションを表示するには、キューブまたはパースペクティブの名前を指定します。

```R
cnnstr <- "Data Source=localhost; Provider=MSOLAP; initial catalog=Analysis Services Tutorial"
ocs \<- OlapConnection(cnnstr)
explore(ocs, "Sales")
```

| [結果]  |
| ----|
| _Customer_|
|_Date_|
|_Region_|


#### <a name="to-return-all-members-of-the-specified-dimension-and-hierarchy"></a>指定したディメンションと階層のすべてのメンバーを取得するには

ソースを定義してハンドルを作成した後、取得するキューブ、ディメンション、および階層を指定します。 返される結果では、に **->** プレフィックスが付いた項目は、前のメンバーの子を表します。

```R
cnnstr <- "Data Source=localhost; Provider=MSOLAP; initial catalog=Analysis Services Tutorial"
ocs <- OlapConnection(cnnstr)
explore(ocs, "Analysis Services Tutorial", "Product", "Product Categories", "Category")
```

| [結果]  |
| ----|
| _Accessories_|
|_Bikes_|
|_Clothing_|
|_Components_|
|-> Assembly Components|
|-> Assembly Components|


## <a name="see-also"></a>関連項目

[R での OLAP キューブからのデータの使用](../../advanced-analytics/r/using-data-from-olap-cubes-in-r.md)
