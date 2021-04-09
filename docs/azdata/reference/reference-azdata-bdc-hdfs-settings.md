---
title: azdata bdc hdfs settings のリファレンス
titleSuffix: SQL Server big data clusters
description: azdata bdc hdfs settings コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 04/06/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 128035616f3cd7ea1cb53a4bc8042db59bc09e03
ms.sourcegitcommit: 7e5414d8005e7b07e537417582fb4132b5832ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556695"
---
# <a name="azdata-bdc-hdfs-settings"></a>azdata bdc hdfs settings

[!INCLUDE [azure-data-cli-azdata](../../includes/azure-data-cli-azdata.md)] への適用

次の記事では、**azdata** ツール内の **hdfs settings** コマンドのリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata bdc hdfs settings set](#azdata-bdc-hdfs-settings-set) | spark service-scope 設定を設定します。
[azdata bdc hdfs settings show](#azdata-bdc-hdfs-settings-show) | 指定されたリソースの hdfs service-scope 設定と、オプションで hdfs 設定を表示します
## <a name="azdata-bdc-hdfs-settings-set"></a>azdata bdc hdfs settings set
service-scoped または resource-scoped 設定を設定できます。 設定の完全な名前と値を指定します。 設定は、実行中の BDC には適用されません。 そのためには、apply を実行します。
```bash
azdata bdc hdfs settings set [--resources -r] 
                             [--settings -s]
```
### <a name="examples"></a>例
ボリュームの readthrough を無効にします。
```bash
azdata bdc hdfs settings set --settings hdfs-site dfs.datanode.provided.volume.readthrough=false
```
記憶域プールの既定のブロック レプリケーション係数を 3 に設定します。
```bash
azdata bdc hdfs settings set --settings hdfs-site.dfs.replication=3 –resources storage-0
```
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--resources -r`
指定されたリソースの指定された設定を設定します。 リソースはコンマ区切りリストとして指定できます。
#### `--settings -s`
指定された設定の構成値を設定します。 コンマ区切り一覧を使用して複数の設定を設定できます。
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
## <a name="azdata-bdc-hdfs-settings-show"></a>azdata bdc hdfs settings show
BDC の hdfs service-scope (オプションで resource-scope) の設定を表示します。 既定では、このコマンドは、ユーザーが構成した service-scope 設定を表示します。 フィルターを使用して、すべての設定 (システム管理および構成可能)、構成可能な設定、または保留中の設定を表示できます。 特定の service-scope または resource-scope 設定を表示するには、設定名を指定します。 サービスの一部としてすべてのリソースの設定を表示するには、"recursive" を使用します。
```bash
azdata bdc hdfs settings show [--resources -r] 
                              [--settings -s]  
                              
[--filter-option -f]  
                              
[--recursive -rec]  
                              
[--include-details -i]  
                              
[--description -d]
```
### <a name="examples"></a>例
ユーザーが構成した HDFS service-scope 設定を表示します。
```bash
azdata bdc hdfs settings show
```
記憶域プール内の HDFS のレプリケーション係数を表示します。
```bash
azdata bdc hdfs settings show --settings hdfs-site.dfs.replication --resources storage-0
```
HDFS service-scope および resource-scope 設定の保留中の設定変更を表示します。
```bash
azdata bdc hdfs settings show --filter-options=pending --recursive
```
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--resources -r`
指定されたリソースの設定情報を表示します。 リソースはコンマ区切りリストとして指定できます。
#### `--settings -s`
指定された設定名の情報を表示します。
#### `--filter-option -f`
'ユーザーが構成した' 設定だけでなく、表示されるクラスタースコープの設定をフィルター処理します。 フィルターを使用して、すべての設定 (システム管理およびユーザー構成可能)、すべての構成可能な設定、または保留中の設定を表示できます。
`userConfigured`
#### `--recursive -rec`
指定されたスコープ (service または service-resource) の設定情報と、すべての下位スコープ コンポーネント (resources) を表示します。
#### `--include-details -i`
表示するように選択された設定に関する追加の詳細が含まれます。
#### `--description -d`
設定の説明が含まれます。
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
