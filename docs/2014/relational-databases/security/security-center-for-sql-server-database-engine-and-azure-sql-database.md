---
title: SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター | Microsoft Docs
ms.custom: ''
ms.date: 09/23/2015
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- Security [SQL Server]
helpviewer_keywords:
- SQL Server, security
- security [SQL Server]
- database security [SQL Server]
- databases [SQL Server], security
ms.assetid: dfb39d16-722a-4734-94bb-98e61e014ee7
author: VanMSFT
ms.author: vanto
manager: craigg
ms.openlocfilehash: e3084e9d878085c20ab8af27cffb04758efceaab
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48200842"
---
# <a name="security-center-for-sql-server-database-engine-and-azure-sql-database"></a>SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター
  このページのセキュリティと保護に関して必要な情報の検索に役立つリンクを提供する、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]と[!INCLUDE[ssSDSfull](../../../includes/sssdsfull-md.md)]します。  
  
> [!NOTE]  
>  機能[!INCLUDE[ssSDSfull](../../../includes/sssdsfull-md.md)]継続的に改善します。 に関する最新情報については、このトピックの最新バージョンを参照してください[!INCLUDE[ssSDS](../../includes/sssds-md.md)]します。  
  
|認証: ユーザーはだれか|認証: 何を実行できるか|暗号化: 秘密データの格納|接続のセキュリティ: 制限とセキュリティ保護|監査: アクセスの記録|  
|----------------------------------|-------------------------------------|-------------------------------------|---------------------------------------------------|--------------------------------|  
|**だれが認証したか**<br /><br /> [![セキュリティ センター マップの Windows 認証](../../database-engine/media/security-center-map-windows-authenticaion.png "セキュリティ センター マップの Windows 認証")](http://msdn.microsoft.com/library/ms144284.aspx)<br /><br /> <br /><br /> [![セキュリティ センター マップの SQL Server 認証](../../database-engine/media/security-center-map-sql-authenticaion.png "セキュリティ センター マップの SQL Server 認証")](http://msdn.microsoft.com/library/ms144284.aspx)<br /><br /> **どこで認証されたか**<br /><br /> [![セキュリティ センター マップのログインとユーザー](../../database-engine/media/security-center-map-logins-users.png "セキュリティ センター マップのログインとユーザー")](http://msdn.microsoft.com/library/aa337562.aspx)<br /><br /> <br /><br /> [![セキュリティ センター マップの包含データベース ユーザー](../../database-engine/media/security-center-map-contained-users.png "セキュリティ センター マップの包含データベース ユーザー")](http://msdn.microsoft.com/library/ff929188.aspx)<br /><br /> **他の ID の使用**<br /><br /> [![セキュリティ センター マップの資格情報](../../database-engine/media/security-center-map-credentials.png "セキュリティ センター マップの資格情報")](http://msdn.microsoft.com/library/ms161950.aspx)<br /><br /> [![セキュリティ センター マップの Execute As Login](../../database-engine/media/security-center-map-exec-as-login.png "セキュリティ センター マップの Execute As Login")](http://msdn.microsoft.com/library/ms181362.aspx)<br /><br /> [![セキュリティ センター マップの Execute As User](../../database-engine/media/security-center-map-exec-as-user.png "セキュリティ センター マップの Execute As User")](http://msdn.microsoft.com/library/ms181362.aspx)<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")|**権限の許可、取り消し、および拒否**<br /><br /> [![セキュリティ センター マップのセキュリティ保護可能なクラス](../../database-engine/media/security-center-map-securable-classes.png "セキュリティ センター マップのセキュリティ保護可能なクラス")](http://msdn.microsoft.com/library/ms190401.aspx)<br /><br /> [![セキュリティ センター マップのサーバー アクセス許可](../../database-engine/media/security-center-map-srv-perms.png "セキュリティ センター マップのサーバー アクセス許可")](http://msdn.microsoft.com/library/ms191291.aspx)<br /><br /> [![セキュリティ センター マップのデータベースのアクセス許可](../../database-engine/media/security-center-map-db-perms.png "セキュリティ センター マップのデータベースのアクセス許可")](http://msdn.microsoft.com/library/ms191291.aspx)<br /><br /> **ロールによるセキュリティ**<br /><br /> [![セキュリティ センター マップのサーバー ロール](../../database-engine/media/security-center-map-srv-roles.png "セキュリティ センター マップのサーバー ロール")](http://msdn.microsoft.com/library/ms188659.aspx)<br /><br /> [![セキュリティ センター マップのデータベース ロール](../../database-engine/media/security-center-map-db-roles.png "セキュリティ センター マップのデータベース ロール")](http://msdn.microsoft.com/library/ms189121.aspx)<br /><br /> `Restricting Data Access to Selected Data Elements`<br /><br /> [![セキュリティ センター マップのビューとプロシージャ](../../database-engine/media/security-center-map-view-procs.png "セキュリティ センター マップのビューとプロシージャ")](http://msdn.microsoft.com/library/ms175503.aspx)<br /><br /> [![セキュリティ センター マップの行レベル セキュリティ](../../database-engine/media/security-center-map-row-level-sec.png "セキュリティ センター マップの行レベルのセキュリティ")](http://msdn.microsoft.com/library/dn765131.aspx)<br /><br /> [![セキュリティ センター マップの動的データ マスク](../../database-engine/media/security-center-map-data-masking.png "セキュリティ センター マップの動的データ マスク")](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/)<br /><br /> [![署名済みオブジェクトのセキュリティ センター マップの](../../database-engine/media/security-center-map-signed-objects.png "セキュリティ センター マップの署名済みオブジェクト")](http://msdn.microsoft.com/library/ms181700.aspx)<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")|**ファイルの暗号化**<br /><br /> [![セキュリティ センター マップの BitLocker](../../database-engine/media/security-center-map-bitlocker.png "セキュリティ センター マップの BitLocker")](http://support.microsoft.com/en-us/kb/2855131)<br /><br /> [![セキュリティ センター マップの NTFS 暗号化](../../database-engine/media/security-center-map-ntfs-encryp.png "セキュリティ センター マップの NTFS 暗号化")](http://msdn.microsoft.com/library/dd163562.aspx)<br /><br /> [![セキュリティ センター マップの TDE](../../database-engine/media/security-center-map-tde.png "セキュリティ センター マップの TDE")](http://msdn.microsoft.com/library/bb934049.aspx)<br /><br /> [![セキュリティ センター マップのバックアップの暗号化](../../database-engine/media/security-center-map-backup-encryp.png "セキュリティ センター マップのバックアップの暗号化")](http://msdn.microsoft.com/library/dn449489.aspx)<br /><br /> **ソースの暗号化**<br /><br /> [![セキュリティ センター マップの EKM](../../database-engine/media/security-center-map-ekm.png "セキュリティ センター マップの EKM")](http://msdn.microsoft.com/library/bb895340.aspx)<br /><br /> [![セキュリティ センター マップの Azure Key Vault](../../database-engine/media/security-center-map-key-vault.png "セキュリティ センター マップの Azure Key Vault")](http://azure.microsoft.com/documentation/articles/key-vault-get-started/)<br /><br /> **列、データ、およびキーの暗号化**<br /><br /> [![セキュリティ センター マップの証明書による暗号化](../../database-engine/media/security-center-map-cert.png "セキュリティ センター マップの証明書による暗号化")](http://msdn.microsoft.com/library/ms188061.aspx)<br /><br /> [![セキュリティ センター マップの対称キーによる暗号化](../../database-engine/media/security-center-map-sym-key.png "セキュリティ センター マップの対称キーによる暗号化")](http://msdn.microsoft.com/library/ms174361.aspx)<br /><br /> [![セキュリティ センター マップの非対称キーによる暗号化](../../database-engine/media/security-center-map-asym-key.png "セキュリティ センター マップの非対称キーによる暗号化")](http://msdn.microsoft.com/library/ms186950.aspx)<br /><br /> [![セキュリティ センター マップのパスフレーズによる暗号化](../../database-engine/media/security-center-map-passphrase.png "セキュリティ センター マップのパスフレーズによる暗号化")](http://msdn.microsoft.com/library/ms190357.aspx)|**ファイアウォールによる防御**<br /><br /> [![セキュリティ センター マップの Windows ファイアウォール](../../database-engine/media/security-center-map-windows-firewall.png "セキュリティ センター マップの Windows ファイアウォール")](http://msdn.microsoft.com/library/ms175043.aspx)<br /><br /> [![セキュリティ センター マップのサービス ファイアウォール](../../database-engine/media/security-center-map-service-firewall.png "セキュリティ センター マップのサービス ファイアウォール")](http://msdn.microsoft.com/library/azure/ee621782.aspx)<br /><br /> [![セキュリティ センター マップのデータベースのファイアウォール](../../database-engine/media/security-center-map-db-firewall.png "セキュリティ センター マップのデータベース ファイアウォール")](http://msdn.microsoft.com/library/azure/ee621782.aspx)<br /><br /> **転送中のデータの暗号化**<br /><br /> [![セキュリティ センター マップの強制 SSL](../../database-engine/media/security-center-map-forced-ssl.png "セキュリティ センター マップの強制 SSL")](http://msdn.microsoft.com/library/ms191192.aspx)<br /><br /> [![セキュリティ センター マップの省略可能な SSL](../../database-engine/media/security-center-map-opt-ssl.png "セキュリティ センター マップの省略可能な SSL")](http://msdn.microsoft.com/library/ms191192.aspx)<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")|**自動監査**<br /><br /> [![セキュリティ センター マップの SQL Server Audit](../../database-engine/media/security-center-map-sql-audit.png "セキュリティ センター マップの SQL Server Audit")](http://msdn.microsoft.com/library/cc280386.aspx)<br /><br /> [![セキュリティ センター マップの SQL Database Audit](../../database-engine/media/security-center-map-sqldb-audit.png "セキュリティ センター マップの SQL データベースの監査")](http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/)<br /><br /> **カスタム監査**<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> [![セキュリティ センター マップのトリガー](../../database-engine/media/security-center-map-triggers.png "セキュリティ センター マップのトリガー")](http://msdn.microsoft.com/library/ms175941.aspx)<br /><br /> **準拠**<br /><br /> [![SecCtrCompliance](../../database-engine/media/secctrcompliance.png "SecCtrCompliance")](http://azure.microsoft.com/support/trust-center/services/)<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![プレース ホルダー](../../database-engine/media/security-center-map-blankplaceholder.png "プレース ホルダー")<br /><br /> ![Security Center の凡例](../../database-engine/media/security-center-map-legend.png "セキュリティ センターの凡例")|  
  
## <a name="links-to-specific-related-topics"></a>関連する特定のトピックへのリンク  
 ![小さいファイル フォルダー アイコン](../../integration-services/media/filefolder-small.gif "小さいファイル フォルダー アイコン")**認証: ユーザーはだれですか?**  
 **だれが認証したか。(Windows または[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)])**  
  
-   [認証モードの選択](choose-an-authentication-mode.md)  
  
 **master データベースでの認証** (ログインとデータベース ユーザー)  
  
-   [SQL Server ログインの作成](authentication-access/create-a-login.md)  
  
-   [Azure SQL データベースにおけるデータベースとログインの管理](http://msdn.microsoft.com/library/ee336235.aspx)  
  
-   [データベース ユーザーの作成](authentication-access/create-a-database-user.md)  
  
 **ユーザー データベースでの認証します。**  
  
-   [包含データベース ユーザー - データベースの可搬性を確保する](contained-database-users-making-your-database-portable.md)  
  
 **他の ID の使用**  
  
-   [資格情報 &#40;データベース エンジン&#41;](authentication-access/credentials-database-engine.md)  
  
-   [別のログインとして実行](/sql/t-sql/statements/execute-as-transact-sql)  
  
-   [別のデータベース ユーザーとして実行](/sql/t-sql/statements/execute-as-transact-sql)  
  
 ![小さいファイル フォルダー アイコン](../../integration-services/media/filefolder-small.gif "小さいファイル フォルダー アイコン")**暗号化: 秘密データの格納**  
 **ファイルの暗号化**  
  
-   [BitLocker (ドライブ レベル)](http://support.microsoft.com/kb/2855131)  
  
-   [NTFS 暗号化 (フォルダー レベル)](http://msdn.microsoft.com/library/dd163562.aspx)  
  
-   [透過的なデータ暗号化 (ファイル レベル)](encryption/transparent-data-encryption.md)  
  
-   [バックアップの暗号化 (ファイル レベル)](../backup-restore/backup-encryption.md)  
  
 **ソースの暗号化**  
  
-   [拡張キー管理モジュール](encryption/extensible-key-management-ekm.md)  
  
-   [Azure キー コンテナーに格納されたキー](encryption/extensible-key-management-using-azure-key-vault-sql-server.md)  
  
 **列、データ、キーの暗号化**  
  
-   [証明書による暗号化](/sql/t-sql/functions/encryptbycert-transact-sql)  
  
-   [非対称キーによる暗号化](/sql/t-sql/functions/encryptbyasymkey-transact-sql)  
  
-   [対称キーによる暗号化](/sql/t-sql/functions/encryptbykey-transact-sql)  
  
-   [パスフレーズによる暗号化](/sql/t-sql/functions/encryptbypassphrase-transact-sql)  
  
 ![小さいファイル フォルダー アイコン](../../integration-services/media/filefolder-small.gif "小さいファイル フォルダー アイコン")**認証: 何ができるでしょうか。**  
 **権限の許可、取り消し、および拒否**  
  
-   [権限の階層 &#40;データベース エンジン&#41;](permissions-hierarchy-database-engine.md)  
  
-   [権限](permissions-database-engine.md)  
  
-   [セキュリティ保護可能](securables.md)  
  
 **ロールによるセキュリティ**  
  
-   [サーバーレベルのロール](authentication-access/server-level-roles.md)  
  
-   [データベース レベルのロール](authentication-access/database-level-roles.md)  
  
 `Restricting Data Access to Selected Data Elements`  
  
-   [ビュー](../views/views.md) と [プロシージャ](../stored-procedures/stored-procedures-database-engine.md)を使用したデータ アクセスの制限  
  
-   [行レベルのセキュリティ](http://msdn.microsoft.com/library/azure/dn765131.aspx)  
  
-   [動的なデータ マスキング](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/)  
  
-   [署名済みオブジェクト](/sql/t-sql/statements/add-signature-transact-sql)  
  
 ![小さいファイル フォルダー アイコン](../../integration-services/media/filefolder-small.gif "小さいファイル フォルダー アイコン")**接続のセキュリティ: 制限とセキュリティ保護**  
 **ファイアウォールによる防御**  
  
-   [データベース エンジン アクセスを有効にするための Windows ファイアウォールを構成する](../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)  
  
-   [Azure SQL データベースのファイアウォール設定](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)  
  
-   [Azure サービスのファイアウォール設定](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)  
  
 **転送中のデータの暗号化**  
  
-   [データベース エンジンの Secure Sockets Layer](../../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)  
  
-   [SQL データベースの Secure Sockets Layer](https://msdn.microsoft.com/library/azure/ff394108.aspx)  
  
 ![小さいファイル フォルダー アイコン](../../integration-services/media/filefolder-small.gif "小さいファイル フォルダー アイコン")**監査: アクセスの記録**  
 **自動監査**  
  
-   [SQL Server Audit &#40;Database Engine&#41;](auditing/sql-server-audit-database-engine.md)  
  
-   [SQL データベースの監査](http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/)  
  
 **カスタム監査の実装**  
  
-   作成[DDL トリガー](../triggers/ddl-triggers.md)と[DML トリガー](../triggers/dml-triggers.md)  
  
 ![小さいファイル フォルダー アイコン](../../integration-services/media/filefolder-small.gif "小さいファイル フォルダー アイコン")**コンプライアンス**  
 **SQL Server**  
  
-   [情報セキュリティ国際評価基準](http://go.microsoft.com/fwlink/?LinkId=616319)  
  
 **SQL Database**  
  
-   [Microsoft Azure セキュリティ センター: 機能による準拠](http://azure.microsoft.com/support/trust-center/services/)  
  
## <a name="see-also"></a>参照  
 [SQL Server の保護](securing-sql-server.md)   
 [プリンシパル &#40;データベース エンジン&#41;](authentication-access/principals-database-engine.md)   
 [SQL Server の証明書と非対称キー](sql-server-certificates-and-asymmetric-keys.md)   
 [SQL Server の暗号化](encryption/sql-server-encryption.md)   
 [セキュリティ構成](surface-area-configuration.md)   
 [強力なパスワード](strong-passwords.md)   
 [TRUSTWORTHY データベース プロパティ](trustworthy-database-property.md)   
 [データベース エンジンの機能とタスク](../../database-engine/database-engine-features-and-tasks.md)  
  
  
