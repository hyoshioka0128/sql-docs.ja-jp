---
description: SQL インジェクション攻撃からアプリケーションを守る上でユーザー入力の検証が非常に重要となる理由について学びます。
title: ユーザー入力の検証
ms.custom: ''
ms.date: 03/31/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 8aa867b0-e6f0-49eb-93d3-817ae2ed8f77
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 1683f99b6c9d188a45434304251168748af32582
ms.sourcegitcommit: ebe81e2daa544f41c8ababb66a91c218ad0c2a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106177049"
---
# <a name="validating-user-input"></a>ユーザー入力の検証

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

データにアクセスするアプリケーションを作成する場合は、すべてのユーザー入力について、悪意がないと確認されるまでは、悪意があるものと見なす必要があります。 攻撃に対する脆弱性をアプリケーションから取り除くことはできません。 発生する可能性のある攻撃の 1 つに、SQL インジェクションと呼ばれるものがあります。 この攻撃により、解析および実行される [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに渡される文字列に悪意のあるコードが追加されます。 この攻撃を防ぐには、パラメータを持つストアド プロシージャを可能な限り使用し、ユーザー入力を常に検証する必要があります。

サーバーへの無駄なラウンド トリップを行わないようにするには、ユーザー入力の検証をクライアント コードで行うことが重要です。 サーバー上のストアド プロシージャのパラメーターを検証することも同様に重要です。 そうすれば、クライアント側の検証をバイパスする入力を捕捉することができます。

SQL インジェクションと、この攻撃を避ける方法の詳細については、「[SQL インジェクション](../../relational-databases/security/sql-injection.md)」を参照してください。 ストアド プロシージャのパラメーターの検証の詳細については、「[ストアド プロシージャ](../../relational-databases/stored-procedures/stored-procedures-database-engine.md)」と関連記事を参照してください。

## <a name="see-also"></a>関連項目

[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)
