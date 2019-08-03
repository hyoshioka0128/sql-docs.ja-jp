---
title: 新しい Python 言語パッケージをインストールする
description: 新しい Python パッケージを SQL Server Machine Learning Services (データベース内) と Machine Learning Server (スタンドアロン) に追加します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/16/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: e107622655d5f00d27de6abcea46a92526f47ada
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715801"
---
# <a name="install-new-python-packages-on-sql-server"></a>SQL Server に新しい Python パッケージをインストールする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server Machine Learning Services のインスタンスに新しい Python パッケージをインストールする方法について説明します。 一般に、新しいパッケージをインストールするプロセスは、標準の Python 環境の場合と似ています。 ただし、サーバーにインターネット接続がない場合は、いくつかの追加の手順が必要になります。

パッケージの場所とインストールパスの詳細について[は、「Get R Or Python package information](../package-management/installed-package-information.md)」を参照してください。

## <a name="prerequisites"></a>必須コンポーネント

+ Python 言語オプションを使用して[Machine Learning Services (データベース内) を SQL Server](../install/sql-machine-learning-services-windows-install.md)します。 

+ パッケージは Python 3.5 に準拠し、Windows 上で実行する必要があります。 

+ パッケージをインストールするには、サーバーへの管理アクセスが必要です。

## <a name="considerations"></a>考慮事項

パッケージを追加する前に、パッケージが SQL Server 環境に適しているかどうかを検討してください。 通常、データベースサーバーは、複数のワークロードに対応する共有資産です。 サーバーで計算の負荷が大きくなるパッケージを追加すると、パフォーマンスが低下します。 

また、いくつかの一般的な Python パッケージ (Flask など) は、スタンドアロン環境に適した web 開発などのタスクを実行します。 データベースをクエリするだけのタスクではなく、機械学習などのデータベースエンジンとの緊密な統合のメリットが得られるタスクには、データベース内の Python を使用することをお勧めします。

多くの場合、データベースサーバーはロックダウンされています。 多くの場合、インターネットアクセスは完全にブロックされます。 依存関係の一覧が長いパッケージの場合は、これらの依存関係を事前に特定し、それぞれを手動でインストールする必要があります。

## <a name="add-a-new-python-package"></a>新しい Python パッケージを追加する

この例では、新しいパッケージを SQL Server コンピューターに直接インストールすることを前提としています。

パッケージのインストールはインスタンス単位です。 Machine Learning Services のインスタンスが複数ある場合は、パッケージを各インスタンスに追加する必要があります。

この例でインストールされているパッケージは、さまざまな種類のニューラルネットワークのカスタマイズ、トレーニング、および共有をサポートする Microsoft のディープラーニングのフレームワークである[Cntk](https://docs.microsoft.com/cognitive-toolkit/)です。

### <a name="step-1-download-the-windows-version-of-the-python-package"></a>手順 1. Python パッケージの Windows バージョンをダウンロードする

+ インターネットにアクセスできないサーバーに Python パッケージをインストールする場合は、WHL ファイルを別のコンピューターにダウンロードしてから、サーバーにコピーする必要があります。

    たとえば、別のコンピューターで、このサイト[https://cntk.ai/PythonWheel/CPU-Only](https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl)から whl ファイルをダウンロードし、SQL Server コンピューターのローカルフォルダーにファイル`cntk-2.1-cp35-cp35m-win_amd64.whl` をコピーできます。

+ SQL Server 2017 は Python 3.5 を使用します。 

> [!IMPORTANT]
> パッケージの Windows バージョンを取得していることを確認します。 ファイルの末尾が gz の場合は、正しいバージョンではないことがあります。

このページには、複数のプラットフォームおよび複数の Python バージョンのダウンロードが含まれています。[CNTK の設定](https://docs.microsoft.com/cognitive-toolkit/Setup-CNTK-on-your-machine)

### <a name="step-2-open-a-python-command-prompt"></a>手順 2. Python コマンドプロンプトを開く

SQL Server によって使用される既定の Python ライブラリの場所を見つけます。 複数のインスタンスがインストールされている場合は、パッケージを追加するインスタンスの PYTHON_SERVICE フォルダーを探します。

たとえば、既定値を使用して Machine Learning Services がインストールされていて、Machine Learning が既定のインスタンスで有効になっている場合、パスは次のようになります。

    `C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES`

インスタンスに関連付けられている Python コマンドプロンプトを開きます。

> [!TIP]
> 将来のデバッグとテストでは、インスタンスライブラリに固有の Python 環境をセットアップすることができます。

### <a name="step-3-install-the-package-using-pip"></a>手順 3. Pip を使用してパッケージをインストールする

+ Python コマンドラインを使用することに慣れている場合は、PIP を使用して新しいパッケージをインストールします。 **Pip**インストーラーは、 `Scripts`サブフォルダーにあります。 

  SQL Server セットアップでは、システムパスにスクリプトは追加されません。 内部または外部のコマンド`pip`として認識されないエラーが発生した場合は、Windows の PATH 変数に Scripts フォルダーを追加できます。

  既定のインストールにおける**Scripts**フォルダーの完全なパスは次のとおりです。

    C:\Program Server\MSSQL14. SQLMSSQLSERVER\PYTHON_SERVICES\Scripts

+ Visual studio 2017 を使用している場合、または python 拡張機能を使用して visual `pip install` studio 2015 を使用している場合は、 **[python 環境]** ウィンドウからを実行できます。 **[パッケージ]** をクリックし、テキストボックスで、インストールするパッケージの名前または場所を指定します。 入力する必要はあり`pip install`ません。自動的に入力されます。 

    - コンピューターがインターネットにアクセスできる場合は、パッケージの名前、または特定のパッケージとバージョンの URL を指定します。 
    
    たとえば、Windows と Python 3.5 でサポートされているバージョンの CNTK をインストールするには、ダウンロード URL を指定します。`https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl`

    - コンピューターがインターネットにアクセスできない場合は、インストールを開始する前に、WHL ファイルをダウンロードする必要があります。 次に、ローカルファイルのパスと名前を指定します。 たとえば、次のパスとファイルを貼り付けて、サイトからダウンロードした WHL ファイルをインストールします。`"C:\Downloads\CNTK\cntk-2.1-cp35-cp35m-win_amd64.whl"`

インストールを完了するためにアクセス許可を昇格するように求めるメッセージが表示される場合があります。

インストールの進行状況に応じて、コマンドプロンプトウィンドウにステータスメッセージが表示されます。

```python
pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
Collecting cntk==2.1 from https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
  Downloading https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl (34.1MB)
    100% |################################| 34.1MB 13kB/s
...
Installing collected packages: cntk
Successfully installed cntk-2.1
```


### <a name="step-4-load-the-package-or-its-functions-as-part-of-your-script"></a>手順 4. スクリプトの一部としてパッケージまたはその機能を読み込む

インストールが完了したら、次の手順で説明するように、すぐにパッケージの使用を開始できます。

CNTK を使用したディープラーニングの例については、次のチュートリアルを参照してください。[CNTK 用 Python API](https://cntk.ai/pythondocs/tutorials.html)

スクリプトでパッケージの関数を使用するには、スクリプトの`import <package_name>`最初の行に標準のステートメントを挿入します。

```python
import numpy as np
import cntk as cntk
cntk._version_
```

## <a name="list-installed-packages-using-conda"></a>Conda を使用してインストールされているパッケージを一覧表示する

インストールされているパッケージの一覧を取得するには、さまざまな方法があります。 たとえば、Visual Studio の **[Python 環境]** ウィンドウで、インストールされているパッケージを表示できます。

Python コマンドラインを使用している場合は、SQL Server セットアップによって追加された Anaconda Python 環境に含まれている**Pip**または**conda** package manager を使用できます。

1. C:\Program Server\MSSQL14. SQL のページにアクセスします。MSSQLSERVER\PYTHON_SERVICES\Scripts

1. **[**  > **管理者として実行**] を右クリックし、 `conda list` 「」と入力して、現在の環境にインストールされているパッケージの一覧を返します。

1. 同様に、 **[管理者として実行]** を右クリック`pip list` > し、「」と入力して同じ情報を返します。 

**Conda**と、それを使用して複数の Python 環境を作成および管理する方法の詳細については、「 [conda を使用した環境の管理](https://conda.io/docs/user-guide/tasks/manage-environments.html)」を参照してください。

> [!Note]
> SQL Server セットアップでは、Pip または Conda がシステムパスと実稼働 SQL Server インスタンスに追加されないため、重要ではない実行可能ファイルをパスの外に保つことがベストプラクティスです。 ただし、開発環境とテスト環境では、スクリプトフォルダーを system PATH 環境変数に追加して、Pip と Conda on command の両方を任意の場所から実行できます。
