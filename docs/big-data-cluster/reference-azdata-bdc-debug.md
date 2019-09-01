---
title: azdata bdc debug リファレンス
titleSuffix: SQL Server big data clusters
description: azdata bdc debug コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/28/2019
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 9e14528baf80d08841f6e9e17a0476dfa81fd48d
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70153198"
---
# <a name="azdata-bdc-debug"></a>azdata bdc debug

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]  

この記事は、 **azdata**のリファレンス記事です。 

## <a name="commands"></a>コマンド
|     |     |
| --- | --- |
[azdata bdc debug copy-logs](#azdata-bdc-debug-copy-logs) | ログをコピーします。
[azdata bdc debug dump](#azdata-bdc-debug-dump) | ダンプのログ記録を開始します。
## <a name="azdata-bdc-debug-copy-logs"></a>azdata bdc debug copy-logs
ビッグデータクラスターからデバッグログをコピーする-システムで Kubernetes 構成が必要です。
```bash
azdata bdc debug copy-logs --namespace -n 
                           [--container -c]  
                           [--target-folder -d]  
                           [--pod -p]  
                           [--timeout -t]
```
### <a name="required-parameters"></a>必要なパラメーター
#### `--namespace -n`
ビッグ データ クラスターの名前。kubernetes 名前空間に使用されます。
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--container -c`
類似した名前のコンテナーのログをコピーします (省略可能)。既定では、すべてのコンテナーのログがコピーされます。 複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます
#### `--target-folder -d`
ログのコピー先となるターゲット フォルダーのパス。 省略可能。既定では、ローカル フォルダー内に結果が作成されます。  複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます
#### `--pod -p`
類似した名前のポッドのログをコピーします。 省略可能。既定では、すべてのポッドのログがコピーされます。 複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます
#### `--timeout -t`
コマンドが完了するまで待機する秒数。 既定値は 0 (無制限) です
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
## <a name="azdata-bdc-debug-dump"></a>azdata bdc debug dump
システムでは、トリガーログダンプをトリガーし、Kubernetes 構成からコピーします。
```bash
azdata bdc debug dump --namespace -n 
                      --container -c  
                      [--target-folder -d]
```
### <a name="required-parameters"></a>必要なパラメーター
#### `--namespace -n`
ビッグ データ クラスターの名前 (Kubernetes 名前空間用に使います)。
#### `--container -c`
類似した名前のコンテナーのログをコピーします (省略可能)。既定では、すべてのコンテナーのログがコピーされます。 複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--target-folder -d`
ログのコピー先となるターゲット フォルダーのパス。 省略可能。既定では、ローカル フォルダー内に結果が作成されます。  複数回指定することはできません。 複数回指定した場合は、最後のものが使用されます `./output/dump`
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
