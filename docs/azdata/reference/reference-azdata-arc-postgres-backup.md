---
title: azdata arc postgres backup リファレンス
titleSuffix: SQL Server big data clusters
description: azdata arc postgres backup コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 04/06/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: e303d6549898084c791d7dd203533d9bd18b50cd
ms.sourcegitcommit: 7e5414d8005e7b07e537417582fb4132b5832ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556955"
---
# <a name="azdata-arc-postgres-backup"></a>azdata arc postgres backup

[!INCLUDE [azure-data-cli-azdata](../../includes/azure-data-cli-azdata.md)] への適用

以下の記事では、**azdata** ツールの **sql** コマンドに関するリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata arc postgres backup create](#azdata-arc-postgres-backup-create) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを作成します。
[azdata arc postgres backup delete](#azdata-arc-postgres-backup-delete) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを削除します。
[azdata arc postgres backup list](#azdata-arc-postgres-backup-list) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを一覧表示します。
[azdata arc postgres backup restore](#azdata-arc-postgres-backup-restore) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを復元します。
## <a name="azdata-arc-postgres-backup-create"></a>azdata arc postgres backup create
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを作成します。
```bash
azdata arc postgres backup create --server-name -sn 
                                  [--name -n]  
                                  
[--no-wait]
```
### <a name="examples"></a>使用例
サービス 'pg' のバックアップを作成します。
```bash
azdata arc postgres backup create -sn pg
```
サービス 'pg' の名前付きバックアップを作成します。
```bash
azdata arc postgres backup create -sn pg -n backup1
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--server-name -sn`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--name -n`
バックアップの名前。 このパラメーターは省略可能です。
#### `--no-wait`
指定した場合、コマンドはバックアップの完了を待機せずに戻ります。
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
## <a name="azdata-arc-postgres-backup-delete"></a>azdata arc postgres backup delete
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを削除します。
```bash
azdata arc postgres backup delete --server-name -sn 
                                  [--name -n]  
                                  
[-id]
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを削除します。
```bash
azdata arc postgres backup delete -sn pg -id e07dd3940e374bd9acbc86869cbc123d
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--server-name -sn`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--name -n`
バックアップの名前。 このパラメーターは -id と同時に指定できません。
#### `-id`
削除するバックアップの ID。 このパラメーターは --name と同時に指定できません。
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
## <a name="azdata-arc-postgres-backup-list"></a>azdata arc postgres backup list
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを一覧表示します。
```bash
azdata arc postgres backup list --server-name -sn 
                                
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを一覧表示します。
```bash
azdata arc postgres backup list -sn pg
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--server-name -sn`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
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
## <a name="azdata-arc-postgres-backup-restore"></a>azdata arc postgres backup restore
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップを復元します。
```bash
azdata arc postgres backup restore --server-name -sn 
                                   [--backup-id -id]  
                                   
[--source-server-name -ssn]  
                                   
[--time -t]
```
### <a name="examples"></a>例
ID によりバックアップを復元します
```bash
azdata arc postgres backup restore -sn pg -id 123e4567e89b12d3a456426655440000
```
時間によりバックアップを復元します (ポイントインタイム リストア)
```bash
azdata arc postgres backup restore -sn pg-dst -ssn pg-src --time "2020-11-18 17:25:34Z"
```
期間によりバックアップを復元します (ポイントインタイム リストア)
```bash
azdata arc postgres backup restore -sn pg-dst -ssn pg-src --time 1d
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--server-name -sn`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--backup-id -id`
バックアップの ID。 指定しない場合、取得された最新のバックアップが復元されます。
#### `--source-server-name -ssn`
ソース Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。 指定しない場合、--server-name で識別されるサーバー グループにバックアップが復元されます。
#### `--time -t`
復元する特定の時点。タイムスタンプまたは数字とサフィックス (m は分、h は時間、d は日、w は週) のいずれかを指定します。 例: 1.5h は、90 分前に戻ります。 指定する場合は、--source-server-name を指定して、別の Azure Arc 対応 PostgreSQL Hyperscale サーバー グループからバックアップを復元する必要があります。
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

