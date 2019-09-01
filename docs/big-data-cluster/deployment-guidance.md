---
title: 展開のガイダンス
titleSuffix: SQL Server big data clusters
description: Kubernetes にデプロイ[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)] (プレビュー) する方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 9a1953ecb17dba3894afe15e88690fbb150fb5a3
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70153454"
---
# <a name="how-to-deploy-includebig-data-clusters-2019includesssbigdataclusters-ss-novermd-on-kubernetes"></a>Kubernetes にデプロイ[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]する方法

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

SQL Server ビッグ データ クラスターは、Kubernetes クラスター上に docker コンテナーとして展開されます。 ここでは、セットアップと構成の手順の概要を説明します。

- 単一の VM 上、VM のクラスター上、または Azure Kubernetes Service (AKS) に、Kubernetes クラスターを設定します。
- クライアント コンピューター上にクラスター構成ツール **azdata** をインストールする。
- Kubernetes クラスターに SQL Server ビッグ データ クラスターを展開する。

[!INCLUDE [Limited public preview note](../includes/big-data-cluster-preview-note.md)]

## <a name="install-sql-server-2019-big-data-tools"></a>SQL Server 2019 ビッグ データ ツールをインストールする

SQL Server 2019 ビッグ データ クラスターを展開する前に、まず、[ビッグ データ ツールをインストール](deploy-big-data-tools.md)します。

- **azdata**
- **kubectl**
- **Azure Data Studio**
- **SQL Server 2019 の拡張機能**

## <a id="prereqs"></a> Kubernetes の前提条件

[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]サーバーとクライアント (kubectl) の両方に対して、少なくとも Kubernetes の最小バージョンを必要とします。

> [!NOTE]
> クライアントとサーバーでの Kubernetes のバージョンは、マイナー バージョンが +1 または -1 の範囲内になっている必要があることに注意してください。 詳細については、[Kubernetes のリリース ノートとバージョン スキューの SKU ポリシー](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/release/versioning.md#supported-releases-and-component-skew)に関するページを参照してください。

### <a id="kubernetes"></a> Kubernetes クラスターの設定

上記の前提条件を満たす Kubernetes クラスターを既にお持ちの場合は、[展開の手順](#deploy)に直接進むことができます。 このセクションでは、Kubernetes の概念の基本的な理解を前提としています。  Kubernetes の詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/home)を参照してください。

次の3つの方法のいずれかを選んで、Kubernetes を展開できます。

| Kubernetes の展開先: | 説明 | リンク |
|---|---|---|
| **Azure Kubernetes Services (AKS)** | Azure にあるマネージド Kubernetes コンテナー サービス。 | [手順](deploy-on-aks.md) |
| **複数のマシン (kubeadm)** | **kubeadm** を使用して物理または仮想マシン上に展開された Kubernetes クラスター | [手順](deploy-with-kubeadm.md) |
| **Minikube** | VM にある単一ノード Kubernetes クラスター。 | [手順](deploy-on-minikube.md) |

> [!TIP]
> また、1 つの手順で AKS とビッグ データ クラスターの展開をスクリプト化することもできます。 詳細については、[Python スクリプト](quickstart-big-data-cluster-deploy.md)または Azure Data Studio の[ノートブック](deploy-notebooks.md)において、この操作を行う方法を確認してください。

### <a name="verify-kubernetes-configuration"></a>Kubernetes の構成を確認する

クラスター構成を表示するには、**kubectl** コマンドを実行します。 Kubectl が正しいクラスター コンテキストを指していることを確認します。

```bash
kubectl config view
```

Kubernetes クラスターを構成したら、新しい SQL Server ビッグ データ クラスターの展開に進むことができます。 以前のリリースからアップグレードする場合は、「[アップグレード[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]方法](deployment-upgrade.md)」を参照してください。

## <a id="deploy"></a> 展開の概要

ほとんどのビッグ データ クラスター設定は、JSON 展開構成ファイルに定義されています。 AKS、`kubeadm`、または `minikube` に対する既定の展開プロファイルを使用しても、セットアップ中に使用する独自の展開構成ファイルをカスタマイズしてもかまいません。 セキュリティ上の理由から、認証設定は環境変数を介して渡されます。

以降のセクションでは、ビッグ データ クラスターの展開を構成する方法と、一般的なカスタマイズの例について詳しく説明します。 また、たとえば VS Code のようなエディターを使用して、カスタムの展開構成ファイルをいつでも編集できます。

## <a id="configfile"></a> 既定の構成

ビッグ データ クラスターの展開オプションは、JSON 構成ファイルに定義されています。 組み込みの展開プロファイルから、開発/テスト環境の既定の設定を使用して、クラスターの展開のカスタマイズを開始できます。

| 展開プロファイル | Kubernetes 環境 |
|---|---|
| **aks-dev-test** | Azure Kubernetes Service (AKS) |
| **kubeadm-dev-test** | 複数のマシン (kubeadm) |
| **minikube-dev-test** | minikube |

**azdata bdc create** を実行することで、ビッグ データ クラスターを展開できます。 これにより、既定の構成の 1 つを選択するよう求めるメッセージが表示され、展開へと進みます。

初めて `azdata` を実行するときは、使用許諾契約書 (EULA) に同意するために `--accept-eula=yes` を含める必要があります。

```bash
azdata bdc create --accept-eula=yes
```

このシナリオでは、パスワードなど、既定の構成に含まれない設定をすべて入力するように求められます。 

> [!IMPORTANT]
> ビッグ データ クラスターの既定の名前は **mssql-cluster** です。 `-n` パラメーターを使用して Kubernetes 名前空間を指定する任意の **kubectl** コマンドを実行するために、このことを知っておくことは重要です。

## <a id="customconfig"></a> カスタムの構成

また、独自の展開構成プロファイルをカスタマイズすることも可能です。 次の手順を利用して、これを行うことができます。

1. Kubernetes 環境に適合する標準の展開プロファイルの 1 つを使用して開始します。 **azdata bdc config list** コマンドを使用して、一覧表示できます。

   ```bash
   azdata bdc config list
   ```

1. 展開をカスタマイズするには、**azdata bdc config init** コマンドを使用して、展開プロファイルのコピーを作成します。 たとえば、次のコマンドでは、**aks-dev-test** 展開構成ファイルのコピーが `custom` という名前のターゲット ディレクトリに作成されます。

   ```bash
   azdata bdc config init --source aks-dev-test --target custom
   ```

   azdata
   > は`--target` 、 `--source`パラメーターに基づいて、構成ファイル ( **bdc**と**control. json**) を格納するディレクトリを指定します。

1. 展開構成プロファイルでの設定をカスタマイズするために、VS Code などの JSON ファイルの編集に適したツール上で、展開構成ファイルを編集できます。 スクリプト化されたオートメーションの場合、**azdata bdc config**  コマンドを使用してカスタムの展開プロファイルを編集することもできます。 たとえば、次のコマンドでは、カスタムの展開プロファイルを変更して、展開されたクラスターの名前を既定 (**mssql-cluster**) から **test-cluster** に変えています。  

   ```bash
   azdata bdc config replace --config-file custom/cluster.json --json-values "metadata.name=test-cluster"
   ```
   
   > [!TIP]
   > また、*azdata create bdc* コマンドに *--name* パラメータ―を使用して、展開時にクラスター名を渡すことも可能です。 コマンド内のパラメーターは、構成ファイルでの値よりも優先されます。

   > JSON パスを検索するための便利なツールとして、[JSONPath Online Evaluator](https://jsonpath.com/) があります。

   キーと値のペアを渡すだけでなく、インライン JSON 値を指定したり、JSON パッチ ファイルを渡したりすることもできます。 詳細については、「[ビッグ データ クラスターの展開設定を構成する](deployment-custom-configuration.md)」を参照してください。

1. 次に、カスタム構成ファイルを **azdata bdc create** に渡します。 必要な[環境変数](#env)を必ず設定することに注意してください。そうしないと、値の入力を求められます。

   ```bash
   azdata bdc create --config-profile custom --accept-eula yes
   ```

> 展開構成ファイルの構造の詳細については、[展開構成ファイルのリファレンス](reference-deployment-config.md)に関するページを参照してください。 他の構成例については、「[ビッグ データ クラスターの展開設定を構成する](deployment-custom-configuration.md)」を参照してください。

## <a id="env"></a> 環境変数

次の環境変数は、展開構成ファイルに保存されないセキュリティ設定に使用されます。 資格情報以外の Docker 設定は、構成ファイル内で設定できることに注意してください。

| 環境変数 | 要件 |説明 |
|---|---|---|
| **CONTROLLER_USERNAME** | 必須 |クラスター管理者のユーザー名。 |
| **CONTROLLER_PASSWORD** | 必須 |クラスター管理者のパスワード。 |
| **MSSQL_SA_PASSWORD** | 必須 |SQL マスター インスタンス用の SA ユーザーのパスワード。 |
| **KNOX_PASSWORD** | 必須 |Knox ユーザーのパスワード。 |
| **ACCEPT_EULA**| `azdata` を初めて使用する場合は必須| 値は必要ありません。 環境変数として設定された場合、SQL Server と `azdata` の両方に EULA が適用されます。 環境変数として設定されない場合、`azdata` コマンドの初めての使用時に `--accept-eula` を含めることができます。|
| **DOCKER_USERNAME** | 省略可 | コンテナー イメージがプライベート リポジトリに格納されている場合に、それらにアクセスするためのユーザー名。 ビッグ データ クラスターの展開にプライベート Docker リポジトリを使用する方法の詳細については、[オフライン展開](deploy-offline.md)に関するトピックを参照してください。|
| **DOCKER_PASSWORD** | 省略可 |上記のプライベート リポジトリにアクセスするためのパスワード。 |

これらの環境変数は、**azdata bdc create** を呼び出す前に設定される必要があります。 設定されていない変数がある場合は、入力を求められます。

次の例は、Linux (bash) と Windows (PowerShell) 用に環境変数を設定する方法を示しています。

```bash
export CONTROLLER_USERNAME=admin
export CONTROLLER_PASSWORD=<password>
export MSSQL_SA_PASSWORD=<password>
export KNOX_PASSWORD=<password>
```

```PowerShell
SET CONTROLLER_USERNAME=admin
SET CONTROLLER_PASSWORD=<password>
SET MSSQL_SA_PASSWORD=<password>
SET KNOX_PASSWORD=<password>
```

環境変数を設定したら、`azdata bdc create` を実行して展開をトリガーする必要があります。 この例では、上記で作成したクラスター構成プロファイルを使用します。

```bash
azdata bdc create --config-profile custom --accept-eula yes
```

次のガイドラインに注意してください。

- 特殊文字が含まれている場合は、必ずパスワードを二重引用符で囲みます。 **MSSQL_SA_PASSWORD** は自由に設定できますが、必ずパスワードを十分に複雑にして、`!`、`&`、`'` 文字は使用しないでください。 二重引用符の区切り記号は bash コマンドでしか機能しないことに注意してください。
- **SA** ログインは、セットアップ時に作成される SQL Server マスター インスタンス上でのシステム管理者です。 SQL Server コンテナーを作成した後、そのコンテナー内で `echo $MSSQL_SA_PASSWORD` を実行すると、指定した **MSSQL_SA_PASSWORD** 環境変数を検索できるようになります。 セキュリティ上の理由から、[こちら](../linux/quickstart-install-connect-docker.md#sapassword)に記載されているベスト プラクティスに従って、SA パスワードを変更してください。

## <a id="unattended"></a> 無人インストール

無人展開の場合は、必要なすべての環境変数を設定して、構成ファイルを使用し、`--accept-eula yes` パラメーターを指定して `azdata bdc create` コマンドを呼び出す必要があります。 前のセクションの例では、無人インストールに対応した構文を示しています。

## <a id="monitor"></a> 展開を監視する

クラスターのブートストラップ中に、クライアントのコマンド ウィンドウに展開の状態が出力されます。 展開プロセス中に、コントローラー ポッドを待機している一連のメッセージが表示されます。

```output
Waiting for cluster controller to start.
```

15 から 30 分以内に、コントローラー ポッドが実行中になっていることが通知されるはずです。

```output
Cluster controller endpoint is available at 11.111.111.11:30080.
Cluster control plane is ready.
```

> [!IMPORTANT]
> ビッグ データ クラスターのコンポーネントに対応するコンテナー イメージのダウンロードに必要な時間によって、展開全体に時間がかかる場合があります。 ただし、数時間もかかることはありません。 展開で問題が発生している場合は、「[監視[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]とトラブルシューティング](cluster-troubleshooting-commands.md)」を参照してください。

展開が完了すると、出力に成功したことが通知されます。

```output
Cluster deployed successfully.
```

> [!TIP]
> カスタム構成によって変更されない限り、展開されたビッグ データ クラスターの既定の名前は `mssql-cluster` になります。

## <a id="endpoints"></a> エンドポイントを取得する

展開スクリプトが正常に完了したら、次の手順を使用して、ビッグ データ クラスターに対応する外部エンドポイントの IP アドレスを取得できます。

1. 展開後、展開の標準出力から、または次の **kubectl** コマンドの EXTERNAL-IP 出力を確認して、コントローラー エンドポイントの IP アドレスを検索します。

   ```bash
   kubectl get svc controller-svc-external -n <your-big-data-cluster-name>
   ```

   > [!TIP]
   > 展開中に既定の名前を変更しなかった場合、前のコマンドにある `-n mssql-cluster` を使用します。 **mssql-cluster** は、ビッグ データ クラスターの既定の名前です。

1. [azdata login](reference-azdata.md) を使用して、ビッグ データ クラスターにログインします。 **--controller-endpoint** パラメーターをコントローラー エンドポイントの外部 IP アドレスに設定します。

   ```bash
   azdata login --controller-endpoint https://<ip-address-of-controller-svc-external>:30080 --controller-username <user-name>
   ```

   展開中にコントローラー (CONTROLLER_USERNAME と CONTROLLER_PASSWORD) に対して構成したユーザー名とパスワードを指定します。

1. [azdata bdc endpoint list](reference-azdata-bdc-endpoint.md) を実行して、各エンドポイントの説明とそれに対応する IP アドレスおよびポート値を示した一覧を取得します。 

   ```bash
   azdata bdc endpoint list -o table
   ```

   次の一覧は、このコマンドからの出力例を示しています。

   ```output
   Description                                             Endpoint                                                   Ip              Name               Port    Protocol
   ------------------------------------------------------  ---------------------------------------------------------  --------------  -----------------  ------  ----------
   Gateway to access HDFS files, Spark                     https://11.111.111.111:30443                               11.111.111.111  gateway            30443   https
   Spark Jobs Management and Monitoring Dashboard          https://11.111.111.111:30443/gateway/default/sparkhistory  11.111.111.111  spark-history      30443   https
   Spark Diagnostics and Monitoring Dashboard              https://11.111.111.111:30443/gateway/default/yarn          11.111.111.111  yarn-ui            30443   https
   Application Proxy                                       https://11.111.111.111:30778                               11.111.111.111  app-proxy          30778   https
   Management Proxy                                        https://11.111.111.111:30777                               11.111.111.111  mgmtproxy          30777   https
   Log Search Dashboard                                    https://11.111.111.111:30777/kibana                        11.111.111.111  logsui             30777   https
   Metrics Dashboard                                       https://11.111.111.111:30777/grafana                       11.111.111.111  metricsui          30777   https
   Cluster Management Service                              https://11.111.111.111:30080                               11.111.111.111  controller         30080   https
   SQL Server Master Instance Front-End                    11.111.111.111,31433                                       11.111.111.111  sql-server-master  31433   tcp
   HDFS File System Proxy                                  https://11.111.111.111:30443/gateway/default/webhdfs/v1    11.111.111.111  webhdfs            30443   https
   Proxy for running Spark statements, jobs, applications  https://11.111.111.111:30443/gateway/default/livy/v1       11.111.111.111  livy               30443   https
   ```

次の **kubectl** コマンドを実行して、クラスターに展開されたすべてのサービス エンドポイントを取得することもできます。

```bash
kubectl get svc -n <your-big-data-cluster-name>
```

### <a name="minikube"></a>Minikube

Minikube を使用している場合は、次のコマンドを実行して、接続に必要な IP アドレスを取得する必要があります。 IP に加えて、接続する必要があるエンドポイントのポートを指定します。

```bash
minikube ip
```

## <a id="status"></a> クラスターの状態を確認する

展開後に、[azdata bdc status show](reference-azdata-bdc-status.md) コマンドを使用して、クラスターの状態を確認できます。

```bash
azdata bdc status show
```

> [!TIP]
> status コマンドを実行するには、最初に、前述のエンドポイントのセクションに示した **azdata login** コマンドを使ってログインする必要があります。

このコマンドからの出力例を、次に示します。

```output
Bdc: ready                                                                                                                                                                                                          Health Status:  healthy
 ===========================================================================================================================================================================================================================================
 Services: ready                                                                                                                                                                                                     Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Servicename    State    Healthstatus    Details

 sql            ready    healthy         -
 hdfs           ready    healthy         -
 spark          ready    healthy         -
 control        ready    healthy         -
 gateway        ready    healthy         -
 app            ready    healthy         -


 Sql Services: ready                                                                                                                                                                                                 Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 master          ready    healthy         StatefulSet master is healthy
 compute-0       ready    healthy         StatefulSet compute-0 is healthy
 data-0          ready    healthy         StatefulSet data-0 is healthy
 storage-0       ready    healthy         StatefulSet storage-0 is healthy


 Hdfs Services: ready                                                                                                                                                                                                Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 nmnode-0        ready    healthy         StatefulSet nmnode-0 is healthy
 storage-0       ready    healthy         StatefulSet storage-0 is healthy
 sparkhead       ready    healthy         StatefulSet sparkhead is healthy


 Spark Services: ready                                                                                                                                                                                               Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 sparkhead       ready    healthy         StatefulSet sparkhead is healthy
 storage-0       ready    healthy         StatefulSet storage-0 is healthy


 Control Services: ready                                                                                                                                                                                             Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 controldb       ready    healthy         -
 control         ready    healthy         -
 metricsdc       ready    healthy         DaemonSet metricsdc is healthy
 metricsui       ready    healthy         ReplicaSet metricsui is healthy
 metricsdb       ready    healthy         StatefulSet metricsdb is healthy
 logsui          ready    healthy         ReplicaSet logsui is healthy
 logsdb          ready    healthy         StatefulSet logsdb is healthy
 mgmtproxy       ready    healthy         ReplicaSet mgmtproxy is healthy


 Gateway Services: ready                                                                                                                                                                                             Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 gateway         ready    healthy         StatefulSet gateway is healthy


 App Services: ready                                                                                                                                                                                                 Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 appproxy        ready    healthy         ReplicaSet appproxy is healthy
```

次のコマンドを使用して、より詳細なステータスを取得することもできます。

- [azdata bdc control status show](reference-azdata-bdc-control-status.md)は、コントロール管理サービスに関連付けられているすべてのコンポーネントの正常性状態を返します
```
azdata bdc control status show
```
サンプル出力:
```output
Control: ready                                                                                                                                                                                                      Health Status:  healthy
 ===========================================================================================================================================================================================================================================
 Resources: ready                                                                                                                                                                                                    Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 controldb       ready    healthy         -
 control         ready    healthy         -
 metricsdc       ready    healthy         DaemonSet metricsdc is healthy
 metricsui       ready    healthy         ReplicaSet metricsui is healthy
 metricsdb       ready    healthy         StatefulSet metricsdb is healthy
 logsui          ready    healthy         ReplicaSet logsui is healthy
 logsdb          ready    healthy         StatefulSet logsdb is healthy
 mgmtproxy       ready    healthy         ReplicaSet mgmtproxy is healthy
```

- **azdata bdc sql status show**は、SQL Server サービスを持つすべてのリソースの正常性状態を返します
```
azdata bdc sql status show
```
サンプル出力:
```output
Sql: ready                                                                                                                                                                                                          Health Status:  healthy
 ===========================================================================================================================================================================================================================================
 Resources: ready                                                                                                                                                                                                    Health Status:  healthy
 -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Resourcename    State    Healthstatus    Details

 master          ready    healthy         StatefulSet master is healthy
 compute-0       ready    healthy         StatefulSet compute-0 is healthy
 data-0          ready    healthy         StatefulSet data-0 is healthy
 storage-0       ready    healthy         StatefulSet storage-0 is healthy
```

> [!IMPORTANT]
> **--All**パラメーターを使用する場合、これらのコマンドからの出力には、詳細な分析のための Kibana および Grafana ダッシュボードへの url が含まれます。

**azdata** を使用するほかに、Azure Data Studio を利用してエンドポイントと状態情報の両方を検索することも可能です。 **azdata** および Azure Data Studio を利用したクラスターの状態の表示に関する詳細については、「[ビッグ データ クラスターの状態を表示する方法](view-cluster-status.md)」を参照してください。

## <a id="connect"></a> クラスターに接続する

ビッグ データ クラスターに接続する方法の詳細については、「[Azure Data Studio を利用して SQL Server ビッグ データ クラスターに接続する](connect-to-big-data-cluster.md)」を参照してください。

## <a name="next-steps"></a>次の手順

ビッグ データ クラスターの展開に関する詳細については、次の資料を参照してください。

- [ビッグ データ クラスターの展開設定を構成する](deployment-custom-configuration.md)
- [SQL Server ビッグ データ クラスターのオフライン展開を実行する](deploy-offline.md)
- [ワークショップ: Microsoft [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]のアーキテクチャ](https://github.com/Microsoft/sqlworkshops/tree/master/sqlserver2019bigdataclusters)
