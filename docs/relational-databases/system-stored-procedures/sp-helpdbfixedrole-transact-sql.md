---
title: sp_helpdbfixedrole (TRANSACT-SQL) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_helpdbfixedrole
- sp_helpdbfixedrole_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_helpdbfixedrole
ms.assetid: ad87e9a0-b901-4e37-9950-aa517d680fc3
author: stevestein
ms.author: sstein
manager: craigg
ms.openlocfilehash: f36cac8a1a21f5e742c9fe7925684a6002f4a2b1
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47777650"
---
# <a name="sphelpdbfixedrole-transact-sql"></a>sp_helpdbfixedrole (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  固定データベース ロールの一覧を返します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_helpdbfixedrole [ [ @rolename = ] 'role' ]   
```  
  
## <a name="arguments"></a>引数  
 [  **@rolename =** ] **'***ロール***'**  
 固定データベース ロールの名前を指定します。 *ロール*は**sysname**、既定値は NULL です。 場合*ロール*が指定すると、そのロールに関する情報のみが返されます。 それ以外の場合、一覧とすべての固定データベース ロールの説明が返されます。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="result-sets"></a>結果セット  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**DbFixedRole**|**sysname**|固定データベース ロールの名前。|  
|**[説明]**|**nvarchar (70)**|説明**DbFixedRole します。**|  
  
## <a name="remarks"></a>コメント  
 次の表に示すように、固定データベース ロールはデータベース レベルで定義され、特定のデータベース レベルの管理操作を実行する権限が与えられています。 固定データベース ロールは、追加または削除できません。 固定データベース ロールに許可されている権限は変更できません。  
  
|固定データベース ロール|説明|  
|-------------------------|-----------------|  
|**db_owner**|データベース所有者|  
|**db_accessadmin**|データベース アクセス管理者|  
|**db_securityadmin**|データベース セキュリティ管理者|  
|**db_ddladmin**|データベース DDL 管理者|  
|**db_backupoperator**|データベース バックアップ オペレーター|  
|**db_datareader**|データベース データ リーダー|  
|**db_datawriter**|データベース データ ライター|  
|**db_denydatareader**|データベース否定データ リーダー|  
|**db_denydatawriter**|データベース否定データ ライター|  
  
 次の表は、データベース ロールを変更するときに使用するストアド プロシージャです。  
  
|ストアド プロシージャ|操作|  
|----------------------|------------|  
|**sp_addrolemember**|固定データベース ロールにデータベース ユーザーを追加します。|  
|**sp_helprole**|固定データベース ロールのメンバーの一覧を表示します。|  
|**sp_droprolemember**|固定データベース ロールからメンバーを削除します。|  
  
## <a name="permissions"></a>アクセス許可  
 ロール **public** のメンバーシップが必要です。  
  
 返される情報は、メタデータへのアクセスに関する制限の対象となります。 プリンシパルに権限がないエンティティは表示されません。 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  
  
## <a name="examples"></a>使用例  
 次の例では、すべての固定データベース ロールの一覧を表示します。  
  
```  
EXEC sp_helpdbfixedrole;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [セキュリティ ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/security-stored-procedures-transact-sql.md)   
 [sp_addrolemember &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addrolemember-transact-sql.md)   
 [sp_dbfixedrolepermission &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dbfixedrolepermission-transact-sql.md)   
 [sp_droprolemember の各&#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-droprolemember-transact-sql.md)   
 [sp_helprole &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helprole-transact-sql.md)   
 [sp_helprolemember &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helprolemember-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
