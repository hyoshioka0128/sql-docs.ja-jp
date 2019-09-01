---
title: azdata app template リファレンス
titleSuffix: SQL Server big data clusters
description: azdata app template コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 07911616659a29df7f7fa6ce4d356a9c82789ae2
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70153227"
---
# <a name="azdata-app-template"></a>azdata app template

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)] 

この記事は、 **azdata**のリファレンス記事です。 

## <a name="commands"></a>コマンド
|     |     |
| --- | --- |
[azdata app template list](#azdata-app-template-list) | サポートされているテンプレートを取得します。
[azdata app template pull](#azdata-app-template-pull) | サポートされているテンプレートをダウンロードします。
## <a name="azdata-app-template-list"></a>azdata app template list
指定した [URL] github リポジトリにある、サポートされているテンプレートを取得します。
```bash
azdata app template list 
```
### <a name="examples"></a>使用例
既定のテンプレート リポジトリの場所にあるすべてのテンプレートを取得します。
```bash
azdata app template list
```
別のリポジトリの場所にあるすべてのテンプレートを取得します。
```bash
azdata app template list --url https://github.com/diffrent/templates.git
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
## <a name="azdata-app-template-pull"></a>azdata app template pull
指定した [URL] github リポジトリにある、サポートされているテンプレートをダウンロードします。
```bash
azdata app template pull 
```
### <a name="examples"></a>使用例
既定のテンプレート リポジトリの場所にあるすべてのテンプレートをダウンロードします。
```bash
azdata app template pull
```
別のリポジトリの場所にあるすべてのテンプレートをダウンロードします。
```bash
azdata app template list --url https://github.com/diffrent/templates.git
```
名前を指定して個々のテンプレートをダウンロードします。
```bash
azdata app template pull --name ssis
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
