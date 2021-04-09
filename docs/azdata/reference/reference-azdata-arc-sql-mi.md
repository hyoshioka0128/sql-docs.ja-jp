---
title: azdata arc sql mi リファレンス
titleSuffix: SQL Server big data clusters
description: azdata arc sql mi コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 04/06/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 2b6a8d15233198dbcdd3aad064e5f7668f39eaef
ms.sourcegitcommit: 7e5414d8005e7b07e537417582fb4132b5832ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556816"
---
# <a name="azdata-arc-sql-mi"></a>azdata arc sql mi

[!INCLUDE [azure-data-cli-azdata](../../includes/azure-data-cli-azdata.md)] への適用

以下の記事では、**azdata** ツールの **sql** コマンドに関するリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata arc sql mi create](#azdata-arc-sql-mi-create) | SQL マネージド インスタンスを作成します。
[azdata arc sql mi edit](#azdata-arc-sql-mi-edit) | SQL マネージド インスタンスの構成を編集します。
[azdata arc sql mi delete](#azdata-arc-sql-mi-delete) | SQL マネージド インスタンスを削除します。
[azdata arc sql mi show](#azdata-arc-sql-mi-show) | SQL マネージド インスタンスの詳細を表示します。
[azdata arc sql mi list](#azdata-arc-sql-mi-list) | SQL マネージド インスタンスの一覧を表示します。
[azdata arc sql mi config](reference-azdata-arc-sql-mi-config.md) | 構成コマンド。
## <a name="azdata-arc-sql-mi-create"></a>azdata arc sql mi create
SQL マネージド インスタンスのパスワードを設定するには、環境変数 AZDATA_PASSWORD を設定してください
```bash
azdata arc sql mi create --name -n 
                         [--path]  
                         
[--replicas]  
                         
[--cores-limit -cl]  
                         
[--cores-request -cr]  
                         
[--memory-limit -ml]  
                         
[--memory-request -mr]  
                         
[--storage-class-data -scd]  
                         
[--storage-class-logs -scl]  
                         
[--storage-class-data-logs -scdl]  
                         
[--storage-class-backups -scb]  
                         
[--volume-size-data -vsd]  
                         
[--volume-size-logs -vsl]  
                         
[--volume-size-data-logs -vsdl]  
                         
[--volume-size-backups -vsb]  
                         
[--no-external-endpoint]  
                         
[--no-wait]
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスを作成します。
```bash
azdata arc sql mi create -n sqlmi1
```
HA シナリオで、3 つのレプリカを持つ SQL マネージド インスタンスを作成します。
```bash
azdata arc sql mi create -n sqlmi2 --replicas 3
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
SQL マネージド インスタンスの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--path`
SQL マネージド インスタンスの json ファイルに対する src ファイルへのパス。
#### `--replicas`
高可用性の目的でデプロイされるレプリカの数。 使用できる値は '3' または '1' で、既定値は '1' です。
#### `--cores-limit -cl`
マネージド インスタンスのコア数の制限 (整数)。
#### `--cores-request -cr`
マネージド インスタンスのコア数の要求 (整数)。
#### `--memory-limit -ml`
マネージド インスタンスの容量の制限 (整数)。
#### `--memory-request -mr`
マネージド インスタンスの容量に対する要求 (GB 単位の整数のメモリー量)。
#### `--storage-class-data -scd`
データ (.mdf) に使用するストレージ クラス。 値を指定しないと、ストレージ クラスは指定されず、その結果、Kubernetes によって既定のストレージ クラスが使用されます。
#### `--storage-class-logs -scl`
ログ (/var/log) に使用するストレージ クラス。 値を指定しないと、ストレージ クラスは指定されず、その結果、Kubernetes によって既定のストレージ クラスが使用されます。
#### `--storage-class-data-logs -scdl`
データベース ログ (.ldf) に使用するストレージ クラス。 値を指定しないと、ストレージ クラスは指定されず、その結果、Kubernetes によって既定のストレージ クラスが使用されます。
#### `--storage-class-backups -scb`
バックアップ (/var/opt/mssql/backups) に使用するストレージ クラス。 値を指定しないと、ストレージ クラスは指定されず、その結果、Kubernetes によって既定のストレージ クラスが使用されます。
#### `--volume-size-data -vsd`
データに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-logs -vsl`
ログに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-data-logs -vsdl`
データ ログに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-backups -vsb`
バックアップに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--no-external-endpoint`
指定した場合、外部サービスは作成されません。 それ以外の場合、データ コントローラーと同じサービスの種類を使用して外部サービスが作成されます。
#### `--no-wait`
指定した場合、コマンドは戻る前に、インスタンスが準備完了状態になるのを待機しません。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-arc-sql-mi-edit"></a>azdata arc sql mi edit
SQL マネージド インスタンスの構成を編集します。
```bash
azdata arc sql mi edit --name -n 
                       [--path]  
                       
[--cores-limit -cl]  
                       
[--cores-request -cr]  
                       
[--memory-limit -ml]  
                       
[--memory-request -mr]  
                       
[--no-wait]
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスの構成を編集します。
```bash
azdata arc sql mi edit --path ./spec.json -n sqlmi1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
編集されている SQL マネージド インスタンスの名前。 インスタンスを配置するときに使用した名前を変更することはできません。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--path`
SQL マネージド インスタンスの json ファイルに対する src ファイルへのパス。
#### `--cores-limit -cl`
マネージド インスタンスのコア数の制限 (整数)。
#### `--cores-request -cr`
マネージド インスタンスのコア数の要求 (整数)。
#### `--memory-limit -ml`
マネージド インスタンスの容量の制限 (整数)。
#### `--memory-request -mr`
マネージド インスタンスの容量に対する要求 (GB 単位の整数のメモリー量)。
#### `--no-wait`
指定した場合、コマンドは戻る前に、インスタンスが準備完了状態になるのを待機しません。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-arc-sql-mi-delete"></a>azdata arc sql mi delete
SQL マネージド インスタンスを削除します。
```bash
azdata arc sql mi delete --name -n 
                         
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスを削除します。
```bash
azdata arc sql mi delete -n sqlmi1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
削除する SQL マネージド インスタンスの名前。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-arc-sql-mi-show"></a>azdata arc sql mi show
SQL マネージド インスタンスの詳細を表示します。
```bash
azdata arc sql mi show --name -n 
                       [--path -p]
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスの詳細を表示します。
```bash
azdata arc sql mi show -n sqlmi1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
表示する SQL マネージド インスタンスの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--path -p`
SQL マネージド インスタンスの完全な仕様が書き込まれるパス。 省略した場合、仕様は標準出力に書き込まれます。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-arc-sql-mi-list"></a>azdata arc sql mi list
SQL マネージド インスタンスの一覧を表示します。
```bash
azdata arc sql mi list 
```
### <a name="examples"></a>使用例
SQL マネージド インスタンスの一覧を表示します。
```bash
azdata arc sql mi list
```
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。

## <a name="next-steps"></a>次のステップ

他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。 

**azdata** ツールをインストールする方法の詳細については、「[azdata のインストール](..\install\deploy-install-azdata.md)」を参照してください。

