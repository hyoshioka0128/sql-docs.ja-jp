---
title: Python 開発用のデータサイエンスクライアントをセットアップする
description: Python で SQL Server Machine Learning Services にリモート接続するための Python ローカル環境 (Jupyter Notebook または PyCharm) を設定します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 6f40f04d677d5dcfa758a13321009da3e535c5d4
ms.sourcegitcommit: 632ff55084339f054d5934a81c63c77a93ede4ce
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69634540"
---
# <a name="set-up-a-data-science-client-for-python-development-on-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services での Python 開発用のデータサイエンスクライアントの設定
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

Python 統合は、 [Machine Learning Services (データベース内) インストール](../install/sql-machine-learning-services-windows-install.md)に python オプションを含めると、SQL Server 2017 以降で使用できます。 

SQL Server 用の Python ソリューションを開発してデプロイするには、Microsoft の[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)およびその他の python ライブラリを開発ワークステーションにインストールします。 Revoscalepy ライブラリは、リモート SQL Server インスタンス上にもあり、両方のシステム間でのコンピューティング要求を調整します。 

この記事では、機械学習と Python 統合で有効になっているリモート SQL Server と対話できるように、Python 開発ワークステーションを構成する方法について説明します。 この記事の手順を完了すると、SQL Server のものと同じ Python ライブラリが作成されます。 また、ローカル Python セッションから SQL Server のリモート Python セッションに計算をプッシュする方法についても説明します。

![クライアント/サーバーコンポーネント](media/sqlmls-python-client-revo.png "ローカルおよびリモートの Python セッションとライブラリ")

インストールを検証するには、この記事で説明されている組み込みの Jupyter Notebook を使用するか、または通常使用する PyCharm または他の IDE に[ライブラリをリンク](#install-ide)します。

> [!Tip]
> これらの演習のビデオデモについては、「 [Jupyter notebook からの SQL Server で R および Python をリモートで実行](https://youtu.be/D5erljpJDjE)する」を参照してください。

> [!Note]
> クライアントライブラリのインストールの代わりに、[スタンドアロンサーバー](../install/sql-machine-learning-standalone-windows-install.md)をリッチクライアントとして使用することもできます。これにより、より高度なシナリオの作業に適しています。 スタンドアロンサーバーは SQL Server から完全に切り離されていますが、Python ライブラリが同じであるため、データベース内分析 SQL Server のクライアントとして使用することができます。 また、他のデータプラットフォームからデータをインポートおよびモデル化する機能など、SQL に関連しない作業にも使用できます。 スタンドアロンサーバーをインストールする場合は、Python 実行可能ファイルを次`C:\Program Files\Microsoft SQL Server\140\PYTHON_SERVER`の場所で見つけることができます。 インストールを検証するには、 [Jupyter notebook を開い](#python-tools)て、その場所で Python .exe を使用してコマンドを実行します。

## <a name="commonly-used-tools"></a>一般的に使用されるツール

Python 開発者が SQL を初めて使用する場合でも、Python とデータベース内分析を初めて使用する SQL developer の場合でも、Python 開発ツールと、 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)などの t-sql クエリエディターの両方で、のすべての機能を実行する必要があります。データベース内分析。

Python の開発では、Jupyter Notebook を使用できます。これは SQL Server によってインストールされた Anaconda ディストリビューションにバンドルされています。 この記事では、Jupyter Notebook を起動して、SQL Server で Python コードをローカルおよびリモートで実行できるようにする方法について説明します。

SSMS は、Python コードが含まれているものを含め、SQL Server でストアドプロシージャを作成および実行する場合に便利な、個別のダウンロードです。 Jupyter Notebook で記述するほとんどすべての Python コードは、ストアドプロシージャに埋め込むことができます。 他のクイックスタートをステップ実行して[、SSMS と埋め込み Python](../tutorials/quickstart-python-verify.md)について学習することができます。

## <a name="1---install-python-packages"></a>1-Python パッケージをインストールする

ローカルワークステーションには、Python 3.5.2 distribution を使用した base Anaconda 4.2.0、Microsoft 固有のパッケージなど、SQL Server と同じバージョンの Python パッケージが必要です。

インストールスクリプトによって、3つの Microsoft 固有のライブラリが Python クライアントに追加されます。 このスクリプトは、データソースオブジェクトと計算コンテキストを定義するために使用される[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)をインストールします。 機械学習アルゴリズムを提供する[microsoft ml](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)がインストールされます。 また、 [azureml](https://docs.microsoft.com/machine-learning-server/python-reference/azureml-model-management-sdk/azureml-model-management-sdk)パッケージもインストールされますが、スタンドアロン (インスタンス以外) の Machine Learning Server コンテキストに関連付けられた運用化タスクに適用され、データベース内分析では使用が制限される場合があります。

1. インストールスクリプトをダウンロードします。

  + [https://aka.ms/mls-py](https://aka.ms/mls-py)Microsoft Python パッケージのバージョン9.2.1 をインストールします。 このバージョンは、既定の SQL Server インスタンスに対応します。 

  + [https://aka.ms/mls93-py](https://aka.ms/mls93-py)Microsoft Python パッケージのバージョン9.3 をインストールします。 このバージョンは、リモート SQL Server インスタンスが[Machine Learning Server 9.3 にバインド](../install/upgrade-r-and-python.md)されている場合に適しています。

2. 管理者特権を使用して PowerShell ウィンドウを開きます ( **[管理者として実行]** を右クリックします)。

3. インストーラーをダウンロードしたフォルダーにアクセスし、スクリプトを実行します。 `-InstallFolder`コマンドライン引数を追加して、ライブラリのフォルダーの場所を指定します。 例 : 

   ```python
   cd {{download-directory}}
   .\Install-PyForMLS.ps1 -InstallFolder "C:\path-to-python-for-mls"
   ```

インストールフォルダーを省略すると、既定値の C:\Program Files\Microsoft\PyForMLS. が使用されます。

インストールの完了には時間がかかります。 PowerShell ウィンドウで進行状況を監視できます。 セットアップが完了すると、パッケージの完全なセットが作成されます。 

> [!Tip] 
> Windows での Python プログラムの実行に関する一般的な情報については、 [python For WINDOWS FAQ](https://docs.python.org/3/faq/windows.html)をお勧めします。

## <a name="2---locate-executables"></a>2-実行可能ファイルの検索

PowerShell で、インストールフォルダーの内容を一覧表示して、Python .exe、スクリプト、およびその他のパッケージがインストールされていることを確認します。 

1. を`cd \`入力してルートドライブにアクセスし、前の手順`-InstallFolder`で指定したパスを入力します。 インストール中にこのパラメーターを省略した場合、 `cd C:\Program Files\Microsoft\PyForMLS`既定値はになります。

2. を`dir *.exe`入力すると、実行可能ファイルが一覧表示されます。 **Python .exe**、 **python w .exe**、および**uninstall-anaconda**が表示されます。

  ![Python 実行可能ファイルの一覧](media/powershell-python-exe.png)
   
Python の複数のバージョンがあるシステムでは、 **revoscalepy**やその他の Microsoft パッケージを読み込む場合は、この特定の python .exe を使用することを忘れないでください。

> [!Note] 
> インストールスクリプトでは、コンピューター上の PATH 環境変数は変更されません。つまり、インストールした新しい python インタープリターとモジュールは、他のツールで自動的に使用できるわけではありません。 Python インタープリターとライブラリをツールにリンクする方法については、「 [IDE をインストール](#install-ide)する」を参照してください。

<a name="python-tools"></a>

## <a name="3---open-jupyter-notebooks"></a>3-Jupyter Notebook を開く

Anaconda には Jupyter Notebook が含まれています。 次の手順として、notebook を作成し、先ほどインストールしたライブラリを含む Python コードを実行します。

1. Powershell プロンプトで、まだ C:\Program Files\Microsoft\PyForMLS ディレクトリにある [Scripts] フォルダーから Jupyter Notebook を開きます。

  ```powershell
  .\Scripts\jupyter-notebook
  ```

  ノートブックは、の既定のブラウザー `https://localhost:8889/tree`で開く必要があります。

  もう1つの方法として、 **jupyter-notebook**をダブルクリックする方法もあります。 

2. **[新規]** をクリックし、 **[Python 3]** をクリックします。

  ![新しい Python 3 を選択した jupyter notebook](media/jupyter-notebook-new-p3.png)

3. コマンド`import revoscalepy`を入力して実行し、Microsoft 固有のライブラリのいずれかを読み込みます。

4. 「」と`print(revoscalepy.__version__)`入力し、を実行してバージョン情報を返します。 9\.2.1 または9.3.0 が表示できます。 [サーバーの revoscalepy](../package-management/r-package-information.md)では、これらのバージョンのいずれかを使用できます。

4. より複雑な一連のステートメントを入力します。 この例では、ローカルデータセットに対して[rx_summary](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-summary)を使用して、概要統計情報を生成します。 その他の関数は、サンプルデータの場所を取得し、ローカルの xdf ファイルのデータソースオブジェクトを作成します。

  ```python
  import os
  from revoscalepy import rx_summary
  from revoscalepy import RxXdfData
  from revoscalepy import RxOptions
  sample_data_path = RxOptions.get_option("sampleDataDir")
  print(sample_data_path)
  ds = RxXdfData(os.path.join(sample_data_path, "AirlineDemoSmall.xdf"))
  summary = rx_summary("ArrDelay+DayOfWeek", ds)
  print(summary)
  ```

次のスクリーンショットは、簡潔にするためにトリミングされた入力と出力部分を示しています。

  ![revoscalepy の入力と出力を示す jupyter notebook](media/jupyter-notebook-local-revo.png)

## <a name="4---get-sql-permissions"></a>4-SQL のアクセス許可を取得する

スクリプトを実行してデータをアップロードするために SQL Server のインスタンスに接続するには、データベースサーバーで有効なログインが必要です。 SQL ログインまたは統合 Windows 認証を使用できます。 一般に、Windows 統合認証を使用することをお勧めしますが、スクリプトに外部データへの接続文字列が含まれている場合は特に、SQL ログインを使用する方が簡単です。

少なくとも、コードの実行に使用するアカウントには、使用しているデータベースから読み取るためのアクセス許可が必要です。さらに、特殊なアクセス許可では、外部スクリプトを実行します。 ほとんどの開発者は、ストアドプロシージャを作成し、トレーニングデータまたはスコア付けデータを含むテーブルにデータを書き込むためのアクセス許可も必要です。 

Python を使用するデータベースで、[アカウントに対する次のアクセス許可を構成](../security/user-permission.md)するように、データベース管理者に依頼してください。

+ サーバーで Python を実行するには **、任意の外部スクリプトを実行**します。
+ モデルのトレーニングに使用するクエリを実行するための特権を**db_datareader**します。
+ トレーニングデータまたはスコア付けされたデータを書き込む**db_datawriter** 。
+ **db_owner** 。ストアドプロシージャ、テーブル、関数などのオブジェクトを作成します。 
  また、サンプルデータベースとテストデータベースを作成するには、 **db_owner**が必要です。 

SQL Server に既定でインストールされていないパッケージがコードに必要な場合は、データベース管理者に配置して、インスタンスと共にパッケージをインストールしてください。 SQL Server はセキュリティで保護された環境であり、パッケージをインストールできる場所に制限があります。 権限を持っている場合でも、コードの一部としてのパッケージのアドホックインストールは推奨されません。 また、サーバーライブラリに新しいパッケージをインストールする前に、常にセキュリティへの影響を慎重に検討してください。


<a name="create-iris-remotely"></a>

## <a name="5---create-test-data"></a>5-テストデータを作成する

リモートサーバーにデータベースを作成する権限がある場合は、次のコードを実行して、この記事の残りの手順で使用する虹彩デモデータベースを作成できます。

### <a name="1---create-the-irissql-database-remotely"></a>1-irissql データベースをリモートで作成する

```python
import pyodbc

# creating a new db to load Iris sample in
new_db_name = "irissql"
connection_string = "Driver=SQL Server;Server=localhost;Database={0};Trusted_Connection=Yes;" 
                        # you can also swap Trusted_Connection for UID={your username};PWD={your password}
cnxn = pyodbc.connect(connection_string.format("master"), autocommit=True)
cnxn.cursor().execute("IF EXISTS(SELECT * FROM sys.databases WHERE [name] = '{0}') DROP DATABASE {0}".format(new_db_name))
cnxn.cursor().execute("CREATE DATABASE " + new_db_name)
cnxn.close()

print("Database created")
```

### <a name="2---import-iris-sample-from-sklearn"></a>2-SkLearn からの虹彩サンプルのインポート

```python
from sklearn import datasets
import pandas as pd

# SkLearn has the Iris sample dataset built in to the package
iris = datasets.load_iris()
df = pd.DataFrame(iris.data, columns=iris.feature_names)
```

### <a name="3---use-revoscalepy-apis-to-create-a-table-and-load-the-iris-data"></a>3-Revoscalepy Api を使用してテーブルを作成し、虹彩データを読み込む

```python
from revoscalepy import RxSqlServerData, rx_data_step

# Example of using RX APIs to load data into SQL table. You can also do this with pyodbc
table_ref = RxSqlServerData(connection_string=connection_string.format(new_db_name), table="iris_data")
rx_data_step(input_data = df, output_file = table_ref, overwrite = True)

print("New Table Created: Iris")
print("Sklearn Iris sample loaded into Iris table")
```

## <a name="6---test-remote-connection"></a>6-リモート接続をテストする

次の手順を実行する前に、SQL Server インスタンスに対するアクセス許可と、[虹彩サンプルデータベース](../tutorials/demo-data-iris-in-sql.md)への接続文字列を持っていることを確認してください。 データベースが存在せず、十分な権限を持っている場合は、[次のインライン命令を使用してデータベースを作成](#create-iris-remotely)できます。

接続文字列を有効な値に置き換えます。 サンプルコードでは`"Driver=SQL Server;Server=localhost;Database=irissql;Trusted_Connection=Yes;"`を使用しますが、コードでは、インスタンス名を使用してリモートサーバーを指定し、データベースユーザーログインにマップする資格情報オプションを指定する必要があります。

### <a name="define-a-function"></a>関数を定義する

次のコードでは、後の手順で SQL Server に送信する関数を定義しています。 実行すると、リモートサーバー上のデータとライブラリ (revoscalepy、pandas、matplotlib) を使用して、虹彩データセットの散布図が作成されます。 このファイルは、ブラウザーに表示するために、バイトストリームの .png を Jupyter Notebook に戻します。

```python
def send_this_func_to_sql():
    from revoscalepy import RxSqlServerData, rx_import
    from pandas.tools.plotting import scatter_matrix
    import matplotlib.pyplot as plt
    import io
    
    # remember the scope of the variables in this func are within our SQL Server Python Runtime
    connection_string = "Driver=SQL Server;Server=localhost;Database=irissql;Trusted_Connection=Yes;"
    
    # specify a query and load into pandas dataframe df
    sql_query = RxSqlServerData(connection_string=connection_string, sql_query = "select * from iris_data")
    df = rx_import(sql_query)
    
    scatter_matrix(df)
    
    # return bytestream of image created by scatter_matrix
    buf = io.BytesIO()
    plt.savefig(buf, format="png")
    buf.seek(0)
    
    return buf.getvalue()
```

### <a name="send-the-function-to-sql-server"></a>関数を SQL Server に送信します。

この例では、リモート計算コンテキストを作成し、関数の実行を[rx_exec](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-exec)を使用して SQL Server に送信します。 **Rx_exec**関数は、計算コンテキストを引数として受け入れるため便利です。 リモートで実行するすべての関数には、計算コンテキスト引数が必要です。 [Rx_lin_mod](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/rx-lin-mod)などの一部の関数では、この引数が直接サポートされます。 そうでない操作については、 **rx_exec**を使用して、リモートの計算コンテキストでコードを配信できます。

この例では、SQL Server から Jupyter Notebook に生データを転送する必要はありませんでした。 すべての計算は、虹彩データベース内で行われ、イメージファイルのみがクライアントに返されます。

```python
from IPython import display
import matplotlib.pyplot as plt 
from revoscalepy import RxInSqlServer, rx_exec

# create a remote compute context with connection to SQL Server
sql_compute_context = RxInSqlServer(connection_string=connection_string.format(new_db_name))

# use rx_exec to send the function execution to SQL Server
image = rx_exec(send_this_func_to_sql, compute_context=sql_compute_context)[0]

# only an image was returned to my jupyter client. All data remained secure and was manipulated in my db.
display.Image(data=image)
```

次のスクリーンショットは、入力と散布図の出力を示しています。

  ![散布図の出力を示す jupyter notebook](media/jupyter-notebook-scatterplot.png)


<a name="install-ide"></a>

## <a name="7---start-python-from-tools"></a>7-ツールから Python を起動する

開発者は複数のバージョンの Python を使用することが多いため、セットアップでは Python をパスに追加しません。 セットアップによってインストールされた Python 実行可能ファイルとライブラリを使用するには、 **revoscalepy**と**microsoft ml**も提供するパスで、IDE を**python .exe**にリンクします。 

### <a name="command-line"></a>[パッケージ実行ユーティリティ]

C:\Program Files\Microsoft\PyForMLS (または Python クライアントライブラリのインストール用に指定した任意の場所) から Python .exe を実行すると、完全な Anaconda 配布に加えて Microsoft Python モジュール、revoscalepy にアクセスできるようになり**ます。** および**microsoft ml**。

1. C:\Program Files\Microsoft\PyForMLS にアクセスし、 **[Python .exe]** をダブルクリックします。
2. 対話型ヘルプを開く:`help()`
3. ヘルププロンプト`help> revoscalepy`で、モジュールの名前を入力します。 ヘルプは、名前、パッケージの内容、バージョン、およびファイルの場所を返します。
4. **ヘルプ >** プロンプト`revoscalepy`で、バージョンおよびパッケージの情報を返します。 Enter キーを数回押してヘルプを終了します。
5. モジュールをインポートします。`import revoscalepy`


### <a name="jupyter-notebooks"></a>Jupyter Notebook

この記事では、組み込みの Jupyter Notebook を使用して、 **revoscalepy**の関数呼び出しを示します。 このツールを初めて使用する場合は、次のスクリーンショットは、各ピースがどのように組み合わされているか、およびすべてが "動作" する理由を示しています。 

親フォルダー C:\Program Files\Microsoft\PyForMLS には、Anaconda に加えて Microsoft パッケージが含まれています。 Jupyter Notebook は Anaconda の Scripts フォルダーの下に含まれており、Python 実行可能ファイルは Jupyter Notebook に自動的に登録されます。 サイトで見つかったパッケージは、データサイエンスと機械学習に使用される3つの Microsoft パッケージを含む notebook にインポートできます。

  ![実行可能ファイルとライブラリ](media/jupyter-notebook-python-registration.png)

別の IDE を使用している場合は、Python 実行可能ファイルと関数ライブラリをツールにリンクする必要があります。 以下のセクションでは、一般的に使用されるツールについて説明します。

### <a name="visual-studio"></a>Visual Studio

[Visual Studio に python](https://code.visualstudio.com/docs/languages/python)がある場合は、次の構成オプションを使用して、Microsoft python パッケージを含む python 環境を作成します。

| 構成設定 | value |
|-----------------------|-------|
| **プレフィックスパス** | C:\Program Files\Microsoft\PyForMLS |
| **インタープリターパス** | C:\Program Files\Microsoft\PyForMLS\python.exe |
| **ウィンドウインタープリター** | C:\Program Files\Microsoft\PyForMLS\pythonw.exe |

Python 環境の構成については、「 [Visual Studio での python 環境の管理](https://docs.microsoft.com/visualstudio/python/managing-python-environments-in-visual-studio)」を参照してください。

### <a name="pycharm"></a>PyCharm

PyCharm で、インタープリターを Machine Learning Server によってインストールされる Python 実行可能ファイルに設定します。

1. 新しいプロジェクトの 設定 で、**ローカルの追加** をクリックします。

2. 「 `C:\Program Files\Microsoft\PyForMLS\`」と入力します。

**Revoscalepy**、 **microsoft ml**、または**azureml**モジュールをインポートできるようになりました。 また、[**ツール** > ] **[Python コンソール]** を選択して、対話型ウィンドウを開くこともできます。

## <a name="next-steps"></a>次のステップ

これで、ツールと SQL Server への接続が完了したので、 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)を使用して Python のクイックスタートでを実行し、スキルを向上させることができます。

> [!div class="nextstepaction"]
> [クイック スタート:SQL Server に Python が存在することを確認する](../tutorials/quickstart-python-verify.md)