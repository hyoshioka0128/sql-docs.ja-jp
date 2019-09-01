---
title: sp_set_firewall_rule (Azure SQL Database) |Microsoft Docs
ms.custom: ''
ms.date: 07/28/2016
ms.service: sql-database
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- sp_set_firewall_rule
- sp_set_firewall_rule_TSQL
- sys.sp_set_firewall_rule
- sys.sp_set_firewall_rule_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_set_firewall_rule
- firewall_rules, setting server rules
ms.assetid: a974a561-5382-4039-8499-3a56767bcefe
author: VanMSFT
ms.author: vanto
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 9bc37626879b743eb3a5d0864490dc3543a8d8a9
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70152063"
---
# <a name="sp_set_firewall_rule-azure-sql-database"></a>sp_set_firewall_rule (Azure SQL データベース)
[!INCLUDE[tsql-appliesto-xxxxxx-asdb-asdw-xxx-md](../../includes/tsql-appliesto-xxxxxx-asdb-asdw-xxx-md.md)]

  [!INCLUDE[ssSDS](../../includes/sssds-md.md)]サーバーのサーバーレベルのファイアウォール設定を作成または更新します。 このストアドプロシージャを使用できるのは、master データベースで、サーバーレベルプリンシパルログインまたは Azure Active Directory プリンシパルが割り当てられている場合のみです。  
  
  
## <a name="syntax"></a>構文  
  
```
sp_set_firewall_rule [@name =] 'name', 
    [@start_ip_address =] 'start_ip_address', 
    [@end_ip_address =] 'end_ip_address'
[ ; ]  
```  
  
## <a name="arguments"></a>引数  
 次の表は、で[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]サポートされている引数とオプションを示しています。  
  
|名前|Datatype|説明|  
|----------|--------------|-----------------|  
|[@name =] ' name '|**NVARCHAR (128)**|サーバーレベルのファイアウォール設定を説明し、区別するために使用される名前。|  
|[@start_ip_address =] ' start_ip_address '|**VARCHAR (50)**|サーバーレベルのファイアウォール設定の範囲の最小の IP アドレス。 IP アドレスがこの値以上の場合は、サーバーへの[!INCLUDE[ssSDS](../../includes/sssds-md.md)]接続を試行できます。 使用可能な最小の IP `0.0.0.0`アドレスはです。|  
|[@end_ip_address =] ' end_ip_address '|**VARCHAR (50)**|サーバーレベルのファイアウォール設定の範囲の最上位の IP アドレス。 これ以下の IP アドレスは、[!INCLUDE[ssSDS](../../includes/sssds-md.md)] サーバーへの接続を試みることができます。 使用可能な最大 IP アドレス`255.255.255.255`はです。<br /><br /> 注:Azure の接続試行は、このフィールドと*start_ip_address*フィールドの両方が`0.0.0.0`と等しい場合に許可されます。|  
  
## <a name="remarks"></a>コメント  
 サーバー レベルのファイアウォール設定の名前は一意である必要があります。 ストアドプロシージャに対して指定された設定の名前がファイアウォール設定テーブルに既に存在する場合は、開始 IP アドレスと終了 IP アドレスが更新されます。 そうでない場合は、新しいサーバー レベルのファイアウォール設定が作成されます。  
  
 開始 IP アドレスと終了 IP アドレスがに`0.0.0.0`等しいサーバーレベルのファイアウォール設定を追加すると、Azure から[!INCLUDE[ssSDS](../../includes/sssds-md.md)]サーバーにアクセスできるようになります。 *Name*パラメーターに値を指定します。これは、サーバーレベルのファイアウォール設定の内容を思い出すのに役立ちます。  
  
 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] では、接続の認証に必要なログイン データおよびサーバー レベルのファイアウォール規則は、各データベースで一時的にキャッシュされます。 このキャッシュは定期的に更新されます。 認証キャッシュを強制的に更新し、データベースにログイン テーブルの最新バージョンがあることを確認するには、[DBCC FLUSHAUTHCACHE &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-flushauthcache-transact-sql.md) を実行します。  
  
## <a name="permissions"></a>アクセス許可  
 サーバーレベルのファイアウォールルールを作成または変更できるのは、プロビジョニング処理によって作成されたサーバーレベルのプリンシパルログイン、または管理者として割り当てられた Azure Active Directory プリンシパルだけです。 Sp_set_firewall_rule を実行するには、ユーザーが master データベースに接続されている必要があります。  
  
## <a name="examples"></a>使用例  
 次のコードでは、Azure からのアクセスを`Allow Azure`可能にするという名前のサーバーレベルのファイアウォール設定を作成します。 仮想 master データベースで次のを実行します。  
  
```  
-- Enable Azure connections.  
exec sp_set_firewall_rule N'Allow Azure', '0.0.0.0', '0.0.0.0';  
  
```  
  
 次のコードでは、IP アドレス`Example setting 1` `0.0.0.2`のみに対してというサーバーレベルのファイアウォール設定を作成します。 次に、 `sp_set_firewall_rule`そのファイアウォール設定で、終了 IP アドレスをに`0.0.0.4`更新するために、ストアドプロシージャが再度呼び出されます。 これにより、IP アドレス`0.0.0.2`、 `0.0.0.3`、および`0.0.0.4`がサーバーにアクセスできるようになる範囲が作成されます。  
  
```  
-- Create server-level firewall setting for only IP 0.0.0.2  
exec sp_set_firewall_rule N'Example setting 1', '0.0.0.2', '0.0.0.2';  
  
-- Update server-level firewall setting to create a range of allowed IP addresses
exec sp_set_firewall_rule N'Example setting 1', '0.0.0.2', '0.0.0.4';  
  
```  
  
## <a name="see-also"></a>関連項目  
 [Azure SQL Database ファイアウォール](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/)   
 [方法:ファイアウォール設定の構成 (Azure SQL Database)](https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/)   
 [firewall_rules &#40;Azure SQL Database&#41;](../../relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database.md)
