---
title: 暗号化のサポートについて
description: JDBC ドライバーが TLS 暗号化を使用して SQL データベースへの接続を確実にセキュリティで保護する方法について説明します。
ms.custom: ''
ms.date: 04/01/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: vanto
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 073f3b9e-8edd-4815-88ea-de0655d0325e
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 51d44e7fad3b554c35ec342c0a198c82e9cf3009
ms.sourcegitcommit: 14f2051d329b69a7b5ff7bce1d136cf7f25bb219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106232039"
---
# <a name="understanding-encryption-support"></a>暗号化のサポートについて

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続する際、アプリケーションによって暗号化が要求され、なおかつ、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスで TLS 暗号化がサポートされる構成になっていた場合、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] では TLS ハンドシェイクが開始されます。 サーバーとクライアントは、このハンドシェイクによって、データの保護に使用する暗号化と暗号アルゴリズムをネゴシエートします。 TLS ハンドシェイクが完了すると、暗号化されたデータを安全に送信できるようになります。 サーバーでは、TLS ハンドシェイクの際にクライアントに公開キー証明書が送信されます。 公開キー証明書の発行者は証明機関 (CA) と呼ばれます。 その証明機関が、クライアントが信頼するいずれかの証明機関に該当するかどうかは、クライアント側で検証する必要があります。

アプリケーションから暗号化を要求されなかった場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、TLS 暗号化をサポートするよう [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] によって強制されることはありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスが TLS 暗号化を強制的に使用するように構成されていない場合、接続は暗号化なしで確立されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスが TLS 暗号化を強制的に使用するように構成されている場合は、使用中の Java 仮想マシン (JVM) が正常に構成されていれば自動的に TLS 暗号化が有効になり、そうでなければ接続が終了してエラーが生成されます。

> [!NOTE]
> TLS 接続に成功するためには、**serverName** に渡された値が、サーバー証明書に含まれる Subject Alternate Name (SAN) の Common Name (CN) または DNS 名と厳密に一致している必要があります。
>
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 用に TLS を構成する方法の詳細については、「[データベース エンジンへの暗号化接続の有効化](../../database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine.md)」を参照してください。

## <a name="remarks"></a>解説

TLS 暗号化をアプリケーションで使用できるようにするために、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] のバージョン 1.2 リリース以降では、**encrypt**、**trustServerCertificate**、**trustStore**、**trustStorePassword**、**hostNameInCertificate** の各接続プロパティが導入されました。 詳細については、「[接続プロパティの設定](setting-the-connection-properties.md)」を参照してください。

次の表は、想定される TLS 接続シナリオでの [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] バージョンの動作をまとめたものです。 使用する TLS 接続プロパティの組み合わせをシナリオごとに変えています。 表の値の意味を以下に示します。

- **blank**: "接続文字列にプロパティが存在しない"
- **value**:"接続文字列にプロパティが存在し、その値が有効である"
- **any**: "接続文字列にプロパティが存在するかどうか、またはその値が有効であるかどうかは関係ない"

> [!NOTE]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ユーザー認証でも Windows 統合認証でも同じ動作になります。

<!-- Do not remove the extra &nbsp;'s in the column1 heading. They keep column1 from wrapping and improve readability of the table. -->
| プロパティの&nbsp;設定&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 動作 |
| ----------------- | -------- |
| **encrypt** = false または blank<br/> **trustServerCertificate** = any<br/> **hostNameInCertificate** = any<br/> **trustStore** = any<br/> **trustStorePassword** = any<br/> | ドライバーによって、TLS 暗号化のサポートはサーバーに強制されません。 サーバーに自己署名証明書が存在する場合、その TLS 証明書の交換がドライバーによって開始されます。 TLS 証明書の検証は行われず、(ログイン パケット内の) 資格情報のみが暗号化されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合は、TLS 証明書の交換が開始されます。 TLS 証明書の検証は行われませんが、通信全体が暗号化されます。 |
| **encrypt** = true<br/> **trustServerCertificate** = true<br/> **hostNameInCertificate** = any<br/> **trustStore** = any<br/> **trustStorePassword** = any<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。 **trustServerCertificate** プロパティが "true" に設定されている場合、TLS 証明書は検証されません。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |
| **encrypt** = true<br/> **trustServerCertificate** = false または blank<br/> **hostNameInCertificate** = blank<br/> **trustStore** = blank<br/> **trustStorePassword** = blank<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。<br /><br /> ドライバーにより、接続 URL に指定されている **serverName** プロパティを使用してサーバーの TLS 証明書が検証され、信頼マネージャー ファクトリの検索ルールに従って、使用する証明書ストアが決定されます。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |
| **encrypt** = true<br/> **trustServerCertificate** = false または blank<br/> **hostNameInCertificate** = value<br/> **trustStore** = blank<br/> **trustStorePassword** = blank<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。<br /><br /> ドライバーにより、**hostNameInCertificate** プロパティに指定されている値を使用して、TLS 証明書のサブジェクトの値が検証されます。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |
| **encrypt** = true<br/> **trustServerCertificate** = false または blank<br/> **hostNameInCertificate** = blank<br/> **trustStore** = value<br/> **trustStorePassword** = value<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。<br /><br /> ドライバーは、**trustStore** プロパティの値を使用して証明書の trustStore ファイルを検索し、**trustStorePassword** プロパティの値を使用して trustStore ファイルの整合性をチェックします。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |
| **encrypt** = true<br/> **trustServerCertificate** = false または blank<br/> **hostNameInCertificate** = blank<br/> **trustStore** = blank<br/> **trustStorePassword** = value<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。<br /><br /> ドライバーは、**trustStorePassword** プロパティの値を使用して、既定の trustStore ファイルの整合性をチェックします。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |
| **encrypt** = true<br/> **trustServerCertificate** = false または blank<br/> **hostNameInCertificate** = blank<br/> **trustStore** = value<br/> **trustStorePassword** = blank<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。<br /><br /> ドライバーは、**trustStore** プロパティの値を使用して、trustStore ファイルの場所を調べます。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |
| **encrypt** = true<br/> **trustServerCertificate** = false または blank<br/> **hostNameInCertificate** = value<br/> **trustStore** = blank<br/> **trustStorePassword** = value<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。<br /><br /> ドライバーは、**trustStorePassword** プロパティの値を使用して、既定の trustStore ファイルの整合性をチェックします。 また、**hostNameInCertificate** プロパティの値を使用して、TLS 証明書が検証されます。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |
| **encrypt** = true<br/> **trustServerCertificate** = false または blank<br/> **hostNameInCertificate** = value<br/> **trustStore** = value<br/> **trustStorePassword** = blank<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。<br /><br /> ドライバーは、**trustStore** プロパティの値を使用して、trustStore ファイルの場所を調べます。 また、**hostNameInCertificate** プロパティの値を使用して、TLS 証明書が検証されます。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |
| **encrypt** = true<br/> **trustServerCertificate** = false または blank<br/> **hostNameInCertificate** = value<br/> **trustStore** = value<br/> **trustStorePassword** = value<br/> | ドライバーによって、サーバーに TLS 暗号化を使用するように要求されます。<br /><br /> サーバーによってクライアントに TLS 暗号化のサポートが要求されている場合、またはサーバーで暗号化がサポートされている場合、ドライバーによって TLS 証明書の交換が開始されます。<br /><br /> ドライバーは、**trustStore** プロパティの値を使用して証明書の trustStore ファイルを検索し、**trustStorePassword** プロパティの値を使用して trustStore ファイルの整合性をチェックします。 また、**hostNameInCertificate** プロパティの値を使用して、TLS 証明書が検証されます。<br /><br /> サーバーが暗号化をサポートするように構成されていない場合、ドライバーはエラーを生成して接続を終了します。 |

encrypt プロパティが **true** に設定されている場合、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] では、JVM の既定の JSSE セキュリティ プロバイダーを使用して、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と TLS 暗号化がネゴシエートされます。 既定のセキュリティ プロバイダーでは、TLS 暗号化の正常なネゴシエートに必要なすべての機能がサポートされているとは限りません。 たとえば、既定のセキュリティ プロバイダーでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の TLS 証明書で使用されている RSA 公開キーのサイズがサポートされていない場合があります。 この場合、既定のセキュリティ プロバイダーでエラーが発生し、その結果 JDBC ドライバーが接続を終了する可能性があります。 この問題を解決するには、次のオプションのいずれかを使用できます。

- サイズの小さい RSA 公開キーを持つサーバー証明書を使用して、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を構成します。
- "\<java-home>/lib/security/java.security" セキュリティ プロパティ ファイルで、別の JSSE セキュリティ プロバイダーを使用するように JVM を構成します。
- 別の JVM を使用します。

## <a name="validating-server-tls-certificate"></a>サーバーの TLS 証明書の検証

サーバーでは、TLS ハンドシェイクの際にクライアントに公開キー証明書が送信されます。 そのサーバー証明書が、クライアントが信頼している証明機関によって発行されているかどうかを、JDBC ドライバーまたはクライアント側が検証する必要があります。 ドライバーにより、サーバー証明書で次の条件が満たされていることが要求されます。

- 信頼されている証明機関から発行されている。
- サーバー認証用に証明書が発行されている。
- 証明書が期限切れではない。
- 証明書の Subject の Common Name (CN) または Subject Alternate Name (SAN) の DNS 名が、接続文字列に指定された **serverName** の値 (または **hostNameInCertificate** プロパティの値が指定されている場合はその値) と厳密に一致している。
- DNS 名にはワイルドカード文字を含めることができます。 7\.2 よりも前のバージョンでは、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] でワイルドカードでのマッチングがサポートされません。 つまり、abc.com は \*.com とは一致せず、\*.com は \*.com と一致します。 バージョン 7.2 以降では、標準証明書のワイルドカードでのマッチングがサポートされています。

## <a name="see-also"></a>関連項目

[暗号化の使用](using-ssl-encryption.md)  
[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)  
