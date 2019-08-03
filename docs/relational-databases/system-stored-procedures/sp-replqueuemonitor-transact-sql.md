---
title: sp_replqueuemonitor (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sp_replqueuemonitor
- sp_replqueuemonitor_TSQL
helpviewer_keywords:
- sp_replqueuemonitor
ms.assetid: 6909a3f1-43a2-4df5-a6a5-9e6f347ac841
author: stevestein
ms.author: sstein
ms.openlocfilehash: baf3281ade8e62b30f87e01a7024dc12d7032667
ms.sourcegitcommit: 728a4fa5a3022c237b68b31724fce441c4e4d0ab
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2019
ms.locfileid: "68770913"
---
# <a name="spreplqueuemonitor-transact-sql"></a>sp_replqueuemonitor (Transact-SQL)
[!INCLUDE[appliesto-ss-asdbmi-xxxx-xxx-md](../../includes/appliesto-ss-asdbmi-xxxx-xxx-md.md)]

  指定されたパブリケーションに[!INCLUDE[msCoName](../../includes/msconame-md.md)]対するキュー [!INCLUDE[msCoName](../../includes/msconame-md.md)]更新サブスクリプションのキューメッセージまたはメッセージキューから[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のキューメッセージを一覧表示します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] キューを使用している場合、このストアド プロシージャはサブスクライバー側のサブスクリプション データベース上で実行されます。 メッセージ キューイングを使用している場合、このストアド プロシージャはディストリビューター側のディストリビューション データベース上で実行されます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_replqueuemonitor [ @publisher = ] 'publisher'  
    [ , [ @publisherdb = ] 'publisher_db' ]  
    [ , [ @publication = ] 'publication' ]  
    [ , [ @tranid = ] 'tranid' ]  
    [ , [ @queuetype = ] 'queuetype' ]  
```  
  
## <a name="arguments"></a>引数  
`[ @publisher = ] 'publisher'`パブリッシャーの名前を指定します。 *publisher*は**sysname**で、既定値は NULL です。 サーバーはパブリッシング用に構成されている必要があります。 NULL はすべてのパブリッシャーを表します。  
  
`[ @publisherdb = ] 'publisher_db' ]`パブリケーションデータベースの名前を指定します。 *publisher_db*は**sysname**,、既定値は NULL です。 すべてのパブリケーションデータベースに対して NULL です。  
  
`[ @publication = ] 'publication' ]`パブリケーションの名前を指定します。 *publication*は**sysname**,、既定値は NULL です。 NULL はすべてのパブリケーションを表します。  
  
`[ @tranid = ] 'tranid' ]`トランザクション ID を示します。 *tranid*は**sysname**,、既定値は NULL です。 すべてのトランザクションに対して NULL です。  
  
 **[@queuetype=** ] **'***queuetype***'** ]  
 トランザクションを格納するキューの種類です。 *queuetype*は**tinyint**で、既定値は**0**です。次のいずれかの値を指定できます。  
  
|[値]|説明|  
|-----------|-----------------|  
|**0**|すべての種類のキュー|  
|**1**|メッセージ キューイング (Message Queuing)|  
|**2**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]再生|  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または**1** (失敗)  
  
## <a name="remarks"></a>コメント  
 **sp_replqueuemonitor**は、スナップショットレプリケーションまたはキュー更新サブスクリプションを使用するトランザクションレプリケーションで使用します。 SQL コマンドが含まれないキュー メッセージ、または SQL コマンドの一部であるキュー メッセージは表示されません。  
  
## <a name="permissions"></a>アクセス許可  
 **Sp_replqueuemonitor**を実行できるのは、固定サーバーロール**sysadmin**または固定データベースロール**db_owner**のメンバーだけです。  
  
## <a name="see-also"></a>関連項目  
 [Updatable Subscriptions for Transactional Replication](../../relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
