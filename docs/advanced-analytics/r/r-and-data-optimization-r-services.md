---
title: データ最適化のパフォーマンスチューニング
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: a65afba9455fb475b760439e92ad8d4d38a70be8
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715656"
---
# <a name="performance-for-r-services---data-optimization"></a>R Services のパフォーマンス-データの最適化
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、2つのケーススタディに基づく R Services のパフォーマンスの最適化について説明している3番目の記事です。 この記事では、SQL Server で実行される R または Python スクリプトのパフォーマンスの最適化について説明します。 また、R コードの更新に使用できるメソッドについても説明します。これにより、パフォーマンスを向上させ、既知の問題を回避することができます。

## <a name="choosing-a-compute-context"></a>コンピューティングコンテキストの選択

SQL Server 2016 および2017では、R または Python スクリプトを実行するときに、**ローカル**または**SQL**のいずれかの計算コンテキストを使用できます。

**ローカル**の計算コンテキストを使用する場合は、サーバー上ではなく、コンピューター上で分析が実行されます。 したがって、コードで使用するために SQL Server からデータを取得する場合は、ネットワーク経由でデータをフェッチする必要があります。 このネットワーク転送によるパフォーマンスの低下は、転送されるデータのサイズ、ネットワークの速度、および同時に発生しているその他のネットワーク転送によって異なります。

**SQL Server の計算コンテキスト**を使用する場合、コードはサーバー上で実行されます。 SQL Server からデータを取得する場合、データは分析を実行しているサーバーに対してローカルである必要があるため、ネットワークのオーバーヘッドは発生しません。 他のソースからデータをインポートする必要がある場合は、事前に ETL を配置することを検討してください。

大規模なデータ セットを操作する場合は、常に SQL 計算コンテキストを使用してください。

## <a name="factors"></a>因子

R 言語には、カテゴリデータに対する特殊な変数である*要素*の概念があります。 多くの場合、データ科学者は、カテゴリ変数を要因として処理することで、データが機械学習関数によって適切に処理されるようにするため、因子変数を式で使用します。 詳細については[、「R for ダミー」を参照してください。Factor 変数](https://www.dummies.com/programming/r/how-to-look-at-the-structure-of-a-factor-in-r/)。

仕様により、係数変数を文字列から整数に変換し、ストレージまたは処理のために再度返すことができます。 引数 stringsAsFactors `data.frame`が**False**に設定されていない限り、 R 関数はすべての文字列を factor 変数として処理します。 これは、文字列が処理のために自動的に整数に変換され、元の文字列にマップされることを意味します。

係数のソースデータが整数として格納されている場合、R は実行時に係数整数を文字列に変換してから、独自の内部文字列から整数への変換を実行するため、パフォーマンスが低下する可能性があります。

このような実行時の変換を回避するには、値を整数として SQL Server テーブルに格納し、 _Colinfo_引数を使用して、係数として使用する列のレベルを指定することを検討してください。 RevoScaleR のほとんどのデータソースオブジェクトは、パラメーター _Colinfo_を受け取ります。 このパラメーターを使用して、データソースによって使用される変数に名前を指定し、その型を指定して、列の値に対して変数のレベルまたは変換を定義します。

たとえば、次の R 関数の呼び出しでは、テーブルから整数1、2、および3が取得されますが、値は "apple"、"オレンジ"、および "バナナ" の各レベルの係数にマップされます。

```R
c("fruit" = c(type = "factor", levels=as.character(c(1:3)), newLevels=c("apple", "orange", "banana")))
```

ソース列に文字列が含まれている場合は、 _Colinfo_パラメーターを使用して事前にレベルを指定する方が効率的です。 たとえば、次の R コードは、文字列を読み取り中の要素として扱います。

```R
c("fruit" = c(type = "factor", levels= c("apple", "orange", "banana")))
```

モデルの生成にセマンティックの違いがない場合、後者のアプローチによってパフォーマンスが向上する可能性があります。

## <a name="data-transformations"></a>データ変換

多くの場合、データ サイエンティストは、分析の一部として R で記述された変換関数を使用します。 変換関数は、テーブルから取得した各行に適用されます。 SQL Server では、このような変換はバッチで取得されるすべての行に適用されます。これには、R インタープリターと分析エンジン間の通信が必要です。 変換を実行するため、データは SQL から分析エンジンに移動し、その後 R インタープリターで処理されて戻ってきます。

このため、R コードの一部として変換を使用すると、関連するデータ量によっては、アルゴリズムのパフォーマンスに大きな悪影響を及ぼす可能性があります。

分析を実行する前に、テーブルまたはビューに必要なすべての列を用意し、計算中に変換を回避する方が効率的です。 既存のテーブルに列を追加できない場合は、変換後の列を含む別のテーブルまたはビューを作成し、適切なクエリを使用してデータを取得することを検討してください。

## <a name="batch-row-reads"></a>バッチ行読み取り

コード内で SQL Server データソース (`RxSqlServerData`) を使用する場合は、 _rowsperread_パラメーターを使用してバッチサイズを指定することをお勧めします。 このパラメーターは、クエリを実行してから外部スクリプトに送信して処理する行の数を定義します。 実行時には、アルゴリズムによって、各バッチ内の指定された行数のみが認識されます。

一度に処理されるデータの量を制御できるため、問題の解決や回避に役立ちます。 たとえば、入力データセットの幅が非常に広い (多数の列がある) 場合や、データセットにいくつかの大きな列 (フリーテキストなど) が含まれている場合、バッチサイズを小さくして、メモリからのページングデータを回避できます。

既定では、このパラメーターの値は5万に設定されており、メモリが不足しているマシンでもパフォーマンスが向上します。 サーバーに十分なメモリがある場合は、この値を50万または100万に増やした方がパフォーマンスが向上する可能性があります (特に大きなテーブルの場合)。

バッチサイズを大きくする利点は、大きなデータセットや、複数のプロセスで実行できるタスクで明らかになります。 ただし、この値を大きくしても、常に最適な結果が得られるとは限りません。 最適な値を判断するには、データとアルゴリズムを試してみることをお勧めします。

## <a name="parallel-processing"></a>並列処理

**Rx**分析関数のパフォーマンスを向上させるために、SQL Server の機能を活用して、サーバーコンピューターで使用可能なコアを使用してタスクを並列に実行することができます。

SQL Server で R を使用して並列化を実現するには、次の2つの方法があります。

-   **並列\@を使用します。** `sp_execute_external_script` ストアド プロシージャを使用して R スクリプトを実行する場合は、`@parallel` パラメーターを `1` に設定します。 これは、R スクリプトが RevoScaleR functions を使用し**ない**場合に最適な方法です。これには、他の処理メカニズムがあります。 スクリプトで RevoScaleR 関数 (通常は "rx" で始まる) を使用している場合は、並列処理が自動的に実行さ`@parallel`れる`1`ため、を明示的にに設定する必要はありません。

    R スクリプトを並列化できる場合、および SQL クエリを並列化できる場合は、データベースエンジンによって複数の並列処理が作成されます。 作成できるプロセスの最大数は、インスタンスの [**並列処理の最大限度**(MAXDOP)] 設定と同じです。 その後、すべてのプロセスが同じスクリプトを実行しますが、データの一部のみを受け取ります。
    
    このため、モデルのトレーニング時など、すべてのデータを表示する必要があるスクリプトでは、この方法は役に立ちません。 ただし、バッチ予測などのタスクを並列で実行する場合は便利です。 で`sp_execute_external_script`の並列処理の使用の詳細については、「 [transact-sql での R コードの使用](../tutorials/rtsql-using-r-code-in-transact-sql-quickstart.md)」の「**高度なヒント: 並列処理**」セクションを参照してください。

-   **NumTasks = 1 を使用します。** SQL Server の計算コンテキストで**rx**関数を使用する場合は、 _numtasks_パラメーターの値を、作成するプロセスの数に設定します。 作成されるプロセスの数は**MAXDOP**を超えることはできません。ただし、実際に作成されるプロセスの数はデータベースエンジンによって決定され、要求した数よりも少なくなる場合があります。

    R スクリプトを並列化でき、SQL クエリを並列化できる場合、SQL Server は、rx 関数を実行するときに複数の並列プロセスを作成します。 実際に作成されるプロセスの数は、リソースガバナンス、リソースの現在の使用状況、その他のセッション、R スクリプトで使用されるクエリのクエリ実行プランなど、さまざまな要因によって異なります。

## <a name="query-parallelization"></a>クエリの並列化

Microsoft R では、RxSqlServerData データソースオブジェクトとしてデータを定義することで、SQL Server データソースを操作できます。

テーブルまたはビュー全体に基づいてデータソースを作成します。

```R
RxSqlServerData(table= "airline", connectionString = sqlConnString)
```

SQL クエリに基づいてデータソースを作成します。

```R
RxSqlServerData(sqlQuery= "SELECT [ArrDelay],[CRSDepTime],[DayOfWeek] FROM  airlineWithIndex WHERE rowNum <= 100000", connectionString = sqlConnString)
```

> [!NOTE]
> クエリではなく、データソースにテーブルが指定されている場合、R Services は内部ヒューリスティックを使用して、テーブルからフェッチするのに必要な列を決定します。ただし、この方法では、並列実行が発生する可能性はほとんどありません。

データを並列で分析できるようにするには、データの取得に使用されるクエリを、データベースエンジンが並列クエリプランを作成できるようにする必要があります。 コードまたはアルゴリズムで大量のデータを使用する場合は、に`RxSqlServerData`指定されたクエリが並列実行のために最適化されていることを確認してください。 並列実行プランにならないクエリは、計算の 1 つのプロセスになる可能性があります。

大規模なデータセットを処理する必要がある場合は、R コードを実行する前に Management Studio または別の SQL クエリアナライザーを使用して、実行プランを分析します。 次に、クエリのパフォーマンスを向上させるために、推奨される手順を実行します。 たとえば、テーブルのインデックス不足は、クエリの実行時間に影響する可能性があります。 詳細については、「[パフォーマンスの監視とチューニング](../../relational-databases/performance/monitor-and-tune-for-performance.md)」を参照してください。

パフォーマンスに影響する可能性があるもう1つの一般的な誤りは、クエリが必要以上に多くの列を取得することです。 たとえば、数式が3つの列にのみ基づいていても、ソーステーブルに30列含まれている場合は、データを不必要に移動します。

 + を使用`SELECT *`しないでください。
 + データセット内の列を確認し、分析に必要な列のみを特定します。
 + GUID や rowguids など、R コードと互換性のないデータ型を含む列をクエリから削除します。
 + サポートされていない日付と時刻の形式を確認する
 + 変換エラーを回避するために、テーブルを読み込むのではなく、特定の値を選択するビューまたは列をキャストするビューを作成します。

## <a name="optimizing-the-machine-learning-algorithm"></a>機械学習アルゴリズムの最適化

このセクションでは、Microsoft R の RevoScaleR およびその他のオプションに固有のその他のヒントとリソースについて説明します。

> [!TIP]
> R の最適化についての一般的な説明は、この記事の範囲外です。 ただし、コードの作成を高速化する必要がある場合は、「 [R inferno 』](https://www.burns-stat.com/pages/Tutor/R_inferno.pdf)」という人気の記事をお勧めします。 R のプログラミング構成要素と、鮮やかな言語と詳細におけるよくある落とし穴について説明し、R プログラミング手法のさまざまな例を示します。

### <a name="optimizations-for-revoscaler"></a>RevoScaleR の最適化

多くの RevoScaleR アルゴリズムは、トレーニング済みモデルの生成方法を制御するためのパラメーターをサポートしています。 モデルの精度と正確性は重要ですが、アルゴリズムのパフォーマンスも同様に重要な場合があります。 精度とトレーニング時間の間で適切なバランスを得るために、パラメーターを変更して計算速度を向上させることができます。多くの場合、精度や正確性を低下させずにパフォーマンスを向上させることができます。

+ [rxDTree](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxdtree)

    `rxDTree`では`maxDepth` 、デシジョンツリーの深さを制御するパラメーターがサポートされています。 増加`maxDepth`に応じてパフォーマンスが低下する可能性があるため、深さと低下のパフォーマンスを向上させる利点を分析することが重要です。

    `maxNumBins` 、`maxDepth`、、などのパラメーターを調整することで、時間の複雑さと予測精度のバランスを制御することもできます。`maxSurrogate` `maxComplete` 深さを 10 または 15 を超えた値に増やすと、計算が非常に不経済になる可能性があります。

+ [rxLinMod](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxlinmod)

    数式の最初`cube`の依存変数が factor 変数の場合は、引数を使用してください。
    
    が`cube` に`TRUE`設定されている場合、回帰はパーティション分割された逆を使用して実行されます。これは、標準の回帰計算よりも高速で使用されるメモリが少なくなる可能性があります。 数式に大量の変数がある場合は、パフォーマンスが大幅に向上する可能性があります。

+ [rxLogit](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxlogit)

    最初の`cube`依存変数が factor 変数の場合は、引数を使用します。
    
    が`cube` に`TRUE`設定されている場合、アルゴリズムではパーティション分割された逆が使用されますが、これは高速で、使用するメモリが少なくなる可能性があります。 数式に大量の変数がある場合は、パフォーマンスが大幅に向上する可能性があります。

RevoScaleR の最適化に関するその他のガイダンスについては、次の記事を参照してください。

+ サポート記事:[RxDForest と rxDTree のパフォーマンスチューニングオプション](https://support.microsoft.com/kb/3104235)

+ ブーストツリーモデルでモデルを制御するためのメソッドは次のとおりです。[ストキャスティクス勾配ブーストを使用したモデルの推定](https://docs.microsoft.com/r-server/r/how-to-revoscaler-boosting)

+ RevoScaleR がデータを移動して処理する方法の概要:[ScaleR でカスタムチャンキングアルゴリズムを作成する](https://docs.microsoft.com/r-server/r/how-to-developer-write-chunking-algorithms)

+ RevoScaleR のプログラミングモデル:[RevoScaleR でのスレッドの管理](https://docs.microsoft.com/r-server/r/how-to-developer-manage-threads)

+ [RxDForest](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxdforest)の関数リファレンス

+ [Rxbtrees](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxbtrees)の関数リファレンス

### <a name="use-microsoftml"></a>Microsoft Ml を使用する

また、RevoScaleR によって提供されるコンピューティングコンテキストと変換を使用できるスケーラブルな機械学習アルゴリズムを提供する、新しい**Microsoft ml**パッケージを参照することをお勧めします。

+ [Microsoft Ml を使ってみる](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)

+ [Microsoft Ml アルゴリズムを選択する方法](https://docs.microsoft.com/r-server/r/how-to-choose-microsoftml-algorithms-cheatsheet)

### <a name="operationalize-a-solution-using-microsoft-r-server"></a>Microsoft R Server を使用したソリューションの運用化

格納済みモデルを使用した迅速な予測、または機械学習をアプリケーションに統合するシナリオでは、Microsoft R Server (旧称 DeployR) の運用[化機能を](https://docs.microsoft.com/r-server/what-is-operationalization)使用できます。

+ **データ科学**者は、 [mrsdeploy パッケージ](https://docs.microsoft.com/r-server/r-reference/mrsdeploy/mrsdeploy-package)を使用して、r コードを他のコンピューターと共有したり、web、デスクトップ、モバイル、およびダッシュボードのアプリケーション内に r analytics を統合したりできます。[R Server で R web サービスを発行および管理する方法](https://docs.microsoft.com/r-server/operationalize/how-to-deploy-web-service-publish-manage-in-r)

+ **管理者**は、パッケージの管理、web ノードとコンピューティングノードの監視、R ジョブのセキュリティの制御を行う方法について説明します。[R で web サービスを操作して使用する方法](https://docs.microsoft.com/r-server/operationalize/how-to-consume-web-service-interact-in-r)

## <a name="articles-in-this-series"></a>このシリーズの記事

[R のパフォーマンスチューニング-概要](sql-server-r-services-performance-tuning.md)

[R SQL Server 構成のパフォーマンスチューニング](sql-server-configuration-r-services.md)

[R のパフォーマンスチューニング-R コードとデータの最適化](r-and-data-optimization-r-services.md)

[パフォーマンスチューニング-ケーススタディの結果](performance-case-study-r-services.md)
