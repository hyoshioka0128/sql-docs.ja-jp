---
title: Python と revoscalepy を使用してモデルを作成する
description: SQL Server でリモートで実行されるデータサイエンスモデルを作成するには、revoscalepy functions を使用して Python スクリプトを記述します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/25/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 596e5f6b5145dc258c781ca7b88a69fc962d7021
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714710"
---
# <a name="use-python-with-revoscalepy-to-create-a-model-that-runs-remotely-on-sql-server"></a>Python と revoscalepy を使用して、SQL Server でリモートで実行されるモデルを作成します。
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

Microsoft の[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) Python ライブラリには、データの探索、視覚化、変換、分析のためのデータサイエンスアルゴリズムが用意されています。 このライブラリは、SQL Server の Python 統合シナリオにおいて、戦略的な重要度を持ちます。 マルチコアサーバーでは、 **revoscalepy**関数を並列で実行できます。 中央サーバーとクライアントワークステーションを使用した分散アーキテクチャ (すべて同じ**revoscalepy**ライブラリを持つ) では、ローカルで起動する Python コードを作成できますが、実行はリモートの SQL Server インスタンスに移ります。データが存在する場所。

**Revoscalepy**は、次の Microsoft 製品およびディストリビューションに含まれています。

+ [SQL Server Machine Learning Services (データベース内)](../install/sql-machine-learning-services-windows-install.md)
+ [Microsoft Machine Learning Server (非 SQL、スタンドアロンサーバー)](https://docs.microsoft.com/machine-learning-server/index)
+ [クライアント側の Python ライブラリ (開発ワークステーション用)](https://docs.microsoft.com/machine-learning-server/install/python-libraries-interpreter) 

この演習では、 [rx_lin_mod](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-lin-mod)に基づく線形回帰モデルを作成する方法を示します。これは、計算コンテキストを入力として受け入れる**revoscalepy**のアルゴリズムの1つです。 この演習で実行するコードでは、リモートの計算コンテキストを有効にする**revoscalepy**関数によって有効になっているローカルとリモートのコンピューティング環境からコードの実行をシフトします。

このチュートリアルを完了すると、次のことを行うことができます。

> [!div class="checklist"]
> * **Revoscalepy**を使用して線形モデルを作成する
> * ローカルとリモートの計算コンテキストから操作をシフトする

## <a name="prerequisites"></a>前提条件

この演習で使用されるサンプルデータは、[**フライトデータ**](demo-data-airlinedemo-in-sql.md)データベースです。

この記事のサンプルコードを実行するには IDE が必要です。また、IDE は Python 実行可能ファイルにリンクされている必要があります。

コンピューティングコンテキストシフトを実践するには、[ローカルワークステーション](../python/setup-python-client-tools-sql.md)と、 [Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)と Python が有効になっている SQL Server データベースエンジンインスタンスが必要です。 

> [!Tip]
> 2台のコンピューターがない場合は、関連するアプリケーションをインストールすることによって、1台の物理コンピューター上でリモートコンピューティングコンテキストをシミュレートできます。 まず、 [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)のインストールは "リモート" インスタンスとして動作します。 次に、 [Python クライアントライブラリ](../python/setup-python-client-tools-sql.md)のインストールがクライアントとして動作します。 同じマシン上に同じ Python ディストリビューションと Microsoft Python ライブラリのコピーを2つ作成します。 この演習を正常に完了するには、ファイルパスと、使用している Python .exe のコピーを追跡する必要があります。

## <a name="remote-compute-contexts-and-revoscalepy"></a>リモート計算コンテキストと revoscalepy

このサンプルでは、リモートコンピューティングコンテキストで Python モデルを作成するプロセスを示します。これにより、クライアントから作業を行うことができますが、SQL Server、Spark、Machine Learning Server など、操作が実際に実行されるリモート環境を選択できます。 リモート計算コンテキストの目的は、データが存在する場所に計算を取り込むことです。

SQL Server で Python コードを実行するには、 **revoscalepy**パッケージが必要です。 これは、R 言語用の**RevoScaleR**パッケージと同様に、Microsoft によって提供される特殊な Python パッケージです。 **Revoscalepy**パッケージは、コンピューティングコンテキストの作成をサポートし、ローカルワークステーションとリモートサーバーの間でデータとモデルを渡すためのインフラストラクチャを提供します。 データベース内のコード実行をサポートする**revoscalepy**関数は、 [RxInSqlServer](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rxinsqlserver)です。

このレッスンでは、SQL Server のデータを使用して、非常に大規模なデータセットに対する回帰をサポートする**revoscalepy**の関数である[rx_lin_mod](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-lin-mod)に基づく線形モデルをトレーニングします。 

このレッスンでは、Python で**SQL Server 計算コンテキスト**を設定して使用する方法の基本についても説明します。 コンピューティングコンテキストが他のプラットフォームとどのように機能し、どの計算コンテキストがサポートされるかについては、「 [Machine Learning Server でのスクリプト実行のコンピューティングコンテキスト](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-compute-context)」を参照してください。


## <a name="run-the-sample-code"></a>サンプルコードを実行する

データベースを準備し、トレーニング用のデータをテーブルに格納したら、Python 開発環境を開き、コードサンプルを実行します。

このコードでは、次の手順を実行します。

1. 必要なライブラリと関数をインポートします。
2. SQL Server への接続を作成します。 データを操作するための**データソース**オブジェクトを作成します。
3. ロジスティック回帰アルゴリズムで使用できるように、**変換**を使用してデータを変更します。
4. を`rx_lin_mod`呼び出し、モデルに適合する数式を定義します。
5. 元のデータに基づいて一連の予測を生成します。
6. 予測値に基づいて概要を作成します。

すべての操作は、計算コンテキストとして SQL Server のインスタンスを使用して実行されます。

> [!NOTE]
> このサンプルをコマンドラインから実行する方法については、次のビデオを参照してください。[Python を使用した高度な分析の SQL Server 2017](https://www.youtube.com/watch?v=FcoY795jTcc)

### <a name="sample-code"></a>サンプル コード

```python
from revoscalepy import RxComputeContext, RxInSqlServer, RxSqlServerData
from revoscalepy import rx_lin_mod, rx_predict, rx_summary
from revoscalepy import RxOptions, rx_import

import os

def test_linmod_sql():
    sql_server = os.getenv('PYTEST_SQL_SERVER', '.')
    
    sql_connection_string = 'Driver=SQL Server;Server=' + sqlServer + ';Database=sqlpy;Trusted_Connection=True;'
    print("connectionString={0!s}".format(sql_connection_string))

    data_source = RxSqlServerData(
        sql_query = "select top 10 * from airlinedemosmall",
        connection_string = sql_connection_string,

        column_info = {
            "ArrDelay" : { "type" : "integer" },
            "DayOfWeek" : {
                "type" : "factor",
                "levels" : [ "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday" ]
            }
        })

    sql_compute_context = RxInSqlServer(
        connection_string = sql_connection_string,
        num_tasks = 4,
        auto_cleanup = False
        )

    #
    # Run linmod locally
    #
    linmod_local = rx_lin_mod("ArrDelay ~ DayOfWeek", data = data_source)
    #
    # Run linmod remotely
    #
    linmod = rx_lin_mod("ArrDelay ~ DayOfWeek", data = data_source, compute_context = sql_compute_context)

    # Predict results
    # 
    predict = rx_predict(linmod, data = rx_import(input_data = data_source))
    summary = rx_summary("ArrDelay ~ DayOfWeek", data = data_source, compute_context = sql_compute_context)
```

### <a name="defining-a-data-source-vs-defining-a-compute-context"></a>データソースの定義と計算コンテキストの定義

データソースは、計算コンテキストとは異なります。 *データソース*は、コードで使用されるデータを定義します。 コンピューティングコンテキストは、コードが実行される場所を定義します。 ただし、同じ情報の一部を使用します。

+ `sql_query` や`sql_connection_string`などの Python 変数は、データのソースを定義します。 

    これらの変数を[RxSqlServerData](https://docs.microsoft.com/r-server/python-reference/revoscalepy/rxsqlserverdata)コンストラクターに渡して、という名前`data_source`の**データソースオブジェクト**を実装します。

+ [RxInSqlServer](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rxinsqlserver)コンストラクターを使用して、**計算コンテキストオブジェクト**を作成します。 結果として得られる`sql_cc`**計算コンテキストオブジェクト**の名前はです。

    この例では、データソースで使用したのと同じ接続文字列を再使用します。これは、計算コンテキストとして使用するのと同じ SQL Server インスタンスにデータがあることを前提としています。 
    
    ただし、データソースと計算コンテキストは、異なるサーバー上に存在する可能性があります。
 
### <a name="changing-compute-contexts"></a>コンピューティングコンテキストの変更

コンピューティングコンテキストを定義したら、**アクティブなコンピューティングコンテキスト**を設定する必要があります。 

既定では、ほとんどの操作はローカルで実行されます。つまり、別の計算コンテキストを指定しない場合、データはデータソースからフェッチされ、コードは現在の Python 環境で実行されます。

アクティブなコンピューティングコンテキストを設定するには、次の2つの方法があります。
+ メソッドまたは関数の引数として
+ を呼び出すことによって`rx_set_computecontext`

#### <a name="set-compute-context-as-an-argument-of-a-method-or-function"></a>コンピューティングコンテキストをメソッドまたは関数の引数として設定する

この例では、個々の**rx**関数の引数を使用して、計算コンテキストを設定します。
    
`linmod = rx_lin_mod_ex("ArrDelay ~ DayOfWeek", data = data, compute_context = sql_compute_context)`

このコンピューティングコンテキストは、 [rxsummary](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-summary)の呼び出しで再利用されます。

`summary = rx_summary("ArrDelay ~ DayOfWeek", data = data_source, compute_context = sql_compute_context)`

#### <a name="set-a-compute-context-explicitly-using-rxsetcomputecontext"></a>Rx_set_compute_context を使用して計算コンテキストを明示的に設定する

関数[rx_set_compute_context](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-set-compute-context)を使用すると、既に定義されている計算コンテキストを切り替えることができます。

アクティブな計算コンテキストを設定した後は、変更するまでアクティブな状態のままになります。

### <a name="using-parallel-processing-and-streaming"></a>並列処理とストリーミングの使用

コンピューティングコンテキストを定義するときに、コンピューティングコンテキストによるデータの処理方法を制御するパラメーターを設定することもできます。 これらのパラメーターは、データソースの種類によって異なります。

SQL Server の計算コンテキストでは、バッチサイズを設定したり、実行中のタスクで使用する並列処理の次数に関するヒントを指定したりすることができます。

+ このサンプルは、4つのプロセッサを搭載したコンピューター `num_tasks`で実行されていたため、リソースの最大使用を可能にするために、パラメーターは4に設定されています。 
+ この値を0に設定した場合、SQL Server は既定のを使用します。これは、サーバーの現在の MAXDOP 設定の下で、できるだけ多くのタスクを並列実行します。 ただし、割り当てられる可能性のあるタスクの正確な数は、サーバーの設定や実行されている他のジョブなど、他の多くの要因によって異なります。

## <a name="next-steps"></a>次のステップ

これらの追加の Python サンプルとチュートリアルでは、より複雑なデータソースを使用したエンドツーエンドのシナリオに加えて、リモートの計算コンテキストを使用します。

+ [SQL 開発者向けのデータベース内 Python](sqldev-in-database-python-for-sql-developers.md)
+ [Python と SQL Server を使用した予測モデルの作成](https://microsoft.github.io/sql-ml-tutorials/python/rentalprediction/)
