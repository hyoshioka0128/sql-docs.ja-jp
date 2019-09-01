---
title: Azure Blob Storage サービスを使用したバックアップと復元の SQL Server |Microsoft Docs
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
ms.assetid: 6a0c9b6a-cf71-4311-82f2-12c445f63935
author: MikeRayMSFT
ms.author: mikeray
manager: craigg
ms.openlocfilehash: 82220ab3649cb3af858e5a61e4c3ddfc5116d661
ms.sourcegitcommit: 3b1f873f02af8f4e89facc7b25f8993f535061c9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70175809"
---
# <a name="sql-server-backup-and-restore-with-azure-blob-storage-service"></a>Azure Blob Storage サービスを使用したバックアップと復元の SQL Server
  このトピックで[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]は、 [Azure Blob ストレージサービス](http://www.windowsazure.com/develop/net/how-to-guides/blob-storage/)に対するバックアップと復元について説明します。 また、Azure Blob service を使用してバックアップを格納[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]する利点の概要についても説明します。  
  
 SQL Server は、次の方法で Azure Blob ストレージサービスへのバックアップの格納をサポートしています。  
  
-   **Azure へのバックアップを管理します。** ディスクとテープへのバックアップに使用したのと同じ方法を使用して、バックアップ先として URL を指定することで、Azure storage にバックアップできるようになりました。  この機能を使用すると、手動でバックアップすることも、ローカル ストレージまたはその他のオフサイト オプションの場合と同じように独自のバックアップ方法を構成することもできます。 この機能は、 **SQL Server Backup to URL**とも呼ばれます。 詳細については、「 [SQL Server Backup to URL](sql-server-backup-to-url.md)」を参照してください。 この機能は、SQL Server 2012 SP1 CU2 以降で使用できます。  
  
    > [!NOTE]  
    >  SQL Server 2014 より前の SQL Server バージョンでは、Azure ツールへのバックアップ SQL Server を使用して、Azure storage へのバックアップをすばやく簡単に作成できます。 詳細については、 [ダウンロード センター](https://go.microsoft.com/fwlink/?LinkID=324399)を参照してください。  
  
-   **Azure へのバックアップを SQL Server 管理するには、次のようにします。** SQL Server を構成することにより、単一データベースまたは複数データベースのバックアップ方法やスケジュールを管理することも、インスタンス レベルでの既定値を設定することもできます。 この機能はと **[!INCLUDE[ss_smartbackup](../../includes/ss-smartbackup-md.md)]** 呼ばれます。 詳細については[、「Azure へのマネージバックアップの SQL Server](sql-server-managed-backup-to-microsoft-azure.md)」を参照してください。 この機能は、SQL Server 2014 以降で使用できます。  
  
## <a name="benefits-of-using-the-azure-blob-service-for-includessnoversionincludesssnoversion-mdmd-backups"></a>バックアップに[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Azure Blob サービスを使用する利点  
  
-   柔軟で信頼性が高く、制限のないオフサイト ストレージ:Azure Blob service にバックアップを格納すると、便利で柔軟性が高く、簡単にアクセスできるオフサイトオプションになります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バックアップ用にオフサイト ストレージを作成するのは、既存のスクリプトやジョブを変更するのと同様に簡単です。 オフサイト ストレージは、通常、災害発生時にオフサイトと運用データベース両方の場所に影響しないように、運用データベースの場所から十分に離れた場所に設置する必要があります。 BLOB ストレージを地理的に離れた場所にレプリケートすることによって、地域全体に影響する可能性がある災害が発生した場合の保護レベルが強化されます。 また、バックアップは場所や時間を問わず使用でき、復元のために簡単にアクセスできます。  
  
-   バックアップ アーカイブ:Azure Blob Storage サービスは、バックアップをアーカイブするために頻繁に使用されるテープオプションに代わる優れた手段を提供します。 テープ ストレージでは、オフサイトの施設への物理的な輸送手段とメディアを保護する手段が必要になります。 Azure Blob Storage にバックアップを格納すると、高速で可用性が高く、持続性のあるアーカイブオプションが提供されます。  
  
-   ハードウェア管理のオーバーヘッドなし: Azure サービスを使用したハードウェア管理のオーバーヘッドはありません。 Azure サービスはハードウェアを管理し、ハードウェア障害に対する冗長性と保護のために geo レプリケーションを提供します。  
  
-   現在、azure 仮想[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]マシンで実行されているのインスタンスでは、アタッチされたディスクを作成することによって、azure Blob ストレージサービスへのバックアップを実行できます。 ただし、Azure 仮想マシンに接続できるディスクの数には制限があります。 ディスク数の上限は、XL インスタンスでは 16 台、サイズの小さいインスタンスではこれより少なくなります。 Azure Blob Storage に直接バックアップを有効にすることで、16ディスク制限を回避できます。  
  
     さらに、azure Blob ストレージサービスに格納されているバックアップファイルは、オンプレミス[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]または azure 仮想マシンで実行されている別[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]ので直接使用できます。データベースのアタッチ/デタッチは不要です。VHD をダウンロードしてアタッチしています。  
  
-   コスト面での利点:使用するサービスにのみ料金がかかります。 オフサイトのバックアップ アーカイブ オプションとして、優れたコスト効果を得ることができます。 詳細とリンクについては、「 [Azure の課金に関する注意点](#Billing)」を参照してください。  
  
##  <a name="Billing"></a>Azure の課金に関する考慮事項:  
 Azure storage のコストを把握することで、Azure でのバックアップの作成と保存にかかるコストを予測することができます。  
  
 [Azure 料金計算ツール](https://go.microsoft.com/fwlink/?LinkId=277060)を使用すると、コストを見積もることができます。  
  
 **ストレージ:** 料金は使用領域に基づいており、段階的に、また、冗長性のレベルに応じて計算されます。 詳細と最新情報については、「 **料金の詳細** 」の「 [データ管理](https://go.microsoft.com/fwlink/?LinkId=277059) 」を参照してください。  
  
 **データ転送:** Azure への受信データ転送は無料です。 送信転送は、帯域幅の使用量に対して課金され、段階的な地域固有の区分に基づいて計算されます。 詳細については、「 [料金の詳細](https://go.microsoft.com/fwlink/?LinkId=277061) 」の「データ転送」を参照してください。  
  
## <a name="see-also"></a>参照  
 [SQL Server Backup to URL に関するベスト プラクティスとトラブルシューティング](sql-server-backup-to-url-best-practices-and-troubleshooting.md)   
 [システム データベースのバックアップと復元 &#40;SQL Server&#41;](back-up-and-restore-of-system-databases-sql-server.md)   
 [チュートリアル: Azure Blob Storage サービスへのバックアップと復元の SQL Server](../tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service.md)   
 [SQL Server Backup to URL](sql-server-backup-to-url.md)  
  
  
