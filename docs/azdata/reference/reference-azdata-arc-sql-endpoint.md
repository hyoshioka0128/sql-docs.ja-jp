---
title: azdata arc sql endpoint リファレンス
titleSuffix: SQL Server big data clusters
description: azdata arc sql endpoint コマンドに関するリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 04/06/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 13ff758f200bd675bc21c020b9c957fb286ecfd6
ms.sourcegitcommit: 7e5414d8005e7b07e537417582fb4132b5832ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556841"
---
# <a name="azdata-arc-sql-endpoint"></a>azdata arc sql endpoint

[!INCLUDE [azure-data-cli-azdata](../../includes/azure-data-cli-azdata.md)] への適用

以下の記事では、**azdata** ツールの **sql** コマンドに関するリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata arc sql endpoint list](#azdata-arc-sql-endpoint-list) | SQL エンドポイントを一覧表示します。
## <a name="azdata-arc-sql-endpoint-list"></a>azdata arc sql endpoint list
SQL エンドポイントを一覧表示します。
```bash
azdata arc sql endpoint list [--name -n] 
                             
```
### <a name="examples"></a>例
SQL マネージド インスタンスのエンドポイントを一覧表示します。
```bash
azdata arc sql endpoint list -n sqlmi1
```
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--name -n`
表示する SQL インスタンスの名前。 省略した場合、すべてのインスタンスのすべてのエンドポイントが表示されます。
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

