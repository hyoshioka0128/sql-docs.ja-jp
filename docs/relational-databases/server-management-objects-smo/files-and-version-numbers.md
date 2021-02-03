---
description: ファイルとバージョン番号
title: ファイルとバージョン番号 |Microsoft Docs
ms.custom: ''
ms.date: 08/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- SQL Server Management Objects, versions
- components [SMO]
- files [SMO], components
- SMO [SQL Server], versions
- versions [SMO]
ms.assetid: 510907b6-e7a9-41bd-b892-d6d99a5118e1
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: e82feaa49d5474cd0f621b36b3099dc6f97c8e65
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97467213"
---
# <a name="files-and-version-numbers"></a>ファイルとバージョン番号
[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  SqlManagementObjects NuGet パッケージには、必要なすべての SQL Server 管理オブジェクト (SMO) コンポーネントが含まれています。 SMO は複数のマネージド アセンブリに実装されます。 クライアント上またはサーバー上のいずれかで SMO アプリケーションを開発することができます。  

> > [!Important]
> > SMO アセンブリのファイルバージョンは、メジャーとして表示されます。**0**。ビルド. リビジョン。 ただし、埋め込みアセンブリのバージョンは Major です。**100**。ビルド. リビジョン。 これは、各アプリケーションで使用されている SMO のバージョンを別々に保持し、1つの更新プログラムが他のアプリケーションに影響を与えないようにするためです。
> > 
> > このため、これらのバージョンのアセンブリをグローバルアセンブリキャッシュ (GAC) にインストール **しない** でください。 これにより、Management Studio などの他のアプリケーションが中断する可能性があり [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 
  
|ファイル|説明|  
|-----------|-----------------|  
|Microsoft.SqlServer.ConnectionInfo.dll|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスへの接続のサポートが含まれています。|  
|Microsoft.SqlServer.ServiceBrokerEnum.dll|[!INCLUDE[msCoName](../../includes/msconame-md.md)] Service Broker のプログラミングのサポートが含まれています。 Service Broker にアクセスするプログラムにのみ必要です。|  
|Microsoft.SqlServer.Smo.dll|SMO クラスの大部分が含まれます。|  
|Microsoft.SqlServer.SmoExtended.dll<br /><br /> Microsoft.SqlServer.Management.Sdk.Sfc.dll<br /><br /> Microsoft.SqlServer.SqlEnum.dll|SMO クラスのサポートが含まれています。|  
|Microsoft.SqlServer.WmiEnum.dll|Windows Management Instrumentation (WMI) プロバイダー クラスが含まれています。 WMI プロバイダー クラスを使用するプログラムにのみ必要です。|  
|Microsoft.SqlServer.RegSvrEnum.dll|登録済みサーバー クラスが含まれています。 登録済みサーバー クラスを使用するプログラムにのみ必要です。|  
  
  
