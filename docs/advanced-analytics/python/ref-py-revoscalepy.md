---
title: revoscalepy Python パッケージ
description: Python での SQL Server Machine Learning Services での revoscalepy モジュールの概要について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 12/12/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 76c68d0753c4ba29387b3378c1086ce9bce4f53b
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715776"
---
# <a name="revoscalepy-python-module-in-sql-server"></a>revoscalepy (SQL Server の Python モジュール)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

**revoscalepy**は、分散コンピューティング、リモート計算コンテキスト、および高パフォーマンスのデータサイエンスアルゴリズムをサポートする、Microsoft の Python35 と互換性のあるモジュールです。 これは、次のような機能を提供する R の**RevoScaleR**パッケージ (Microsoft R Server および SQL Server R Services と共に配布されます) に基づいています。

+ 同じバージョンの**revoscalepy**を持つシステム上のローカルとリモートの計算コンテキスト
+ データ変換と視覚化の機能
+ データサイエンス関数、分散または並列処理によってスケーラブル
+ Intel math ライブラリの使用など、パフォーマンスの向上

**Revoscalepy**で作成したデータソースと計算コンテキストは、機械学習アルゴリズムでも使用できます。 これらのアルゴリズムの概要については、 [SQL Server の「microsoft Ml Python モジュール](ref-py-microsoftml.md)」を参照してください。

## <a name="full-reference-documentation"></a>完全なリファレンスドキュメント

**Revoscalepy**ライブラリは複数の Microsoft 製品に配布されていますが、SQL Server または別の製品でライブラリを入手したとしても、使用方法は同じです。 関数は同じであるため、[個々の revoscalepy 関数のドキュメント](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)は、Microsoft Machine Learning Server の[Python リファレンス](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)の下にある1つの場所にのみ発行されます。 製品固有の動作が存在する場合、関数のヘルプページには相違点が示されます。

## <a name="versions-and-platforms"></a>バージョンとプラットフォーム

**Revoscalepy**モジュールは Python 3.5 に基づいており、次のいずれかの Microsoft 製品またはダウンロードをインストールした場合にのみ使用できます。

+ [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)
+ [Microsoft Machine Learning Server 9.2.0 以降](https://docs.microsoft.com/machine-learning-server/)
+ [データサイエンスクライアント用の Python クライアントライブラリ](setup-python-client-tools-sql.md)

> [!NOTE]
> 製品の完全リリースバージョンは、SQL Server 2017 以降の Windows のみで使用できます。 Linux support for **revoscalepy**は、 [SQL Server 2019 Preview](../../linux/sql-server-linux-setup-machine-learning.md)の新機能です。

## <a name="functions-by-category"></a>カテゴリ別の関数

このセクションでは、関数をカテゴリ別に一覧表示して、それぞれの使用方法について説明します。 また、[目次](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)を使用して、関数をアルファベット順に検索することもできます。

## <a name="1-data-source-and-compute"></a>1-データソースとコンピューティング

**revoscalepy**には、データソースを作成し、計算を実行する場所の場所または*計算コンテキスト*を設定するための関数が含まれています。 SQL Server シナリオに関連する関数を次の表に示します。

SQL Server と Python は、場合によっては異なるデータ型を使用します。 SQL データ型と Python データ型の間のマッピングの一覧については、「 [python と sql のデータ型](python-libraries-and-data-types.md)」を参照してください。

| 関数| 説明|
| ------- | ---------- |
| [RxInSqlServer](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rxinsqlserver) |  計算コンテキストオブジェクト SQL Server を作成して、リモートインスタンスに計算をプッシュします。 いくつかの**revoscalepy**関数は、引数として計算コンテキストを受け取ります。 コンテキストスイッチの例については、「 [revoscalepy を使用してモデルを作成](../tutorials/use-python-revoscalepy-to-create-model.md)する」を参照してください。|
| [RxSqlServerData](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rxsqlserverdata) | SQL Server クエリまたはテーブルに基づいてデータオブジェクトを作成します。 |
| [RxOdbcData](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rxodbcdata)| ODBC 接続に基づいてデータソースを作成します。 |
| [RxXdfData](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rxxdfdata) | ローカル XDF ファイルに基づいてデータソースを作成します。 XDF ファイルは、メモリ内データをディスクにオフロードするためによく使用されます。 XDF ファイルは、1つのバッチでデータベースから転送できるよりも多くのデータを操作する場合や、メモリに収まりきらないデータを追加する場合に役立ちます。 たとえば、データベースからローカルワークステーションに大量のデータを定期的に移動する場合は、R 操作ごとにデータベースに対して繰り返しクエリを実行するのではなく、XDF ファイルをキャッシュの一種として使用して、データをローカルに保存し、R ワークスペースで操作することができます。 |

> [!TIP]
> データソースまたはコンピューティングコンテキストの概念を初めて使用する場合は、Microsoft Machine Learning Server のドキュメントで[分散コンピューティング](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing)から始めることをお勧めします。

## <a name="2-data-manipulation-etl"></a>2-データ操作 (ETL)

| 関数 | 説明 |
|----------|-------------|
|[rx_import](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-import) | データを xdf ファイルまたはデータフレームにインポートします。|
|[rx_data_step](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-data-step) | 入力データセットから出力データセットにデータを変換します。|

<a name="bkmk_algorithms"></a>

## <a name="3-training-and-summarization"></a>3-トレーニングと概要

| 関数| 説明|
| ------- | ---------- |
|[rx_btrees](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-btrees) | ストキャスティクス勾配ブーストデシジョンツリーに合わせる|
|[rx_dforest](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-dforest) | 分類と回帰のデシジョンフォレストに合わせる|
|[rx_dtree](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-dtree) | 分類と回帰ツリーに合わせる |
|[rx_lin_mod](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-lin-mod) | 線形回帰モデルを作成する|
|[rx_logit](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-logit) | ロジスティック回帰モデルを作成する|
|[rx_summary](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-summary) | Revoscalepy 内のオブジェクトの一覧を生成します。|

また、その他の方法については、「 [microsoft ml](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)の関数」も確認してください。

<a name="ml-scoring"></a>

## <a name="4-scoring-functions"></a>4-スコアリング関数

| 関数| 説明|
| ------- | ---------- |
| [rx_predict](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-predict) | トレーニング済みのモデルから予測を生成する|) | トレーニング済みのモデルから予測を生成し、リアルタイムのスコアリングに使用できます。 |
|[rx_predict_default](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-predict-default) | Rx_lin_mod および rx_logit オブジェクトを使用して予測値と残余を計算します。 |
|[rx_predict_rx_dforest](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-predict-rx-dforest) | Rx_dforest または rx_btrees オブジェクトからのデータセットの予測値または値を計算します。 |
|[rx_predict_rx_dtree](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-predict-rx-dtree) | Rx_dtree オブジェクトからデータセットの予測値または値を計算します。 |

## <a name="how-to-work-with-revoscalepy"></a>Revoscalepy を使用する方法

**Revoscalepy**の関数は、ストアドプロシージャでカプセル化された Python コードで呼び出すことができます。 ほとんどの開発者はローカルで**revoscalepy**ソリューションをビルドし、完成した Python コードを配置の演習としてストアドプロシージャに移行します。

ローカルで実行する場合は、通常、コマンドラインまたは Python 開発環境から Python スクリプトを実行し、 **revoscalepy**関数のいずれかを使用して SQL Server 計算コンテキストを指定します。 リモートコンピューティングコンテキストは、コード全体または個々の関数に使用できます。 たとえば、最新のデータを使用してデータの移動を回避するために、モデルのトレーニングをサーバーにオフロードすることができます。

Python スクリプトをストアドプロシージャ[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)内にカプセル化する準備ができたら、明確に定義された入力と出力を持つ単一の関数としてコードを書き直すことをお勧めします。 

入力と出力は、**pandas** のデータフレームである必要があります。 この処理が完了すると、T-sql をサポートする任意のクライアントからストアドプロシージャを呼び出し、SQL クエリを入力として簡単に渡すことができ、結果を SQL テーブルに保存できます。 例については、「 [SQL 開発者向けのデータベース内 Python analytics につい](../tutorials/sqldev-in-database-python-for-sql-developers.md)て」を参照してください。

### <a name="using-revoscalepy-with-microsoftml"></a>Microsoft ml での revoscalepy の使用

[Microsoft ml](ref-py-microsoftml.md)用の Python 関数は、revoscalepy で提供されているコンピューティングコンテキストとデータソースと統合されています。 たとえば、モデルの定義とトレーニングを行うときに、microsoft ml から関数を呼び出すときは、revoscalepy 関数を使用して、ローカルまたは SQl Server のリモート計算コンテキストで Python コードを実行します。

次の例は、Python コードでモジュールをインポートするための構文を示しています。 その後、必要な個々の関数を参照できます。

```python
from microsoftml.modules.logistic_regression.rx_logistic_regression import rx_logistic_regression
from revoscalepy.functions.RxSummary import rx_summary
from revoscalepy.etl.RxImport import rx_import_datasource
```

## <a name="see-also"></a>関連項目

+ [Python のチュートリアル](../tutorials/sql-server-python-tutorials.md)
+ [チュートリアル: T-sql に Python コードを埋め込む](../tutorials/run-python-using-t-sql.md)
+ [Python リファレンス (Microsoft Machine Learning Server)](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)
