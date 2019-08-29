---
title: コンテナーで SQL Server Machine Learning Services を実行する | Microsoft Docs
description: このチュートリアルでは、Docker で実行されている Linux コンテナーで SQL Server Machine Learning サービスを使用する方法について説明します。
author: uc-msft
ms.author: umajay
ms.date: 06/26/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.collection: linux-container
moniker: '>= sql-server-linux-ver15 || =sqlallproducts-allversions'
ms.openlocfilehash: 4fa470ce5b35cf8e78f919080988b5091af6e04e
ms.sourcegitcommit: cbbb210c0315f9e2be2b9cd68db888ac53429814
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69890908"
---
# <a name="run-sql-server-machine-learning-services-in-a-container"></a>コンテナーで SQL Server Machine Learning Services を実行する

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-linuxonly](../includes/appliesto-ss-xxxx-xxxx-xxx-md-linuxonly.md)]

このチュートリアルでは、SQL Server Machine Learning Services を使用して Docker コンテナーをビルドする方法、および Transact-SQL から Machine Learning スクリプトを実行する方法について説明します。 次のタスクが含まれます。

> [!div class="checklist"]
> * mssql-docker リポジトリをクローンする。
> * Machine Learning Services を使用して SQL Server Linux コンテナー イメージをビルドする。
> * Machine Learning Services を使用して SQL Server Linux コンテナー イメージを実行する。
> * Transact-SQL ステートメントを使用して R または Python スクリプトを実行する。
> * SQL Server Linux コンテナーを停止して削除する。

## <a name="prerequisites"></a>Prerequisites

* Git のコマンド ライン インターフェイス。
* サポートされているいずれかの Linux ディストリビューションの Docker エンジン 1.8 以降 または Mac/Windows 用 Docker。 詳細については、「[Install Docker](https://docs.docker.com/engine/installation/)」(Docker をインストールする) を参照してください。
* 2 ギガバイト (GB) 以上のディスク領域。
* 2 GB 以上の RAM。
* [SQL Server on Linux のシステム要件](sql-server-linux-setup.md#system)。

## <a name="clone-the-mssql-docker-repository"></a>mssql-docker リポジトリをクローンする

1. Linux または Mac で Bash ターミナルを開くか、または Windows で WSL ターミナルを開きます。

1. mssql-docker リポジトリのローカル コピーを保持するローカル ディレクトリを作成します。
1. git clone コマンドを実行して、mssql-docker リポジトリを複製します。

    ```bash
    git clone https://github.com/microsoft/mssql-docker mssql-docker
    ```

## <a name="build-a-sql-server-linux-container-image-with-machine-learning-services"></a>Machine Learning Services を使用して SQL Server Linux コンテナー イメージをビルドする

1. ディレクトリを mssql-mlservices ディレクトリに変更します。

    ```bash
    cd mssql-docker/linux/preview/examples/mssql-mlservices
    ```

1. build.sh スクリプトを実行します。

   ```bash
   ./build.sh
   ```

   > [!NOTE]
   > Docker イメージをビルドするには、サイズが数 GB のパッケージをいくつかインストールする必要があります。 このスクリプトは、ネットワークの帯域幅によって、完了までに最大 20 分かかります。

## <a name="run-the-sql-server-linux-container-image-with-machine-learning-services"></a>Machine Learning Services を使用して SQL Server Linux コンテナー イメージを実行する

1. コンテナーを実行する前に環境変数を設定します。 PATH_TO_MSSQL 環境変数をホスト ディレクトリに設定します。

   ```bash
    export MSSQL_PID='Developer'
    export ACCEPT_EULA='Y'
    export ACCEPT_EULA_ML='Y'
    export PATH_TO_MSSQL='/home/mssql/'
   ```

1. run.sh スクリプトを実行します。

   ```bash
   ./run.sh
   ```

   このコマンドでは、Developer エディション (既定) を使用して、Machine Learning Services を含む SQL Server コンテナーが作成されます。 SQL Server のポート **1433** は、ホスト上ではポート **1401** として公開されています。

   > [!NOTE]
   > SQL Server の実稼働エディションをコンテナーで実行するプロセスは、若干異なります。 詳細については、「[Docker で SQL Server コンテナーイメージを構成する](sql-server-linux-configure-docker.md)」を参照してください。 同じコンテナー名とポートを使う場合でも、このチュートリアルの残りの部分は実稼働のコンテナーで機能します。

1. Docker コンテナーを表示するには、`docker ps` コマンドを実行します。

   ```bash
   sudo docker ps -a
   ```

1. **[STATUS]** 列に表示されている状態が **[Up]** の場合、SQL Server はコンテナーで実行されており、 **[PORTS]** 列で指定されているポートでリッスンしています。 SQL Server コンテナーの **[STATUS]** 列に **[Exited]** と表示されている場合は、[構成ガイドのトラブルシューティングのセクション](sql-server-linux-configure-docker.md#troubleshooting)を参照してください。

   ```bash
   $ sudo docker ps -a
   ```

    出力結果: 
    
    ```
    CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS              PORTS                    NAMES
    941e1bdf8e1d        mcr.microsoft.com/mssql/server/mssql-server-linux   "/bin/sh -c /opt/m..."   About an hour ago   Up About an hour     0.0.0.0:1401->1433/tcp   sql1
    ```

## <a name="change-the-sa-password"></a>SA パスワードの変更

[!INCLUDE [Change docker password](../includes/sql-server-linux-change-docker-password.md)]

## <a name="execute-r--python-scripts-from-transact-sql"></a>Transact-SQL で R スクリプトまたは Python スクリプトを実行する

1. コンテナーの SQL Server に接続し、次の T-SQL ステートメントを実行して外部スクリプトの構成オプションを有効にします。

    ```sql
    EXEC sp_configure  'external scripts enabled', 1
    RECONFIGURE WITH OVERRIDE
    go
    ```

1. 次の簡単な R/Python の sp_execute_external_script を実行し、Machine Learning Services が機能していることを確認します。

    ```sql
    execute sp_execute_external_script 
    @language = N'R',
    @script = N'
    print("Hello World!")
    print(R.version)
    print(Revo.version)
    OutputDataSet <- InputDataSet', 
    @input_data_1 = N'select 1'
    with result sets((i int));
    go
    ```

    ```sql
    execute sp_execute_external_script 
    @language = N'Python',
    @script = N'
    import sys
    print(sys.version)
    print("Hello World!")
    OutputDataSet = InputDataSet',
    @input_data_1 = N'select 1'
    with result sets((i int));
    go 
    ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次を学習しました。

> [!div class="checklist"]
> * mssql-docker リポジトリをクローンする
> * Machine Learning サービスを使用して SQL Server Linux コンテナー イメージをビルドする
> * Machine Learning サービスを使用して SQL Server Linux コンテナー イメージを実行する
> * Transact-SQL ステートメントを使用して R または Python スクリプトを実行する
> * SQL Server Linux コンテナーを停止して削除する

次に、その他の Docker の構成とトラブルシューティングのシナリオを確認します。

> [!div class="nextstepaction"]
>[Docker 上の SQL Server の構成ガイド](sql-server-linux-configure-docker.md)
