---
title: azdata context リファレンス
titleSuffix: SQL Server big data clusters
description: azdata context コマンドのリファレンス記事です。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 04/06/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 3d7f09126ed0c3475718c8182a27b6b49b2f379b
ms.sourcegitcommit: 7e5414d8005e7b07e537417582fb4132b5832ded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106557065"
---
# <a name="azdata-context"></a>azdata context

[!INCLUDE [azure-data-cli-azdata](../../includes/azure-data-cli-azdata.md)] への適用

以下の記事では、**azdata** ツールの **sql** コマンドに関するリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
[azdata context list](#azdata-context-list) | ユーザー プロファイルで使用可能なコンテキストの一覧を表示します。
[azdata context delete](#azdata-context-delete) | 指定した名前空間のコンテキストをユーザー プロファイルから削除します。
[azdata context set](#azdata-context-set) | 指定した名前空間のコンテキストを、ユーザー プロファイルでのアクティブなコンテキストとして設定します。
## <a name="azdata-context-list"></a>azdata context list
`azdata context set` または `azdata context delete` を使用して、これらのいずれかを設定または削除することができます。 新しいコンテキストにログインするには、`azdata login` を使用します。
```bash
azdata context list [--active -a] 
                    
```
### <a name="examples"></a>例
ユーザー プロファイルで使用可能なすべてのコンテキストの一覧を表示します。
```bash
azdata context list
```
ユーザー プロファイルでアクティブなコンテキストの一覧を表示します。
```bash
azdata context list --active
```
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--active -a`
現在アクティブなコンテキストのみを一覧表示します。
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
## <a name="azdata-context-delete"></a>azdata context delete
アクティブなコンテキストを削除した場合、ユーザーは新しいアクティブなコンテキストを設定する必要があります。 設定または削除できるコンテキストを表示するには、`azdata context list` を使用します。 複数のコンテキスト名前空間に一致する名前空間が指定されている場合は、そのコンテキストを削除するために、一意性のためにすべての [--namespace --username --endpoint] を指定する必要があります。
```bash
azdata context delete --namespace -ns 
                      [--endpoint -e]  
                      
[--username -u]
```
### <a name="examples"></a>例
一意の名前空間に基づいて、ユーザー プロファイルのコンテキストを削除します。
```bash
azdata context delete --namespace contextNamespace
```
名前空間、ユーザー名、およびコントローラーのエンドポイントに基づいて、ユーザー プロファイルのコンテキストを削除します。
```bash
azdata context set --namespace contextNamespace --username johndoe --endpoint https://<ip or domain name>:30080
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--namespace -ns`
削除するコンテキストの名前空間。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--endpoint -e`
クラスター コントローラーのエンドポイント "https://host:port"。
#### `--username -u`
アカウント ユーザー。
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
## <a name="azdata-context-set"></a>azdata context set
設定できるコンテキストを表示するには、`azdata context list` を使用します。 コンテキストが表示されない場合は、`azdata login` を使用してログインし、ユーザー プロファイルにコンテキストを作成する必要があります。 ログインしたコンテキストが、アクティブなコンテキストになります。 複数のエンティティにログインした場合は、このコマンドを使用してアクティブなコンテキストを切り替えることができます。 現在アクティブなコンテキストを表示するには、`azdata context list --active` を使用します。 複数のコンテキスト名前空間に一致する名前空間が指定されている場合は、アクティブなコンテキストを設定するために、一意性のためにすべての [--namespace --username --endpoint] を指定する必要があります。
```bash
azdata context set --namespace -ns 
                   [--endpoint -e]  
                   
[--username -u]
```
### <a name="examples"></a>例
一意の名前空間に基づいて、ユーザー プロファイルのアクティブなコンテキストを設定します。
```bash
azdata context set --namespace contextNamespace
```
名前空間、ユーザー名、およびコントローラーのエンドポイントに基づいて、ユーザー プロファイルのアクティブなコンテキストを設定します。
```bash
azdata context set --namespace contextNamespace --username johndoe --endpoint https://<ip or domain name>:30080     
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--namespace -ns`
設定するコンテキストの名前空間。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--endpoint -e`
クラスター コントローラーのエンドポイント "https://host:port"。
#### `--username -u`
アカウント ユーザー。
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

