---
title: rxExecBy を使用して複数のモデルを作成する
description: SQL Server に格納されているマシンデータに対して複数のミニモデルを構築するには、RevoScaleR ライブラリの rxExecBy 関数を使用します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 1b2e6e1d546b9bbd1b3b3196c37716acbbe0ae74
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715710"
---
# <a name="creating-multiple-models-using-rxexecby"></a>rxExecBy を使用して複数のモデルを作成する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

RevoScaleR の**rxExecBy**関数は、複数の関連モデルの並列処理をサポートしています。 データ科学者は、類似した複数のエンティティのデータに基づいて1つの大きなモデルをトレーニングするのではなく、1つのエンティティに固有のデータを使用して、多数の関連モデルをすばやく作成できます。 

たとえば、デバイスの障害を監視していて、さまざまな種類の機器のデータをキャプチャしているとします。 RxExecBy を使用すると、1つの大規模なデータセットを入力として指定し、デバイスの種類など、データセットを分配する列を指定してから、個々のデバイスに対して複数のモデルを作成できます。

このユースケースは、大量の複雑な問題をコンポーネントパーツに分割して同時処理するため、 ["pleasingly parallel"](https://en.wikipedia.org/wiki/Embarrassingly_parallel)と呼ばれていました。

この方法の一般的なアプリケーションには、個々の家庭用スマートメーターの予測、個別の製品ラインの収益予測の作成、個々の銀行の分岐に合わせて調整されたローン承認のためのモデルの作成などがあります。

## <a name="how-rxexec-works"></a>RxExec のしくみ

RevoScaleR の rxExecBy 関数は、多数の小さなデータセットに対して大量の並列処理を行うように設計されています。

1. R コードの一部として rxExecBy 関数を呼び出し、順序付けられていないデータのデータセットを渡します。
2. データのグループ化と並べ替えを行うパーティションを指定します。
3. 各データパーティションに適用する必要がある変換関数またはモデリング関数を定義します。
4. 関数を実行すると、データクエリは、環境でサポートされている場合は並列処理されます。 さらに、モデリングタスクまたは変換タスクは個々のコアに分散され、並列で実行されます。 RxSpark や RxInSQLServer など、操作に対してサポートされている計算コンテキスト。
5. 複数の結果が返されます。

## <a name="rxexecby-syntax-and-examples"></a>rxExecBy の構文と例

**rxExecBy**は4つの入力を受け取ります。入力の1つは、指定された**キー**列でパーティション分割できるデータセットまたはデータソースオブジェクトです。 関数は、各パーティションの出力を返します。 出力形式は、引数として渡される関数によって異なります。 たとえば、rxLinMod などのモデリング関数を渡した場合、データセットの各パーティションに対して個別のトレーニング済みモデルを返すことができます。

### <a name="supported-functions"></a>サポートされる関数

モデリング: `rxLinMod` `rxLogit` 、、、`rxGlm``rxDtree`

スコアリング: `rxPredict`、

変換または分析:`rxCovCor`

## <a name="example"></a>例

次の例は、航空データセットを使用して複数のモデルを作成する方法を示しています。このデータセットは、[DayOfWeek] 列でパーティション分割されています。 ユーザー定義関数`delayFunc`は、rxExecBy を呼び出すことによって各パーティションに適用されます。 この関数は、月曜日、火曜日などに個別のモデルを作成します。

```sql
EXEC sp_execute_external_script
@language = N'R'
, @script = N'
delayFunc <- function(key, data, params) { 
    df <- rxImport(inData = airlineData) 
    rxLinMod(ArrDelay ~ CRSDepTime, data = df) 
} 
OutputDataSet <- rxExecBy(airlineData, c("DayOfWeek"), delayFunc)
'
, @input_data_1 = N'select ArrDelay, DayOfWeek, CRSDepTime from AirlineDemoSmall]'
, @input_data_1_name = N'airlineData'

```

エラーが発生`varsToPartition is invalid`した場合は、キー列の名前が正しく入力されているかどうかを確認します。 R 言語では大文字と小文字が区別されます。

この特定の例は SQL Server に最適化されていません。多くの場合、SQL を使用してデータをグループ化することで、パフォーマンスを向上させることができます。 ただし、rxExecBy を使用すると、R から並列ジョブを作成できます。

次の例は、SQL Server を計算コンテキストとして使用して、R でのプロセスを示しています。

```R
sqlServerConnString <- "SERVER=hostname;DATABASE=TestDB;UID=DBUser;PWD=Password;"
inTable <- paste("airlinedemosmall")
sqlServerDataDS <- RxSqlServerData(table = inTable, connectionString = sqlServerConnString)

# user function
".Count" <- function(keys, data, params)
{
  myDF <- rxImport(inData = data)
  return (nrow(myDF))
}

# Set SQL Server compute context with level of parallelism = 2
sqlServerCC <- RxInSqlServer(connectionString = sqlServerConnString, numTasks = 4)
rxSetComputeContext(sqlServerCC)

# Execute rxExecBy in SQL Server compute context
sqlServerCCResults <- rxExecBy(inData = sqlServerDataDS, keys = c("DayOfWeek"), func = .Count)
```


