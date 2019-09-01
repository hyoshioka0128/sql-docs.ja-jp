---
title: Kubernetes でのデータ永続化
titleSuffix: SQL Server big data clusters
description: SQL Server 2019 ビック データ クラスターでのデータ永続化のしくみについて説明します。
author: mihaelablendea
ms.author: mihaelab
ms.reviewer: mikeray
ms.date: 08/28/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 7a12afd88f0eb83de7d5c5bd4a3735e71e037138
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70155347"
---
# <a name="data-persistence-with-sql-server-big-data-cluster-on-kubernetes"></a>Kubernetes 上の SQL Server ビッグ データ クラスターでのデータ永続化

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

[永続ボリューム](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)では、Kubernetes 内にあるストレージ向けのプラグイン モデルを提供します。 ストレージの提供方法は、その使用方法から抽象化されます。 そのため、独自の高可用性ストレージを利用して、SQL Server ビッグ データ クラスターに接続できます。 これにより、必要としているストレージの種類、可用性、およびパフォーマンスを完全に制御できます。 Kubernetes では、Azure ディスク/ファイル、NFS、ローカル ストレージなどのさまざまな種類のストレージ ソリューションがサポートされます。

## <a name="configure-persistent-volumes"></a>永続ボリュームを構成する

SQL Server ビッグ データ クラスターがこれらの永続ボリュームを使用する方法は、[ストレージ クラス](https://kubernetes.io/docs/concepts/storage/storage-classes/)を利用することです。 異なるストレージの種類に対しては異なるストレージ クラスを作成し、ビッグ データ クラスターの展開時に指定できます。 プール レベルでの目的に使用するためのストレージ クラスと永続ボリューム要求のサイズを構成できます。 SQL Server ビッグ データ クラスターでは、永続ボリュームを必要とするコンポーネントごとに指定したストレージ クラス名を使って、[永続ボリューム要求](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)が作成されます。 その後、ポッド内で該当の永続ボリュームがマウントされます。 

## <a name="configure-big-data-cluster-storage-settings"></a>ビッグ データ クラスターのストレージ設定を構成する

他のカスタマイズと同様に、各プールとコントロール プレーンに対して、展開時にクラスター構成ファイル内にストレージの設定を指定できます。 プールの仕様にストレージ構成の設定がない場合は、コントロール プレーンのストレージ設定が使用されます。 これは、仕様に含めることができるストレージ構成セクションのサンプルです。

```json
    "storage": 
    {
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
```

ビッグ データ クラスターの展開では、永続ストレージを使用して、さまざまなコンポーネントに対するデータ、メタデータ、およびログが格納されます。 展開の一部として作成された永続ボリューム要求のサイズをカスタマイズできます。 ベスト プラクティスとして、ストレージ クラスは、*Retain* [再要求ポリシー](https://kubernetes.io/docs/concepts/storage/storage-classes/#reclaim-policy)によってストレージ クラスを使用することをお勧めします。

> [!NOTE]
> CTP 3.2 では、展開後のストレージ構成設定は変更できません。 また、クラスター全体に対する `ReadWriteOnce` アクセス モードのみがサポートされます。

> [!WARNING]
> 永続ストレージを使用せずに実行すると、テスト環境内では動作しますが、クラスターが機能しなくなる可能性があります。 ポッドの再起動時には、クラスターのメタデータやユーザー データが完全に失われます。 この構成では実行しないことをお勧めします。 

[ストレージの構成](#config-samples)に関するセクションで、SQL Server ビッグ データ クラスターの展開に対するストレージ設定の構成方法の例をさらに紹介しています。

## <a name="aks-storage-classes"></a>AKS のストレージ クラス

AKS には、対応する動的プロビジョナーを備えた [2 つの組み込みのストレージ クラス](https://docs.microsoft.com/azure/aks/azure-disks-dynamic-pv)である **default** と **managed-premium** が付属しています。 永続ストレージが有効になったビッグ データ クラスターを展開する場合、それらのうちどちらかを指定するか、または、独自のストレージ クラスを作成することができます。 既定では、aks の *aks-dev-test* に対する組み込みのクラスター構成ファイルは、**default** ストレージ クラスを使用する永続ストレージ構成を備えています。

> [!WARNING]
> 組み込みのストレージ クラスである **default** および **managed-premium** によって作成された永続ボリュームには、*Delete* の再要求ポリシーが用意されています。 そのため、SQL Server ビッグ データ クラスターを削除すると、永続ボリューム要求が削除された後、永続ボリュームも削除されます。 [こちらの](https://docs.microsoft.com/azure/aks/concepts-storage#storage-classes)記事に示すように、*Retain* 再要求ポリシーによって **azure-disk** プロビジョナーを使用して、カスタム ストレージ クラスを作成することが可能です。


## <a name="minikube-storage-class"></a>Minikube のストレージ クラス

Minikube には、対応する動的プロビジョナーを備えた **standard** という組み込みのストレージ クラスが付属しています。 minikube 用の組み込みの構成ファイルである *minikube-dev-test* には、コントロール プレーンの仕様の中にストレージ構成の設定が含まれています。すべてのプールの仕様に、同じ設定が適用されます。 また、このファイルのコピーをカスタマイズして、minikube 上でビッグ データ クラスターの展開に使用することもできます。 カスタム ファイルを手動で編集して、実行するワークロードに対応するように、特定のプールに対する永続ボリューム要求のサイズを変更することができます。 または、*azdata* コマンドを使用した編集方法の例について、[ストレージの構成](#config-samples)に関するセクションを参照してください。

## <a name="kubeadm-storage-classes"></a>Kubeadm のストレージ クラス

Kubeadm には、組み込みのストレージ クラスは付属していません。 ローカル ストレージまたは [Rook](https://github.com/rook/rook) などの推奨のプロビジョナーを使用して、独自のストレージ クラスと永続ボリュームを作成する必要があります。 その場合は、構成したストレージ クラスを **className** に設定します。 

> [!NOTE]
>  *kubeadm kubeadm-dev-test* 用の組み込みの展開構成ファイルでは、データおよびログのストレージ用のストレージ クラス名は指定されていません。 展開の前に、構成ファイルをカスタマイズして className に値を設定する必要があります。そうしないと、展開前の検証でエラーになります。 また、展開には、ストレージ クラスの存在について確認する検証手順が含まれますが、必要な永続ボリュームについては含まれません。 クラスターのスケールに応じた十分なボリュームを確実に作成しておく必要があります。 CTP 3.1 では、既定のクラスター サイズとして、少なくとも 23 個のボリュームを作成する必要があります。 ローカル プロビジョナーを使用して永続ボリュームを作成する方法の例を、[こちら](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-big-data-cluster/deployment/kubeadm/ubuntu)に示しています。


## <a name="customize-storage-configurations-for-each-pool"></a>各プール用のストレージ構成をカスタマイズする

すべてのカスタマイズにおいて、使用する組み込みの構成ファイルのコピーを最初に作成しておく必要があります。 たとえば、次のコマンドでは、*aks-dev-test* 展開構成ファイルのコピーが `custom` という名前のサブディレクトリに作成されます。

```bash
azdata bdc config init --source aks-dev-test --target custom
```

これにより、 **bdc**と**control. json**が2つ作成されます。これらのファイルは、手動で編集するか、 **azdata bdc config**コマンドを使用してカスタマイズできます。 jsonpath および jsonpatch ライブラリを組み合わせて使用すると、構成ファイルを編集する手段を提供できます。


### <a id="config-samples"></a>ストレージ クラス名および要求サイズを構成する

既定では、クラスター内にプロビジョニングされた各ポッドに対応する、プロビジョニングされた永続ボリューム要求のサイズは、10 GB です。 この値は、クラスターの展開前にカスタム構成ファイル内で実行しているワークロードに合わせて更新できます。

次の例では、**control.jsaon** 内で永続ボリューム要求のサイズを 32Gi に更新しています。 プール レベルで上書きされない場合、この設定はすべてのプールに適用されます。

```bash
azdata bdc config replace --config-file custom/control.json --json-values "$.spec.storage.data.size=100Gi"
```

次の例は、**control.json** ファイルでのストレージ クラスの変更方法を示しています。

```bash
azdata bdc config replace --config-file custom/control.json --json-values "$.spec.storage.data.className=<yourStorageClassName>"
```

別のオプションとして、カスタム構成ファイルを手動で編集するか、次の例にあるように json パッチを使用して Storage プールに対するストレージ クラスを変更することもできます。 次のコンテンツを含む *patch.json* ファイルを作成します。

```json
{
  "patch": [
    {
      "op": "replace",
      "path": "$.spec.resources.storage-0.spec",
      "value": {
        "type":"Storage",
        "replicas":2,
        "storage":{
            "data":{
                    "size": "100Gi",
                    "className": "myStorageClass",
                    "accessMode":"ReadWriteOnce"
                    },
            "logs":{
                    "size":"32Gi",
                    "className":"myStorageClass",
                    "accessMode":"ReadWriteOnce"
                    }
                }
            }
        }
    ]
}
```

パッチ ファイルを適用します。 **azdata bdc config patch** コマンドを使用して、JSON パッチ ファイルでの変更を適用します。 次の例では、patch.json ファイルをターゲットの展開構成ファイルである custom.json に適用します。

```bash
azdata bdc config patch --config-file custom/bdc.json --patch-file ./patch.json
```

## <a name="next-steps"></a>次の手順

Kubernetes でのボリュームに関する詳細なドキュメントについては、[ボリュームに関する Kubernetes ドキュメント](https://kubernetes.io/docs/concepts/storage/volumes/)を参照してください。

SQL Server ビッグ データ クラスターの展開の詳細については、「[Kubernetes に SQL Server ビッグ データ クラスターを展開する方法](deployment-guidance.md)」を参照してください。

