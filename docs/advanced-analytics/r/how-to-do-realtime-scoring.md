---
title: 機械学習モデルを使用して予測と予測を生成する
description: リアルタイムスコアリングには rxPredict または sp_rxPredict を使用し、SQL Server Machine Learning での R および Python の予測と予測については、ネイティブスコアリングのために T-sql を予測します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 08/30/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: d01be0f7d7a18091b965ad73b9bf035558b34864
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715694"
---
# <a name="how-to-generate-forecasts-and-predictions-using-machine-learning-models-in-sql-server"></a>SQL Server で機械学習モデルを使用して予測と予測を生成する方法
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

既存のモデルを使用して新しいデータ入力の結果を予測または予測することは、機械学習の中核となるタスクです。 この記事では、SQL Server で予測を生成する方法を列挙します。 これらの方法の中には、高速予測の内部処理手法があります。速度は、実行時の依存関係の増分削減に基づいています。 依存関係が減るほど、予測が高速になります。

内部処理インフラストラクチャ (リアルタイムまたはネイティブスコアリング) を使用するには、ライブラリの要件があります。 関数は、Microsoft ライブラリからのものである必要があります。 オープンソースまたはサードパーティの関数を呼び出す R または Python コードは、CLR またC++は拡張機能ではサポートされていません。

次の表に、予測と予測に使用するスコアリングフレームワークの概要を示します。 

| 手法           | Interface         | ライブラリの要件 | 処理速度 |
|-----------------------|-------------------|----------------------|----------------------|
| 機能拡張フレームワーク | [rxPredict (R)](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) <br/>[rx_predict (Python)](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-predict) | [なし] : モデルは任意の R または Python 関数に基づくことができます | 数百ミリ秒。 <br/>ランタイム環境の読み込みには、新しいデータがスコア付けされる前に、3 ~ 600 ミリ秒という固定コストがかかります。 |
| [リアルタイムスコアリング CLR 拡張機能](../real-time-scoring.md) | シリアル化されたモデルでの[sp_rxPredict](https://docs.microsoft.com//sql/relational-databases/system-stored-procedures/sp-rxpredict-transact-sql) | \R\N\R\NRevoScaleR、Microsoft Ml <br/>Python: revoscalepy、microsoft ml | 平均で数十ミリ秒。 |
| [ネイティブスコアリングC++拡張機能](../sql-native-scoring.md) | シリアル化されたモデルでの[t-sql 関数の予測](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) | \R\N\R\NRevoScaleR <br/>Python: revoscalepy | 平均で20ミリ秒未満。 | 

処理速度は、出力の物質ではなく、区別機能です。 同じ関数と入力を想定して、スコア付けされた出力は、使用する方法によって異なることはありません。

モデルは、サポートされている関数を使用して作成するか、ディスクに保存された未加工のバイトストリームにシリアル化するか、またはデータベースにバイナリ形式で格納する必要があります。 ストアドプロシージャまたは T-sql を使用すると、R または Python 言語の実行時間のオーバーヘッドなしでバイナリモデルを読み込んで使用できます。その結果、新しい入力で予測スコアを生成する際の時間が短縮されます。

CLR およびC++拡張機能の重要度は、データベースエンジン自体に近接しています。 データベースエンジンのネイティブ言語はです。 C++つまり、実行時にC++記述された拡張機能は、依存関係が少数になります。 これに対し、CLR 拡張機能は .NET Core に依存します。 

ご想像のとおり、プラットフォームのサポートは、これらの実行時環境の影響を受けます。 ネイティブデータベースエンジン拡張機能は、リレーショナルデータベースがサポートされている任意の場所で実行されます。Windows、Linux、Azure。 .NET Core 要件を持つ CLR 拡張は、現在は Windows のみです。

## <a name="scoring-overview"></a>スコア付けの概要

_スコアリング_は2段階のプロセスです。 まず、既にトレーニング済みのモデルをテーブルから読み込むように指定します。 次に、新しい入力データを関数に渡して、予測値 (または_スコア_) を生成します。 多くの場合、入力は T-sql クエリであり、テーブルまたは単一の行を返します。 確率を表す1つの列の値を出力することも、信頼区間、エラー、その他の便利な予測への補足など、いくつかの値を出力することもできます。

手順を実行すると、モデルを準備してスコアを生成する全体的なプロセスは、次のようにまとめられます。

1. サポートされているアルゴリズムを使用してモデルを作成します。 サポートは、選択したスコア付け方法によって異なります。
2. モデルをトレーニングします。
3. 特別なバイナリ形式を使用してモデルをシリアル化します。
3. モデルを SQL Server に保存します。 通常、これは、シリアル化されたモデルを SQL Server テーブルに格納することを意味します。
4. モデルとデータの入力をパラメーターとして指定して、関数またはストアドプロシージャを呼び出します。

入力に多数のデータ行が含まれている場合、通常は、スコア付けプロセスの一部として予測値をテーブルに挿入する方が高速です。 1つのスコアを生成することは、フォームまたはユーザー要求から入力値を取得し、そのスコアをクライアントアプリケーションに返すシナリオで、より一般的です。 連続したスコアを生成するときのパフォーマンスを向上させるために、SQL Server は、メモリに再読み込みできるようにモデルをキャッシュすることがあります。

## <a name="compare-methods"></a>メソッドの比較

コアデータベースエンジンプロセスの整合性を維持するために、R と Python のサポートは、RDBMS 処理から言語処理を分離するデュアルアーキテクチャで有効になっています。 SQL Server 2016 以降、Microsoft は、T-sql から R スクリプトを実行できる機能拡張フレームワークを追加しました。 SQL Server 2017 では、Python 統合が追加されました。 

拡張性フレームワークは、単純な関数から複雑な機械学習モデルのトレーニングまで、R または Python で実行できるあらゆる操作をサポートしています。 ただし、デュアルプロセスアーキテクチャでは、操作の複雑さに関係なく、すべての呼び出しに対して外部の R または Python プロセスを呼び出す必要があります。 ワークロードで事前トレーニング済みのモデルをテーブルから読み込んで、既に SQL Server にあるデータに対してスコア付けする必要がある場合、外部プロセスを呼び出すことによるオーバーヘッドによって、特定の状況で許容できない待機時間が発生します。 たとえば、不正行為の検出では、迅速なスコア付けが必要です。

不正行為の検出などのシナリオでスコアリング速度を向上させるために、にC++は、組み込みのスコアリングライブラリとして、および R と Python のスタートアッププロセスのオーバーヘッドを解消する CLR 拡張機能 SQL Server 追加されました。

[**リアルタイムスコアリング**](../real-time-scoring.md)は、ハイパフォーマンススコアリングのための最初のソリューションでした。 SQL Server 2017 の初期バージョンで導入され、SQL Server 2016 に更新されました。リアルタイムスコアリングは、RevoScaleR、Microsoft Ml (R)、revoscalepy、およびの Microsoft が管理する関数を介して R と Python を処理するのに役立つ CLR ライブラリに依存しています。microsoft ml (Python)。 CLR ライブラリは、R または Python ランタイムを呼び出さずに、サポートされている任意のモデル型からスコアを生成するために、 **sp_rxPredict**ストアドプロシージャを使用して呼び出されます。

[**ネイティブスコアリング**](../sql-native-scoring.md)は SQL Server 2017 機能で、ネイティブC++ライブラリとして実装されていますが、RevoScaleR モデルと revoscalepy モデルにのみ実装されています。 これは、最速で安全な方法ですが、他の方法と比較して、より小さな関数セットをサポートしています。

## <a name="choose-a-scoring-method"></a>スコア付け方法を選択する

プラットフォームの要件によって、使用するスコア付け方法が決まります。

| 製品バージョンとプラットフォーム | 手法 |
|------------------------------|-------------|
| Windows SQL Server 2017、SQL Server 2017 Linux、および Azure SQL Database | T-sql PREDICT を使用した**ネイティブスコアリング** |
| SQL Server 2017 (Windows のみ)、SQL Server 2016 R Services SP1 以降 | Sp\_rxPredict ストアドプロシージャを使用した**リアルタイムスコアリング** |

PREDICT 関数を使用したネイティブスコアリングをお勧めします。 Sp\_rxPredict を使用するには、SQLCLR 統合を有効にする必要があります。 このオプションを有効にする前に、セキュリティへの影響について検討してください。

## <a name="serialization-and-storage"></a>シリアル化とストレージ

高速スコア付けオプションのいずれかを使用してモデルを使用するには、サイズとスコア付けの効率のために最適化された特別なシリアル化形式を使用してモデルを保存します。

+ [RxSerializeModel](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxserializemodel)を呼び出して、サポートされているモデルを**raw**形式に書き込みます。
+ [RxUnserializeModel](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxserializemodel)' を呼び出して、他の R コードで使用するモデルを再構築するか、モデルを表示します。

**SQL の使用**

SQL コードからは、 [sp_execute_external_script](https://docs.microsoft.com//sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)を使用してモデルをトレーニングし、 **varbinary (max)** 型の列で、トレーニング済みのモデルをテーブルに直接挿入することができます。 単純な例については、「 [R での事前対応モデルの作成](../tutorials/rtsql-create-a-predictive-model-r.md)」を参照してください。

**R の使用**

R コードから、RevoScaleR パッケージから[rxWriteObject](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxwriteobject)関数を呼び出して、モデルをデータベースに直接書き込みます。 **RxWriteObject ()** 関数は、SQL Server などの ODBC データソースから R オブジェクトを取得したり、SQL Server にオブジェクトを書き込んだりできます。 この API は、単純なキーと値のストアの後にモデル化されています。
  
この関数を使用する場合は、最初に[rxSerializeModel](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxserializemodel)を使用してモデルをシリアル化してください。 次に、シリアル化の手順が繰り返されないように、 **rxWriteObject**の*SERIALIZE*引数を FALSE に設定します。

モデルをバイナリ形式にシリアル化することは便利ですが、拡張性フレームワークで R および Python 実行時環境を使用して予測をスコア付けする場合は必要ありません。 モデルを未加工のバイト形式でファイルに保存し、そのファイルから SQL Server に読み取ることができます。 このオプションは、環境間でモデルを移動またはコピーする場合に便利です。

## <a name="scoring-in-related-products"></a>関連製品でのスコアリング

[スタンドアロンサーバー](r-server-standalone.md)または[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)を使用している場合は、予測を迅速に生成するためのストアドプロシージャと t-sql 関数以外にもオプションがあります。 スタンドアロンサーバーと Machine Learning Server はどちらも、コード配置用の*web サービス*の概念をサポートしています。 R または Python 事前トレーニング済みモデルを web サービスとしてバンドルすることができます。これは、新しいデータ入力を評価するために、実行時にと呼ばれます。 詳細については、次の記事を参照してください。

+ [Machine Learning Server の web サービスとは何ですか。](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)
+ [運用化とは何ですか。](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)
+ [Azureml を使用して Python モデルを web サービスとしてデプロイする-sdk](https://docs.microsoft.com/machine-learning-server/operationalize/python/quickstart-deploy-python-web-service)
+ [新しい web サービスとして R コードブロックまたはリアルタイムモデルを発行する](https://docs.microsoft.com/machine-learning-server/r-reference/mrsdeploy/publishservice)
+ [R 用の mrsdeploy パッケージ](https://docs.microsoft.com/machine-learning-server/r-reference/mrsdeploy/mrsdeploy-package)


## <a name="see-also"></a>関連項目

+ [rxSerializeModel](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)  
+ [rxRealTimeScoring](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxrealtimescoring)
+ [sp-rxPredict](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rxpredict-transact-sql)
+ [T-SQL の予測](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql)