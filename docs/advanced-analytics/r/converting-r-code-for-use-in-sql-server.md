---
title: ストアドプロシージャの R コードの変換
description: ソリューションの配置とデータアクセスを SQL Server 上の SQL Server ストアドプロシージャに R コードを移行します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 536be600d319335173dbf112ec2d8f67cc7bf14b
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715747"
---
# <a name="convert-r-code-for-execution-in-sql-server-in-database-instances"></a>SQL Server (データベース内) のインスタンスで実行する R コードの変換
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server で動作するように R コードを変更する方法の概要について説明します。 

R コードを R Studio または別の環境から SQL Server に移動する場合、ほとんどの場合、コードは追加の変更なしで動作します。たとえば、入力を受け取り、値を返す関数など、コードが単純である場合などです。 また、 **RevoScaleR**または**microsoft ml**パッケージを使用する移植も簡単です。これにより、変更が最小限に抑えられたさまざまな実行コンテキストでの実行がサポートされます。

ただし、次のいずれかに該当する場合は、コードに大幅な変更が必要になることがあります。

+ ネットワークにアクセスするか、SQL Server にインストールできない R ライブラリを使用しています。
+ このコードでは、Excel ワークシート、共有上のファイル、その他のデータベースなど、SQL Server 外部のデータソースに対して個別の呼び出しを行います。 
+ *@script* [Sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)のパラメーターでコードを実行し、ストアドプロシージャもパラメーター化する必要があります。
+ 元のソリューションには、データの準備、特徴エンジニアリング、モデルのトレーニング、スコアリング、レポート作成など、独立して実行した場合に、運用環境でより効率的な複数の手順が含まれています。
+ ライブラリを変更したり、並列実行を使用したり、一部の処理を SQL Server にオフロードしたりすることにより、パフォーマンスを最適化できます。 

## <a name="step-1-plan-requirements-and-resources"></a>手順 1. 要件とリソースを計画する

**パッケージ**

+ 必要なパッケージを特定し、SQL Server で動作することを確認します。
 
+ Machine Learning Services によって使用される既定のパッケージライブラリに、事前にパッケージをインストールします。 ユーザーライブラリはサポートされていません。

**[データ ソース]** 

+ [Sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)に R コードを埋め込む場合は、プライマリとセカンダリのデータソースを特定します。 

    + **プライマリ**データソースは、モデルのトレーニングデータや予測の入力データなどの大規模なデータセットです。 最大のデータセットを[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)の入力パラメーターにマップすることを計画します。

    + 通常、**セカンダリ**データソースは、要因の一覧や追加のグループ化変数など、データセットのサイズが小さくなります。 
    
    現在、sp_execute_external_script では、ストアドプロシージャへの入力として1つのデータセットのみがサポートされています。 ただし、複数のスカラーまたはバイナリ入力を追加することはできます。

    EXECUTE で始まるストアドプロシージャの呼び出しは、 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)への入力として使用できません。 クエリ、ビュー、またはその他の有効な SELECT ステートメントを使用できます。

+ 必要な出力を決定します。 Sp_execute_external_script を使用して R コードを実行する場合、ストアドプロシージャは、結果として1つのデータフレームだけを出力できます。 ただし、バイナリ形式のプロットおよびモデルや、R コードまたは SQL パラメーターから派生したその他のスカラー値を含む、複数のスカラー出力を出力することもできます。

**データ型**

+ 起こり得るデータ型の問題のチェックリストを作成します。

    すべての R データ型は、SQL Server machine Learning サービスでサポートされています。 ただし、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]では、R よりも多くのデータ型がサポートされています。そのため、データを R に送信[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]するときに、暗黙的なデータ型変換が実行されます。その逆も同様です。 一部のデータを明示的にキャストまたは変換することが必要になる場合があります。 

    NULL 値がサポートされます。 ただし、R はデータ`na`構造を使用して欠損値を表します。これは null に似ています。

+ R で使用できないデータへの依存関係を排除することを検討してください。たとえば、SQL Server の rowid および GUID データ型を R で使用してエラーを生成することはできません。

    詳細については、「 [R ライブラリとデータ型](../r/r-libraries-and-data-types.md)」を参照してください。

## <a name="step-2-convert-or-repackage-code"></a>手順 2. コードの変換または再パッケージ化

コードの変更量は、リモートクライアントから R コードを送信して SQL Server の計算コンテキストで実行するか、ストアドプロシージャの一部としてコードを配置するかによって異なります。これにより、パフォーマンスとデータのセキュリティが向上します。 ストアドプロシージャでコードをラップすると、いくつかの追加要件があります。 

+ データの移動を回避するために、可能な限り、プライマリ入力データを SQL クエリとして定義します。

+ ストアドプロシージャで R を実行する場合は、複数の**スカラー**入力を使用して渡すことができます。 出力で使用するパラメーターについては、 **output**キーワードを追加します。 

    たとえば、次のスカラー入力`@model_name`には、モデル名が含まれています。これは、結果の独自の列にも出力されます。

    ```sql
    EXEC sp_execute_external_script @model_name="DefaultModel" OUTPUT, @language=N'R', @script=N'R code here'
    ``` 

+ ストアドプロシージャ[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)のパラメーターとして渡す変数は、R コード内の変数にマップする必要があります。 既定では、変数は、名前によってマップされます。

    入力データセット内のすべての列は、R スクリプトの変数にもマップする必要があります。  たとえば、R スクリプトに次のような数式が含まれているとします。

    ```R
    formula <- ArrDelay ~ CRSDepTime + DayOfWeek + CRSDepHour:DayOfWeek
    ```
    
    入力データセットに、ArrDelay、CRSDepTime、DayOfWeek、CRSDepHour、および DayOfWeek の名前が一致する列が含まれていない場合、エラーが発生します。

+ 場合によっては、結果に対して事前に出力スキーマを定義する必要があります。

    たとえば、データをテーブルに挿入するには、 **WITH RESULT SET**句を使用してスキーマを指定する必要があります。

    R スクリプトで引数`@parallel=1`を使用する場合は、出力スキーマも必要です。 理由は、クエリを並列で実行するために SQL Server が複数のプロセスを作成し、最後に結果を収集する可能性があるためです。 したがって、並列処理を作成するには、事前に出力スキーマを準備しておく必要があります。
    
    それ以外の場合は、結果**セットが定義**されていないオプションを使用して、結果スキーマを省略できます。 このステートメントは、列に名前を付けたり SQL データ型を指定したりせずに、R スクリプトからデータセットを返します。

+ R ではなく T-sql を使用して、タイミングまたは追跡データを生成することを検討してください。

    たとえば、R スクリプトに似たデータを生成するのではなく、結果に渡される T-sql 呼び出しを追加することによって、監査とストレージに使用されるシステム時刻やその他の情報を渡すことができます。 

**パフォーマンスとセキュリティを向上させる**

+ 予測または中間結果をファイルに書き込むことは避けてください。 データの移動を避けるために、予測をテーブルに書き込みます。

+ すべてのクエリを事前に実行し、SQL Server のクエリプランをレビューして、並列で実行できるタスクを特定します。

    入力クエリを並列化できる場合は、 `@parallel=1`引数の一部として[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)に設定します。 

    このフラグによる並列処理は、SQL Server がパーティション分割されたテーブルを操作できるか、クエリを複数のプロセスに分散して最後に結果を集計できるときは、通常はいつでも実行できます。 このフラグによる並列処理は、すべてのデータを読み取る必要があるアルゴリズムを使用するモデルをトレーニングする場合、または集計を作成する必要がある場合は、通常は実行できません。

+ R コードを見直して、別のストアド プロシージャの呼び出しを使用して単独で実行できる手順またはより効率的に実行できる手順があるかどうかを判断します。 たとえば、特徴エンジニアリングや特徴抽出を個別に行い、値をテーブルに保存することで、パフォーマンスを向上させることができます。

+ セットベースの計算に R コードではなく T-sql を使用する方法を探します。

    たとえば、次の R ソリューションでは、ユーザー定義の T-sql 関数と R が同じ特徴エンジニアリングタスクを実行する方法を示しています。[データサイエンスのエンドツーエンドチュートリアル](../tutorials/walkthrough-data-science-end-to-end-walkthrough.md)。

+ 可能であれば、従来の R 関数を、分散実行をサポートする**ScaleR**関数に置き換えます。 詳細については、「 [Base r 関数と Scale r 関数の比較](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-compared-to-base-r)」を参照してください。

+ [メモリ最適化テーブル](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)などの SQL Server の機能を使用してパフォーマンスを向上させる方法を決定するには、データベース開発者に相談してください。または、Enterprise Edition をお持ちの場合は[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor))。

    詳細については、「 [Analytics Services の SQL Server 最適化に関するヒントとテクニック](https://gallery.cortanaintelligence.com/Tutorial/SQL-Server-Optimization-Tips-and-Tricks-for-Analytics-Services)」を参照してください。

### <a name="step-3-prepare-for-deployment"></a>手順 3. 展開の準備

+ 管理者に通知して、コードを展開する前にパッケージをインストールして事前にテストできるようにしてください。 

    開発環境では、コードの一部としてパッケージをインストールすることができますが、これは運用環境では不適切な方法です。 

    ストアドプロシージャを使用しているか、SQL Server の計算コンテキストで R コードを実行しているかに関係なく、ユーザーライブラリはサポートされません。

**ストアドプロシージャでの R コードのパッケージ化**

+ コードが比較的単純な場合は、次のサンプルで説明されているように、変更せずに T-sql ユーザー定義関数に埋め込むことができます。

    + [RxExec で実行する R 関数を作成する](../tutorials/deepdive-create-a-simple-simulation.md)
    + [T-sql と R を使用した特徴エンジニアリング](../tutorials/sqldev-create-data-features-using-t-sql.md)

+ コードがより複雑な場合は、R パッケージ**sqlrutils**を使用してコードを変換します。 このパッケージは、経験豊富な R ユーザーが適切なストアドプロシージャコードを記述できるように設計されています。 

    最初の手順では、明示的に定義された入力と出力を使用して、R コードを1つの関数として書き直します。

    次に、 **sqlrutils**パッケージを使用して、正しい形式で入力と出力を生成します。 **Sqlrutils**パッケージは、ストアドプロシージャの完全なコードを生成し、データベースにストアドプロシージャを登録することもできます。 

    詳細と例については、「 [sqlrutils (SQL)](ref-r-sqlrutils.md)」を参照してください。

**他のワークフローとの統合**

+ T-sql ツールと ETL プロセスを活用します。 データワークフローの一部として、機能エンジニアリング、特徴抽出、データクレンジングを事前に実行します。

    [!INCLUDE[rsql_rtvs_md](../../includes/rsql-rtvs-md.md)]や rstudio などの専用の R 開発環境で作業している場合、データをコンピューターにプルし、データを繰り返し分析して、結果を書き出すか表示することができます。 
    
    ただし、スタンドアロンの R コードを SQL Server に移行すると、このプロセスの大部分を簡素化したり、他の SQL Server ツールに委任したりすることができます。 

+ セキュリティで保護された非同期の視覚化戦略を使用します。

    多くの場合、SQL Server のユーザーがサーバー上のファイルにアクセスすることはできません。また、SQL クライアントツールは通常、R グラフィックスデバイスをサポートしていません。 ソリューションの一部としてプロットまたはその他のグラフィックスを生成する場合は、プロットをバイナリデータとしてエクスポートし、テーブルまたは書き込みに保存することを検討してください。

+ アプリケーションによる直接アクセスを行うために、ストアドプロシージャで予測関数とスコアリング関数をラップします。

### <a name="other-resources"></a>その他のリソース

SQL Server で R ソリューションをデプロイする方法の例を確認するには、次のサンプルを参照してください。

+ [R と SQL Server を使用して、ski レンタル事業の予測モデルを構築する](https://microsoft.github.io/sql-ml-tutorials/R/rentalprediction/)

+ [SQL 開発者向けのデータベース内分析](../tutorials/sqldev-in-database-r-for-sql-developers.md)ストアドプロシージャにラップすることにより、R コードをよりモジュール化する方法を示します。

+ [エンドツーエンドのデータサイエンスソリューション](../tutorials/walkthrough-data-science-end-to-end-walkthrough.md)R と T-sql の特徴エンジニアリングの比較が含まれます。
