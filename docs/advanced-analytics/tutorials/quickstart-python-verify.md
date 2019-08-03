---
title: SQL Server に Python が存在することを確認するためのクイックスタート
description: Python と Machine Learning Services が SQL Server に存在することを確認するためのクイックスタートです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 98e89cf61e5c53793108a455873382da00a8ea35
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715456"
---
# <a name="quickstart-verify-python-exists-in-sql-server"></a>クイック スタート: SQL Server に Python が存在することを確認する 
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server には、常駐 SQL Server データに対するデータサイエンス分析のための Python 言語サポートが含まれています。 スクリプトの実行は、次のいずれかの方法を使用して、ストアドプロシージャを使用します。

+ 組み込みの[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)ストアドプロシージャ。入力パラメーターとして Python スクリプトを渡します。
+ 作成した[カスタムストアドプロシージャ](sqldev-in-database-r-for-sql-developers.md)に Python スクリプトをラップします。

このクイックスタートでは、 [SQL Server Machine Learning Services](../what-is-sql-server-machine-learning.md)がインストールおよび構成されていることを確認します。

## <a name="prerequisites"></a>必須コンポーネント

この演習では、 [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)がインストールされている SQL Server のインスタンスにアクセスする必要があります。

SQL Server インスタンスは、Azure 仮想マシンまたはオンプレミスに配置できます。 外部スクリプト機能が既定で無効になっているため、[外部スクリプトを有効](../install/sql-machine-learning-services-windows-install.md#bkmk_enableFeature)にし、開始する前に**SQL Server Launchpad サービス**が実行されていることを確認する必要がある場合があることに注意してください。

また、SQL クエリを実行するためのツールも必要です。 SQL Server インスタンスに接続し、T-sql クエリまたはストアドプロシージャを実行できる限り、任意のデータベース管理ツールまたはクエリツールを使用して Python スクリプトを実行できます。 このクイックスタートでは、 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)を使用します。

## <a name="verify-python-exists"></a>Python の存在を確認する

Machine Learning Services (が SQL Server インスタンスで有効になっていること、およびインストールされている Python のバージョンを確認できます。 次の手順に従います。

1. SQL Server Management Studio を開き、SQL Server インスタンスに接続します。

2. 次のコードを実行します。 

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'Python',
    @script=N'import sys
    print(sys.version)';
    GO
    ```

3. Python `print`関数は、バージョンを **[メッセージ]** ウィンドウに返します。 次の出力例では、SQL Server に Python バージョン3.5.2 がインストールされていることがわかります。

    **結果**

    ```text
    STDOUT message(s) from external script: 
    3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
    ```

エラーが発生した場合は、インスタンスと Python が通信できるように、さまざまなことが可能です。

まず、インストールに関する問題をすべて除外します。 外部コードライブラリを使用できるようにするには、インストール後の構成が必要です。 「 [Install SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)」を参照してください。 同様に、スタートパッドサービスが実行されていることを確認します。

また、スタートパッドが Python と SQL Server `SQLRUserGroup`間の通信を確実に提供できるように、インスタンスでログインとして Windows ユーザーグループを追加する必要があります。 (R と Python の両方のコードの実行に同じグループが使用されます)。詳細については、「 [Create a login For SQLRUserGroup](../security/create-a-login-for-sqlrusergroup.md)」を参照してください。

また、無効になっているネットワークプロトコルを有効にしたり、SQL Server が外部クライアントと通信できるようにファイアウォールを開いたりすることが必要になる場合があります。 詳細については、「[セットアップのトラブルシューティング](../common-issues-external-script-execution.md)」を参照してください。

## <a name="call-revoscalepy-functions"></a>Revoscalepy 関数の呼び出し

**Revoscalepy**が使用可能であることを確認するには、統計概要データを生成する[rx_summary](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-summary)を含むサンプルスクリプトを実行します。 次のスクリプトは、revoscalepy に含まれている組み込みサンプルからサンプルの xdf データファイルを取得する方法を示しています。 RxOptions 関数は、サンプルファイルの場所を返す**sampleDataDir**パラメーターを提供します。

Rx_summary は、複数の要素を`class revoscalepy.functions.RxSummary.RxSummaryResults`含む型のオブジェクトを返すため、パンダを使用して、データフレームだけを表形式で抽出することができます。

```sql
EXEC sp_execute_external_script @language = N'Python', 
@script = N'
import os
from pandas import DataFrame
from revoscalepy import rx_summary
from revoscalepy import RxXdfData
from revoscalepy import RxOptions

sample_data_path = RxOptions.get_option("sampleDataDir")
print(sample_data_path)

ds = RxXdfData(os.path.join(sample_data_path, "AirlineDemoSmall.xdf"))
summary = rx_summary("ArrDelay + DayOfWeek", ds)
print(summary)
dfsummary = summary.summary_data_frame
OutputDataSet = dfsummary
'
WITH RESULT SETS  ((ColName nvarchar(25) , ColMean float, ColStdDev  float, ColMin  float,   ColMax  float, Col_ValidObs  float, Col_MissingObs int))
```

## <a name="list-python-packages"></a>Python パッケージの一覧表示

Microsoft では、SQL Server インスタンスに Machine Learning Services と共にプレインストールされた多数の Python パッケージを提供しています。 インストールされている Python パッケージの一覧 (バージョンなど) を表示するには、次の手順に従います。

1. SQL Server インスタンスで次のスクリプトを実行します。

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'Python',
    @script=N'import pip
    for i in pip.get_installed_distributions():
        print(i)';
    GO
    ```

2. 出力は Python の`pip.get_installed_distributions()`からのものであり`STDOUT` 、メッセージとして返されます。

    **結果**

    ```text
    STDOUT message(s) from external script: 
    xlwt 1.2.0
    XlsxWriter 0.9.6
    xlrd 1.0.0
    win-unicode-console 0.5
    widgetsnbextension 2.0.0
    wheel 0.29.0
    Werkzeug 0.12.1
    wcwidth 0.1.7
    unicodecsv 0.14.1
    traitlets 4.3.2
    tornado 4.4.2
    toolz 0.8.2
    testpath 0.3
    tables 3.2.2
    sympy 1.0
    statsmodels 0.8.0
    sqlparse 0.1.19
    SQLAlchemy 1.1.9
    snowballstemmer 1.2.1
    six 1.10.0
    simplegeneric 0.8.1
    seaborn 0.7.1
    scipy 0.19.0
    scikit-learn 0.18.1
    ...
    ```

## <a name="next-steps"></a>次のステップ

これで、インスタンスが Python で使用する準備ができたことを確認できたので、基本的な Python の相互作用について詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [クイック スタート:SQL Server の "Hello world" Python スクリプト](quickstart-python-run-using-t-sql.md)
