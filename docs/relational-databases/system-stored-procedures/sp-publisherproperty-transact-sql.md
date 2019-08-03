---
title: sp_publisherproperty (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sp_publisherproperty
- sp_publisherproperty_TSQL
helpviewer_keywords:
- sp_publisherproperty
ms.assetid: 0ed1ebc1-a1bd-4aed-9f46-615c5cf07827
author: stevestein
ms.author: sstein
ms.openlocfilehash: 185ad0ad33419b20fffae9bff3e5562761ea7b31
ms.sourcegitcommit: 728a4fa5a3022c237b68b31724fce441c4e4d0ab
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2019
ms.locfileid: "68771174"
---
# <a name="sppublisherproperty-transact-sql"></a>sp_publisherproperty (Transact-sql)
[!INCLUDE[appliesto-ss-asdbmi-xxxx-xxx-md](../../includes/appliesto-ss-asdbmi-xxxx-xxx-md.md)]

  [!INCLUDE[msCoName](../../includes/msconame-md.md)] 以外[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のパブリッシャーのパブリッシャーのプロパティを表示または変更します。 このストアドプロシージャは、ディストリビューター側で実行されます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_publisherproperty [ @publisher = ] 'publisher'   
   [ , [ @propertyname = ] 'propertyname' ]   
   [ , [ @propertyvalue = ] 'propertyvalue' ]  
```  
  
## <a name="arguments"></a>引数  
 [ **@publisher** =] **'***パブリッシャー***'**  
 異種パブリッシャーの名前を指定します。 *パブリッシャー* は **sysname** 、既定値はありません。  
  
 [ **@propertyname** =] **'***propertyname***'**  
 設定するプロパティの名前を指定します。 *propertyname*は**sysname**で、次のいずれかの値を指定できます。  
  
|値|説明|  
|-----------|-----------------|  
|**xactsetbatching**|パブリッシャーのトランザクションが、後続の処理のために、トランザクション全体で一貫性のあるセット (Xactsets と呼ばれます) にまとめられるかどうかを示します。 値が**enabled**の場合は、既定の Xactsets を作成できます。 値**disabled**は、既存の Xactsets が新しい Xactsets を作成せずに処理されることを意味します。|  
|**xactsetjob**|Xactsets を作成するために Xactset ジョブを有効にするかどうかを示します。 値が**enabled**の場合は、Xactset ジョブが定期的に実行され、パブリッシャーで Xactsets が作成されます。 値が**disabled**の場合は、パブリッシャーに変更をポーリングするときに、ログリーダーエージェントによってのみ Xactsets が作成されることを意味します。|  
|**xactsetjobinterval**|Xactset ジョブの実行間隔 (分単位) です。|  
  
 *Propertyname*を省略すると、設定可能なすべてのプロパティが返されます。  
  
 [ **@propertyvalue** =] **'***propertyvalue***'**  
 プロパティ設定の新しい値を指定します。 *propertyvalue*は**sysname**,、既定値は NULL です。 *Propertyvalue*を省略すると、プロパティの現在の設定が返されます。  
  
## <a name="result-sets"></a>結果セット  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**propertyname**|**sysname**|設定可能な次のパブリケーションプロパティを返します。<br /><br /> **xactsetbatching**<br /><br /> **xactsetjob**<br /><br /> **xactsetjobinterval**|  
|**propertyvalue**|**sysname**|**Propertyname**列のプロパティの現在の設定です。|  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または**1** (失敗)  
  
## <a name="remarks"></a>コメント  
 **sp_publisherproperty**は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]以外のパブリッシャーのトランザクションレプリケーションで使用します。  
  
 *パブリッシャー*だけを指定した場合、結果セットには、設定可能なすべてのプロパティの現在の設定が含まれます。  
  
 *Propertyname*を指定した場合、結果セットには名前付きプロパティだけが表示されます。  
  
 すべてのパラメーターを指定した場合は、プロパティが変更され、結果セットは返されません。  
  
 実行中のジョブの**xactsetjobinterval**プロパティを変更する場合は、新しい間隔を有効にするためにジョブを再起動する必要があります。  
  
## <a name="permissions"></a>アクセス許可  
 **Sp_publisherproperty**を実行できるのは、ディストリビューター側の固定サーバーロール**sysadmin**のメンバーだけです。  
  
## <a name="see-also"></a>関連項目  
 [Oracle パブリッシャー用のトランザクション セット ジョブの構成 &#40;レプリケーション Transact-SQL プログラミング&#41;](../../relational-databases/replication/administration/configure-the-transaction-set-job-for-an-oracle-publisher.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
