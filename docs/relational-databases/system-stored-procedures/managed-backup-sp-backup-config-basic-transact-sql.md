---
title: managed_backup sp_backup_config_basic (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 10/03/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_backup_config_basic_TSQL
- sp_backup_config_basic
- managed_backup.sp_backup_config_basic
- managed_backup.sp_backup_config_basic_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- managed_backup.sp_backup_config_basic
- sp_backup_config_basic
ms.assetid: 3ad73051-ae9a-4e41-a889-166146e5508f
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 92cbea99941b6e9378c4400ae0b563d462c34f27
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70152029"
---
# <a name="managed_backupsp_backup_config_basic-transact-sql"></a>managed_backup.sp_backup_config_basic (TRANSACT-SQL)
[!INCLUDE[tsql-appliesto-ss2016-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2016-xxxx-xxxx-xxx-md.md)]

  特定のデータベースまたはの[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスの基本設定を構成します。[!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)]  
  
> [!NOTE]  
>  このプロシージャは、基本的なマネージバックアップ構成を作成するために独自に呼び出すことができます。 ただし、高度な機能またはカスタムスケジュールを追加する予定がある場合は、まず[managed_backup &#40;&#41; ](../../relational-databases/system-stored-procedures/managed-backup-sp-backup-config-advanced-transact-sql.md)と[managed_backup &#40;を使用してこれらの設定を構成します。 sp_backup_config_advancedTransact-sql:&#41; ](../../relational-databases/system-stored-procedures/managed-backup-sp-backup-config-schedule-transact-sql.md)この手順でマネージバックアップを有効にする前に実行します。  
   
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```Transact-SQL   
EXEC managed_backup.sp_backup_config_basic  
    [@enable_backup = ] { 0 | 1}    ,[@database_name = ] 'database_name'    ,[@container_url = ] 'Azure_Storage_blob_container  
    ,[@retention_days = ] 'retention_period_in_days'    ,[@credential_name = ] 'sql_credential_name'  
```  
  
##  <a name="Arguments"></a> 引数  
 @enable_backup  
 指定したデータベースに対して [!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)]を有効または無効にします。 @enable_backupは **ビット** します。 の最初の[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンス[!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)]に対してを構成するときに必要なパラメーター。 既存[!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)]の構成を変更する場合、このパラメーターは省略可能です。 この場合、指定されていない構成値は、既存の値を保持します。  
  
 @database_name  
 特定のデータベースでマネージバックアップを有効にするためのデータベース名。  
  
 @container_url  
 バックアップの場所を示す URL。 が@credential_name NULL の場合、この url は Azure Storage の blob コンテナーへの shared access signature (SAS) url であり、バックアップは新しいバックアップを使用して blob の機能をブロックします。 詳細については、「 [SAS につい](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)て」を参照してください。 を@credential_name指定した場合、これはストレージアカウントの URL であり、バックアップでは、非推奨のバックアップページ blob の機能が使用されます。  
  
> [!NOTE]  
>  現時点では、このパラメーターでは SAS URL のみがサポートされています。  
  
 @retention_days  
 バックアップ ファイルの保有期間 (日数)。 @storage_urlは INT です。 これは、の[!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスで初めてを構成するときに必要なパラメーターです。 構成の[!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)]変更中は、このパラメーターは省略可能です。 指定しない場合は、既存の構成値が保持されます。  
  
 @credential_name  
 Azure ストレージアカウントに対する認証に使用される SQL 資格情報の名前。 @credentail_nameは**SYSNAME**です。 指定した場合、バックアップはページ blob に格納されます。 このパラメーターが NULL の場合、バックアップはブロック blob として格納されます。 ページ blob へのバックアップは非推奨とされるため、新しいブロック blob バックアップ機能を使用することをお勧めします。 [!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)] の構成を変更するために使用する場合、このパラメーターは省略可能です。 指定しない場合、既存の構成値が保持されます。  
  
> [!WARNING]
>  **\@credential_name** パラメーターは、この時点ではサポートされていません。 ブロック blob へのバックアップのみがサポートされています。この場合、このパラメーターは NULL である必要があります。  
  
## <a name="return-code-value"></a>リターン コード値  
 0 (成功) または 1 (失敗)  
  
## <a name="security"></a>セキュリティ  
  
### <a name="permissions"></a>アクセス許可  
 **Db_backupoperator**データベースロールのメンバーシップ、 **ALTER ANY CREDENTIAL**権限、および**Sp_delete_backuphistory**ストアドプロシージャに対する**EXECUTE**権限が必要です。  
  
## <a name="examples"></a>使用例  
 最新の Azure PowerShell コマンドを使用して、ストレージアカウントコンテナーと SAS URL の両方を作成できます。 次の例では、mystorageaccount ストレージアカウントに新しいコンテナー mycontainer を作成し、完全なアクセス許可を持つ SAS URL を取得します。  
  
```powershell  
$context = New-AzureStorageContext -StorageAccountName mystorageaccount -StorageAccountKey (Get-AzureStorageKey -StorageAccountName mystorageaccount).Primary  
New-AzureStorageContainer -Name mycontainer -Context $context  
New-AzureStorageContainerSASToken -Name mycontainer -Permission rwdl -FullUri -Context $context  
```  
  
 次の例で[!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)]は、実行 SQL Server のインスタンスに対してを有効にし、リテンション期間ポリシーを30日に設定し、' mystorageaccount ' という名前のストレージアカウントの ' mycontainer ' という名前のコンテナーに宛先を設定します。  
  
```Transact-SQL 
Use msdb;  
Go  
   EXEC managed_backup.sp_backup_config_basic  
                @enable_backup=1  
                ,@container_url = 'https://mystorageaccount.blob.core.windows.net/mycontainer'  
                ,@retention_days=30;   
GO  
  
```
  
 次に、実行されている SQL Server インスタンスに対して [!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)] を無効にする例を示します。  
  
```Transact-SQL  
Use msdb;  
Go  
EXEC managed_backup.sp_backup_config_basic  
                @enable_backup=0;  
GO  
  
```  
  
## <a name="see-also"></a>関連項目  
 [managed_backup sp_backup_config_advanced &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/managed-backup-sp-backup-config-advanced-transact-sql.md)   
 [managed_backup.sp_backup_config_schedule &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/managed-backup-sp-backup-config-schedule-transact-sql.md)  
  
  
