---
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 04/07/2021
ms.topic: include
author: anmunde
ms.author: anmunde
ms.reviewer: dphansen
ms.openlocfilehash: dbfc7de86a0ecf06c2eca0c6d49e96ca8e3ea39e
ms.sourcegitcommit: 09122d02fc3d86c6028366653337c083da8a3f4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107072382"
---
## <a name="known-issues"></a>既知の問題

[SQL Server Machine Learning Services](../../sql-server-machine-learning-services.md) の一部として提供されている R ランタイムを使用している場合は、言語拡張を登録するときに `R_HOME` を `C:\Program Files\Microsoft SQL Server\MSSQL15.<INSTANCE_NAME>\R_SERVICES` に設定することにより、[sp_execute_external スクリプト](../../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)で外部カスタム R スクリプトを実行すると、次のエラーが発生する可能性があります。

"*エラー: メモリが不足しています (上限に達しましたか?)* "

この問題を解決するには、次の手順を実行します。
 1. 固定サイズのオブジェクトの数 (`cons cells`) を示す環境変数 `R_NSIZE` を、適切な値 (`200000` など) に設定します。
 1. **スタート パッド** サービスを再起動し、スクリプトの実行を再試行します。