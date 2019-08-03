---
title: ストアドプロシージャを使用した R コードの運用化
description: SQL Server ストアドプロシージャに R 言語コードを埋め込み、SQL Server データベースにアクセスできる任意のクライアントアプリケーションで使用できるようにします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 03/15/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: adcac48bc7d90aae5f05a9b671f05e34cc8cf554
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715685"
---
# <a name="operationalize-r-code-using-stored-procedures-in-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services のストアドプロシージャを使用した R コードの運用化
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server Machine Learning Services の R および Python 機能を使用する場合、ソリューションを運用環境に移行するための最も一般的な方法は、ストアドプロシージャにコードを埋め込むことです。 この記事では、SQL Server を使用して R コードを運用する際に考慮する必要がある SQL 開発者向けの重要な点について説明します。

## <a name="deploy-production-ready-script-using-t-sql-and-stored-procedures"></a>T-sql とストアドプロシージャを使用した実稼働対応スクリプトのデプロイ

従来、データサイエンスソリューションの統合には、パフォーマンスと統合をサポートするための広範な記録が含まれていました。 R と Python のコードは SQL Server で実行でき、ストアドプロシージャを使用して呼び出すことができるため、SQL Server Machine Learning Services によってこのタスクが簡単になります。 ストアドプロシージャにコードを埋め込むしくみの詳細については、以下を参照してください。

+ [クイック スタート:SQL Server の "Hello world" R スクリプト](../../advanced-analytics/tutorials//quickstart-r-run-using-tsql.md)
+ [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)

ストアドプロシージャを使用して R コードを運用環境にデプロイする包括的な例に[ついては、チュートリアルを参照してください。SQL 開発者向け R Data Analytics](../../advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers.md)

## <a name="guidelines-for-optimizing-r-code-for-sql"></a>SQl の R コードを最適化するためのガイドライン

R または Python コードで事前にいくつかの最適化を実行すると、SQL での R コードの変換が簡単になります。 これには、問題の原因となるデータ型の回避、不要なデータ変換の回避、簡単にパラメーター化できる単一の関数呼び出しとしての R コードの書き直しなどが含まれます。 詳細については、以下をご覧ください。

+ [R ライブラリとデータ型](r-libraries-and-data-types.md)
+ [R Services で使用するための R コードの変換](converting-r-code-for-use-in-sql-server.md)
+ [Sqlrutils ヘルパー関数を使用する](ref-r-sqlrutils.md)

## <a name="integrate-r-and-python-with-applications"></a>R と Python をアプリケーションと統合する

ストアドプロシージャから R または Python を実行できるため、T-sql ステートメントを送信して結果を処理できるアプリケーションからスクリプトを実行できます。 たとえば、Integration Services で[t-sql の実行タスク](https://docs.microsoft.com/sql/integration-services/control-flow/execute-t-sql-statement-task)を使用して、またはストアドプロシージャを実行できる別のジョブスケジューラを使用して、スケジュールに従ってモデルを再トレーニングすることができます。

スコアリングは、外部アプリケーションから簡単に自動化または開始できる重要なタスクです。 R または Python またはストアドプロシージャを使用してモデルを事前にトレーニングし、[モデルをバイナリ形式で](../tutorials/walkthrough-build-and-save-the-model.md)テーブルに保存します。 次に、次のいずれかのオプションを使用して、ストアドプロシージャの呼び出しの一部としてモデルを変数に読み込むことができます。

+ [リアルタイムスコアリング、小規模なバッチ用に最適化
+ 単一行スコアリング、アプリケーションからの呼び出し
+ [ネイティブスコアリング](../sql-native-scoring.md)(R を呼び出さずに SQL Server からの高速バッチ予測用)

このチュートリアルでは、バッチと単一行の両方のモードでストアドプロシージャを使用したスコアリングの例を示します。

+ [SQL Server の R のエンドツーエンドのデータサイエンスチュートリアル](../tutorials/walkthrough-data-science-end-to-end-walkthrough.md)

アプリケーションでスコアリングを統合する方法の例については、次のソリューションテンプレートを参照してください。

+ [小売予測](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/RetailForecasting/Introduction.md)
+ [不正行為の検出](https://github.com/Microsoft/r-server-fraud-detection)
+ [顧客クラスタリング](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/r-services/getting-started/customer-clustering)

## <a name="boost-performance-and-scale"></a>パフォーマンスとスケールの向上

オープンソースの R 言語には大きなデータセットに関する既知の制限がありますが、SQL Server Machine Learning サービスに含まれる[RevoScaleR Package api](ref-r-revoscaler.md)は大規模なデータセットに対して動作し、マルチスレッド、マルチコア、複数プロセスのデータベース内計算。

R ソリューションで複雑な集計を使用している場合や、大規模なデータセットが含まれている場合は、SQL Server の非常に効率的なインメモリ集計と列ストアインデックスを活用し、R コードで統計的な計算とスコア付けを処理することができます。

SQL Server Machine Learning のパフォーマンスを向上させる方法の詳細については、次を参照してください。

+ [SQL Server R Services のパフォーマンスチューニング](../../advanced-analytics/r/sql-server-r-services-performance-tuning.md)
+ [パフォーマンスの最適化に関するヒントとテクニック](https://gallery.cortanaintelligence.com/Tutorial/SQL-Server-Optimization-Tips-and-Tricks-for-Analytics-Services)

## <a name="adapt-r-code-for-other-platforms-or-compute-contexts"></a>他のプラットフォームまたはコンピューティングコンテキストに対して R コードを調整する

データに対し[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]て実行するのと同じ R コードを、SQL Server セットアップで[スタンドアロンサーバーオプション](../install/sql-machine-learning-standalone-windows-install.md)を使用する場合、または SQL server 以外の製品をインストールする場合に、他のデータソース (Spark over HDFS など) に対して使用できます。 Microsoft Machine Learningサーバー (旧称**Microsoft R Server**):

+ [Machine Learning Server のドキュメント](https://docs.microsoft.com/r-server/)

+ [R から RevoScaleR への探索](https://docs.microsoft.com/r-server/r/tutorial-r-to-revoscaler)

+ [チャンクの作成アルゴリズム](https://docs.microsoft.com/r-server/r/how-to-developer-write-chunking-algorithms)

+ [R でのビッグデータを使用したコンピューティング](https://docs.microsoft.com/r-server/r/tutorial-large-data-tips)

+ [独自の並列アルゴリズムを開発する](https://docs.microsoft.com/r-server/r-reference/revopemar/pemar)

