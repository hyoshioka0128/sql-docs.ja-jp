---
title: azdata arc postgres server リファレンス
titleSuffix: SQL Server big data clusters
description: azdata arc postgres server コマンドに関するリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 04/06/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 09c873049d81a62ae53f882396fb0230e11ce155
ms.sourcegitcommit: 7e5414d8005e7b07e537417582fb4132b5832ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556885"
---
# <a name="azdata-arc-postgres-server"></a>azdata arc postgres server

[!INCLUDE [azure-data-cli-azdata](../../includes/azure-data-cli-azdata.md)] への適用

以下の記事では、**azdata** ツールの **sql** コマンドに関するリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata arc postgres server create](#azdata-arc-postgres-server-create) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成します。
[azdata arc postgres server edit](#azdata-arc-postgres-server-edit) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を編集します。
[azdata arc postgres server delete](#azdata-arc-postgres-server-delete) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除します。
[azdata arc postgres server show](#azdata-arc-postgres-server-show) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの詳細を表示します。
[azdata arc postgres server list](#azdata-arc-postgres-server-list) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを一覧表示します。
[azdata arc postgres server config](reference-azdata-arc-postgres-server-config.md) | 構成コマンド。
## <a name="azdata-arc-postgres-server-create"></a>azdata arc postgres server create
サーバー グループのパスワードを設定するには、環境変数 AZDATA_PASSWORD を設定してください
```bash
azdata arc postgres server create --name -n 
                                  [--path]  
                                  
[--cores-limit -cl]  
                                  
[--cores-request -cr]  
                                  
[--memory-limit -ml]  
                                  
[--memory-request -mr]  
                                  
[--storage-class-data -scd]  
                                  
[--storage-class-logs -scl]  
                                  
[--storage-class-backups -scb]  
                                  
[--volume-claim-mounts -vcm]  
                                  
[--extensions]  
                                  
[--volume-size-data -vsd]  
                                  
[--volume-size-logs -vsl]  
                                  
[--volume-size-backups -vsb]  
                                  
[--workers -w]  
                                  
[--engine-version -ev]  
                                  
[--no-external-endpoint]  
                                  
[--port]  
                                  
[--no-wait]  
                                  
[--engine-settings -e]
```
### <a name="examples"></a>使用例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成します。
```bash
azdata arc postgres server create -n pg1
```
エンジン設定を使用して Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成します。 次の例はいずれも有効です。
```bash
azdata arc postgres server create -n pg1 --engine-settings "key1=val1"
azdata arc postgres server create -n pg1 --engine-settings "key2=val2"
```
ボリューム要求マウントを使用して PostgreSQL サーバー グループを作成します。
```bash
azdata arc postgres server create -n pg1 --volume-claim-mounts backup-pvc:backup
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--path`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのソース json ファイルのパス。 これは省略可能です。
#### `--cores-limit -cl`
ノードごとに使用できる Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの CPU コアの最大数。 少数のコアがサポートされています。
#### `--cores-request -cr`
サービスをスケジュールするためにノードごとに使用できる必要がある CPU コアの最小数。 少数のコアがサポートされています。
#### `--memory-limit -ml`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメモリ制限。数値の後に Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト) が続きます。
#### `--memory-request -mr`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメモリ要求。数値の後に Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト) が続きます。
#### `--storage-class-data -scd`
データ永続ボリュームに使用するストレージ クラス。
#### `--storage-class-logs -scl`
ログ永続ボリュームに使用するストレージ クラス。
#### `--storage-class-backups -scb`
バックアップ永続ボリュームに使用するストレージ クラス。
#### `--volume-claim-mounts -vcm`
ボリューム要求マウントのコンマ区切りの一覧。 ボリューム要求マウントは、(同じ名前空間内の) 既存の永続的ボリューム要求と、コロンで区切られたボリュームの種類 (およびボリュームの種類に応じたオプションのメタデータ) のペアです。永続的ボリュームは、PostgreSQL サーバー グループの各ポッドにマウントされます。 マウント パスは、ボリュームの種類によって異なる場合があります。
#### `--extensions`
起動時に読み込む必要のある Postgres 拡張機能のコンマ区切りのリスト。 サポートされている値については、Postgres のドキュメントを参照してください。
#### `--volume-size-data -vsd`
データに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-logs -vsl`
ログに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--volume-size-backups -vsb`
バックアップに使用するストレージ ボリュームのサイズを示す正の数値と、その後の Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト)。
#### `--workers -w`
サーバー グループにプロビジョニングするワーカー ノードの数。 プレビューでは、ワーカー ノードの数を減らすことはサポートされていません。 詳細については、ドキュメントを参照してください。
#### `--engine-version -ev`
11 または 12 にする必要があります。 既定値は 12 です。
`12`
#### `--no-external-endpoint`
指定した場合、外部サービスは作成されません。 それ以外の場合、データ コントローラーと同じサービスの種類を使用して外部サービスが作成されます。
#### `--port`
省略可能。
#### `--no-wait`
指定した場合、コマンドは戻る前に、インスタンスが準備完了状態になるのを待機しません。
#### `--engine-settings -e`
"key1=val1, key2=val2" という形式の、Postgres エンジン設定のコンマ区切りリスト。
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
## <a name="azdata-arc-postgres-server-edit"></a>azdata arc postgres server edit
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を編集します。
```bash
azdata arc postgres server edit --name -n 
                                [--path]  
                                
[--workers -w]  
                                
[]  
                                
[--cores-limit -cl]  
                                
[--cores-request -cr]  
                                
[--memory-limit -ml]  
                                
[--memory-request -mr]  
                                
[--extensions]  
                                
[--port]  
                                
[--no-wait]  
                                
[--engine-settings -e]  
                                
[--replace-engine-settings -re]  
                                
[--admin-password]
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの構成を編集します。
```bash
azdata arc postgres server edit --src ./spec.json -n pg1
```
エンジン設定を使用して Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを編集します。
```bash
azdata arc postgres server edit -n pg1 --engine-settings "key2=val2"
```
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを編集し、既存のエンジン設定を新しい設定の key1=val1 に置き換えます。
```bash
azdata arc postgres server edit -n pg1 --engine-settings "key1=val1" --replace-engine-settings
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
編集する Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。 インスタンスを配置するときに使用した名前を変更することはできません。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--path`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのソース json ファイルのパス。 これは省略可能です。
#### `--workers -w`
サーバー グループにプロビジョニングするワーカー ノードの数。 プレビューでは、ワーカー ノードの数を減らすことはサポートされていません。 詳細については、ドキュメントを参照してください。
#### <a name=""></a>``
エンジンのバージョンは変更できません。 エンジン バージョンが異なる 2 つのサーバー グループの名前が同じである場合に、--engine-version を --name と組み合わせて使用すると、PostgreSQL Hyperscale サーバー グループを識別できます。 --engine-version は省略可能で、サーバー グループの識別に使用する場合、11 または 12 を使用する必要があります。
#### `--cores-limit -cl`
ノードごとに使用できる Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの CPU コアの最大数。少数のコアがサポートされています。 cores_limit を削除するには、その値を空の文字列として指定します。
#### `--cores-request -cr`
サービスをスケジュールするためにノードごとに使用できる必要がある CPU コアの最小数。少数のコアがサポートされています。 cores_request を削除するには、その値を空の文字列として指定します。
#### `--memory-limit -ml`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメモリ制限。数値の後に Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト) が続きます。 memory_limit を削除するには、その値を空の文字列として指定します。
#### `--memory-request -mr`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのメモリ要求。数値の後に Ki (キロバイト)、Mi (メガバイト)、または Gi (ギガバイト) が続きます。 memory_request を削除するには、その値を空の文字列として指定します。
#### `--extensions`
起動時に読み込む必要のある Postgres 拡張機能のコンマ区切りのリスト。 サポートされている値については、Postgres のドキュメントを参照してください。
#### `--port`
省略可能。
#### `--no-wait`
指定した場合、コマンドは戻る前に、インスタンスが準備完了状態になるのを待機しません。
#### `--engine-settings -e`
"key1=val1, key2=val2" という形式の、Postgres エンジン設定のコンマ区切りリスト。 指定した設定は、既存の設定とマージされます。 設定を削除するには、"removedKey=" のように空の値を指定します。 再起動が必要なエンジン設定を変更すると、すぐに設定を適用するためにサービスが再起動されます。
#### `--replace-engine-settings -re`
--engine-settings と共に指定すると、既存のすべてのカスタム エンジンの設定が設定と値の新しいセットに置き換えられます。
#### `--admin-password`
指定すると、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの管理者パスワードは、AZDATA_PASSWORD 環境変数が存在する場合はその値に設定され、それ以外の場合はプロンプトが表示されます。
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
## <a name="azdata-arc-postgres-server-delete"></a>azdata arc postgres server delete
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除します。
```bash
azdata arc postgres server delete --name -n 
                                  []  
                                  
[--force -f]
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを削除します。
```bash
azdata arc postgres server delete -n pg1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### <a name=""></a>``
エンジン バージョンが異なる 2 つのサーバー グループの名前が同じである場合に、--engine-version を --name と組み合わせて使用すると、PostgreSQL Hyperscale サーバー グループを識別できます。 --engine-version は省略可能で、サーバー グループの識別に使用する場合、11 または 12 を使用する必要があります。
#### `--force -f`
確認せずに Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを強制的に削除します。
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
## <a name="azdata-arc-postgres-server-show"></a>azdata arc postgres server show
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの詳細を表示します。
```bash
azdata arc postgres server show --name -n 
                                []  
                                
[--path -p]
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの詳細を表示します。
```bash
azdata arc postgres server show -n pg1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### <a name=""></a>``
エンジン バージョンが異なる 2 つのサーバー グループの名前が同じである場合に、--engine-version を --name と組み合わせて使用すると、PostgreSQL Hyperscale サーバー グループを識別できます。 --engine-version は省略可能で、サーバー グループの識別に使用する場合、11 または 12 を使用する必要があります。
#### `--path -p`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの完全な仕様を書き込む先のパス。 省略した場合、仕様は標準出力に書き込まれます。
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
## <a name="azdata-arc-postgres-server-list"></a>azdata arc postgres server list
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを一覧表示します。
```bash
azdata arc postgres server list 
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを一覧表示します。
```bash
azdata arc postgres server list
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

