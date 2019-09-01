---
title: azdata app リファレンス
titleSuffix: SQL Server big data clusters
description: azdata app コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: ec7e461705138905713b803e2f0f96934044d971
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70155300"
---
# <a name="azdata-app"></a>azdata app

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)] 

この記事は、 **azdata**のリファレンス記事です。 

## <a name="commands"></a>コマンド
|     |     |
| --- | --- |
[azdata app template](reference-azdata-app-template.md) | テンプレート。
[azdata app init](#azdata-app-init) | 新しいアプリケーションのスケルトンを開始します。
[azdata app create](#azdata-app-create) | アプリケーションを作成します。
[azdata app update](#azdata-app-update) | アプリケーションを更新します。
[azdata app list](#azdata-app-list) | アプリケーションを一覧表示します。
[azdata app delete](#azdata-app-delete) | アプリケーションを削除します。
[azdata app run](#azdata-app-run) | アプリケーションを実行します。
[azdata app describe](#azdata-app-describe) | アプリケーションについて記述します。
## <a name="azdata-app-init"></a>azdata app init
ランタイム環境に基づいて、新しいアプリケーションのスケルトンや仕様ファイルを開始するのに役立ちます。
```bash
azdata app init 
```
### <a name="examples"></a>使用例
新しいアプリケーション `spec.yaml` のみをスキャフォールディングします。
```bash
azdata app init --spec
```
スキャフォールディングは、 `r`テンプレートに基づいて新しい R アプリケーションアプリケーションスケルトンを作成します。
```bash
azdata app init --name reduce --template r
```
スキャフォールディングは、 `python`テンプレートに基づいて新しい Python アプリケーションアプリケーションスケルトンを作成します。
```bash
azdata app init --name reduce --template python
```
スキャフォールディングは、 `ssis`テンプレートに基づいて新しい SSIS アプリケーションアプリケーションスケルトンを作成します。
```bash
azdata app init --name reduce --template ssis            
```
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
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-app-create"></a>azdata app create
アプリケーションを作成します。
```bash
azdata app create 
```
### <a name="examples"></a>使用例
有効な spec.yaml デプロイ仕様を含むディレクトリから新しいアプリケーションを作成します。
```bash
azdata app create --spec /path/to/dir/with/spec/yaml
```
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
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-app-update"></a>azdata app update
アプリケーションを更新します。
```bash
azdata app update 
```
### <a name="examples"></a>使用例
有効な spec.yaml デプロイ仕様を含むディレクトリから既存のアプリケーションを更新します。
```bash
azdata app update --spec /path/to/dir/with/spec/yaml    
```
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
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-app-list"></a>azdata app list
アプリケーションを一覧表示します。
```bash
azdata app list 
```
### <a name="examples"></a>使用例
名前とバージョンでアプリケーションを一覧表示します。
```bash
azdata app list --name reduce  --version v1
```
名前ですべてのアプリケーションのバージョンを一覧表示します。
```bash
azdata app list --name reduce
```
名前ですべてのアプリケーションのバージョンを一覧表示します。
```bash
azdata app list
```
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
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-app-delete"></a>azdata app delete
アプリケーションを削除します。
```bash
azdata app delete 
```
### <a name="examples"></a>使用例
名前とバージョンでアプリケーションを削除します。
```bash
azdata app delete --name reduce --version v1    
```
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
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-app-run"></a>azdata app run
アプリケーションを実行します。
```bash
azdata app run 
```
### <a name="examples"></a>使用例
入力パラメーターなしでアプリケーションを実行します。
```bash
azdata app run --name reduce --version v1
```
1 つの入力パラメーターを使ってアプリケーションを実行します。
```bash
azdata app run --name reduce --version v1 --inputs x=10
```
複数の入力パラメーターを使ってアプリケーションを実行します。
```bash
azdata app run --name reduce --version v1 --inputs x=10,y5.6    
```
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
ログの詳細レベルを上げます。 完全なデバッグ ログを表示するには --debug を使います。
## <a name="azdata-app-describe"></a>azdata app describe
アプリケーションについて記述します。
```bash
azdata app describe 
```
### <a name="examples"></a>使用例
アプリケーションについて記述します。
```bash
azdata app describe --name reduce --version v1    
```
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
