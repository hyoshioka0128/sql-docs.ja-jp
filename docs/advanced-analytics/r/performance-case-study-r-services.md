---
title: SQL Server R Services 結果とリソースのパフォーマンス
ms.prod: sql
ms.technology: machine-learning
ms.date: 03/29/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: aa56a9367271df2172236b133d85b5771089b1ac
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715042"
---
# <a name="performance-for-r-services-results-and-resources"></a>R Services のパフォーマンス: 結果とリソース
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、R Services のパフォーマンスの最適化について説明するシリーズの4番目と最後のものです。 この記事では、さまざまな最適化方法をテストした2つのケーススタディの方法、結果、および結論をまとめています。

2つのケーススタディでは、異なる目標がありました。

+ R Services 開発チームによる最初のケーススタディでは、特定の最適化手法の影響を測定します。
+ 2番目のケーススタディでは、データサイエンスチームによって、特定の大規模なスコアリングシナリオに最適な最適化を決定するために複数の方法が実験されています。

このトピックでは、最初のケーススタディの詳細な結果を示します。 2番目のケーススタディでは、全体の結果について概要を説明します。 このトピックの最後には、すべてのスクリプトとサンプルデータへのリンクと、元の作成者が使用したリソースへのリンクがあります。

## <a name="performance-case-study-airline-dataset"></a>パフォーマンスのケーススタディ:航空会社のデータセット

SQL Server R Services 開発チームによるこのケーススタディでは、さまざまな最適化の効果をテストしています。 航空会社のデータセットに対して1つの rxLogit モデルが作成され、スコアリングが実行されました。 個々の影響を評価するために、トレーニングおよびスコア付けのプロセス中に最適化が適用されました。

- GitHubSQL Server 最適化スタディの[サンプルデータとスクリプト](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PerfTuning)

### <a name="test-methods"></a>テストメソッド

1. 航空データセットは、10万行の1つのテーブルで構成されます。 ダウンロードされ、SQL Server に一括で読み込まれました。
2. テーブルのコピーが6つ作成されました。
3. ページの圧縮、行の圧縮、インデックス作成、列形式のデータストアなどの SQL Server 機能をテストするために、テーブルのコピーにさまざまな変更が適用されました。
4. 各最適化が適用される前後にパフォーマンスが測定されました。

| テーブル名| 説明|
|------|------|
| *airline* | `rxDataStep` を使用して元の xdf ファイルから変換されたデータ。|                          |
| *airlineWithIntCol*   | *DayOfWeek* が文字列ではなく整数に変換されています。 また、*rowNum* 列が追加されています。|
| *airlineWithIndex*    | *airlineWithIntCol* テーブルと同じデータですが、*rowNum* 列を使用する 1 つのクラスター化インデックスが含まれます。|
| *airlineWithPageComp* | *airlineWithIndex* テーブルと同じデータですが、ページ圧縮が有効になっています。 また、*CRSDepTime* と *ArrDelay* から計算された、*CRSDepHour* と *Late* の 2 つの列が追加されています。 |
| *airlineWithRowComp*  | *airlineWithIndex* テーブルと同じデータですが、行圧縮が有効になっています。 また、*CRSDepTime* と *ArrDelay* から計算された、*CRSDepHour* と *Late* の 2 つの列が追加されています。 |
| *airlineColumnar*     | 1 つのクラスター化インデックスを含む単票形式ストア。 このテーブルには、クリーンアップされた csv ファイルのデータが含まれます。|

各テストは、次の手順で行います。

1. R ガベージ コレクションは各テストの前に発生します。
2. ロジスティック回帰モデルは、テーブルデータに基づいて作成されました。 各テストの *rowsPerRead* の値は 500000 に設定されます。
3. トレーニング済みのモデルを使用してスコアが生成されました
4. 各テストは6回実行されました。 最初の実行の時刻 ("コールド実行") が削除されました。 不定期に外れ値を許可するために、残りの5つの実行の**最大**時間も削除されました。 それ以外の 4 回の実行の平均値から、各テストの平均実行時間を計算します。
5. テストは、値が 3 (= レポートのタイミングと進行状況) の*Reportprogress*パラメーターを使用して実行されました。 各出力ファイルには、IO、移行時間、およびコンピューティング時間に費やされた時間に関する情報が含まれています。 これらの時間はトラブルシューティングと診断に役立ちます。
6. コンソールの出力も、出力ディレクトリ内のファイルに送られました。
7. テストスクリプトは、これらのファイルの時刻を処理し、平均実行時間を計算します。

たとえば、次の結果は、1つのテストからの時間を示します。 関心がある主な時間は、**合計読み取り時間** (IO 時間) と **遷移時間** (計算のプロセスを設定するオーバーヘッド) です。

**サンプルのタイミング**

```text
Running IntCol Test. Using airlineWithIntCol table.
run 1 took 3.66 seconds
run 2 took 3.44 seconds
run 3 took 3.44 seconds
run 4 took 3.44 seconds
run 5 took 3.45 seconds
run 6 took 3.75 seconds
  
Average Time: 3.4425
metric time pct
1 Total time 3.4425 100.00
2 Overall compute time 2.8512 82.82
3 Total read time 2.5378 73.72
4 Transition time 0.5913 17.18
5 Total non IO time 0.3134 9.10
```

R Services または RevoScaleR functions に関する問題のトラブルシューティングに役立つように、テストスクリプトをダウンロードして変更することをお勧めします。

### <a name="test-results-all"></a>テスト結果 (すべて)

このセクションでは、各テストの前後の結果を比較します。

#### <a name="data-size-with-compression-and-a-columnar-table-store"></a>圧縮と列テーブルストアを使用したデータサイズ

最初のテストでは、圧縮と列形式のテーブルの使用を比較して、データのサイズを小さくします。

| テーブル名            | 行     | 予約済み   | data       | index_size | 未使用  | 節約率 (予約済み) |
|-----------------------|----------|------------|------------|------------|---------|---------------------|
| *airlineWithIndex*    | 10000000 | 2978816 KB | 2972160 KB | 6128 KB    | 528 KB  | 0                   |
| *airlineWithPageComp* | 10000000 | 625784 KB  | 623744 KB  | 1352 KB    | 688 KB  | 79%                 |
| *airlineWithRowComp*  | 10000000 | 1262520 KB | 1258880 KB | 2552 KB    | 1088 KB | 58%                 |
| *airlineColumnar*     | 9999999  | 201992 KB  | 201624 KB  | n/a        | 368 KB  | 93%                 |

**わかり**

列ストアインデックスを適用し、その後にページの圧縮を行うことにより、データサイズの最大値を減らすことができました。

#### <a name="effects-of-compression"></a>圧縮の効果

このテストでは、行の圧縮、ページの圧縮、および圧縮の利点を比較しています。 モデルは、3つ`rxLinMod`の異なるデータテーブルのデータを使用してトレーニングされています。 すべてのテーブルで同じ式とクエリが使用されました。

| テーブル名            | テスト名       | numTasks | 平均時間 |
|-----------------------|-----------------|----------|--------------|
| *airlineWithIndex*    | NoCompression   | 1        | 5.6775       |
|                       | NoCompression-parallel| 4        | 5.1775       |
| *airlineWithPageComp* | PageCompression | 1        | 6.7875       |
|                       | PageCompression-並列 | 4        | 5.3225       |
| *airlineWithRowComp*  | RowCompression  | 1        | 6.1325       |
|                       | RowCompression-parallel  | 4        | 5.2375       |

**わかり**

圧縮だけでは役に立ちません。 この例では、圧縮を処理する CPU の増加によって IO 時間の短縮が補正されています。

ただし、*numTasks* を 4 に設定してテストを並列実行すると、平均時間が短縮されます。

データ セットの量が増えると、圧縮の効果はさらに顕著になる可能性があります。 圧縮はデータ セットと値によって変わるため、自らのデータ セットに圧縮が与える効果を判別するには実験することをお勧めします。

### <a name="effect-of-windows-power-plan-options"></a>Windows の電源プランオプションの効果

この実験では、`rxLinMod` が *airlineWithIntCol* テーブルで使用されました。 Windows の電源プランが**均衡**または**高パフォーマンス**のいずれかに設定されました。 すべてのテストで *numTasks* は 1 に設定されました。 テストは6回実行され、両方の電源オプションで2回実行され、結果の変動を調査しました。

**高パフォーマンス**の電源オプション:

| テスト名 | \#を実行します。 | 経過時間 | 平均時間 |
|-----------|--------|--------------|--------------|
| IntCol    | 1      | 3.57 秒 |              |
|           | 2      | 3.45 秒 |              |
|           | 3      | 3.45 秒 |              |
|           | 4      | 3.55 秒 |              |
|           | 5      | 3.55 秒 |              |
|           | 6      | 3.45 秒 |              |
|           |        |              | 3.475        |
|           | 1      | 3.45 秒 |              |
|           | 2      | 3.53 秒 |              |
|           | 3      | 3.63 秒 |              |
|           | 4      | 3.49 秒 |              |
|           | 5      | 3.54 秒 |              |
|           | 6      | 3.47 秒 |              |
|           |        |              | 3.5075       |

**[バランス]** 電源オプション:

| テスト名 | \#を実行します。 | 経過時間 | 平均時間 |
|-----------|--------|--------------|--------------|
| IntCol    | 1      | 3.89 秒 |              |
|           | 2      | 4.15 秒 |              |
|           | 3      | 3.77 秒 |              |
|           | 4      | 5 秒    |              |
|           | 5      | 3.92 秒 |              |
|           | 6      | 3.8 秒  |              |
|           |        |              | 3.91         |
|           | 1      | 3.82 秒 |              |
|           | 2      | 3.84 秒 |              |
|           | 3      | 3.86 秒 |              |
|           | 4      | 4.07 秒 |              |
|           | 5      | 4.86 秒 |              |
|           | 6      | 3.75 秒 |              |
|           |        |              | 3.88         |

**わかり**

Windows の**高パフォーマンス**の電源プランを使用すると、実行時間の整合性が向上し、より高速になります。

#### <a name="using-integer-vs-strings-in-formulas"></a>数式での整数と文字列の使用

このテストでは、文字列要因に関する一般的な問題を回避するために、R コードを変更した場合の影響を評価します。 具体的には、2つの`rxLinMod`テーブルを使用してモデルがトレーニングされています。1つ目の要素は文字列として格納されます。2番目のテーブルでは、要素が整数として格納されます。

+ *航空*テーブルの [DayOfWeek] 列には、文字列が含まれています。 _Colinfo_パラメーターを使用して係数レベルを指定しました (月曜日、火曜日、...)

+  *航空 Linewithindex*テーブルの場合、[DayOfWeek] は整数です。 _Colinfo_パラメーターが指定されていません。

+ どちらの場合も、同じ式 `ArrDelay ~ CRSDepTime + DayOfWeek` が使用されました。

| テーブル名          | テスト名   | 平均時間 |
|---------------------|-------------|--------------|
| *航空会社*           | *FactorCol* | 10.72        |
| *airlineWithIntCol* | *IntCol*    | 3.4475       |

**わかり**

要素の文字列ではなく整数を使用する場合、明確な利点があります。

### <a name="avoiding-transformation-functions"></a>変換関数の回避

このテストでは、を使用して`rxLinMod`モデルをトレーニングしましたが、次の2つの実行の間でコードが変更されました。

+ 最初の実行では、変換関数がモデル構築の一部として適用されました。 
+ 2番目の実行では、機能の値が事前計算済みされ、使用できるようになりました。変換関数は必要ありませんでした。

| テスト名             | 平均時間 |
|-----------------------|--------------|
| WithTransformation    | 5.1675       |
| WithoutTransformation | 4.7          |

**わかり**

変換関数を使用し**ない**と、トレーニング時間が短縮されました。 つまり、事前に計算され、テーブルに保存されている列を使用すると、モデルのトレーニングが高速になりました。

変換が多く、データセットのサイズが大きい (\> 100 mb) 場合は、節約率が高くなることが予想されます。

### <a name="using-columnar-store"></a>列ストアの使用

このテストでは、単票形式のデータストアとインデックスを使用した場合のパフォーマンス上の利点を評価します。 同じモデルは、を使用`rxLinMod`してトレーニングされ、データ変換は行われませんでした。

+ 最初の実行では、データテーブルで標準の行ストアが使用されていました。
+ 2番目の実行では、列ストアが使用されていました。

| テーブル名         | テスト名 | 平均時間 |
|--------------------|-----------|--------------|
| *airlineWithIndex* | RowStore  | 4.67         |
| *airlineColumnar*  | ColStore  | 4.555        |

**わかり**

標準の行ストアよりも、列ストアのパフォーマンスが向上しています。 大きなデータセット (\> 100 M) では、パフォーマンスの大きな違いが予想される場合があります。

### <a name="effect-of-using-the-cube-parameter"></a>Cube パラメーターを使用した場合の影響

このテストの目的は、事前計算済み**cube**パラメーターを使用するための RevoScaleR のオプションによってパフォーマンスが向上するかどうかを判断することでした。 モデルは、次の`rxLinMod`式を使用してトレーニングされました。

```R
ArrDelay ~ Origin:DayOfWeek + Month + DayofMonth + CRSDepTime
```

テーブルでは、 *DayOfWeek*要素が文字列として格納されます。

| テスト名     | Cube パラメーター | numTasks | 平均時間 | 単一行予測 (ArrDelay_Pred) |
|---------------|----------------|----------|--------------|---------------------------------|
| CubeArgEffect | `cube = F`     | 1        | 91.0725      | 9.959204                        |
|               |                | 4        | 44.09        | 9.959204                        |
|               | `cube = T`     | 1        | 21.1125      | 9.959204                        |
|               |                | 4        | 8.08         | 9.959204                        |

**わかり**

Cube パラメーター引数を使用すると、パフォーマンスが明らかに向上します。

### <a name="effect-of-changing-maxdepth-for-rxdtree-models"></a>RxDTree モデルの maxDepth を変更した場合の影響

この実験`rxDTree`では、アルゴリズムを使用して、 *airlineColumnar*テーブルにモデルを作成しました。 このテストでは *numTasks* は 4 に設定されました。 *MaxDepth*のいくつかの異なる値は、ツリーの深さの変更が実行時に与える影響を示すために使用されていました。

| テスト名       | maxDepth | 平均時間 |
|-----------------|----------|--------------|
| TreeDepthEffect | 1        | 10.1975      |
|                 | 2        | 13.2575      |
|                 | 4        | 19.27        |
|                 | 8        | 45.5775      |
|                 | 16       | 339.54       |

**わかり**

ツリーの深さが増えると、ノードの合計数が指数関数的に増加します。 また、モデル作成の経過時間も大幅に増加しています。

### <a name="prediction-on-a-stored-model"></a>格納済みモデルの予測

このテストの目的は、トレーニング済みのモデルが現在実行中のコードの一部として生成されるのではなく、SQL Server テーブルに保存される場合のスコアリングのパフォーマンスへの影響を判断することでした。 スコアリングのために、格納されているモデルはデータベースから読み込まれ、メモリ内の1行のデータフレーム (ローカルの計算コンテキスト) を使用して予測が作成されます。

テスト結果には、モデルを保存する時間と、モデルの読み込みと予測にかかる時間が表示されます。

| テーブル名 | テスト名 | 平均時間 (モデルのトレーニング) | モデルの保存/読み込みの時間|
|------------|------------|------------|------------|
| airline    | SaveModel| 21.59| 2.08|
| airline    | LoadModelAndPredict | | 2.09 (予測する時間を含む) |

**わかり**

トレーニング済みのモデルをテーブルから読み込むことは、より迅速に予測を行うことができます。 同じスクリプトでモデルを作成し、スコア付けをすべて実行しないことをお勧めします。

## <a name="case-study-optimization-for-the-resume-matching-task"></a>ケーススタディ:再開照合タスクの最適化

再開照合モデルは、Microsoft データサイエンス Ke によって開発され、SQL Server で R コードのパフォーマンスをテストし、データ科学者がスケーラブルなエンタープライズレベルのソリューションを作成できるようにしました。

### <a name="methods"></a>メソッド

RevoScaleR パッケージと Microsoft Ml パッケージは両方とも、大規模なデータセットを含む複雑な R ソリューションで予測モデルをトレーニングするために使用されていました。 SQL クエリと R コードは、すべてのテストで同一でした。 テストは、SQL Server がインストールされた単一の Azure VM で実行されました。 作成者は、SQL Server によって提供される次の最適化を使用して、スコア付け時間を比較します。

- インメモリテーブル
- ssNoVersion
- [リソース ガバナー]

R スクリプトの実行時にソフト NUMA の効果を評価するために、データサイエンスチームは20個の物理コアを持つ Azure 仮想マシンでソリューションをテストしています。 これらの物理コアでは、各ノードに5つのコアが含まれるように、4つのソフト NUMA ノードが自動的に作成されました。

R ジョブへの影響を評価するために、再開照合シナリオで CPU affinitization が適用されました。 4つの**SQL リソースプール**と4つの**外部リソースプール**が作成され、cpu 関係が指定され、各ノードで同じ cpu セットが使用されるようになりました。

各リソースプールは、ハードウェアの使用率を最適化するために、別のワークロードグループに割り当てられています。 これは、ソフト NUMA と CPU アフィニティが物理 NUMA ノードの物理メモリを分割できないためです。したがって、定義上、同じ物理 NUMA ノードに基づくすべてのソフト NUMA ノードは、同じ OS メモリブロック内のメモリを使用する必要があります。 つまり、メモリとプロセッサ間の関係はありません。

この構成を作成するには、次のプロセスが使用されました。

1. 既定で SQL Server に割り当てられるメモリの量を減らします。

2. R ジョブを並行して実行するために、4つの新しいプールを作成します。

3. 各ワークロードグループがリソースプールに関連付けられるように、4つのワークロードグループを作成します。

4. 新しいワークロードグループと割り当てを使用して Resource Governor を再起動します。

5. 異なるワークロードグループに異なるタスクを割り当てるユーザー定義の分類子関数 (UDF) を作成します。

6. 適切なワークロードグループに関数を使用するように Resource Governor 構成を更新します。

### <a name="results"></a>[結果]

再開照合の調査で最高のパフォーマンスが得られた構成は次のとおりです。

-   4つの内部リソースプール (SQL Server 用)

-   4つの外部リソースプール (外部スクリプトジョブ用)

-   各リソースプールは、特定のワークロードグループに関連付けられています。

-   各リソースプールは異なる Cpu に割り当てられます。

-   内部メモリの最大使用量 (SQL Server) = 30%

-   R セッションで使用する最大メモリ = 70%

再開照合モデルの場合、外部スクリプトの使用は非常に高く、他のデータベースエンジンサービスは実行されていませんでした。 そのため、外部スクリプトに割り当てられたリソースが 70% に増加し、スクリプトのパフォーマンスが最適な構成になりました。

この構成は、別の値を使用して実験することによって到着しました。 異なるハードウェアや別のソリューションを使用している場合は、最適な構成が異なる場合があります。 ケースに最適な構成を見つけるには、常に試してください。

最適化されたソリューションでは、20コアのコンピューターでは、110万のデータ (100 機能を含む) が8.5 秒未満に評価されました。 最適化により、スコアリング時間の観点からパフォーマンスが大幅に向上しました。

また、この結果には、**特徴の数**がスコア付け時間に大きな影響を与えたことが示されています。 予測モデルで多くの機能が使用されている場合、改善はさらに目立つようになりました。

詳細については、このブログ記事と付随するチュートリアルをお読みになることをお勧めします。

-   [SQL Server での機械学習の最適化に関するヒントとコツ](https://azure.microsoft.com/blog/optimization-tips-and-tricks-on-azure-sql-server-for-machine-learning-services/)

多くのユーザーは、R (または Python) ランタイムが初めて読み込まれるため、小さな一時停止が発生していることに注意してください。 このため、これらのテストで説明したように、最初の実行時間は通常、測定されますが、その後は破棄されます。 後続のキャッシュでは、最初の実行と2回目の実行の間に、パフォーマンスが顕著に異なる場合があります。 また、SQL Server から直接読み込まれるのではなく、データがネットワーク経由で渡される場合は特に、SQL Server と外部ランタイム間でデータが移動される場合にもオーバーヘッドが発生します。

これらのすべての理由により、この最初の読み込み時間を軽減するためのソリューションはありません。パフォーマンスへの影響はタスクによって大きく異なります。 たとえば、キャッシュは、1行のスコアリングをバッチで実行します。そのため、連続したスコア付け操作がはるかに高速になり、モデルも R ランタイムも再読み込みされません。 [ネイティブスコアリング](../sql-native-scoring.md)を使用して、R ランタイムを完全に読み込まないようにすることもできます。

大規模なモデルをトレーニングしたり、大きなバッチでスコアを付けたりする場合、データの移動を回避したり、ストリーミングや並列処理を行ったりすることによるオーバーヘッドを最小限に抑えることができます。 その他のパフォーマンスガイダンスについては、こちらのブログ投稿を参照してください。

+ [R を使用して1秒あたり100万トランザクションで不正行為を検出する](https://blog.revolutionanalytics.com/2016/09/fraud-detection.html/)

## <a name="resources"></a>リソース

これらのテストの開発で使用される情報、ツール、およびスクリプトへのリンクを次に示します。

+ パフォーマンステストスクリプトとデータへのリンク:[SQL Server 最適化スタディのサンプルデータとスクリプト](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PerfTuning)

+ 再開照合ソリューションについて説明する記事:[SQL Server R Services の最適化に関するヒントとコツ](https://azure.microsoft.com/blog/optimization-tips-and-tricks-on-azure-sql-server-for-machine-learning-services/)

+ 再開照合ソリューションの SQL 最適化で使用されるスクリプト:[GitHub リポジトリ](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/SQLOptimizationTips)

### <a name="learn-about-windows-server-management"></a>Windows server の管理について

+ [64 ビット版 Windows 用の適切なページ ファイルのサイズを確認する方法](https://support.microsoft.com/kb/2860880)

+ [NUMA について](https://technet.microsoft.com/library/ms178144.aspx)

+ [SQL Server が NUMA をサポートする方法](https://technet.microsoft.com/library/ms180954.aspx)

+ [ソフト NUMA](https://docs.microsoft.com/sql/database-engine/configure-windows/soft-numa-sql-server)

### <a name="learn-about-sql-server-optimizations"></a>SQL Server の最適化について

+ [インデックスの再編成と再構築](../../relational-databases/indexes/reorganize-and-rebuild-indexes.md)

+ [メモリ最適化テーブルの概要](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)

+ 「[実証: インメモリ OLTP のパフォーマンスの向上](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/demonstration-performance-improvement-of-in-memory-oltp)

+ [データの圧縮](../../relational-databases/data-compression/data-compression.md)

+ [テーブルまたはインデックスの圧縮の有効化](../../relational-databases/data-compression/enable-compression-on-a-table-or-index.md)

+ [テーブルまたはインデックスの圧縮の無効化](../../relational-databases/data-compression/disable-compression-on-a-table-or-index.md)

### <a name="learn-about-managing-sql-server"></a>SQL Server の管理についての詳細情報

+ [パフォーマンスの監視とチューニング](../../relational-databases/performance/monitor-and-tune-for-performance.md)

+ [リソース ガバナー](../../relational-databases/resource-governor/resource-governor.md)

+ [Resource Governor の概要](https://technet.microsoft.com/library/bb895232.aspx)

+ [R Services のリソース管理](resource-governance-for-r-services.md)

+ [R 用のリソースプールを作成する方法](how-to-create-a-resource-pool-for-r.md)

+ [Resource Governor を構成する例](https://blog.sqlauthority.com/2012/06/04/sql-server-simple-example-to-configure-resource-governor-introduction-to-resource-governor/)

### <a name="tools"></a>ツール

+ [DISKSPD ストレージ ロード ジェネレーター/パフォーマンス テスト ツール](https://github.com/microsoft/diskspd)

+ [FSUtil ユーティリティ リファレンス](https://technet.microsoft.com/library/cc753059.aspx)


## <a name="other-articles-in-this-series"></a>このシリーズの他の記事

[R のパフォーマンスチューニング-概要](sql-server-r-services-performance-tuning.md)

[R SQL Server 構成のパフォーマンスチューニング](sql-server-configuration-r-services.md)

[R のパフォーマンスチューニング-R コードとデータの最適化](r-and-data-optimization-r-services.md)

[パフォーマンスチューニング-ケーススタディの結果](performance-case-study-r-services.md)
