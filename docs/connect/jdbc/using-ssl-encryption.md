---
description: SQL データベース接続で TLS 暗号化を使用してセキュリティで保護された通信チャネルを確立する方法について説明します。
title: 暗号化の使用
ms.custom: ''
ms.date: 03/31/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: vanto
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 8e566243-2f93-4b21-8065-3c8336649309
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 85407549aba05de1acae7108ab2fc14212c954cf
ms.sourcegitcommit: ebe81e2daa544f41c8ababb66a91c218ad0c2a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106176933"
---
# <a name="using-encryption"></a>暗号化の使用

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

トランスポート層セキュリティ (TLS) 暗号化により、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスとクライアント アプリケーションの間で、暗号化されたデータをネットワーク経由で送信できるようになります。  
  
トランスポート層セキュリティ (TLS) は、ネットワークやその他のインターネット通信において、重要な情報や機密情報の傍受を防ぐ安全な通信チャネルを確立するためのプロトコルです。 TLS を使用すると、クライアントとサーバー間で互いの ID を認証することができます。 参加者の認証が完了すると、TLS によって安全なメッセージ送信のための暗号化された接続が参加者間で提供されます。  
  
[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] は、接続プロパティで指定されたユーザーと、サーバーおよびクライアントの設定に基づいて、特定の接続上で暗号化を有効および無効にするためのインフラストラクチャを提供します。 ユーザーは、証明書ストアの場所とパスワード、証明書の検証に使用するホスト名、および通信チャネルを暗号化するタイミングを指定できます。  
  
TLS 暗号化を有効にすると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスとアプリケーション間でネットワーク送信されるデータのセキュリティが強化されます。 ただし、暗号化を有効にすると、パフォーマンスが低下します。  
  
このセクションの記事では、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] で TLS 暗号化をサポートするしくみについて説明します。これには、新しい接続プロパティと、クライアント側でトラスト ストアを構成する方法についての説明が含まれます。  
  
> [!NOTE]  
> TLS 証明書を検証するには、**hostNameInCertificate** 接続プロパティが推奨されます。  

## <a name="in-this-section"></a>このセクションの内容  

| [アーティクル] | 説明 |
| ----- | ----------- |
| [暗号化のサポートについて](understanding-ssl-support.md) | [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] で TLS 暗号化をサポートするしくみについて説明します。 |
| [暗号化を使用した接続](connecting-with-ssl-encryption.md) | 新しい TLS 固有の接続プロパティを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースに接続する方法について説明します。 |
| [暗号化のためのクライアントの構成](configuring-the-client-for-ssl-encryption.md) | クライアント側で既定のトラスト ストアを構成する方法と、クライアント コンピューターのトラスト ストアにプライベート証明書をインポートする方法について説明します。 |

## <a name="see-also"></a>参照

[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)
