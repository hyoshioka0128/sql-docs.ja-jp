---
title: SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター | Microsoft Docs
ms.custom: ''
ms.date: 09/27/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
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
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ed88293239d29533b4374541ab4af8e12fb55900
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68126871"
---
# <a name="security-center-for-sql-server-database-engine-and-azure-sql-database"></a>SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター
[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md](../../includes/appliesto-ss-asdb-asdw-pdw-md.md)]

  このページでは、 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] および [!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]でのセキュリティと保護に関して必要になる情報の検索に役立つリンクを示します。  
  
 **凡例**  
  
 ![security-center-legend](../performance/media/security-center-legend.PNG "security-center-legend")  
  
##  <a name="Who"></a> 認証:ユーザーはだれか  
  
|||  
|-|-|  
|**だれが認証したか**<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") Windows 認証<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証<br /><br /> ![security-center-sqldb](../../relational-databases/security/media/security-center-sqldb.png "security-center-sqldb") Azure Active Directory|だれが認証したか (Windows または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)])<br /><br /> [認証モードの選択](../../relational-databases/security/choose-an-authentication-mode.md)<br /><br /> [Azure Active Directory の認証を使用して、SQL データベースに接続します。](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/)|  
|**どこで認証されたか**<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") マスター データベース:ログインと DB ユーザー<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") ユーザー データベース:包含 DB ユーザー|マスター データベースでの認証 (ログインとデータベース ユーザー)<br /><br /> [SQL Server ログインの作成](../../relational-databases/security/authentication-access/create-a-login.md)<br /><br /> [Azure SQL データベースにおけるデータベースとログインの管理](https://msdn.microsoft.com/library/ee336235.aspx)<br /><br /> [データベース ユーザーの作成](../../relational-databases/security/authentication-access/create-a-database-user.md)<br /><br /> <br /><br /> ユーザー データベースでの認証<br /><br /> [包含データベース ユーザー - データベースの可搬性を確保する](../../relational-databases/security/contained-database-users-making-your-database-portable.md)|  
|**他の ID の使用**<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 資格情報<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") 別のログインとして実行<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 別のデータベース ユーザーとして実行|[資格情報 &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/credentials-database-engine.md)<br /><br /> [別のログインとして実行](../../t-sql/statements/execute-as-transact-sql.md)<br /><br /> [別のデータベース ユーザーとして実行](../../t-sql/statements/execute-as-transact-sql.md)|  
  
##  <a name="What"></a> 認可:何を実行できるか  
  
|||  
|-|-|  
|**権限の許可、取り消し、および拒否**<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") セキュリティ保護可能なクラス<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") 詳細なサーバーのアクセス許可<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 詳細なデータベースのアクセス許可|[権限の階層 &#40;データベース エンジン&#41;](../../relational-databases/security/permissions-hierarchy-database-engine.md)<br /><br /> [権限](../../relational-databases/security/permissions-database-engine.md)<br /><br /> [セキュリティ保護可能](../../relational-databases/security/securables.md)<br /><br /> [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)|  
|**ロールによるセキュリティ**<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") サーバー レベルのロール<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") データベース レベルのロール|[サーバーレベルのロール](../../relational-databases/security/authentication-access/server-level-roles.md)<br /><br /> [データベース レベルのロール](../../relational-databases/security/authentication-access/database-level-roles.md)|  
|**選んだデータ要素へのデータ アクセスを制限する**<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") ビュー/プロシージャを使用したデータ アクセスの制限<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 行レベルのセキュリティ<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 動的なデータ マスキング<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 署名済みオブジェクト|[ビュー](../../relational-databases/views/views.md) と [プロシージャ](../../relational-databases/stored-procedures/stored-procedures-database-engine.md)を使用したデータ アクセスの制限<br /><br /> [行レベルのセキュリティ (SQL Server)](../../relational-databases/security/row-level-security.md)<br /><br /> [行レベルのセキュリティ (Azure SQL Database)](https://msdn.microsoft.com/library/azure/dn765131.aspx)<br /><br /> [動的なデータ マスキング (SQL Server)](../../relational-databases/security/dynamic-data-masking.md)<br /><br /> [動的なデータ マスキング (Azure SQL Database)](https://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/)<br /><br /> [署名済みオブジェクト](../../t-sql/statements/add-signature-transact-sql.md)|  
  
##  <a name="Encrypt"></a> 暗号化:秘密データの格納  
  
|||  
|-|-|  
|**ファイルの暗号化**<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") BitLocker 暗号化 (ドライブ レベル)<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") NTFS 暗号化 (フォルダー レベル)<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 透過的なデータ暗号化 (ファイル レベル)<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") バックアップの暗号化 (ファイル レベル)|[BitLocker (ドライブ レベル)](https://support.microsoft.com/kb/2855131)<br /><br /> [NTFS 暗号化 (フォルダー レベル)](https://msdn.microsoft.com/library/dd163562.aspx)<br /><br /> [透過的なデータ暗号化 (ファイル レベル)](../../relational-databases/security/encryption/transparent-data-encryption.md)<br /><br /> [バックアップの暗号化 (ファイル レベル)](../../relational-databases/backup-restore/backup-encryption.md)|  
|**ソースの暗号化**<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") 拡張キー管理モジュール<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") Azure Key Vault に格納されたキー<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") Always Encrypted|[拡張キー管理モジュール](../../relational-databases/security/encryption/extensible-key-management-ekm.md)<br /><br /> [Azure キー コンテナーに格納されたキー](../../relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server.md)<br /><br /> [Always Encrypted](../../relational-databases/security/encryption/always-encrypted-database-engine.md)|  
|**列、データ、キーの暗号化**<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 証明書による暗号化<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 対称キーによる暗号化<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 非対称キーによる暗号化<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") パスフレーズによる暗号化|[証明書による暗号化](../../t-sql/functions/encryptbycert-transact-sql.md)<br /><br /> [非対称キーによる暗号化](../../t-sql/functions/encryptbyasymkey-transact-sql.md)<br /><br /> [対称キーによる暗号化](../../t-sql/functions/encryptbykey-transact-sql.md)<br /><br /> [パスフレーズによる暗号化](../../t-sql/functions/encryptbypassphrase-transact-sql.md)<br /><br /> [データの列の暗号化](../../relational-databases/security/encryption/encrypt-a-column-of-data.md)|  
  
##  <a name="Connect"></a> 接続のセキュリティ:制限と保護  
  
|||  
|-|-|  
|**ファイアウォールによる防御**<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") Windows ファイアウォールの設定<br /><br /> ![security-center-sqldb](../../relational-databases/security/media/security-center-sqldb.png "security-center-sqldb") Azure サービスのファイアウォール設定<br /><br /> ![security-center-sqldb](../../relational-databases/security/media/security-center-sqldb.png "security-center-sqldb") データベース ファイアウォールの設定|[データベース エンジン アクセスを有効にするための Windows ファイアウォールを構成する](../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)<br /><br /> [Azure SQL データベースのファイアウォール設定](../../relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database.md)<br /><br /> [Azure サービスのファイアウォール設定](../../relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database.md)|  
|**転送中のデータの暗号化**<br /><br /> ![security-center-both](../performance/media/security-center-both.png "security-center-both") 強制 SSL 接続<br /><br /> ![security-center-sqlserver](../performance/media/security-center-sqlserver.png "security-center-sqlserver") SSL 接続 (オプション)|[データベース エンジンの Secure Sockets Layer](../../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)<br /><br /> [SQL データベースの Secure Sockets Layer](https://msdn.microsoft.com/library/azure/ff394108.aspx)<br /><br /> [Microsoft SQL Server の TLS 1.2 サポート](https://support.microsoft.com/kb/3135244)|  
  
##  <a name="Audit"></a> 監査:アクセスの記録  
  
|||  
|-|-|  
|**自動監査**<br /><br /> ![security-center-sqlserver](../../relational-databases/performance/media/security-center-sqlserver.png "security-center-sqlserver") [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 監査 (サーバーと DB レベル)<br /><br /> ![security-center-sqldb](../../relational-databases/security/media/security-center-sqldb.png "security-center-sqldb") [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 監査 (データベース レベル)<br /><br /> ![security-center-sqldb](../../relational-databases/security/media/security-center-sqldb.png "security-center-sqldb") 脅威検出| <br /><br /> [SQL Server Audit &#40;Database Engine&#41;](../../relational-databases/security/auditing/sql-server-audit-database-engine.md)<br /><br /> [SQL データベースの監査](https://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/)<br /><br /> [SQL データベース脅威検出を開始](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/) <br /><br /> [SQL データベースの脆弱性評価](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) |  
|**カスタム監査**<br /><br /> ![security-center-both](../../relational-databases/performance/media/security-center-both.png "security-center-both") トリガー|カスタム監査の実装: [DDL Triggers](../../relational-databases/triggers/ddl-triggers.md) と [DML Triggers](../../relational-databases/triggers/dml-triggers.md)の作成|  
|**準拠**<br /><br /> ![security-center-both](../../relational-databases/performance/media/security-center-both.png "security-center-both") コンプライアンス|SQL Server:<br />                        [情報セキュリティ国際評価基準](https://go.microsoft.com/fwlink/?LinkId=616319)<br /><br /> SQL データベース:<br />                        [Microsoft Azure セキュリティ センター:機能ごとのコンプライアンス](https://azure.microsoft.com/support/trust-center/services/)|  
  
##  <a name="SQLInjection"></a> SQL インジェクション  
 SQL インジェクションとは、後で [!INCLUDE[ssDE](../../includes/ssde-md.md)] に渡され解析および実行が行われる文字列に、有害なコードを挿入するという攻撃です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、構文的に有効であれば受信したクエリがすべて実行されるため、SQL ステートメントを構成するすべてのプロシージャにおいて、インジェクションに対する脆弱性を検証する必要があります。 すべてのデータベース システムに SQL インジェクションのリスクがあり、脆弱性の多くが、 [!INCLUDE[ssDE](../../includes/ssde-md.md)]にクエリを実行するアプリケーションで確認されています。 SQL インジェクション攻撃を阻止するには、ストアド プロシージャとパラメーター化コマンドを使用し、動的 SQL を回避して、すべてのユーザーのアクセス許可を制限します。  詳細については、「 [SQL Injection](../../relational-databases/security/sql-injection.md)」を参照してください。  
  
 アプリケーション プログラマ向けのその他のリンク:  
  
-   [SQL Server におけるアプリケーション セキュリティのシナリオ](/dotnet/framework/data/adonet/sql/application-security-scenarios-in-sql-server)  
  
-   [SQL Server での安全な動的 SQL の作成](/dotnet/framework/data/adonet/sql/writing-secure-dynamic-sql-in-sql-server)  
  
-   [方法: ASP.NET で SQL インジェクションから保護する](https://msdn.microsoft.com/library/ff648339.aspx)  
  
## <a name="see-also"></a>参照  
 [データベース エンジンの権限の概要](../../relational-databases/security/authentication-access/getting-started-with-database-engine-permissions.md)   
 [SQL Server の保護](../../relational-databases/security/securing-sql-server.md)   
 [プリンシパル &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [SQL Server の証明書と非対称キー](../../relational-databases/security/sql-server-certificates-and-asymmetric-keys.md)   
 [SQL Server の暗号化](../../relational-databases/security/encryption/sql-server-encryption.md)   
 [セキュリティ構成](../../relational-databases/security/surface-area-configuration.md)   
 [強力なパスワード](../../relational-databases/security/strong-passwords.md)   
 [TRUSTWORTHY データベース プロパティ](../../relational-databases/security/trustworthy-database-property.md)   
 [データベース エンジンの機能とタスク](https://msdn.microsoft.com/library/d9efe145-3306-4d61-bd77-e2af43e19c34)  
 [SQL Server の知的所有権の保護](../../relational-databases/security/protecting-your-sql-server-intellectual-property.md)   
  
[!INCLUDE[get-help-security](../../includes/get-help-security.md)]
