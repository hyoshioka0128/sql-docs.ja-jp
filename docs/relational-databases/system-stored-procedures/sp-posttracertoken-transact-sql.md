---
title: sp_posttracertoken (TRANSACT-SQL) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- posttracerttoken
- posttracerttoken_TSQL
- sp_posttracertoken
- sp_posttracertoken_TSQL
helpviewer_keywords:
- sp_posttracertoken
ms.assetid: 24da5cd2-1c45-475e-93db-5bdf660f1c2c
author: stevestein
ms.author: sstein
ms.openlocfilehash: 490f67e48d9cb5a578162d12523e2bb39bc1908d
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68132004"
---
# <a name="sp_posttracertoken-transact-sql"></a>sp_posttracertoken (TRANSACT-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  このプロシージャは、パブリッシャー側のトランザクション ログにトレーサー トークンを送信し、待機時間の統計を追跡する処理を開始します。 情報が記録されるタイミングは、トレーサー トークンがトランザクション ログに書き込まれたとき、ログ リーダー エージェントに処理されたとき、およびディストリビューション エージェントによって適用されたときです。 このストアド プロシージャは、パブリッシャー側でパブリケーション データベースについて実行されます。 詳細については、「 [トランザクション レプリケーションの待機時間の計測および接続の検証](../../relational-databases/replication/monitor/measure-latency-and-validate-connections-for-transactional-replication.md)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_posttracertoken [ @publication = ] 'publication'   
    [ , [ @tracer_token_id = ] tracer_token_id OUTPUT  
    [ , [ @publisher = ] 'publisher'   
```  
  
## <a name="arguments"></a>引数  
`[ @publication = ] 'publication'` 待機時間が測定されているパブリケーションの名前です。 *パブリケーション* は **sysname** 、既定値はありません。  
  
`[ @tracer_token_id = ] _tracer_token_idOUTPUT` は挿入されたトレーサー トークンの ID です。 *@tracer_token_id* の既定値は null の **int** で、出力パラメーターです。 この値は、最初に [sp_helptracertokens (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-helptracertokenhistory-transact-sql.md) を実行せずに[sp_helptracertokenhistory (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-deletetracertokenhistory-transact-sql.md) または [sp_deletetracertokenhistory (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-helptracertokens-transact-sql.md) を実行するために使用できます。  
  
`[ @publisher = ] 'publisher'` 以外を指定[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]パブリッシャーです。 *パブリッシャー*は**sysname**、既定値は NULL を指定する必要があります、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]パブリッシャーです。  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または**1** (失敗)  
  
## <a name="remarks"></a>コメント  
 **sp_posttracertoken**はトランザクション レプリケーションで使用します。  
  
## <a name="example"></a>例  
 [!code-sql[HowTo#sp_tracertokens](../../relational-databases/replication/codesnippet/tsql/sp-posttracertoken-trans_1.sql)]  
  
## <a name="permissions"></a>アクセス許可  
 メンバーのみ、 **sysadmin**固定サーバー ロールまたは**db_owner**固定データベース ロールが実行できる**sp_posttracertoken**します。  
  
## <a name="see-also"></a>関連項目  
 [トランザクション レプリケーションの待機時間の計測および接続の検証](../../relational-databases/replication/monitor/measure-latency-and-validate-connections-for-transactional-replication.md)  
  
  
