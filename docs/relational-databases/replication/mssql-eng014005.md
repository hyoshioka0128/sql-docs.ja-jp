---
description: MSSQL_ENG014005
title: MSSQL_ENG014005 | Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
helpviewer_keywords:
- MSSQL_ENG014005 error
ms.assetid: f168f0d6-cb11-45d4-9781-c374d7f388ee
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: 951b147c70d23a9f3a780d9bc1b047481d9fcd1d
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97475823"
---
# <a name="mssql_eng014005"></a>MSSQL_ENG014005
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]
    
## <a name="message-details"></a>メッセージの詳細  
  
|属性|値|  
|-|-|  
|製品名|SQL Server|  
|イベント ID|14005|  
|イベント ソース|MSSQLSERVER|  
|コンポーネント|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|  
|シンボル名||  
|メッセージ テキスト|パブリケーションを削除できませんでした。 サブスクリプションが存在します。|  
  
## <a name="explanation"></a>説明  
 1 つ以上のサブスクリプションが関連付けられているパブリケーションを削除しようとしました。 パブリケーションは、サブスクリプションが関連付けられていない場合にのみ削除できます。  
  
## <a name="user-action"></a>ユーザーの操作  
 パブリケーションを削除する前にサブスクリプションを削除します。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使用してパブリケーションを削除する場合は、パブリケーションを削除する前に関連するすべてのサブスクリプションを自動的に削除するオプションを使用できます。 ストアド プロシージャを使用する場合は、最初にサブスクリプションを明示的に削除する必要があります。 詳細については、「 [Delete a Push Subscription](../../relational-databases/replication/delete-a-push-subscription.md) 」および「 [Delete a Pull Subscription](../../relational-databases/replication/delete-a-pull-subscription.md)」を参照してください。  
  
 パブリケーションに存在するサブスクリプションが表示されない場合や、パブリケーションの作成時にこのエラーが表示される場合は、前のサブスクリプションを削除したときに完全にクリーンアップされていない可能性があります。 レプリケーションに関連するすべてのオブジェクトと設定を削除するには、データベースで [sp_removedbreplication &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-removedbreplication-transact-sql.md) を実行します。  
  
## <a name="see-also"></a>参照  
 [エラーとイベントのリファレンス &#40;レプリケーション&#41;](../../relational-databases/replication/errors-and-events-reference-replication.md)  
  
  
