---
title: PREDICT ステートメントを監視するための拡張イベント
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 283e128285fc50b9109d7950b171e30224fb9692
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714641"
---
# <a name="extended-events-for-monitoring-predict-statements"></a>PREDICT ステートメントを監視するための拡張イベント

この記事では、SQL Server で提供される拡張イベントについて説明します。このイベントを使用して、SQL Server でのリアルタイムのスコアリングを実行するために[PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql)を使用するジョブを監視および分析できます。

リアルタイムスコアリングは、SQL Server に格納されている機械学習モデルからスコアを生成します。 PREDICT 関数では、R や Python などの外部の実行時間は必要ありません。特定のバイナリ形式を使用して作成されたモデルのみです。 詳細については、「[リアルタイムスコアリング](https://docs.microsoft.com/sql/advanced-analytics/real-time-scoring)」を参照してください。

## <a name="prerequisites"></a>前提条件

拡張イベント (Xevent とも呼ばれます) に関する一般的な情報と、セッションのイベントを追跡する方法については、次の記事を参照してください。

+ [拡張イベントの概念とアーキテクチャ](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)
+ [SSMS でのイベントキャプチャの設定](https://docs.microsoft.com/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
+ [オブジェクトエクスプローラーでのイベントセッションの管理](https://docs.microsoft.com/sql/relational-databases/extended-events/manage-event-sessions-in-the-object-explorer)

## <a name="table-of-extended-events"></a>拡張イベントの表

次の拡張イベントは、SQL Server on Linux、Azure SQL Database など、 [T-SQL PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql)ステートメントをサポートするすべてのバージョンの SQL Server で使用できます。 

T-sql PREDICT ステートメントは SQL Server 2017 で導入されました。 

|NAME |object_type|description| 
|----|----|----|
|predict_function_completed |イベント  |組み込みの実行時間の内訳|
|predict_model_cache_hit |イベント|モデルが PREDICT 関数モデルキャッシュから取得されたときに発生します。 このイベントを他の predict_model_cache_ * イベントと共に使用して、PREDICT 関数モデルキャッシュが原因で発生した問題のトラブルシューティングを行います。|
|predict_model_cache_insert |イベント  |   モデルが PREDICT 関数モデルキャッシュに挿入されるときに発生します。 このイベントを他の predict_model_cache_ * イベントと共に使用して、PREDICT 関数モデルキャッシュが原因で発生した問題のトラブルシューティングを行います。    |
|predict_model_cache_miss   |イベント|PREDICT 関数モデルキャッシュでモデルが見つからない場合に発生します。 このイベントが頻繁に発生するのは、SQL Server により多くのメモリが必要であることを示す可能性があります。 このイベントを他の predict_model_cache_ * イベントと共に使用して、PREDICT 関数モデルキャッシュが原因で発生した問題のトラブルシューティングを行います。|
|predict_model_cache_remove |イベント| 予測関数のモデルキャッシュからモデルが削除されたときに発生します。 このイベントを他の predict_model_cache_ * イベントと共に使用して、PREDICT 関数モデルキャッシュが原因で発生した問題のトラブルシューティングを行います。|

## <a name="query-for-related-events"></a>関連イベントのクエリ

これらのイベントに対して返されたすべての列の一覧を表示するには、SQL Server Management Studio で次のクエリを実行します。

```sql
SELECT * FROM sys.dm_xe_object_columns WHERE object_name LIKE `predict%'
```

## <a name="examples"></a>使用例

PREDICT を使用してスコアリングセッションのパフォーマンスに関する情報を取得するには

1. Management Studio またはサポートされている別の[ツール](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events-tools)を使用して、新しい拡張イベントセッションを作成します。
2. イベント`predict_function_completed` と`predict_model_cache_hit`をセッションに追加します。
3. 拡張イベントセッションを開始します。
4. PREDICT を使用するクエリを実行します。

結果で、次の列を確認します。

+ の`predict_function_completed`値は、クエリがモデルの読み込みとスコア付けに費やした時間を示します。
+ の`predict_model_cache_hit`ブール値は、クエリがキャッシュされたモデルを使用したかどうかを示します。 

### <a name="native-scoring-model-cache"></a>ネイティブスコアリングモデルキャッシュ

予測に固有のイベントに加えて、次のクエリを使用して、キャッシュされたモデルとキャッシュの使用に関する詳細情報を取得できます。

ネイティブスコアリングモデルキャッシュを表示します。

```sql
SELECT *
FROM sys.dm_os_memory_clerks
WHERE type = 'CACHESTORE_NATIVESCORING';
```

モデルキャッシュ内のオブジェクトを表示します。

```sql
SELECT *
FROM sys.dm_os_memory_objects
WHERE TYPE = 'MEMOBJ_NATIVESCORING';
```

