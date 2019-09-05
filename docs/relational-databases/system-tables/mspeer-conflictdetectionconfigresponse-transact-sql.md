---
title: MSpeer_conflictdetectionconfigresponse (Transact-SQL) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- MSpeer_conflictdetectionconfigresponse
- MSpeer_conflictdetectionconfigresponse_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- MSpeer_conflictdetectionconfigureresponse
ms.assetid: 2685fb66-731d-40f7-af4b-596b9222c5d4
author: stevestein
ms.author: sstein
ms.openlocfilehash: 430008b1a689c413bf69c9907a60f4129dc8e5b9
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68006877"
---
# <a name="mspeer_conflictdetectionconfigresponse-transact-sql"></a>MSpeer_conflictdetectionconfigresponse (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  トポロジ全体の構成要求に対する各ノードの応答を保存するために、ピア ツー ピア レプリケーションで使用されます。 このテーブルは、パブリケーション データベース内に保存されます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|request_id|**int**|競合構成要求エントリを識別、 [MSpeer_conflictdetectionconfigrequest](../../relational-databases/system-tables/mspeer-conflictdetectionconfigrequest-transact-sql.md)テーブル。|  
|peer_node|**sysname**|応答を生成するサーバー インスタンスの名前です。|  
|peer_db|**sysname**|応答を生成したピアでサブスクリプション データベースです。|  
|peer_version|**sysname**|パブリッシャーのバージョン番号を識別します。|  
|peer_db_version|**sysname**|ピア データベースのバージョン番号を識別します。|  
|is_peer|**bit**|ノードが読み取り専用サブスクライバーであるかどうかを示します。 値**0**読み取り専用サブスクライバーが示されます。|  
|conflict_detection_enabled|**bit**|トポロジの競合の検出が有効になっているかどうかを示します。|  
|originator_id|**varbinary(16)**|競合検出のためにトポロジの各ノードを識別します。 詳細については、「 [Conflict Detection in Peer-to-Peer Replication](../../relational-databases/replication/transactional/peer-to-peer-conflict-detection-in-peer-to-peer-replication.md)」を参照してください。|  
|peer_conflict_retention|**int**|期間を日、そのメタデータは競合テーブルに格納されます。|  
|peer_subscriptions|**XML**|要求に応答したノードに関する情報。|  
|progress_phase|**nvarchar(32)**|次の値のいずれかを使用して、処理の現在のフェーズを識別します。<br /><br /> Started<br /><br /> 収集されたピア バージョン<br /><br /> 収集された状態|  
|modified_date|**datetime**|日付と時刻のフェーズが完了したこと。|  
  
## <a name="see-also"></a>関連項目  
 [レプリケーション テーブル &#40;Transact-SQL&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [レプリケーション ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
