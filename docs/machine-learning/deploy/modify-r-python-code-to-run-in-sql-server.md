---
title: R または Python コードを変更して SQL Server で実行する
description: SQL データにアクセスする際のパフォーマンスを向上させるために、SQL Server ストアド プロシージャとして実行するように R または Python コードを変更する方法について説明します。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 04/05/2021
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019, contperf-fy21q3
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: ed7baa3040be8d0ed72e7ec9d02184b147e69f17
ms.sourcegitcommit: 09122d02fc3d86c6028366653337c083da8a3f4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107072417"
---
# <a name="modify-rpython-code-to-run-in-sql-server-in-database-instances"></a>R または Python コードを変更して SQL Server (データベース内) インスタンスで実行する
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

この記事では、SQL データにアクセスする際のパフォーマンスを向上するために、SQL Server ストアド プロシージャとして実行するように R または Python コードを変更する方法の概要について説明します。

R または Python コードをローカル IDE またはその他の環境から SQL Server に移動した場合、通常、コードは、それ以上変更することなく機能します。 これは、何らかの入力を受け取り、値を返す関数などの単純なコードに特に当てはまります。 また、最小限の変更でさまざまな実行コンテキストでの実行をサポートする **RevoScaleR**/**revoscalepy** または **MicrosoftML** パッケージを使用するソリューションの移植はさらに容易です。

ただし、次のいずれかに該当する場合は、コードに大幅な変更が必要になることがあります。

+ ネットワークにアクセスするライブラリ、または SQL Server にインストールできないライブラリを使用する。
+ コードが、Excel ワークシート、共有のファイル、その他のデータベースなど、SQL Server 外部のデータソースに対して個別の呼び出しを行う。
+ ストアド プロシージャをパラメーター化し、コードを [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) の *\@script* パラメーターで実行する必要がある。
+ 元のソリューションに、データ準備や特徴エンジニアリング、またはモデル トレーニング、スコアリング、レポート作成など、単独で実行した方が運用環境で効率的な場合がある複数の手順が含まれている。
+ ライブラリを変更したり、並列実行を使用したり、一部の処理を SQL Server にオフロードしたりして、パフォーマンスを最適化したい。

## <a name="step-1-plan-requirements-and-resources"></a>手順 1. 要件とリソースを計画する

### <a name="packages"></a>パッケージ

+ 必要なパッケージを特定し、SQL Server で動作することを確認します。

+ Machine Learning Services によって使用される既定のパッケージ ライブラリに、事前にパッケージをインストールします。 ユーザー ライブラリはサポートされていません。

### <a name="data-sources"></a>データ ソース

+ コードを [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) に埋め込む場合は、プライマリとセカンダリのデータ ソースを特定します。

  + **プライマリ** データソースは、モデル トレーニング データや予測用の入力データなどの大規模なデータセットです。 最も大きなデータセットを、[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) の入力パラメーターにマップするよう計画します。

  + **セカンダリ** データソースは、通常、要因の一覧や追加のグループ化変数など、サイズの小さいデータセットです。
  
  現在、sp_execute_external_script では、ストアド プロシージャへの入力として 1 つのデータセットのみがサポートされています。 ただし、複数のスカラーまたはバイナリの入力を追加できます。

  EXECUTE で始まるストアド プロシージャの呼び出しは、[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) への入力として使用することはできません。 クエリ、ビュー、またはその他の有効な SELECT ステートメントを使用できます。

+ 必要な出力を決定します。 sp_execute_external_script を使用してコードを実行した場合、ストアド プロシージャは、1 つのデータ フレームだけを結果として出力できます。 ただし、バイナリ形式のプロットおよびモデルや、コードまたは SQL パラメーターから派生したその他のスカラー値など、複数のスカラー出力を出力することもできます。

### <a name="data-types"></a>データ型

R または Python と SQL Server の間のデータ型マッピングの詳細については、次の記事を参照してください。
+ [R と SQL Server の間のデータ型マッピング](../r/r-libraries-and-data-types.md)
+ [Python と SQL Server の間のデータ型マッピング](../python/python-libraries-and-data-types.md)

R または Python コードで使用されているデータ型を確認し、次の手順を実行します。

+ 起こり得るデータ型の問題のチェックリストを作成します。

  SQL Server 機械学習サービスでは、すべての R または Python データ型がサポートされています。 ただし、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、R または Python よりも多種多様なデータ型をサポートしています。 このため、コードとの間で [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データを移動するときにいくつかの暗黙的なデータ型変換が実行されます。 一部のデータは、明示的なキャストまたは変換が必要な場合もあります。

  NULL 値がサポートされます。 ただし、R では、`na` データ コンストラクトを使用して、null 値に似た欠落値を表します。

+ R で使用できないデータに対する依存関係を排除することを検討してください。たとえば、SQL Server の ROWID および GUID データ型は R で使用できないため、エラーが発生します。

## <a name="step-2-convert-or-repackage-code"></a>手順 2. コードを変換または再パッケージ化する

コードをどの程度変更するかは、リモート クライアントからコードを送信して SQL Server の計算コンテキストで実行するか、またはコードをストアド プロシージャの一部として展開するかによって異なります。 後者の場合、パフォーマンスが向上し、データのセキュリティが強化されますが、いくつかの追加要件があります。

+ データ移動を回避するために、可能な限り、プライマリ入力データを SQL クエリとして定義します。

+ ストアド プロシージャでコードを実行する場合、複数の **スカラー** 入力をパススルーできます。 出力で使用するパラメーターについては、**OUTPUT** キーワードを追加します。

  たとえば、次のスカラー入力 `@model_name` には、モデル名が含まれています。これは、結果の独自の列にも出力されます。

  ```sql
  EXECUTE sp_execute_external_script @model_name = "DefaultModel" OUTPUT
    ,@language = N'R'
    ,@script = N'R code here'
  ```

+ ストアド プロシージャ [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) のパラメーターとして渡される変数は、コード内の変数にマップする必要があります。 既定では、変数は、名前によってマップされます。 入力データセット内のすべての列も、スクリプト内の変数にマップする必要があります。
  
  たとえば、R スクリプトに次のような数式が含まれているとします。

  ```R
  formula <- ArrDelay ~ CRSDepTime + DayOfWeek + CRSDepHour:DayOfWeek
  ```
  
  ArrDelay、CRSDepTime、DayOfWeek、CRSDepHour、および DayOfWeek と一致する名前の列が入力データセットに含まれていない場合は、エラーが発生します。

+ 場合によっては、結果に対して事前に出力スキーマを定義する必要があります。

  たとえば、データをテーブルに挿入するには、**WITH RESULT SET** 句を使用してスキーマを指定する必要があります。

  スクリプトで引数 `@parallel=1` を使用する場合は、出力スキーマも必要です。 理由は、クエリを並列で実行するために SQL Server が複数のプロセスを作成し、最後に結果を収集する可能性があるためです。 したがって、並列処理を作成する前に、出力スキーマを準備する必要があります。
  
  それ以外の場合は、オプション **WITH RESULT SETS UNDEFINED** を使用して結果スキーマを省略できます。 このステートメントは、列に名前を付けたり、SQL データ型を指定したりせずに、スクリプトからデータセットを返します。

+ R または Python ではなく T-SQL を使用して、タイミング データまたは追跡データを生成することを検討してください。

  たとえば、スクリプトで同様のデータを生成するのではなく、結果にパススルーされる T-SQL 呼び出しを追加することによって、監査とストレージに使用されるシステム時刻やその他の情報を渡すことができます。

### <a name="improve-performance-and-security"></a>パフォーマンスとセキュリティを向上させる

::: moniker range=">=sql-server-2016||>=sql-server-linux-ver15"
+ 予測または中間結果をファイルに書き込むことは避けてください。 データ移動を回避するために、予測はテーブルに書き込みます。
::: moniker-end

+ すべてのクエリを事前に実行し、SQL Server クエリ プランを確認して、並列で実行できるタスクを識別します。

  入力クエリを並列化できる場合は、[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) に渡す引数の一部として `@parallel=1` を設定します。

  このフラグによる並列処理は、SQL Server がパーティション分割されたテーブルを操作できるか、クエリを複数のプロセスに分散して最後に結果を集計できるときは、通常はいつでも実行できます。 すべてのデータを読み取る必要があるアルゴリズムを使用してモデルをトレーニングする場合、または集計を作成する必要がある場合、通常、このフラグによる並列処理を行うことはできません。

+ コードを見直し、別のストアド プロシージャの呼び出しを使用して、単独で実行できるステップ、またはより効率的に実行できるステップがあるかどうかを判断します。 たとえば、特徴エンジニアリングや特徴抽出を別々に実行し、値をテーブルに保存することによって、パフォーマンスを向上できる場合があります。

+ セットベースの計算では、R または Python コードではなく T-SQL を使用する方法を探します。

  ::: moniker range=">=sql-server-2016||>=sql-server-linux-ver15"
  たとえば、次の R ソリューションは、ユーザー定義の T-SQL 関数と R が同じ特徴エンジニアリング タスクをどのように実行できるかを示しています。[データ サイエンスのエンドツーエンド チュートリアル](../tutorials/walkthrough-data-science-end-to-end-walkthrough.md)。
  ::: moniker-end

+ データベース開発者と相談して、[メモリ最適化テーブル](../../relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables.md)などの SQL Server 機能、または Enterprise Edition を使用している場合は [Resource Governor](../../relational-databases/resource-governor/resource-governor.md) を使用してパフォーマンスを向上させる方法を決定します。

+ R を使用する場合、可能であれば、従来の R 関数を、分散実行をサポートする **RevoScaleR** 関数に置き換えます。 詳細については、「[Base R 関数と RevoScaleR 関数の比較](/machine-learning-server/r-reference/revoscaler/revoscaler-compared-to-base-r)」を参照してください。

## <a name="step-3-prepare-for-deployment"></a>手順 3. 展開を準備する

+ 管理者に通知して、コードを展開する前にパッケージをインストールして事前にテストできるようにしてください。

  開発環境では、パッケージをコードの一部としてインストールしても問題はないかもしれませんが、これは運用環境では不適切な行為です。

  ストアド プロシージャを使用しているか、SQL Server の計算コンテキストで R または Python コードを実行しているかに関係なく、ユーザー ライブラリはサポートされません。

### <a name="package-your-rpython-code-in-a-stored-procedure"></a>ストアド プロシージャで R または Python コードをパッケージ化する

+ T-SQL ユーザー定義関数を作成し、[sp-execute-external-script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) ステートメントを使用してコードを埋め込みます。

+ 複雑な R コードを使用する場合、R パッケージ **sqlrutils** を使用してコードを変換します。 このパッケージは、経験豊富な R ユーザーが適切なストアド プロシージャ コードを記述できるように設計されています。
  R コードを、明確に定義された入力と出力を持つ単一の関数として書き換えた後、**sqlrutils** パッケージを使用して、入力と出力を正しい形式で生成します。 **sqlrutils** パッケージは、ストアド プロシージャの完全なコードを自動的に生成します。また、データベースにストアド プロシージャを登録することもできます。

  詳細と例については、[sqlrutils (SQL)](../r/ref-r-sqlrutils.md) に関する記事を参照してください。

### <a name="integrate-with-other-workflows"></a>他のワークフローと統合する

+ T-SQL ツールと ETL プロセスを活用します。 データ ワークフローの一部として、特徴エンジニアリング、特徴抽出、およびデータ クレンジングを事前に実行します。

  専用の開発環境で作業している場合は、データをコンピューターにプルし、データを繰り返し分析した後、結果を書き出したり表示したりすることができます。
  ただし、スタンドアロン コードを SQL Server に移行する場合、このプロセスの大半は、簡素化するか、他の SQL Server ツールに委任できます。

+ セキュリティで保護された非同期の視覚化方法を使用します。

  多くの場合、SQL Server のユーザーはサーバー上のファイルにアクセスすることはできず、通常、SQL クライアント ツールでは、R または Python グラフィックス デバイスがサポートされていません。 ソリューションの一部としてプロットまたはその他のグラフィックスを生成する場合は、プロットをバイナリ データとしてエクスポートし、テーブルに保存するか、書き込みを行うことを検討してください。

+ アプリケーションによる直接アクセスのために、予測およびスコアリング関数をストアド プロシージャにラップします。

## <a name="next-steps"></a>次のステップ

R および Python ソリューションを SQL Server に展開する方法の例を確認するには、次のチュートリアルを参照してください。

### <a name="r-tutorials"></a>R のチュートリアル

+ [SQL 機械学習を使用して R で予測モデルを開発する](../tutorials/r-predictive-model-introduction.md)

+ [二項分類を使用して NYC タクシーの料金を予測する](../tutorials/r-taxi-classification-introduction.md)

::: moniker range=">=sql-server-2016||>=sql-server-linux-ver15"
+ [R データ サイエンティスト向けの SQL 開発](../tutorials/walkthrough-data-science-end-to-end-walkthrough.md)
::: moniker-end

### <a name="python-tutorials"></a>Python のチュートリアル

+ [SQL 機械学習での線形回帰を使用したスキー レンタルの予測](../tutorials/python-ski-rental-linear-regression.md)

+ [二項分類を使用して NYC タクシーの料金を予測する](../tutorials/python-taxi-classification-introduction.md)
