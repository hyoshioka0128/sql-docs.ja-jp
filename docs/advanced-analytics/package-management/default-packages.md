---
title: R と Python のパッケージ情報の取得
description: SQL Server R Services または Machine Learning Services で、R と Python のパッケージバージョンを確認し、インストールを確認し、インストールされているパッケージの一覧を取得します。
ms.custom: ''
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: ff4d0839cfdf24b1b43fe9d5a371092713bc63cf
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715793"
---
#  <a name="get-r-and-python-package-information"></a>R と Python のパッケージ情報の取得
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

場合によっては、複数の環境または R または Python のインストールを使用しているときに、実行しているコードが Python に期待される環境を使用しているか、R の適切なワークスペースを使用していることを確認する必要があります。たとえば、 [r または Python をアップグレード](../install/upgrade-r-and-python.md)した場合、r ライブラリへのパスは、既定とは異なるフォルダーに存在する可能性があります。 また、R Client またはスタンドアロンサーバーのインスタンスをインストールする場合は、コンピューターに複数の R ライブラリがある可能性があります。

この記事の R および Python スクリプトの例では、SQL Server によって使用されるパッケージのパスとバージョンを取得する方法について説明します。

## <a name="get-the-r-library-location"></a>R ライブラリの場所を取得する

任意のバージョンの SQL Server について、次のステートメントを実行して、現在のインスタンスの既定の R パッケージライブラリを確認します。

```sql
EXECUTE sp_execute_external_script  
  @language = N'R',
  @script = N'OutputDataSet <- data.frame(.libPaths());'
WITH RESULT SETS (([DefaultLibraryName] VARCHAR(MAX) NOT NULL));
GO
```

必要に応じて、SQL Server Machine Learning Services の新しいバージョンの RevoScaleR、または r [Services で r が RevoScaleR 9.0.1 以上にアップグレード](../install/upgrade-r-and-python.md)された[rxSqlLibPaths](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqllibpaths)を使用できます。 このストアドプロシージャは、SQL Server によって使用されるインスタンスライブラリとバージョンの RevoScaleR のパスを返します。

```sql
EXECUTE sp_execute_external_script
  @language =N'R',
  @script=N'
  sql_r_path <- rxSqlLibPaths("local")
  print(sql_r_path)
  version_info <-packageVersion("RevoScaleR")
  print(version_info)'
```

> [!NOTE]
> [RxSqlLibPaths](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqllibpaths)関数は、ローカルコンピューター上でのみ実行できます。 関数は、リモート接続のライブラリパスを返すことはできません。

**結果**

```text
STDOUT message(s) from external script: 
[1] "C:/Program Files/Microsoft SQL Server/MSSQL14.MSSQLSERVER1000/R_SERVICES/library"
[1] '9.3.0'
```

## <a name="get-the-python-library-location"></a>Python ライブラリの場所を取得する

**Python**の場合は、次のステートメントを実行して、現在のインスタンスの既定のライブラリを確認します。 この例では、Python `sys.path`変数に含まれるフォルダーの一覧を返します。 一覧には、現在のディレクトリと標準ライブラリパスが含まれています。

```sql
EXECUTE sp_execute_external_script
  @language =N'Python',
  @script=N'import sys; print("\n".join(sys.path))'
```

**結果**

```text
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\python35.zip
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\DLLs
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib\site-packages
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib\site-packages\Sphinx-1.5.4-py3.5.egg
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib\site-packages\win32
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib\site-packages\win32\lib
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib\site-packages\Pythonwin
C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\lib\site-packages\setuptools-27.2.0-py3.5.egg
```

変数の詳細と、その`sys.path`変数を使用してモジュールのインタープリターの検索パスを設定する方法の詳細については、 [Python のドキュメント](https://docs.python.org/2/tutorial/modules.html#the-module-search-path)を参照してください。

## <a name="list-all-packages"></a>すべてのパッケージを一覧表示する

現在インストールされているパッケージの完全な一覧を取得するには、いくつかの方法があります。 Sp_execute_external_script からパッケージ一覧コマンドを実行する利点の1つは、インスタンスライブラリにパッケージがインストールされていることが保証されることです。

### <a name="r"></a>R

次の例では、 `installed.packages()` [!INCLUDE[tsql](../../includes/tsql-md.md)]ストアドプロシージャで R 関数を使用して、現在のインスタンスの R_SERVICES ライブラリにインストールされているパッケージのマトリックスを取得します。 このスクリプトでは、説明ファイル内のパッケージ名とバージョンフィールドが返されます。名前だけが返されます。

```sql
EXECUTE sp_execute_external_script
  @language=N'R',
  @script = N'str(OutputDataSet);
  packagematrix <- installed.packages();
  Name <- packagematrix[,1];
  Version <- packagematrix[,3];
  OutputDataSet <- data.frame(Name, Version);',
  @input_data_1 = N''
WITH RESULT SETS ((PackageName nvarchar(250), PackageVersion nvarchar(max) ))
```

[R パッケージの説明] フィールドのオプションフィールドおよび既定フィールドの詳細につい[https://cran.r-project.org](https://cran.r-project.org/doc/manuals/R-exts.html#The-DESCRIPTION-file)ては、「」を参照してください。

### <a name="python"></a>Python

`pip`モジュールは既定でインストールされ、標準の Python でサポートされているパッケージに加えて、インストールされているパッケージを一覧表示するための多くの操作をサポートします。 もちろん、Python `pip`コマンドプロンプトからを実行できますが、から`sp_execute_external_script`一部の pip 関数を呼び出すこともできます。

```sql
EXECUTE sp_execute_external_script 
  @language = N'Python', 
  @script = N'
  import pip
  import pandas as pd
  installed_packages = pip.get_installed_distributions()
  installed_packages_list = sorted(["%s==%s" % (i.key, i.version)
     for i in installed_packages])
  df = pd.DataFrame(installed_packages_list)
  OutputDataSet = df'
WITH RESULT SETS (( PackageVersion nvarchar (150) ))
```

コマンドライン`pip`から実行する場合、他にも多くの便利な関数があります。には、 `pip freeze`インストールされているすべてのパッケージが含まれます。一方、によっ`pip`てインストールされたパッケージの一覧が表示されます。 `pip list`はに依存します。 また、を使用`pip freeze`して、依存関係ファイルを生成することもできます。

## <a name="find-a-single-package"></a>1つのパッケージを検索する

パッケージをインストールし、特定の SQL Server インスタンスで使用できるようにする必要がある場合は、次のストアドプロシージャ呼び出しを実行して、パッケージを読み込み、メッセージのみを返すことができます。

### <a name="r"></a>R

この例では、RevoScaleR ライブラリを検索して読み込みます (使用可能な場合)。

```sql
EXECUTE sp_execute_external_script  
  @language =N'R',
  @script=N'require("RevoScaleR")'
GO
```

+ パッケージが見つかった場合は、次のメッセージが返されます。"コマンドは正常に完了しました。"

+ パッケージが見つからないか、または読み込めない場合は、"MissingPackageName ' という名前のパッケージがありません" というテキストを含むエラーが表示されます。

### <a name="python"></a>Python

Python の同等のチェックは、コマンドまたは`conda` `pip`コマンドを使用して python シェルから実行できます。 または、ストアドプロシージャで次のステートメントを実行します。

```sql
EXECUTE sp_execute_external_script
  @language = N'Python',
  @script = N'
  import pip
  import pkg_resources
  pckg_name = "revoscalepy"
  pckgs = pandas.DataFrame([(i.key) for i in pip.get_installed_distributions()], columns = ["key"])
  installed_pckg = pckgs.query(''key == @pckg_name'')
  print("Package", pckg_name, "is", "not" if installed_pckg.empty else "", "installed")'
```

<a name="get-package-vers"></a>

## <a name="get-package-version"></a>パッケージのバージョンを取得する

Management Studio を使用して、R および Python パッケージのバージョン情報を取得できます。

### <a name="r-package-version"></a>R パッケージのバージョン

このステートメントは、RevoScaleR パッケージバージョンと base R バージョンを返します。

```sql
EXECUTE sp_execute_external_script
  @language = N'R',
  @script = N'
print(packageDescription("RevoScaleR"))
print(packageDescription("base"))
'
```

### <a name="python-package-version"></a>Python パッケージのバージョン

このステートメントは、revoscalepy パッケージバージョンと Python のバージョンを返します。

```sql
EXECUTE sp_execute_external_script
  @language = N'Python',
  @script = N'
import revoscalepy
import sys
print(revoscalepy.__version__)
print(sys.version)
'
```

## <a name="next-steps"></a>次のステップ

+ [新しい R パッケージのインストール](../r/install-additional-r-packages-on-sql-server.md)
+ [新しい Python パッケージのインストール](../python/install-additional-python-packages-on-sql-server.md)
+ [チュートリアル、サンプル、ソリューション](../tutorials/machine-learning-services-tutorials.md)