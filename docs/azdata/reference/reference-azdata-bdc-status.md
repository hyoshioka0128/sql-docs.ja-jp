---
title: azdata bdc status リファレンス
titleSuffix: SQL Server big data clusters
description: azdata bdc status コマンドに関するリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 09/22/2020
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 2fec875f3093b3419574871cf8bca59b2be1ec2f
ms.sourcegitcommit: d56f1eca807c55cf606a6316f3872585f014fec1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90914746"
---
# <a name="azdata-bdc-status"></a>azdata bdc status

`azdata` への適用

以下の記事では、**azdata** ツールの **sql** コマンドに関するリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata bdc status show](#azdata-bdc-status-show) | BDC の状態を表示します。
## <a name="azdata-bdc-status-show"></a>azdata bdc status show
BDC の状態を表示します。
```bash
azdata bdc status show [--resource -r] 
                       [--all -a]
```
### <a name="examples"></a>例
ユーザーがログインしている BDC の状態。
```bash
azdata bdc status show
```
リソースのすべてのインスタンスを含む BDC の状態。
```bash
azdata bdc status show --all
```
制御リソースが含まれるサービスの BDC の状態。
```bash
azdata bdc status show --resource control
```
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--resource -r`
このリソースに関連付けられているサービスを取得します。
#### `--all -a`
サービス内の各リソースのすべてのインスタンスを表示します。
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

