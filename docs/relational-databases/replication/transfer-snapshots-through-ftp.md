---
title: FTP によるスナップショットの転送 | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
helpviewer_keywords:
- snapshots [SQL Server replication], FTP snapshots
- FTP snapshots [SQL Server replication]
- snapshot replication [SQL Server], FTP
ms.assetid: 55c30791-cd2a-420b-8ba7-5700e005cb45
author: MashaMSFT
ms.author: mathoma
manager: craigg
ms.openlocfilehash: 98ea94e54518a5978bb974c1e9ce2f1f2b9f9372
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47690147"
---
# <a name="transfer-snapshots-through-ftp"></a>FTP によるスナップショットの転送
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]
  既定では、スナップショットは、UNC (Universal Naming Convention) 共有として定義されたフォルダーに格納されます。 レプリケーションでは、UNC 共有ではなく、FTP (File Transfer Protocol) 共有を指定することもできます。 FTP を使用するには、FTP サーバーを構成してから、FTP を使用するためのパブリケーションと 1 つ以上のサブスクリプションを構成する必要があります。 FTP サーバーの構成方法の詳細については、インターネット インフォメーション サービス (IIS) のドキュメントを参照してください。 パブリケーションに対して FTP 情報を指定すると、そのパブリケーションに対するサブスクリプションでは、既定で FTP を使用します。 IIS が動作しているコンピューターがファイアウォールによってディストリビューターから分離されている場合、FTP は Web 同期との組み合わせでのみ使用されます。 この場合、FTP を使用してディストリビューター、および IIS を実行中のコンピューターからスナップショットを転送できます (スナップショットは常に HTTPS を使用してサブスクライバーに転送されます)。  
  
> [!IMPORTANT]  
>  FTP 共有では、FTP パスワードを格納する必要があり、このパスワードがプレーン テキストでサブスクライバー (Web 同期が使用されている場合は、IIS が動作しているコンピューター) から FTP サーバーに送信されるため、FTP 共有ではなく [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows 認証と UNC 共有を使用することをお勧めします。 また、1 つのアカウントがスナップショット共有へのアクセスを制御するため、フィルター選択されたマージ パブリケーションへのサブスクライバーだけに、スナップショット ファイルのデータ パーティションからスナップショット ファイルへのアクセス権が与えられていることを確認することはできません。  
  
 FTP でスナップショットを配信するには、「 [Deliver a Snapshot Through FTP](../../relational-databases/replication/publish/deliver-a-snapshot-through-ftp.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [マージ レプリケーションの Web 同期](../../relational-databases/replication/web-synchronization-for-merge-replication.md)   
 [Initialize a Subscription with a Snapshot (スナップショットを使用したサブスクリプションの初期化)](../../relational-databases/replication/initialize-a-subscription-with-a-snapshot.md)   
 [スナップショット オプション](../../relational-databases/replication/snapshot-options.md)  
  
  
