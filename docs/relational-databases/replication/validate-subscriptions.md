---
description: '[サブスクリプションの検証]'
title: サブスクリプションの検証 | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
f1_keywords:
- sql13.rep.validate.subscriptions.f1
helpviewer_keywords:
- Validate Subscriptions dialog box
ms.assetid: 0ca39a35-f22c-46c5-82a4-342e34bf5d1b
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-current||>=sql-server-2016
ms.openlocfilehash: bb8a56e2b165f5ccfa7db701a2d95792de47e19d
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97468683"
---
# <a name="validate-subscriptions"></a>[サブスクリプションの検証]
[!INCLUDE[sql-asdb](../../includes/applies-to-version/sql-asdb.md)]
  **[サブスクリプションの検証]** ダイアログ ボックスを使用すると、各サブスクリプションのディストリビューション エージェントを次に実行するときに、トランザクション パブリケーションに対するサブスクリプションを検証するように指定できます。 検証の結果はレプリケーション モニターに表示されます。 詳しくは、「 [Validate Data at the Subscriber](../../relational-databases/replication/validate-data-at-the-subscriber.md)」をご覧ください。  

[!INCLUDE[azure-sql-db-replication-supportability-note](../../includes/azure-sql-db-replication-supportability-note.md)]
  
## <a name="options"></a>オプション  
 **[すべての SQL Server サブスクリプションを検証する]**  
 選択すると、このパブリケーションに対するすべての SQL Server サブスクリプションのデータが検証されます。  
  
 **[以下のサブスクリプションを検証する]**  
 すべてのサブスクリプションを検証しない場合に選択します。 検証するサブスクリプションを一覧から選択します。  
  
 **[検証オプション]**  
 クリックすると、 **[サブスクリプションの検証オプション]** ダイアログ ボックスが開き、行数の検証とバイナリ チェックサムの検証のどちらを使用するかを指定できます。  
  
## <a name="see-also"></a>参照  
 [レプリケートされたデータの検証](../../relational-databases/replication/validate-data-at-the-subscriber.md)  
  
  
