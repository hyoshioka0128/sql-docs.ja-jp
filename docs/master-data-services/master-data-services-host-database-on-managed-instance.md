---
title: マネージインスタンスのホストデータベース |Microsoft Docs
description: マネージインスタンスで MDS データベースを構成する方法について説明します。
ms.custom: ''
ms.date: 07/01/2019
ms.prod: sql
ms.prod_service: mds
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: conceptual
ms.assetid: 19519697-c219-44a8-9339-ee1b02545445
author: v-redu
ms.author: lle
manager: craigg
monikerRange: '>=sql-server-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 0081ea193452e4e92938051bc7b4a40bc8631eaa
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70155378"
---
# <a name="host-database-on-managed-instance"></a>マネージインスタンスのホストデータベース

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

  この記事では、マネージインスタンスで MDS データベースを構成する方法について説明します。
  
## <a name="preparation"></a>準備

準備の次の手順を完了する必要があります。
- マネージインスタンスの作成と構成を完了します。 仮想ネットワークとポイント対サイト VPN を含めます。
- Web アプリケーションコンピューターの構成を完了します。
  - インストールポイント対サイト VPN を含めます。
  - 役割と機能をインストールします。

**データベース側:**

1. 仮想ネットワークを含む Azure SQL Database マネージインスタンスを作成します。 [クイック スタート:Azure SQL Database マネージインスタンスを作成する](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)
2. ポイント対サイト接続を構成します。 [ネイティブの Azure 証明書認証を使用して VNet へのポイント対サイト接続を構成する:Azure portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
3. SQL Database マネージインスタンスを使用して Azure Active Directory 認証を構成します。 [SQL を使用した Azure Active Directory 認証の構成と管理](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

**Web アプリケーションコンピューター側:**

1. ポイント対サイト接続証明書と VPN をインストールして、コンピューターが SQL Database マネージインスタンスにアクセスできることを確認します。 [ネイティブの Azure 証明書認証を使用して VNet へのポイント対サイト接続を構成する:Azure portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
2. 役割と機能をインストールします。 次の機能が必要です。

- ロール

      Internet Information Services
      Web Management Tools
      IIS Management Console
      World Wide Web Services
      Application Development
      .NET Extensibility 3.5
      .NET Extensibility 4.5
      ASP.NET 3.5
      ASP.NET 4.5
      ISAPI Extensions
      ISAPI Filters
      Common HTTP Features
      Default Document
      Directory Browsing
      HTTP Errors
      Static Content
      [Note: Do not install WebDAV Publishing]
      Health and Diagnostics
      HTTP Logging
      Request Monitor
      Performance
      Static Content Compression
      Security
      Request Filtering
      Windows Authentication

- 機能

      .NET Framework 3.5 (includes .NET 2.0 and 3.0)
      .NET Framework 4.5 Advanced Services
      ASP.NET 4.5
      WCF Services
      HTTP Activation [Note: This is required.]
      TCP Port Sharing
      Windows Process Activation Service
      Process Model
      .NET Environment
      Configuration APIs
      Dynamic Content Compression

## <a name="install-and-configure-includessmdsshort_mdincludesssmdsshort-mdmd-web-application"></a>Web アプリケーションの[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]インストールと構成

をインストールして構成[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]するには、次の手順を完了する必要があります。

1. SQL Server 2019 インクルード[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]機能をインストールします。
2. 管理インスタンス[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]にデータベースを作成します。
3. 用に web アプリケーションを[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]作成して構成します。
  
**SQL Server 2019 をインストールする**

をインストール[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]するには、SQL Server セットアップインストールウィザードまたはコマンドプロンプトを使用します。

1. Setup.exe をダブルクリックして、インストール ウィザードの手順を実行します。

2. [ [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]共有機能] の [機能の選択] ページで、を選択します。
これは [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]、アセンブリ、Windows PowerShell スナップインのほか、Web アプリケーションおよび Web サービス用のフォルダーおよびファイルがインストールされます。

    ![SQLServer2019--Config-MI-SQLFeatureSelection](../master-data-services/media/mds-sqlserver2019-config-mi-sqlfeatureselection.png "SQLServer2019-MI_SQLFeatureSelection")  

**データベースと Web サイトの設定**

1. Azure Virtual Network に接続して、マネージインスタンスに接続できることを確認します。

    ![mds-SQLServer2019-P2SVPNConnect](../master-data-services/media/mds-sqlserver2019-config-mi-p2svpnconnect.png "SQLServer2019-MI_P2SVPNConnect")  

2. を[!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]起動します。 左側のウィンドウで、 **[データベースの構成]** をクリックします。

3. **[データベースの作成]** をクリックし、**データベースの作成**ウィザードで [次へ] をクリックします。

4. **[データベースサーバー]** ページで**SQL Server インスタンス**に入力し、**認証の種類**を選択します。次に、接続の **[テスト]** をクリックして、認証の種類の資格情報を使用してデータベースに接続できることを確認します。オフ. [次へ] をクリックします。

   > [!NOTE]
   > - マネージインスタンスの SQL Server インスタンス ("xxxxxxx.xxxxxxx.database.windows.net" など)
   > - マネージインスタンスでは、 **"SQL Server アカウント"** と **"現在のユーザー– Active Directory 統合"** 認証の種類がサポートされています。
   > - 認証の種類として **[現在のユーザー-Active Directory 統合]** を選択すると、 **[ユーザー名]** ボックスは読み取り専用になり、コンピューターにログオンしている Windows ユーザーアカウントの名前が表示されます。 Azure 仮想マシン (vm) [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]で SQL Server 2019 を実行している場合は、 **[ユーザー名]** ボックスに、vm の名前と、vm 上のローカル管理者アカウントのユーザー名が表示されます。

    認証にマネージインスタンスの **"sysadmin"** 規則が含まれていることを確認してください。
![SQLServer2019-MI-CreateDBConnect](../master-data-services/media/mds-sqlserver2019-config-mi-createdbconnect.png "SQLServer2019-MI_CreateDBConnect")  

5. **[データベース名]** フィールドに名前を入力します。 必要に応じて、Windows 照合順序を選択して、 **[SQL Server の既定の照合順序]** チェック ボックスをオフにし、 **[大文字小文字を区別する]** などの利用可能なオプションを 1 つ以上クリックします。 **[次へ]** をクリックします。

    ![SQLServer2019] ---//(../master-data-services/media/mds-sqlserver2019-config-mi-createddbname.png "SQLServer2019-MI_CreatedDBName")  

6. **[ユーザー名]** フィールドに、の[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]既定のスーパーユーザーになるユーザーの Windows アカウントを指定します。 スーパー ユーザーには、すべての機能領域へのアクセス権があり、すべてのモデルを追加、削除、および更新できます。

    ![SQLServer2019] ------//(../master-data-services/media/mds-sqlserver2019-config-mi-createdbusername.png "SQLServer2019-MI_createDBUserName")

7. **[次へ]** をクリックして、 [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] データベースの設定の概要を表示し、 **[次へ]** をもう一度クリックしてデータベースを作成します。 **[続行して完了する]** ページが表示されます。

8. データベースが作成され、構成されたら、 **[完了]** をクリックします。

    **データベースの作成ウィザード**の設定の詳細については、 [「データベースの&#40; [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]作成&#41;ウィザード Configuration Manager](../master-data-services/create-database-wizard-master-data-services-configuration-manager.md)」を参照してください。

9. の **[データベースの構成]** ページ[!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]で、 **[データベースの選択]** をクリックします。

10. **[接続]** をクリック[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]し、手順8で作成したデータベースを選択して、[ **OK]** をクリックします。

    ![SQLServer2019-MI-connectDBName](../master-data-services/media/mds-sqlserver2019-config-mi-connectdbname.png "SQLServer2019-MI_connectDBName")

11. [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]の左ペインで、 **[Web の構成]** をクリックします。

12. **[Web サイト]** リスト ボックスで、 **[既定の Web サイト]** **[作成]** の順にクリックして、Web アプリケーションを作成します。
![mds-SQLServer2019-WebConfiguration](../master-data-services/media/mds-sqlserver2019-config-mi-webconfiguration.png "SQLServer2019-MI_WebConfiguration")

   > [!NOTE] 
   > **[既定の Web サイト]** を選択した場合、Web アプリケーションを作成する必要があります。 リスト ボックスで **[新しい Web サイトの作成]** を選択した場合、アプリケーションが自動的に作成されます。

    

13. **アプリケーションプール** セクションで、別のユーザー名を入力し、パスワードを入力して、OK をクリックします。
![SQLServer2019--Config-MI-CreateWebApplication](../master-data-services/media/mds-sqlserver2019-config-mi-createwebapplication.png "SQLServer2019-MI_CreateWebApplication")

   > [!NOTE]
   > ユーザーが、先ほど作成した Active Directory 統合認証を使用してデータベースにアクセスできることを確認する必要があります。 または、後で web.config の接続を変更する必要があります。

    

14. **[Web アプリケーションの作成]** ダイアログボックスの詳細については、「[ [web&#41;アプリケーションの作成] ダイアログボックス&#40; [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] Configuration Manager](../master-data-services/create-web-application-dialog-box-master-data-services-configuration-manager.md)」を参照してください。

15. Web **[アプリケーション]** ボックスの **[web 構成]** ページで、作成したアプリケーションをクリックし、 **[アプリケーションとデータベースの関連付け]** セクションで **[選択]** をクリックします。

16. **[接続]** をクリックして、Web アプリケーションと関連付けする必要がある [!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] データベースを選択し、 **[OK]** をクリックします。

    Web サイトの設定が完了しました。 **[Web 構成]** ページに、選択した web サイト、作成した web アプリケーション[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)] 、およびアプリケーションに関連付けられているデータベースが表示されるようになります。

    ![mds-SQLServer2019-WebConfigSelectDB](../master-data-services/media/mds-sqlserver2019-config-mi-webconfigselectdb.png "SQLServer2019-MI_WebConfigSelectDB")

17. **[適用]** をクリックします。 **[構成の完了]** メッセージ ボックスが表示されます。 メッセージ ボックスで **[OK]** をクリックして、Web アプリケーションを起動します。 Web サイトのアドレスは http://server 、name/web application/です。

## <a name="other-authentication-type-to-connect-managed-instance-database-on-web-application"></a>Web アプリケーションでマネージインスタンスデータベースを接続するためのその他の認証の種類

Web.config ファイルは、C:\Program Services\webapplication です。 SQL のサイト名の下に**あります。** ConnectionString を変更して、マネージインスタンスデータベースを接続する他の認証の種類を変更できます。

既定の認証の種類は "**Active Directory 統合**" です。接続文字列の例を次に示します。

```xml
<add name="MDS1" connectionString="Data Source=*****.*****.database.windows.net;Initial Catalog=MasterDataServices;Integrated Security=False;Connect Timeout=60;Authentication=&quot;Active Directory Integrated&quot;" />
```

また、Active Directory パスワード認証と SQL Server 認証もサポートしています。接続文字列の例を次に示します。

パスワード認証の Active Directory

```xml
<add name="MDS1" connectionString="Data Source=*****.*****.database.windows.net;Initial Catalog=MasterDataServices;Integrated Security=False;Connect Timeout=60;Authentication=&quot;Active Directory Password&quot; ; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!" />
```

SQL Server 認証 (SQL Server Authentication)

```xml
<add name="MDS1" connectionString="Data Source=*****.*****.database.windows.net;Initial Catalog=MasterDataServices;Integrated Security=False;Connect Timeout=60;User ID=UserName;Password=MyPassword!;" />
```

## <a name="upgrade-includessmdsshort_mdincludesssmdsshort-mdmd-and-database-version"></a>アップグレード[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]とデータベースのバージョン

**増設[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]**

SQL Server 2019 の**累積的な更新プログラム**をインストールすると、が[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]自動的に更新されます。

**データベースバージョンのアップグレード**

2019の**累積的な更新プログラム**SQL Server インストールした後に "クライアントのバージョンとデータベースのバージョンに互換性がない" という問題が発生した場合は、データベースのバージョンをアップグレードする必要があります。

![SQLServer2019--Config-MI-UpgradeDBPage](../master-data-services/media/mds-sqlserver2019-config-mi-upgradedbpage.png "SQLServer2019-MI_UpgradeDBPage")

1. を[!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]起動します。 左側のウィンドウで、 **[データベースの構成]** をクリックします。

2. の **[データベースの構成]** ページ[!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]で、 **[データベースの選択]** をクリックします。

3. **[接続]** をクリック[!INCLUDE[ssMDSshort_md](../includes/ssmdsshort-md.md)]し、web アプリケーションに関連付けられているデータベースを選択して、[ **OK]** をクリックします。

    ![SQLServer2019-MI-ConnectDBName](../master-data-services/media/mds-sqlserver2019-config-mi-connectdbname.png "SQLServer2019-MI_ConnectDBName")

4. **[データベースのアップグレード]** をクリックします。 ボタン

    ![SQLServer2019--Config-MI-SelectUpgradeDB](../master-data-services/media/mds-sqlserver2019-config-mi-selectupgradedb.png "SQLServer2019-MI_SelectUpgradeDB")

5. データベースのアップグレードウィザードの **[ようこそ]** ページで **[次へ]** をクリックし、 **[アップグレードの確認]** ページをクリックします。

    ![SQLServer2019--MI-UpgradeDBWizard](../master-data-services/media/mds-sqlserver2019-config-mi-upgradedbwizard.png "SQLServer2019-MI_UpgradeDBWizard")

6. すべてのタスクが完了したら、 **[完了]** をクリックします。

## <a name="see-also"></a>関連項目

 [マスターデータサービスデータベース](../master-data-services/master-data-services-database.md)[マスターデータマネージャー Web アプリケーション](../master-data-services/master-data-manager-web-application.md)[[データベースの&#40;構成&#41; ] ページマスターデータサービス構成マネージャー](../master-data-services/database-configuration-page-master-data-services-configuration-manager.md)[マスターデータサービス&#40;MDS&#41;の新機能](../master-data-services/what-s-new-in-master-data-services-mds.md)
