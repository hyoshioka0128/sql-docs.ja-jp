---
title: MSsubscriptions (Transact-SQL) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSsubscriptions_TSQL
- MSsubscriptions
dev_langs:
- TSQL
helpviewer_keywords:
- MSsubscriptions system table
ms.assetid: b7e8301d-d115-41f6-8d4f-e0d25f453b25
author: stevestein
ms.author: sstein
ms.openlocfilehash: cf40b3ea8a8984ee711401adfb561ac86fe0a6ea
ms.sourcegitcommit: 01c8df19cdf0670c02c645ac7d8cc9720c5db084
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "70000439"
---
# <a name="mssubscriptions-transact-sql"></a>MSsubscriptions (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  **Mssubscriptions**テーブルには、ローカルディストリビューターによってサービスが提供されるサブスクリプション内のパブリッシュされたアーティクルごとに1行のレコードが含まれます。 このテーブルは、ディストリビューションデータベースに格納されます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**publisher_database_id**|**int**|パブリッシャーデータベースの ID。|  
|**publisher_id**|**smallint**|パブリッシャーの ID。|  
|**publisher_db**|**sysname**|パブリッシャーデータベースの名前。|  
|**publication_id**|**int**|パブリケーションの ID です。|  
|**article_id**|**int**|アーティクルの ID です。|  
|**subscriber_id**|**smallint**|サブスクライバーの ID です。|  
|**subscriber_db**|**sysname**|サブスクリプション データベースの名前。|  
|**subscription_type**|**int**|サブスクリプションの種類です。<br /><br /> **0** = プッシュ。<br /><br /> **1** = プル。<br /><br /> **2** = 匿名。|  
|**sync_type**|**tinyint**|同期の種類。<br /><br /> **1** = 自動。<br /><br /> **2** = 同期なし。|  
|**status**|**tinyint**|サブスクリプションの状態。<br /><br /> **0** = 非アクティブ。<br /><br /> **1** = サブスクライブ済み。<br /><br /> **2** = アクティブ。|  
|**subscription_seqno**|**varbinary(16)**|スナップショットトランザクションのシーケンス番号です。|  
|**snapshot_seqno_flag**|**bit**|スナップショットトランザクションのシーケンス番号のソースを示します。値**1**は、 **subscription_seqno**がスナップショットのシーケンス番号であることを意味します。|  
|**independent_agent**|**bit**|このパブリケーションに対してスタンドアロンのディストリビューションエージェントがあるかどうかを示します。|  
|**subscription_time**|**datetime**|内部使用のみです。|  
|**loopback_detection**|**bit**|双方向トランザクション レプリケーション トポロジの一部であるサブスクリプションに適用されます。 ディストリビューション エージェントが、サブスクライバーで発生したトランザクションをサブスクライバーに戻すかどうかを示します。<br /><br /> **1** = を返しません。<br /><br /> **0** = 返送します。<br /><br /> 注:この列は、の[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)]双方向レプリケーション機能との下位互換性を維持するためにのみサポートされています。 新しいバージョンの[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]では、代わりにピアツーピアレプリケーションを使用する必要があります。 詳細については、「 [Peer-to-Peer Transactional Replication](../../relational-databases/replication/transactional/peer-to-peer-transactional-replication.md)」を参照してください。|  
|**agent_id**|**int**|エージェントの ID。|  
|**update_mode**|**tinyint**|更新の種類。|  
|**publisher_seqno**|**varbinary(16)**|このサブスクリプションに対するパブリッシャー側のトランザクションのシーケンス番号。|  
|**ss_cplt_seqno**|**varbinary(16)**|同時実行スナップショット処理の完了を示すために使用するシーケンス番号。|  
  
## <a name="see-also"></a>関連項目  
 [レプリケーション テーブル &#40; です。TRANSACT-SQL と &#41; です。](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [レプリケーションビュー &#40;transact-sql&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_helpsubscription &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpsubscription-transact-sql.md)  
  
  
