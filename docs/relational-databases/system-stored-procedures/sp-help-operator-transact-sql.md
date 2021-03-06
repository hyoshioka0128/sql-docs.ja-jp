---
title: sp_help_operator (TRANSACT-SQL) |Microsoft Docs
ms.custom: ''
ms.date: 08/01/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_help_operator
- sp_help_operator_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_help_operator
ms.assetid: caedc43d-44b8-415a-897e-92923f6de3b8
author: stevestein
ms.author: sstein
manager: craigg
ms.openlocfilehash: bbecf5d57ae6e11f3a29aca64b7ce8c52a6f6b76
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47733834"
---
# <a name="sphelpoperator-transact-sql"></a>sp_help_operator (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  サーバーに対して定義されたオペレーターに関する情報をレポートします。  
  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_help_operator  
     { [ @operator_name = ] 'operator_name'   
     | [ @operator_id = ] operator_id }  
```  
  
## <a name="arguments"></a>引数  
 [  **@operator_name=** ] **'***operator_name***'**  
 オペレーター名を指定します。 *operator_name*は**sysname**します。 場合*operator_name*が指定されていないすべての演算子に関する情報が返されます。  
  
 [ **@operator_id=** ] *operator_id*  
 要求する情報の対象となるオペレーターの識別番号を指定します。 *operator_id*は**int**、既定値は NULL です。  
  
> [!NOTE]  
>  いずれか*operator_id*または*operator_name*指定する必要がありますが、両方を指定することはできません。  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または**1** (失敗)  
  
## <a name="result-sets"></a>結果セット  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**id**|**int**|オペレーター識別番号。|  
|**name**|**sysname**|演算子の名前。|  
|**enabled**|**tinyint**|オペレーターが通知を受け取ることが可能かどうか。<br /><br /> **1** = はい<br /><br /> **0** = いいえ|  
|**email_address**|**nvarchar(100)**|オペレーターの電子メール アドレスです。|  
|**last_email_date**|**int**|オペレーターが前回、電子メールによる通知を受け取った日付。|  
|**last_email_time**|**int**|オペレーターが前回、電子メールによる通知を受け取った時刻。|  
|**pager_address**|**nvarchar(100)**|オペレーターのポケットベル アドレス。|  
|**last_pager_date**|**int**|オペレーターが前回、ポケットベルによる通知を受け取った日付。|  
|**last_pager_time**|**int**|オペレーターが前回、ポケットベルによる通知を受け取った時刻。|  
|**weekday_pager_start_time**|**int**|平日にオペレーターがポケットベルによる通知を受け取ることのできる開始時刻。|  
|**weekday_pager_end_time**|**int**|平日にオペレーターがポケットベルによる通知を受け取ることのできる最終時刻。|  
|**saturday_pager_start_time**|**int**|土曜日にオペレーターがポケットベルによる通知を受け取ることのできる開始時刻。|  
|**saturday_pager_end_time**|**int**|土曜日にオペレーターがポケットベルによる通知を受け取ることのできる最終時刻。|  
|**sunday_pager_start_time**|**int**|日曜日にオペレーターがポケットベルによる通知を受け取ることのできる開始時刻。|  
|**sunday_pager_end_time**|**int**|日曜日にオペレーターがポケットベルによる通知を受け取ることのできる最終時刻。|  
|**pager_days**|**tinyint**|ビットマスク (**1** = 日曜日、 **64** = 土曜日) のオペレーターがポケットベルによる通知の受信に使用できることを示す日の週。|  
|**netsend_address**|**nvarchar(100)**|ネットワーク ポップアップ通知のオペレーター アドレス。|  
|**last_netsend_date**|**int**|オペレーターが前回、ネットワーク ポップアップによる通知を受け取った日付。|  
|**last_netsend_time**|**int**|オペレーターが前回、ネットワーク ポップアップによる通知を受け取った時刻。|  
|**category_name**|**sysname**|このオペレーターが所属するオペレーター カテゴリの名前。|  
  
## <a name="remarks"></a>コメント  
 **sp_help_operator**から実行する必要があります、 **msdb**データベース。  
  
## <a name="permissions"></a>アクセス許可  
 既定では、このストアド プロシージャを実行できるのは、 **sysadmin** 固定サーバー ロールのメンバーです。 他のユーザーには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] msdb **データベースの次のいずれかの** エージェント固定データベース ロールが許可されている必要があります。  
  
-   **SQLAgentUserRole**  
  
-   **SQLAgentReaderRole**  
  
-   **SQLAgentOperatorRole**  
  
 これらのロールの権限の詳細については、「 [SQL Server エージェントの固定データベース ロール](../../ssms/agent/sql-server-agent-fixed-database-roles.md)」を参照してください。  
  
## <a name="examples"></a>使用例  
 次の例では、オペレーター `François Ajenstat` についての情報をレポートします。  
  
```  
USE msdb ;  
GO  
  
EXEC dbo.sp_help_operator  
    @operator_name = N'François Ajenstat' ;  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [sp_add_operator &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-add-operator-transact-sql.md)   
 [sp_delete_operator &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-delete-operator-transact-sql.md)   
 [sp_update_operator &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-update-operator-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
