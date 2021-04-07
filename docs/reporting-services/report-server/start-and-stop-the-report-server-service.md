---
title: レポート サーバー サービスの開始と停止 | Microsoft Docs
description: レポート サーバー Web サービス、Web ポータル、バックグラウンド処理アプリケーションが含まれる Windows サービスを起動および停止する方法について説明します。
ms.date: 03/22/2021
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: report-server
ms.topic: conceptual
helpviewer_keywords:
- stopping Report Server service
- Report Server Windows service, starting
- Report Server service, starting
- starting Report Server service
ms.assetid: 6ec69ac3-27b0-472d-91e1-733af9078ed2
author: maggiesMSFT
ms.author: maggies
ms.openlocfilehash: d56cf652fab81403c50a0de122c1b5ad004de1a8
ms.sourcegitcommit: ab0c654d924eeb5647e47444abb59d934345b205
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450143"
---
# <a name="start-and-stop-the-report-server-service"></a>レポート サーバー サービスの開始と停止

[!INCLUDE[ssrs-appliesto](../../includes/ssrs-appliesto.md)] [!INCLUDE[ssrs-appliesto-2016-and-later](../../includes/ssrs-appliesto-2016-and-later.md)] [!INCLUDE[ssrs-appliesto-pbirsi](../../includes/ssrs-appliesto-pbirs.md)]

  レポート サーバーは、レポート サーバー Web サービス、Web ポータル、およびバックグラウンド処理アプリケーションを含んだ Windows サービスとして実装されています。 レポート サーバーのなんらかの機能を使用するには、このサービスが実行されている必要があります。 このサービスを停止すると、すべてのレポート サーバー処理が停止されます。  
  
 このサービスが停止している間は、スケジュール設定されたレポートやサブスクリプション処理 (つまり、サービスが実行されていれば、本来発生するはずの処理) に対する要求はキューに追加されます。 これは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントが実行するジョブによって、イベントが作成されるためです。 サービスの停止中に処理のバックログが蓄積されないようにする場合は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントも停止してください。  
  
 レポート サーバー サービスの開始および停止には、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 構成ツール、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成マネージャー、 [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows で提供されるサービス ツールなどさまざまなツールを使用できます。  
  
> [!NOTE]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Configuration Manager は、SQL Server Reporting Services 2016 以前のオプションに過ぎません。 Reporting Services 2017 以降または Power BI Report Server は含まれません。
  
 サービス アカウントの変更など、サービスの開始および停止以外の操作を行うには、[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 構成ツールを使用する必要があります。 他のツールでサービス アカウントを変更すると、[!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] のインストールと暗号化キーが壊れる可能性があります。 詳細については、[レポート サーバー サービス アカウントの構成 (レポート サーバー構成マネージャー)](../../reporting-services/install-windows/configure-the-report-server-service-account-ssrs-configuration-manager.md) に関する記事を参照してください。  
  
 サービスを一時停止および再開することはできません。 開始パラメーターはありません。 明示的な依存関係はありませんが、レポート サーバー上のサブスクリプションまたはスケジュールされた操作をサポートする場合は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントも実行している必要があります。  
  
## <a name="use-the-reporting-services-configuration-tool"></a>Reporting Services 構成ツールの使用  
  
1. [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 構成ツールを起動して、レポート サーバーに接続します。  
  
2. [レポート サーバーの状態] ページで、 **[停止]** または **[開始]** を選択します。  
  
## <a name="use-administrative-tools"></a>管理ツールの使用  

1. [管理ツール] の **[サービス]** を開きます。
2. **[SQL Server Reporting Services (MSSQLSERVER)]** 、 **[SQL Server Reporting Services]** 、 **[Power BI Report Server]** のいずれかを右クリックします。
3. **[停止]** または **[再起動]** を選択します。

  
Reporting Services 2016 以前のバージョンでは、複数のインスタンスを実行しているか、レポート サーバーを名前付きインスタンスとして実行している場合は、かっこ内のインスタンス名が、停止または再起動するレポート サーバー インスタンスに対応していることを確認してください。  

  
## <a name="see-also"></a>関連項目  
 [レポート サーバー構成マネージャー (ネイティブ モード)](../../reporting-services/install-windows/reporting-services-configuration-manager-native-mode.md)   
 [SQL Server エージェント サービスの開始、停止、または一時停止](../../ssms/agent/start-stop-or-pause-the-sql-server-agent-service.md)  
  
