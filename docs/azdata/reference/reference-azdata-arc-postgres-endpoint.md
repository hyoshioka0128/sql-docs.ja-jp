---
title: azdata arc postgres endpoint リファレンス
titleSuffix: SQL Server big data clusters
description: azdata arc postgres endpoint コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 04/06/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: dc94dad852cf016ac9bcb47935544df2a97eebd7
ms.sourcegitcommit: 7e5414d8005e7b07e537417582fb4132b5832ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106557425"
---
# <a name="azdata-arc-postgres-endpoint"></a>azdata arc postgres endpoint

[!INCLUDE [azure-data-cli-azdata](../../includes/azure-data-cli-azdata.md)] への適用

以下の記事では、**azdata** ツールの **sql** コマンドに関するリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata arc postgres endpoint list](#azdata-arc-postgres-endpoint-list) | Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
## <a name="azdata-arc-postgres-endpoint-list"></a>azdata arc postgres endpoint list
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
```bash
azdata arc postgres endpoint list [--name -n] 
                                  []
```
### <a name="examples"></a>例
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのエンドポイントを一覧表示します。
```bash
azdata arc postgres endpoint list -n postgres01
```
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--name -n`
Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの名前。
#### <a name=""></a>``
エンジン バージョンが異なる 2 つのサーバー グループの名前が同じである場合に、--engine-version を --name と組み合わせて使用すると、PostgreSQL Hyperscale サーバー グループを識別できます。 --engine-version は省略可能で、サーバー グループの識別に使用する場合、11 または 12 を使用する必要があります。
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

