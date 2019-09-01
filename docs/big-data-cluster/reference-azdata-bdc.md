---
title: azdata bdc リファレンス
titleSuffix: SQL Server big data clusters
description: azdata bdc コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 408b3c2d55d5e2515a2df979cd54b380a0d54704
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70155140"
---
# <a name="azdata-bdc"></a>azdata bdc

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]  

この記事は、 **azdata**のリファレンス記事です。 

## <a name="commands"></a>コマンド
|     |     |
| --- | --- |
[azdata bdc create](#azdata-bdc-create) | ビッグ データ クラスターを作成します。
[azdata bdc delete](#azdata-bdc-delete) | ビッグ データ クラスターを削除します。
[azdata bdc config](reference-azdata-bdc-config.md) | 構成コマンド。
[azdata bdc endpoint](reference-azdata-bdc-endpoint.md) | エンドポイント コマンド。
[azdata bdc debug](reference-azdata-bdc-debug.md) | デバッグ コマンド。
[azdata bdc status](reference-azdata-bdc-status.md) | BDC のステータスコマンド。
[azdata bdc control](reference-azdata-bdc-control.md) | サービスコマンドを制御します。
[azdata bdc sql](reference-azdata-bdc-sql.md) | Sql サービスコマンド。
[azdata bdc hdfs](reference-azdata-bdc-hdfs.md) | Hdfs サービスコマンド。
[azdata bdc spark](reference-azdata-bdc-spark.md) | Spark サービスのコマンド。
[azdata bdc ゲートウェイ](reference-azdata-bdc-gateway.md) | ゲートウェイサービスのコマンド。
[azdata bdc アプリ](reference-azdata-bdc-app.md) | App service のコマンド。
[azdata bdc hdfs](reference-azdata-bdc-hdfs.md) | HDFS モジュールには、HDFS ファイル システムにアクセスするコマンドが用意されています。
[azdata bdc spark](reference-azdata-bdc-spark.md) | Spark コマンドを使用してセッション、ステートメント、およびバッチを作成および管理することで、ユーザーは Spark システムと対話できます。
## <a name="azdata-bdc-create"></a>azdata bdc create
SQL Server ビッグデータクラスターを作成する-Kubernetes 構成は、次の環境変数 [' CONTROLLER_USERNAME '、' CONTROLLER_PASSWORD '、' MSSQL_SA_PASSWORD '、' KNOX_PASSWORD '] と共に、システムに必要です。
```bash
azdata bdc create [--name -n] 
                  [--config-profile -c]  
                  [--accept-eula -a]  
                  [--node-label -l]  
                  [--force -f]
```
### <a name="examples"></a>使用例
ガイド付きの BDC 展開のエクスペリエンス。必要な値の入力を求めるプロンプトが表示されます。
```bash
azdata bdc create
```
引数を使用した BDC 展開。
```bash
azdata bdc create --accept-eula yes --config-profile aks-dev-test
```
既定の名前ではなくプロファイルに名前を指定した BDC 展開。
```bash
azdata bdc create --name <cluster_name> --accept-eula yes --config-profile aks-dev-test --force
```
引数を使用した BDC 展開。--force フラグが使用されるため、プロンプトは表示されません。
```bash
azdata bdc create --accept-eula yes --config-profile aks-dev-test --force
```
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--name -n`
ビッグ データ クラスターの名前。kubernetes 名前空間に使用されます。
#### `--config-profile -c`
クラスターのデプロイに使用されるビッグデータクラスターの構成プロファイル: [' aks ', ' kubeadm ', ' minikube ', ' kubeadm ', ' ', ' ']
#### `--accept-eula -a`
Do you accept the license terms? (ライセンス条項に同意しますか?) [yes/no]. ([はい/いいえ]。) この引数を使用しない場合は、環境変数 ACCEPT_EULA を 'yes' に設定できます。 この製品のライセンス条項は https://aka.ms/azdata-eula と https://go.microsoft.com/fwlink/?LinkId=2002534 で表示できます。
#### `--node-label -l`
ビッグ データ クラスター ノードのラベル。展開先のノードを指定するために使用されます。
#### `--force -f`
強制的に作成すると、ユーザーは値の入力を求められず、すべての問題が stderr の一部として出力されます。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-bdc-delete"></a>azdata bdc delete
SQL Server ビッグデータクラスターを削除する-Kubernetes 構成がシステムで必要です。
```bash
azdata bdc delete --name -n 
                  [--force -f]
```
### <a name="examples"></a>使用例
BDC の削除。
```bash
azdata bdc delete --name <cluster_name>
```
### <a name="required-parameters"></a>必要なパラメーター
#### `--name -n`
ビッグ データ クラスターの名前。kubernetes 名前空間に使用されます。
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--force -f`
ビッグ データ クラスターを強制的に削除します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。

## <a name="next-steps"></a>次の手順

- 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。 

- **azdata** ツールをインストールする方法の詳細については、[SQL Server 2019 ビッグ データ クラスターを管理する azdata のインストール](deploy-install-azdata.md)に関するページを参照してください。
