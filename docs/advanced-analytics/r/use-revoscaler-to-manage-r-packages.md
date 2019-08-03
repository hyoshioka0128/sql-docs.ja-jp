---
title: RevoScaleR functions を使用して R パッケージを検索またはインストールする方法
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 8db20295c2e21b6499d4d935f9c99161983b588f
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715580"
---
# <a name="how-to-use-revoscaler-functions-to-find-or-install-r-packages-on-sql-server"></a>RevoScaleR functions を使用して SQL Server で R パッケージを検索またはインストールする方法
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

RevoScaleR 9.0.1 以降には、R パッケージ管理のための関数が含まれています。 SQL Server コンピューティングコンテキストです。 これらの関数は、リモートの非管理者がサーバーに直接アクセスせずに SQL Server にパッケージをインストールするために使用できます。

SQL Server Machine Learning Services には、RevoScaleR の新しいバージョンが既に含まれています。 SQL Server 2016 R Services のお客様は、RevoScaleR package management functions を入手するために[コンポーネントのアップグレード](../install/upgrade-r-and-python.md)を行う必要があります。 パッケージのバージョンと内容を取得する方法については、「[パッケージ情報の取得](../package-management/installed-package-information.md)」を参照してください。

## <a name="revoscaler-functions-for-package-management"></a>パッケージ管理用の RevoScaleR 関数

次の表では、R パッケージのインストールと管理に使用される関数について説明します。

| 関数 | 説明 |
|----------|-------------|
| [rxSqlLibPaths](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqllibpaths) | リモート SQL Server のインスタンスライブラリのパスを確認します。 |
| [rxFindPackage](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxfindpackage) | リモート SQL Server 上の1つ以上のパッケージのパスを取得します。 |
| [rxInstallPackages](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinstallpackages) | リモート R クライアントからこの関数を呼び出して、指定されたリポジトリから、またはローカルに保存された zip パッケージを読み取ることによって、SQL Server のコンピューティングコンテキストにパッケージをインストールします。 この関数は、ローカルの計算コンテキストでの R パッケージのインストールと同様に、依存関係を確認し、関連するパッケージを SQL Server にインストールできることを確認します。 このオプションを使用するには、サーバーとデータベースでパッケージ管理が有効になっている必要があります。 クライアント環境とサーバー環境の両方で、RevoScaleR のバージョンが同じである必要があります。 |
| [rxInstalledPackages](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinstalledpackages) | 指定した計算コンテキストにインストールされているパッケージの一覧を取得します。 |
| [rxSyncPackages](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsyncpackages) | 指定された計算コンテキストについて、ファイルシステムとデータベース間のパッケージライブラリに関する情報をコピーします。 |
| [rxRemovePackages](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxremovepackages) | 指定された計算コンテキストからパッケージを削除します。 また、依存関係を計算し、SQL Server の他のパッケージによって使用されなくなったパッケージを削除して、リソースを解放します。 |

## <a name="prerequisites"></a>前提条件

+ [SQL Server でのリモート R パッケージ管理を有効にする](r-package-how-to-enable-or-disable.md)

+ RevoScaleR のバージョンは、クライアント環境とサーバー環境の両方で同じである必要があります。 詳細については、「[パッケージ情報の取得](../package-management/installed-package-information.md)」を参照してください。

+ サーバーとデータベースに接続し、R コマンドを実行する権限。 指定したインスタンスとデータベースにパッケージをインストールできるようにするには、データベースロールのメンバーである必要があります。

+ **共有スコープ**内のパッケージは、指定されたデータベース`rpkgs-shared`のロールに属しているユーザーがインストールできます。 このロールのすべてのユーザーが、共有パッケージをアンインストールできます。

+ **プライベートスコープ**内のパッケージは、データベース内の`rpkgs-private`ロールに属している任意のユーザーがインストールできます。 ただし、ユーザーは自分のパッケージのみを表示およびアンインストールできます。

+ データベース所有者は、共有またはプライベートパッケージを操作できます。

## <a name="client-connections"></a>クライアント接続

クライアントワークステーションは、同じネットワーク上で[Microsoft R Client](https://docs.microsoft.com/machine-learning-server/r-client/install-on-windows)または[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-install) (多くの場合、データ科学者エディションを使用します) にすることができます。

リモート R クライアントからパッケージ管理関数を呼び出すときは、まず、 [RxInSqlServer](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinsqlserver)関数を使用して計算コンテキストオブジェクトを作成する必要があります。 その後、使用する各パッケージ管理関数に対して、計算コンテキストを引数として渡します。

ユーザー id は、通常、コンピューティングコンテキストを設定するときに指定します。 コンピューティングコンテキストを作成するときにユーザー名とパスワードを指定しない場合は、R コードを実行しているユーザーの id が使用されます。

1. R コマンドラインで、インスタンスとデータベースへの接続文字列を定義します。
2. [RxInSqlServer](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinsqlserver)コンストラクターを使用して、接続文字列を使用して SQL Server の計算コンテキストを定義します。

    ```R
    sqlcc <- RxInSqlServer(connectionString = myConnString, shareDir = sqlShareDir, wait = sqlWait, consoleOutput = sqlConsoleOutput)
    ```
3. インストールするパッケージの一覧を作成し、その一覧を文字列変数に保存します。

    ```R
    packageList <- c("e1071", "mice")
    ```

4. [RxInstallPackages](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinstallpackages)を呼び出して、コンピューティングコンテキストと、パッケージ名を含む文字列変数を渡します。

    ```R
    rxInstallPackages(pkgs = packageList, verbose = TRUE, computeContext = sqlcc)
    ```

    依存パッケージが必要な場合は、クライアントでインターネット接続を利用できることを前提として、依存パッケージもインストールされます。
    
    パッケージは、接続しようとしているユーザーの資格情報を使用して、そのユーザーの既定のスコープでインストールされます。

## <a name="call-package-management-functions-in-stored-procedures"></a>ストアドプロシージャでのパッケージ管理関数の呼び出し

で`sp_execute_external_script`パッケージ管理関数を実行します。 この操作を行うと、ストアドプロシージャの呼び出し元のセキュリティコンテキストを使用して関数が実行されます。

## <a name="examples"></a>使用例

このセクションでは、計算コンテキストとして SQL Server インスタンスまたはデータベースに接続するときに、これらの関数をリモートクライアントから使用する方法の例を示します。

すべての例では、接続文字列を指定するか、接続文字列を必要とする計算コンテキストを指定する必要があります。 この例では、SQL Server のコンピューティングコンテキストを作成する方法の1つを示します。

```R
instance_name <- "computer-name/instance-name";
database_name <- "TestDB";
sqlWait= TRUE;
sqlConsoleOutput <- TRUE;
connString <- paste("Driver=SQL Server;Server=", instance_name, ";Database=", database_name, ";Trusted_Connection=true;", sep="");
sqlcc <- RxInSqlServer(connectionString = connString, wait = sqlWait, consoleOutput = sqlConsoleOutput, numTasks = 4);
```

サーバーが配置されている場所とセキュリティモデルによっては、接続文字列にドメインとサブネットの指定を指定したり、SQL ログインを使用したりすることが必要になる場合があります。 例 :

```R
connStr <- "Driver=SQL Server;Server=myserver.financeweb.contoso.com;Database=Finance;Uid=RUser1;Pwd=RUserPassword"
```

### <a name="get-package-path-on-a-remote-sql-server-compute-context"></a>リモート SQL Server の計算コンテキストでパッケージパスを取得する

この例では、 `sqlcc`コンピューティングコンテキストの**RevoScaleR**パッケージのパスを取得します。

```R
sqlPackagePaths <- rxFindPackage(package = "RevoScaleR", computeContext = sqlcc)
print(sqlPackagePaths)
```

**結果**

"C:/Program Files/Microsoft SQL Server/MSSQL14.。MSSQLSERVER/R_SERVICES/library/RevoScaleR "

> [!TIP]
> SQL コンソールの出力を表示するオプションを有効にした場合、ステートメントの`print`前にある関数からのステータスメッセージが表示されることがあります。 コードのテストが完了したら、compute `consoleOutput`コンテキストコンストラクターでを FALSE に設定して、メッセージを排除します。

### <a name="get-locations-for-multiple-packages"></a>複数のパッケージの場所を取得する

次の例では、 `sqlcc`計算コンテキストで**RevoScaleR**パッケージと**lattice**パッケージのパスを取得します。 複数のパッケージに関する情報を取得するには、パッケージ名を含む文字列ベクターを渡します。

```R
packagePaths <- rxFindPackage(package = c("RevoScaleR", "lattice"), computeContext = sqlcc)
print(packagePaths)
```

### <a name="get-package-versions-on-a-remote-compute-context"></a>リモートコンピューティングコンテキストでパッケージのバージョンを取得する

このコマンドを R コンソールから実行して、コンピューティングコンテキスト*sqlServer*にインストールされているパッケージのビルド番号とバージョン番号を取得します。

```R
sqlPackages <- rxInstalledPackages(fields = c("Package", "Version", "Built"), computeContext = sqlServer)
```

**結果**

```text
[1] "C:/Program Files/Microsoft SQL Server/MSSQL14.MSSQLSERVER/R_SERVICES/library/RevoScaleR"

[2] "C:/Program Files/Microsoft SQL Server/MSSQL14.MSSQLSERVER/R_SERVICES/library/lattice"
```

### <a name="install-a-package-on-sql-server"></a>SQL Server にパッケージをインストールする

この例では、**予測**パッケージとその依存関係を計算コンテキストにインストールします。

  ```R
  pkgs <- c("forecast")
  rxInstallPackages(pkgs = pkgs, verbose = TRUE, scope = "private", computeContext = sqlcc)
  ```

### <a name="remove-a-package-from-sql-server"></a>パッケージを SQL Server から削除する

この例では、**予測**パッケージとその依存関係を計算コンテキストから削除します。

  ```R
  pkgs <- c("forecast")
  rxRemovePackages(pkgs = pkgs, verbose = TRUE, scope = "private", computeContext = sqlcc)
  ```

### <a name="synchronize-packages-between-database-and-file-system"></a>データベースとファイルシステムの間でパッケージを同期する

次の例では、データベース**TestDB**をチェックし、すべてのパッケージがファイルシステムにインストールされているかどうかを確認します。 一部のパッケージが不足している場合は、ファイルシステムにインストールされます。

```R
# Instantiate the compute context
connectionString <- "Driver=SQL Server;Server=myServer;Database=TestDB;Trusted_Connection=True;"
computeContext <- RxInSqlServer(connectionString = connectionString )

# Synchronize the packages in the file system for all scopes and users
rxSyncPackages(computeContext=computeContext, verbose=TRUE)
```

パッケージの同期は、データベースごと、およびユーザーごとに実行されます。 詳細については、「 [SQL Server の R パッケージの同期](../r/package-install-uninstall-and-sync.md)」を参照してください。

### <a name="use-a-stored-procedure-to-list-packages-in-sql-server"></a>ストアドプロシージャを使用して SQL Server にパッケージを一覧表示する

ストアドプロシージャでを使用し`rxInstalledPackages`て、現在のインスタンスにインストールされているパッケージの一覧を取得するには、Management Studio または t-sql をサポートする別のツールからこのコマンドを実行します。

```sql
EXEC sp_execute_external_script 
  @language=N'R', 
  @script=N'
    myPackages <- rxInstalledPackages();
    OutputDataSet <- as.data.frame(myPackages);
    '
```

`rxSqlLibPaths`関数を使用すると、SQL Server Machine Learning Services によって使用されるアクティブなライブラリを特定できます。 このスクリプトは、現在のサーバーのライブラリパスのみを返すことができます。 

```sql
declare @instance_name nvarchar(100) = @@SERVERNAME, @database_name nvarchar(128) = db_name();
exec sp_execute_external_script 
  @language = N'R',
  @script = N'
    connStr <- paste("Driver=SQL Server;Server=", instance_name, ";Database=", database_name, ";Trusted_Connection=true;", sep="");
    .libPaths(rxSqlLibPaths(connStr));
    print(.libPaths());
  ', 
  @input_data_1 = N'', 
  @params = N'@instance_name nvarchar(100), @database_name nvarchar(128)',
  @instance_name = @instance_name, 
  @database_name = @database_name;
```

## <a name="see-also"></a>関連項目

+ [リモートの R パッケージ管理を有効にする](r-package-how-to-enable-or-disable.md)
+ [R パッケージの同期](package-install-uninstall-and-sync.md)
+ [R パッケージのインストールに関するヒント](packages-installed-in-user-libraries.md)
+ [既定のパッケージ](../package-management/default-packages.md)