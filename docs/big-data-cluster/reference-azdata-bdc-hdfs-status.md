---
title: azdata bdc hdfs 状態リファレンス
titleSuffix: SQL Server big data clusters
description: Azdata bdc hdfs ステータスコマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 3853e23a0def787a65febf142de4c1d8d8216df7
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70158087"
---
# <a name="azdata-bdc-hdfs-status"></a>azdata bdc hdfs の状態

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]  

この記事は、 **azdata**のリファレンス記事です。 

## <a name="commands"></a>コマンド
|     |     |
| --- | --- |
[azdata bdc hdfs の状態の表示](#azdata-bdc-hdfs-status-show) | Hdfs サービスの状態。
## <a name="azdata-bdc-hdfs-status-show"></a>azdata bdc hdfs の状態の表示
Hdfs サービスの状態。
```bash
azdata bdc hdfs status show [--resource -r] 
                            [--all -a]
```
### <a name="examples"></a>使用例
Hdfs サービスの状態を取得します。
```bash
azdata bdc hdfs status show
```
すべてのインスタンスを含む hdfs サービスの状態を取得します。
```bash
azdata bdc hdfs status show --all
```
Hdfs サービス内のストレージリソースの状態を取得します。
```bash
azdata bdc hdfs status show --resource storage-0
```
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--resource -r`
このサービスでこのリソースを取得します。
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
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。

## <a name="next-steps"></a>次の手順

- 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。 

- **azdata** ツールをインストールする方法の詳細については、[SQL Server 2019 ビッグ データ クラスターを管理する azdata のインストール](deploy-install-azdata.md)に関するページを参照してください。
