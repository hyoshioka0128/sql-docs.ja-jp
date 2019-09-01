---
title: オフラインで展開する
titleSuffix: SQL Server big data clusters
description: SQL Server ビッグ データ クラスターのオフライン展開を実行する方法について説明します。
author: mihaelablendea
ms.author: mihaelab
ms.reviewer: mikeray
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 243771141bbd255e045ef0a1667235f1c414777b
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70155269"
---
# <a name="perform-an-offline-deployment-of-a-sql-server-big-data-cluster"></a>SQL Server ビッグ データ クラスターのオフライン展開を実行する

この記事では、 [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]のオフライン展開を実行する方法について説明します。 ビッグ データ クラスターには、コンテナー イメージをプルする Docker リポジトリへのアクセス権が必要です。 オフライン インストールでは、必要なイメージがプライベート Docker リポジトリに配置されています。 そのプライベート リポジトリはその後、新しい展開のイメージ ソースとして使用されます。

## <a name="prerequisites"></a>前提条件

- サポートされている任意の Linux ディストリビューションの Docker エンジン 1.8 + または Docker for Mac/Windows。 詳細については「[Install Docker](https://docs.docker.com/engine/installation/)」(Docker のインストール) を参照してください。

[!INCLUDE [Limited public preview note](../includes/big-data-cluster-preview-note.md)]

## <a name="load-images-into-a-private-repository"></a>プライベート リポジトリにイメージを読み込む

次の手順では、ビッグ データ クラスター コンテナー イメージを Microsoft リポジトリからプルし、プライベート リポジトリにプッシュする方法について説明します。

> [!TIP]
> 次の手順では、このプロセスについて説明します。 ただし、タスクを簡略化するために、これらのコマンドを手動で実行する代わりに、[自動スクリプト](#automated)を使用できます。

1. 次のコマンドを繰り返して、ビッグ データ クラスター コンテナー イメージをプルします。 `<SOURCE_IMAGE_NAME>` をそれぞれの[イメージ名](#images)で置き換えます。 `<SOURCE_DOCKER_TAG>` **2019-RC1-ubuntu**など、ビッグデータクラスターのリリースのタグで置き換えます。  

   ```PowerShell
   docker pull mcr.microsoft.com/mssql/bdc/<SOURCE_IMAGE_NAME>:<SOURCE_DOCKER_TAG>
   ```

1. ターゲット プライベート Docker レジストリにログインします。

   ```PowerShell
   docker login <TARGET_DOCKER_REGISTRY> -u <TARGET_DOCKER_USERNAME> -p <TARGET_DOCKER_PASSWORD>
   ```

1. ローカル イメージごとに次のコマンドを使用して、イメージにタグを付けます。

   ```PowerShell
   docker tag mcr.microsoft.com/mssql/bdc/<SOURCE_IMAGE_NAME>:<SOURCE_DOCKER_TAG> <TARGET_DOCKER_REGISTRY>/<TARGET_DOCKER_REPOSITORY>/<SOURCE_IMAGE_NAME>:<TARGET_DOCKER_TAG>
   ```

1. ローカル イメージをプライベート Docker リポジトリにプッシュします。

   ```PowerShell
   docker push <TARGET_DOCKER_REGISTRY>/<TARGET_DOCKER_REPOSITORY>/<SOURCE_IMAGE_NAME>:<TARGET_DOCKER_TAG>
   ```

### <a id="images"></a> ビッグ データ クラスター コンテナー イメージ

オフライン インストールには、次のビッグ データ クラスター コンテナー イメージが必要です。
- **mssql-app-service-proxy**
- **mssql-コントロール-ウォッチドッグ**
- **mssql-controller**
- **mssql-dns**
- **mssql-hadoop**
- **mssql-mleap-serving-runtime**
- **mssql-mlserver-py-runtime**
- **mssql-mlserver-r-runtime**
- **mssql-monitor-collectd**
- **mssql-monitor-elasticsearch**
- **mssql-monitor-fluentbit**
- **mssql-monitor-grafana**
- **mssql-monitor-influxdb**
- **mssql-monitor-kibana**
- **mssql-monitor-telegraf**
- **mssql-security-domainctl**
- **mssql-security-knox**
- **mssql-security-support**
- **mssql-server**
- **mssql-server-controller**
- **mssql-server-data**
- **mssql-サーバー-ha**
- **mssql-service-proxy**
- **mssql-ssis-app-runtime**


## <a id="automated"></a> 自動スクリプト

自動的にすべての必要なコンテナー イメージをプルし、それらをプライベート リポジトリにプッシュする、自動化された Python スクリプトを使用できます。

> [!NOTE]
> Python は、スクリプトを使用するための前提条件です。 Python のインストール方法の詳細については、[Python のドキュメント](https://wiki.python.org/moin/BeginnersGuide/Download)を参照してください。

1. Bash または PowerShell から、**curl** を使用してスクリプトをダウンロードします。

   ```PowerShell
   curl -o push-bdc-images-to-custom-private-repo.py "https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/sql-big-data-cluster/deployment/offline/push-bdc-images-to-custom-private-repo.py"
   ```

1. その後、次のいずれかのコマンドを使用してスクリプトを実行します。

   **Windows:**

   ```PowerShell
   python deploy-sql-big-data-aks.py
   ```

   **Linux:**

   ```bash
   sudo python deploy-sql-big-data-aks.py
   ```

1. 画面の指示に従って、Microsoft リポジトリとご自分のプライベート リポジトリの情報を入力します。 スクリプトを完了すると、必要なすべてのイメージがプライベート リポジトリに配置されているはずです。

## <a name="install-tools-offline"></a>ツールをオフラインでインストールする

ビッグデータクラスターのデプロイには、 **Python**、 `azdata`、 **kubectl**など、いくつかのツールが必要です。 これらのツールをオフライン サーバーにインストールするには、次の手順に従います。

### <a id="python"></a> Python をオフラインでインストールする

1. インターネットにアクセスできるコンピューターで、Python が含まれる次のいずれかの圧縮ファイルをダウンロードします。

   | オペレーティング システム | ダウンロード |
   |---|---|
   | Windows | [https://go.microsoft.com/fwlink/?linkid=2074021](https://go.microsoft.com/fwlink/?linkid=2074021) |
   | Linux   | [https://go.microsoft.com/fwlink/?linkid=2065975](https://go.microsoft.com/fwlink/?linkid=2065975) |
   | OSX     | [https://go.microsoft.com/fwlink/?linkid=2065976](https://go.microsoft.com/fwlink/?linkid=2065976) |

1. 圧縮されたファイルをターゲット コンピューターにコピーし、任意のフォルダーに抽出します。

1. Windows の場合にのみ、そのフォルダーから `installLocalPythonPackages.bat` を実行して、パラメーターと同じフォルダーへの完全なパスを渡します。

   ```PowerShell
   installLocalPythonPackages.bat "C:\python-3.6.6-win-x64-0.0.1-offline\0.0.1"
   ```

### <a id="azdata"></a> azdata をオフラインでインストールする

1. インターネットアクセスと[Python](https://wiki.python.org/moin/BeginnersGuide/Download)が搭載されたコンピューターで、次のコマンドを実行し`azdata`て、すべてのパッケージを現在のフォルダーにダウンロードします。

   ```PowerShell
   pip download -r https://aka.ms/azdata
   ```

1. ダウンロードしたパッケージと`requirements.txt`ファイルをターゲットコンピューターにコピーします。

1. ターゲット コンピューターで、前のファイルをコピーしたフォルダーを指定して次のコマンドを実行します。

   ```PowerShell
   pip install --no-index --find-links <path-to-packages> -r <path-to-requirements.txt>
   ```

### <a id="kubectl"></a> kubectl をオフラインでインストールする

オフライン コンピューターに **kubectl** をインストールするには、次の手順を実行します。

1. **curl** を使用して、選択したフォルダーに **kubectl** をダウンロードします。 詳細については、[curl を使用した kubectl バイナリのインストール](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl)に関するページを参照してください。

1. フォルダーをターゲット コンピューターにコピーします。

## <a name="deploy-from-private-repository"></a>プライベート リポジトリから展開する

プライベート リポジトリから展開するには、[展開ガイド](deployment-guidance.md)に記載されている手順を使用しますが、プライベート Docker リポジトリ情報を指定するカスタムの展開構成ファイルを使用します。 次`azdata`のコマンドは、という名前`control.json`のカスタム展開構成ファイルの Docker 設定を変更する方法を示しています。

```bash
azdata bdc config replace --config-file custom/control.json --json-values "$.spec.docker.repository=<your-docker-repository>"
azdata bdc config replace --config-file custom/control.json --json-values "$.spec.docker.registry=<your-docker-registry>"
azdata bdc config replace --config-file custom/control.json --json-values "$.spec.docker.imageTag=<your-docker-image-tag>"
```

デプロイによって、docker のユーザー名とパスワードの入力が求められます`DOCKER_USERNAME` 。 `DOCKER_PASSWORD`また、との環境変数で指定することもできます。

## <a name="next-steps"></a>次の手順

ビッグデータクラスターの展開の詳細については、「 [How to deploy [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] on Kubernetes](deployment-guidance.md)」を参照してください。
