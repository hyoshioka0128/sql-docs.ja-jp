---
description: ターゲット サーバーでの暗号化オプションの設定
title: ターゲット サーバーでの暗号化オプションの設定
ms.custom: seo-lt-2019
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
helpviewer_keywords:
- SQL Server Agent, encryption
- target servers [SQL Server], encryption
- multiserver environments [SQL Server], setting encryption options on target servers
ms.assetid: 1a9fd539-e166-4ea8-9f21-ac400ca74dee
author: markingmyname
ms.author: maghan
ms.reviewer: ''
monikerRange: = azuresqldb-mi-current || >= sql-server-2016 || = sqlallproducts-allversions
ms.openlocfilehash: 832b73c751a6c475afd75769c4cf18d8e2e609a5
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88418068"
---
# <a name="set-encryption-options-on-target-servers"></a>ターゲット サーバーでの暗号化オプションの設定
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

> [!IMPORTANT]  
> 現在、[Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) によって、すべてではありませんが、ほとんどの SQL Server エージェントの機能がサポートされています。 詳細については、[Azure SQL Managed Instance と SQL Server の T-SQL の相違点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)に関するページを参照してください。

マスター サーバーと一部またはすべてのターゲット サーバーの間で トランスポート層セキュリティ (TLS) (旧称 Secure Sockets Layer (SSL)) 暗号通信の証明書を使用できない場合、これらの間のチャネルを暗号化するには、必要なセキュリティ レベルを使用するようにターゲット サーバーを構成します。  
  
マスター サーバーとターゲット サーバーの間の特定の通信チャネルに求められる適切なセキュリティ レベルを構成するには、ターゲット サーバーの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent registry subkey **\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\\** \<*instance_name*> **\SQLServerAgent\MsxEncryptChannelOptions(REG_DWORD)** を、次のいずれかの値に設定します。 \<*instance_name*> の値は **MSSQL.** _n_ です。 たとえば、 **MSSQL.1** や **MSSQL.3**となります。  
  
|値|説明|  
|---------|---------------|  
|**0**|このターゲット サーバーとマスター サーバーの間の暗号化を無効にします。 ターゲット サーバーとマスター サーバー間のチャネルのセキュリティを別の手段で保護する場合にこのオプションを選択します。|  
|**1**|このターゲット サーバーとマスター サーバーの間のみ暗号化を有効にしますが、証明書の検証は必要ありません。|  
|**2**|このターゲット サーバーとマスター サーバーの間で、完全な TLS 暗号化と証明書の検証を有効にします。 これは、既定の設定です。 別の値を選択する具体的な理由がある場合を除いて、この値を変更しないことをお勧めします。|  
  
**1** または **2** を指定する場合、マスター サーバーとターゲット サーバーの両方で TLS を有効にしている必要があります。 **2** を指定する場合、マスター サーバーには適切に署名された証明書も必要になります。  
  
> [!CAUTION]  
> [!INCLUDE[ssNoteRegistry](../../includes/ssnoteregistry-md.md)]  
  
## <a name="see-also"></a>参照  
[方法: データベース エンジンへの暗号化接続の有効化 (SQL Server 構成マネージャー)](https://msdn.microsoft.com/e1e55519-97ec-4404-81ef-881da3b42006)  
  
