---
title: azdata arc dc リファレンス
titleSuffix: SQL Server big data clusters
description: azdata arc dc コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 04/06/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 1f1464d84655b942f294f71af53cf55b226372c5
ms.sourcegitcommit: 7e5414d8005e7b07e537417582fb4132b5832ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106557435"
---
# <a name="azdata-arc-dc"></a>azdata arc dc

[!INCLUDE [azure-data-cli-azdata](../../includes/azure-data-cli-azdata.md)] への適用

以下の記事では、**azdata** ツールの **sql** コマンドに関するリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata arc dc create](#azdata-arc-dc-create) | データ コントローラーを作成します。
[azdata arc dc delete](#azdata-arc-dc-delete) | データ コントローラーを削除します。
[azdata arc dc endpoint](reference-azdata-arc-dc-endpoint.md) | エンドポイント コマンド。
[azdata arc dc status](reference-azdata-arc-dc-status.md) | 状態コマンド。
[azdata arc dc config](reference-azdata-arc-dc-config.md) | 構成コマンド。
[azdata arc dc debug](reference-azdata-arc-dc-debug.md) | デバッグ コマンド。
[azdata arc dc export](#azdata-arc-dc-export) | メトリック、ログ、使用状況をエクスポートします。
[azdata arc dc upload](#azdata-arc-dc-upload) | エクスポートされたデータ ファイルをアップロードします。
## <a name="azdata-arc-dc-create"></a>azdata arc dc create
データ コントローラーを作成します。システムに Kube 構成と環境変数 ['AZDATA_USERNAME', 'AZDATA_PASSWORD'] が必要です。
```bash
azdata arc dc create 
```
### <a name="examples"></a>使用例
データ コントローラーの配置。
```bash
azdata arc dc create
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
## <a name="azdata-arc-dc-delete"></a>azdata arc dc delete
データ コントローラーを削除します。Kube 構成がシステムに必要です。
```bash
azdata arc dc delete 
```
### <a name="examples"></a>使用例
データ コントローラーの配置。
```bash
azdata arc dc delete
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
## <a name="azdata-arc-dc-export"></a>azdata arc dc export
メトリック、ログ、または使用状況をファイルにエクスポートします。
```bash
azdata arc dc export 
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
## <a name="azdata-arc-dc-upload"></a>azdata arc dc upload
データ コントローラーからエクスポートされたデータ ファイルを Azure にアップロードします。
```bash
azdata arc dc upload 
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

