---
title: さまざまなエディションでサポートされている機能 - SQL Server Reporting Services | Microsoft Docs
description: SQL Server のさまざまなエディションでサポートされる SQL Server Reporting Services (SSRS) 機能の詳細について説明します。
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: reporting-services
ms.topic: conceptual
author: maggiesMSFT
ms.author: maggies
ms.date: 12/16/2019
ms.openlocfilehash: a818db1a2bc8626f19bd3f98abdbb721ccbd74b1
ms.sourcegitcommit: 09122d02fc3d86c6028366653337c083da8a3f4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107072401"
---
# <a name="sql-server-reporting-services-features-supported-by-editions"></a>SQL Server の各エディションでサポートされる SQL Server Reporting Services の機能

[!INCLUDE[ssrs-appliesto](../includes/ssrs-appliesto.md)] [!INCLUDE[ssrs-appliesto-2016-and-later](../includes/ssrs-appliesto-2016-and-later.md)] [!INCLUDE[ssrs-appliesto-pbirsi](../includes/ssrs-appliesto-pbirs.md)]

このトピックでは、SQL Server のさまざまなエディションでサポートされる SQL Server Reporting Services (SSRS) 機能の詳細について説明します。 180 日の試用期間中、SQL Server Evaluation Edition をご利用いただけます。  

## <a name="related-links"></a>関連リンク
  
 - [SQL Server Reporting Services (SSRS) のリリース ノート](release-notes-reporting-services.md)。 
 - [SQL Server Reporting Services (SSRS) の新機能](~/reporting-services/what-s-new-in-sql-server-reporting-services-ssrs.md)。
 - [SQL Server の各エディションでサポートされている機能](~/sql-server/editions-and-components-of-sql-server-version-15.md)

##  <a name="sql-server-reporting-services"></a><a name="SSRS"></a> SQL Server Reporting Services (SQL Server Reporting Services)  

Evaluation Edition および Developer Edition でサポートされている機能については、次の表の SQL Server Enterprise Edition の列を参照してください。

|機能名|Enterprise|Standard|Web|Express with Advanced Services|Developer|  
|------|---------|---------------|-----------|-------|---------|  
| Power BI レポートと Excel ブック | はい (ソフトウェア アシュアランスあり) | | | | はい |
|モバイル レポートと分析|はい||||はい|  
|サポートされているカタログ データベースの [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディション|Standard 以上|Standard 以上|Web|Express|Standard 以上|  
|サポートされているデータ ソースの [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディション|すべての   [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディション|すべての [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディション|Web|Express|すべての [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディション|  
|レポート サーバー|はい|Yes|Yes|Yes|はい|  
|レポート デザイナー|はい|Yes|Yes|Yes|はい|  
|レポート デザイナー Web ポータル|はい|Yes|Yes|Yes|はい|  
|ロール ベース セキュリティ|はい|Yes|Yes|Yes|はい|  
|Excel、PowerPoint、Word、PDF、およびイメージへのエクスポート|はい|Yes|Yes|Yes|はい|  
|強化されたゲージとグラフ|はい|Yes|Yes|Yes|はい|  
|[!INCLUDE[sspowerbi](../includes/sspowerbi-md.md)] ダッシュボードへのレポート アイテムのピン留め|はい|Yes|Yes|Yes|はい|  
|カスタム認証|はい|Yes|Yes||はい|  
|データ フィードとしてのレポート|はい|Yes|Yes|Yes|はい|  
|モデルのサポート|はい|Yes|Yes||はい|  
|ロールベースのセキュリティのカスタム ロールの作成|はい|Yes|||はい|  
|モデル アイテムのセキュリティ|はい|Yes|||はい|  
|無限クリック スルー|はい|Yes|||はい|  
|共有コンポーネント ライブラリ|はい|Yes|||はい|  
|電子メールおよびファイル共有のサブスクリプションとスケジュール設定|はい|Yes|||はい|  
|レポート履歴、実行スナップショット、およびキャッシュ|はい|Yes|||はい|  
|SharePoint 統合<sup>2</sup>|はい|Yes|||はい|  
|リモートおよび非 SQL データ ソースのサポート<sup>1</sup>|はい|Yes|||はい|  
|データ ソース、配信およびレンダリング、RDCE の拡張性|はい|Yes|||はい|  
|カスタム ブランド化|はい||||はい|  
|データ ドリブン レポートのサブスクリプション|はい||||はい|  
|スケール アウト配置 (Web ファーム)|はい||||はい|  
|警告<sup>2</sup> (SSRS 2016) |はい||||はい|  
|Power View<sup>2</sup> (SSRS 2016) |はい||||はい| 
|コメント<sup>3</sup> |はい|Yes|Yes|Yes|はい|  

 <sup>1</sup> SQL Server Reporting Services (SSRS) でサポートされるデータ ソースの詳細については、「[Reporting Services でサポートされるデータ ソース (SSRS)](../reporting-services/report-data/data-sources-supported-by-reporting-services-ssrs.md)」を参照してください。  
  
 <sup>2</sup> SharePoint モードの SQL Server 2016 Reporting Services が必要です。 詳細については、[SharePoint モードでの SQL Server Reporting Services のインストール](../reporting-services/install-windows/install-reporting-services-sharepoint-mode.md)に関するページを参照してください。 SQL Server 2017 Reporting Services 以降では、SharePoint との統合は使用できません。 

<sup>3</sup> Power BI Report Server と SQL Server 2017 Reporting Services 以降のみ。

> [!NOTE]
> SQL Server Express with Tools および SQL Server Express では、SQL Server Reporting Services はサポートされていません。
  
## <a name="edition-requirements-for-the-report-server-database"></a>レポート サーバー データベースのエディションの要件
 レポート サーバー データベースを作成するときは、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のすべてのエディションでデータベースをホストできるわけではないことに注意してください。 次の表に、SQL Server [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] の特定のエディションで使用できる [!INCLUDE[ssDE](../includes/ssde-md.md)] のエディションを示します。  
  
|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Reporting Services のエディション|データベースをホストするために使用するデータベース エンジン インスタンスのエディション|  
|----------------------------------------------------------------------|---------------------------------------------------------------------------|  
|Enterprise|Enterprise Edition または Standard Edition (ローカルまたはリモート)|  
|Standard|Enterprise Edition または Standard Edition (ローカルまたはリモート)|  
|Web|Web Edition (ローカルのみ)|  
|Express with Advanced Services|Express with Advanced Services (ローカルのみ)|  
|評価|評価|  
  
##  <a name="business-intelligence-clients"></a><a name="BIC"></a> Business Intelligence クライアント  
次のソフトウェア クライアント アプリケーションは、Microsoft ダウンロード センターで入手できます。 これらは、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] インスタンス上で実行されるビジネス インテリジェンス ドキュメントの作成に役立ちます。 作成したドキュメントをサーバー環境でホストする場合は、そのドキュメントの種類をサポートする [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] のエディションを使用してください。 これらのクライアント アプリケーションで作成したドキュメントをホストするのに必要なサーバー機能を備えた [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディションを次の表に示します。  
  
|ツール名|Enterprise|Standard|Web|Express with Advanced Services|Developer|  
|---------------|----------------|--------------|------------------------|-------------|---------------| 
| Power BI Report Server、 **.pbix** 用に最適化された Power BI Desktop | はい (ソフトウェア アシュアランスあり) | | | | はい |
|[!INCLUDE[ssRBnoversion](../includes/ssrbnoversion.md)]、 **.rdl**、 **.rds**|はい|Yes|Yes|Yes|はい|  
|[!INCLUDE[SS_MobileReptPub_Long](../includes/ss-mobilereptpub-long.md)]、 **.rsmobile**|はい||||はい|  
|モバイル デバイス (iOS、Windows 10、Android) 用 Power BI アプリ、 **.rsmobile**|はい||||はい|  
  
> [!NOTE]  
> * 上記の表では、これらのクライアント ツールを有効にするために必要な [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] エディションが示されています。 ただし、これらのツールは、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] の任意のエディションでホストされているデータにアクセスできます。  
> * [!INCLUDE[SS_MobileReptPub_Long](../includes/ss-mobilereptpub-long.md)] モバイル レポートの作成の単一ポイントです。 SSRS サーバーに接続してデータ ソースにアクセスし、レポートを作成します。 次に、それらを SSRS サーバーにパブリッシュすることで、組織内の他のユーザーがサーバーまたはモバイル デバイスでアクセスできるようにします。 ローカル データ ソースの場合は、[!INCLUDE[SS_MobileReptPub_Long](../includes/ss-mobilereptpub-long.md)] スタンド アロンを使用することもできます。  
> * オンプレミスの [!INCLUDE[ssRSCurrent](../includes/ssrscurrent-md.md)]、クラウドの [!INCLUDE[sspowerbi](../includes/sspowerbi-md.md)]、またはその両方のいずれをレポート配信ソリューションとして使用しても、モバイル デバイスでダッシュボードおよびモバイル レポートにアクセスするために必要なのは、1 つのモバイル アプリのみです。 [!INCLUDE[sspowerbi](../includes/sspowerbi-md.md)] アプリは Windows、iOS、または Android アプリ ストアからダウンロードできます。  

## <a name="next-steps"></a>次のステップ

* [SQL Server 2017 の各エディションでサポートされている機能](~/sql-server/editions-and-components-of-sql-server-2017.md)を確認する。 

* [SQL Server のインストールを計画する](../sql-server/install/planning-a-sql-server-installation.md)。

* その他の質問 [SQL Server Reporting Services フォーラム](https://go.microsoft.com/fwlink/?LinkId=620231)で質問する。
