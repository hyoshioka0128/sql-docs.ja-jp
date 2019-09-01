---
title: 展開を構成する
titleSuffix: SQL Server big data clusters
description: 構成ファイルを使用してビッグ データ クラスターの展開をカスタマイズする方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 230ec2300bff55cefbb176c69d677b4e04d6ad30
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70155321"
---
# <a name="configure-deployment-settings-for-cluster-resources-and-services"></a>クラスターリソースとサービスの展開設定を構成する

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

Azdata management tool で組み込まれている構成プロファイルの定義済みセットから開始すると、既定の設定を簡単に変更して、BDC のワークロード要件に合わせることができます。 リリース候補リリース以降、構成ファイルの構造が更新され、リソースの各サービスごとに設定を細かく更新できるようになりました。 

リソースレベルの構成を設定したり、リソース内のすべてのサービスの構成を更新したりすることもできます。 次に、bdc の構造の概要を示します **。**

```json
{
    "apiVersion": "v1",
    "metadata": {
        "kind": "BigDataCluster",
        "name": "mssql-cluster"
    },
    "spec": {
        "resources": {
            "nmnode-0": {...
            },
            "sparkhead": {...
            },
            "zookeeper": {...
            },
            "gateway": {...
            },
            "appproxy": {...
            },
            "master": {...
            },
            "compute-0": {...
            },
            "data-0": {...
            },
            "storage-0": {...
        },
        "services": {
            "sql": {
                "resources": [
                    "master",
                    "compute-0",
                    "data-0",
                    "storage-0"
                ]
            },
            "hdfs": {
                "resources": [
                    "nmnode-0",
                    "zookeeper",
                    "storage-0",
                    "sparkhead"
                ],
                "settings": {...
            },
            "spark": {
                "resources": [
                    "sparkhead",
                    "storage-0"
                ],
                "settings": {...
            }
        }
    }
}
```

プール内のインスタンスなどのリソースレベルの構成を更新するには、リソース仕様を更新します。たとえば、コンピューティングプール内のインスタンスの数を更新するには、次のように、 **bdc**構成ファイルでこのセクションを変更します。
```json
"resources": {
    ...
    "compute-0": {
        "metadata": {
            "kind": "Pool",
            "name": "default"
        },
        "spec": {
            "type": "Compute",
            "replicas": 4
        }
    }
    ...
}
``` 

同様に、特定のリソース内のシングルサービスの設定を変更します。 たとえば、ストレージプール内の Spark コンポーネントに対してのみ Spark メモリの設定を変更する場合は、れる update 構成ファイルの**spark**サービスの**settings**セクションで**storage 0**リソースを使用します **。** .
```json
"resources":{
    ...
     "storage-0": {
        "metadata": {
            "kind": "Pool",
            "name": "default"
        },
        "spec": {
            "type": "Storage",
            "replicas": 2,
            "settings": {
                "spark": {
                    "driverMemory": "2g",
                    "driverCores": "1",
                    "executorInstances": "3",
                    "executorMemory": "1536m",
                    "executorCores": "1"
                }
            }
        }
    }
    ...
}
```

複数のリソースに関連付けられているサービスに対して同じ構成を適用する場合は、 **[サービス]** セクションで対応する**設定**を更新します。 たとえば、ストレージプールと Spark プールの両方で、Spark に同じ設定を同じように設定する場合は、 **bdc**構成ファイルの**spark**サービスセクションの **[設定]** セクションを更新します。

```json
"services": {
    ...
    "spark": {
        "resources": [
            "sparkhead",
            "storage-0"
        ],
        "settings": {
            "driverMemory": "2g",
            "driverCores": "1",
            "executorInstances": "3",
            "executorMemory": "1536m",
            "executorCores": "1"
        }
    }
    ...
}
```


クラスターの展開構成ファイルをカスタマイズするには、VSCode など、任意の JSON 形式エディターを使用できます。 自動化のためにこれらの編集をスクリプト化するには、**azdata bdc config** コマンドを使用します。 この記事では、展開構成ファイルを変更してビッグ データ クラスターの展開を構成する方法について説明します。 さまざまなシナリオについて構成の変更方法の例を示します。 展開に構成ファイルを使用する方法の詳細詳細については、「[展開のガイダンス](deployment-guidance.md#configfile)」を参照してください。

## <a name="prerequisites"></a>前提条件

- [azdata をインストールします](deploy-install-azdata.md)。

- このセクションの各例では、標準構成のいずれかのコピーを作成済みであることを前提としています。 詳細については、[カスタム構成の作成](deployment-guidance.md#customconfig)に関する記事を参照してください。 たとえば、次のコマンドは、 `custom`既定の**aks**構成に基づいて、2つの json デプロイ構成ファイル ( **bdc**と**control. json**) を含むという名前のディレクトリを作成します。

   ```bash
   azdata bdc config init --source aks-dev-test --target custom
   ```

## <a id="clustername"></a> クラスター名を変更する

クラスター名は、ビッグ データ クラスターと、展開時に作成される Kubernetes 名前空間の両方の名前です。 これは、 **bdc**の展開構成ファイルの次の部分で指定されています。

```json
"metadata": {
    "kind": "BigDataCluster",
    "name": "mssql-cluster"
},
```

次のコマンドでは、キーと値のペアを **--json-values** パラメーターに送信し、ビッグ データ クラスター名を **test-cluster** に変更します。

```bash
azdata bdc config replace --config-file custom/bdc.json --json-values "metadata.name=test-cluster"
```

> [!IMPORTANT]
> ビッグ データ クラスターの名前は、小文字の英数字のみを使用し、スペースを含めない必要があります クラスターのすべての Kubernetes 成果物 (コンテナー、ポッド、ステートフル セット、サービス) は、指定したクラスター名と同じ名前の名前空間に作成されます。

## <a id="ports"></a> エンドポイント ポートを更新する

エンドポイントは、コントローラーに対して定義されてい**ます。** また、ゲートウェイの場合は、 **bdc**の対応するセクションにある SQL Server マスターインスタンスです。 **control.json** 構成ファイルの次の部分は、コントローラーのエンドポイント定義を示しています。

```json
{
  "endpoints": [
    {
      "name": "Controller",
      "serviceType": "LoadBalancer",
      "port": 30080
    },
    {
      "name": "ServiceProxy",
      "serviceType": "LoadBalancer",
      "port": 30777
    }
  ]
}
```

次の例では、インライン JSON を使用して **controller** エンドポイントのポートを変更します。

```bash
azdata bdc config replace --config-file custom/control.json --json-values "$.spec.endpoints[?(@.name==""Controller"")].port=30000"
```

## <a id="replicas"></a> プールのレプリカを構成する

各リソースの構成 (記憶域プールなど) は、 **bdc**の構成ファイルで定義されています。 たとえば、bdc の次の部分は、**ストレージ 0**のリソース定義を示して**い**ます。

```json
"storage-0": {
    "metadata": {
        "kind": "Pool",
        "name": "default"
    },
    "spec": {
        "type": "Storage",
        "replicas": 2,
        "settings": {
            "spark": {
                "driverMemory": "2g",
                "driverCores": "1",
                "executorInstances": "3",
                "executorMemory": "1536m",
                "executorCores": "1"
            }
        }
    }
}
```

プール内のインスタンスの数を構成するには、各プールの **replicas** 値を変更します。 次の例では、インライン JSON を使用して、記憶域プールとデータ プールのこれらの値をそれぞれ `10` と `4` に変更します。

```bash
azdata bdc config replace --config-file custom/bdc.json --json-values "$.spec.resources.storage-0.spec.replicas=10"
azdata bdc config replace --config-file custom/bdc.json --json-values "$.spec.resources.data-0.spec.replicas=4"
```

## <a id="storage"></a> 記憶域を構成する

各プールに使用される記憶域クラスと特性を変更することもできます。 次の例では、カスタム記憶域クラスを記憶域プールに割り当て、データを保存する永続ボリューム要求のサイズを 100Gb に更新します。 まず、次のように、*type* と *replicas* に加えて新しい *storage* セクションを含む patch.json ファイルを作成します

```json
{
  "patch": [
    {
      "op": "replace",
      "path": "spec.resources.storage-0.spec",
      "value": {
        "type": "Storage",
        "replicas": 2,
        "storage": {
          "data": {
            "size": "100Gi",
            "className": "myStorageClass",
            "accessMode": "ReadWriteOnce"
          },
          "logs": {
            "size": "32Gi",
            "className": "myStorageClass",
            "accessMode": "ReadWriteOnce"
          }
        }
      }
    }
  ]
}
```

その後、 **azdata bdc**の構成パッチコマンドを使用して、 **bdc**の構成ファイルを更新できます。
```bash
azdata bdc config patch --config-file custom/bdc.json --patch ./patch.json
```

> [!NOTE]
> **kubeadm-dev-test** に基づく構成ファイルには、各プールの記憶域の定義はありませんが、必要に応じて上のプロセスを使用して追加できます。

記憶域構成の詳細については、「[Kubernetes 上の SQL Server ビッグ データ クラスターでのデータ永続化](concept-data-persistence.md)」を参照してください。

## <a id="sparkstorage"></a> Spark を使用せずに記憶域プールを構成する

また、Spark を使用せずに実行し、別の Spark プールを作成するように記憶域プールを構成することもできます。 こうすると、記憶域に依存せず、Spark コンピューティングの機能を拡張することができます。 Spark プールを構成する方法については、この記事の末尾にある [JSON 修正プログラム ファイルの例](#jsonpatch)を参照してください。


既定では、記憶域プールリソースの [格納] 設定は [true] に設定されているため、変更を行うには、[格納] フィールドをストレージ構成に編集する必要があります。 次のコマンドは、インライン json を使用してこの値を編集する方法を示しています。

```bash
azdata bdc config replace --config-file custom/bdc.json --json-values "$.spec.resources.storage-0.spec.settings.spark.includeSpark=false"
```

## <a id="podplacement"></a> Kubernetes ラベルを使用してポッドの配置を構成する

Kubernetes ノード上で特定のリソースを持つポッドの配置を制御することで、さまざまな種類のワークロード要件に対応できます。 たとえば、記憶域プールリソースポッドがより多くの記憶域を持つノードに配置されていること、また SQL Server は CPU とメモリリソースが高いノードにマスターインスタンスが配置されていることを確認する必要がある場合があります。 この場合、まず異なる種類のハードウェアが混在する Kubernetes クラスターを構築してから、それに応じて[ノード ラベルを割り当てます](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)。 ビッグ データ クラスターを展開する際に、クラスター展開構成ファイルのプール レベルで同じラベルを指定できます。 Kubernetes では、ノード上の指定されたラベルに一致するポッドの関連付けが処理されます。 Kubernetes クラスター内のノードに追加する必要がある特定のラベルキーは**mssql-クラスター全体**です。 このラベル自体の値には、任意の文字列を指定できます。

次の例では、カスタム構成ファイルを編集して、SQL Server マスターインスタンス、コンピューティングプール、データプール & 記憶域プールのノードラベル設定を含める方法を示します。 組み込みの構成には *nodeLabel* キーがないため、カスタム構成ファイルを手動で編集するか、修正プログラム ファイルを作成してカスタム構成ファイルに適用する必要があります。 SQL Server マスターインスタンスポッドは、" **mssql-クラスター全体**" という値を含むノードにデプロイされます。 コンピューティングプールとデータプールポッドは、" **mssql-クラスター全体**" という値を含むノードにデプロイされます。 記憶域プールポッドは、" **mssql-クラスター全体**" という値を含むノードにデプロイされます。

現在のディレクトリに次の内容の **patch.json** という名前のファイルを作成します。

```json
{
  "patch": [
    {
      "op": "replace",
      "path": "spec.resources.master.spec",
      "value": {
        "type": "Master",
        "replicas": 1,
        "endpoints": [
          {
            "name": "Master",
            "serviceType": "NodePort",
            "port": 31433
          }
        ],
        "settings": {
          "sql": {
            "hadr.enabled": "false"
          }
        },
        "nodeLabel": "bdc-master"
      }
    },
    {
      "op": "replace",
      "path": "spec.resources.compute-0.spec",
      "value": {
        "type": "Compute",
        "replicas": 1,
        "nodeLabel": "bdc-sql"
      }
    },
    {
      "op": "replace",
      "path": "spec.resources.data-0.spec",
      "value": {
        "type": "Data",
        "replicas": 2,
        "nodeLabel": "bdc-sql"
      }
    },
    {
      "op": "replace",
      "path": "spec.resources.storage-0.spec",
      "value": {
        "type": "Storage",
        "replicas": 3,
        "nodeLabel": "bdc-storage",
        "settings": {
          "spark": {
            "includeSpark": "true"
          }
        }
      }
    }
  ]
}
```

```bash
azdata bdc config patch --config-file custom/bdc.json --patch-file ./patch.json
```

## <a id="jsonpatch"></a> JSON 修正プログラム ファイル

JSON 修正プログラム ファイルによって、複数の設定が一度で構成されます。 Json 修正プログラムの詳細については、[Python の JSON 修正プログラム](https://github.com/stefankoegl/python-json-patch)と [JSONPath Online Evaluator](https://jsonpath.com/) のページを参照してください。

次の **patch.json** ファイルでは、次の変更が実行されます。

- **control.json** で、1 つのエンドポイントのポートを更新します。
```json
{
  "patch": [
    {
      "op": "replace",
      "path": "$.spec.endpoints[?(@.name=='Controller')].port",
      "value": 30000
    }
  ]
}
```

- **control.json** で、すべてのエンドポイント (**port** と **serviceType**) を更新します。
```json
{
  "patch": [
    {
      "op": "replace",
      "path": "spec.endpoints",
      "value": [
        {
          "serviceType": "LoadBalancer",
          "port": 30001,
          "name": "Controller"
        },
        {
          "serviceType": "LoadBalancer",
          "port": 30778,
          "name": "ServiceProxy"
        }
      ]
    }
  ]
}
```

- **control.json** で、コントローラーの記憶域設定を更新します。 これらの設定は、プール レベルで上書きされない限り、すべてのクラスター コンポーネントに適用されます。
```json
{
  "patch": [
    {
      "op": "replace",
      "path": "spec.storage",
      "value": {
        "data": {
          "className": "managed-premium",
          "accessMode": "ReadWriteOnce",
          "size": "100Gi"
        },
        "logs": {
          "className": "managed-premium",
          "accessMode": "ReadWriteOnce",
          "size": "32Gi"
        }
      }
    }
  ]
}
```

- **control.json** で、記憶域クラス名を更新します。
```json
{
  "patch": [
    {
      "op": "replace",
      "path": "spec.storage.data.className",
      "value": "managed-premium"
    }
  ]
}
```

- **Bdc**の記憶域プールのプール記憶域設定を更新します。
```json
{
  "patch": [
    {
      "op": "replace",
      "path": "spec.resources.storage-0.spec",
      "value": {
        "type": "Storage",
        "replicas": 2,
        "storage": {
          "data": {
            "size": "100Gi",
            "className": "myStorageClass",
            "accessMode": "ReadWriteOnce"
          },
          "logs": {
            "size": "32Gi",
            "className": "myStorageClass",
            "accessMode": "ReadWriteOnce"
          }
        }
      }
    }
  ]
}
```

- **Bdc**の記憶域プールの Spark 設定を更新します。
```json
{
  "patch": [
    {
      "op": "replace",
      "path": "spec.services.spark.settings",
      "value": {
        "driverMemory": "2g",
        "driverCores": 1,
        "executorInstances": 3,
        "executorCores": 1,
        "executorMemory": "1536m"
      }
    }
  ]
}
```

- **Bdc**に2つのインスタンスを持つ spark プールを作成します。
```json
{
  "patch": [
    {
      "op": "add",
      "path": "spec.resources.spark-0",
      "value": {
        "metadata": {
          "kind": "Pool",
          "name": "default"
        },
        "spec": {
          "type": "Spark",
          "replicas": 2
        }
      }
    },
    {
      "op": "add",
      "path": "spec.services.spark.resources/-",
      "value": "spark-0"
    },
    {
      "op": "add",
      "path": "spec.services.hdfs.resources/-",
      "value": "spark-0"
    },
    {
      "op": "add",
      "path": "spec.services.spark.settings",
      "value": {
        "DriverMemory": "2g",
        "DriverCores": "1",
        "ExecutorInstances": "2",
        "ExecutorMemory": "2g",
        "ExecutorCores": "1"
      }
    }
  ]
}
```

> [!TIP]
> 展開構成ファイルを変更するための構造とオプションの詳細については、「[ビッグ データ クラスターの展開構成ファイル リファレンス](reference-deployment-config.md)」を参照してください。

**azdata bdc config** コマンドを使用して、JSON 修正プログラム ファイルに変更を適用します。 次の例では、**修正プログラムの json**ファイルを、**カスタム/bdc. json**に適用します。

```bash
azdata bdc config patch --config-file custom/bdc.json --patch-file ./patch.json
```

## <a name="disable-elasticsearch-to-run-in-privileged-mode"></a>特権モードで実行するために ElasticSearch を無効にする
既定では、ElasticSearch コンテナーはビッグデータクラスターの特権モードで実行されます。 これは、コンテナーの初期化時に、ElasticSearch がより多くのログを処理するときに、コンテナーがホスト必要ですの設定を更新するための十分なアクセス許可を持っていることを確認するためです。 このトピックの詳細については、[この記事](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html)を参照してください。 

ElasticSearch を実行するコンテナーが特権モードで実行されるようにするには、**コントロール**の **[設定]** セクションを更新して、vm の値を指定する必要があり**ます。 max _map_count**は **-1**です。 このセクションの例を次に示します。
```json
"settings": {
    "ElasticSearch": {
        "vm.max_map_count": "-1"
      }
}
```

手動でを編集して上記のセクションを**仕様**に追加することができます。または、次のような修正プログラムファイル**elasticsearch**を作成し、 **azdata** CLI を使用して、 **config.xml**ファイルに修正プログラムを適用することもできます。

```json
{
  "patch": [
    {
      "op": "replace",
      "path": "spec",
      "value": {
        "docker": {
            "registry": "mcr.microsoft.com",
            "repository": "mssql/bdc",
            "imageTag": "2019-RC1-ubuntu",
            "imagePullPolicy": "Always"
        },
        "storage": {
            "data": {
                "className": "default",
                "accessMode": "ReadWriteOnce",
                "size": "15Gi"
            },
            "logs": {
                "className": "default",
                "accessMode": "ReadWriteOnce",
                "size": "10Gi"
            }
        },
        "endpoints": [
            {
                "name": "Controller",
                "serviceType": "LoadBalancer",
                "port": 30080
            },
            {
                "name": "ServiceProxy",
                "serviceType": "LoadBalancer",
                "port": 30777
            }
        ],
        "settings": {
            "ElasticSearch": {
                "vm.max_map_count": "-1"
             }
        }
       }
    }
  ]
}
```

次のコマンドを実行して、構成ファイルに修正プログラムを適用します。
```
azdata bdc config patch --config-file control.json --patch-file elasticsearch-patch.json
```

> [!IMPORTANT]
> [この記事](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html)の手順に従って、Kubernetes クラスター内の各ホストで手動で**max_map_count**設定を手動で更新することをお勧めします。
## <a name="next-steps"></a>次の手順

ビッグデータクラスターの展開における構成ファイルの使用の詳細については、「 [How to deploy [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] on Kubernetes](deployment-guidance.md#configfile)」を参照してください。
