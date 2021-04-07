---
title: 暗号化のためのクライアントの構成
description: Microsoft JDBC Driver for SQL Server を使用してクライアントのセキュリティを確保するためのクライアント側の暗号化と証明書の信頼について説明します。
ms.custom: ''
ms.date: 03/31/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: vanto
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: ae34cd1f-3569-4759-80c7-7c9b33b3e9eb
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: cc0d713f3e7b2f1cb508d1a6f366d7c1a97617e9
ms.sourcegitcommit: ebe81e2daa544f41c8ababb66a91c218ad0c2a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106176959"
---
# <a name="configuring-the-client-for-encryption"></a>暗号化のためのクライアントの構成

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] またはクライアント側で、サーバーが正しいサーバーであること、およびその証明書がクライアントの信頼する証明機関によって発行されていることを検証する必要があります。 サーバー証明書を検証するには、接続時に信頼マテリアルを指定する必要があります。 また、サーバー証明書の発行者が、クライアントによって信頼されている証明機関である必要があります。

この記事では、まず、クライアント コンピューターのトラスト マテリアルを提供する方法を説明します。 次に、SQL Server のインスタンスのトランスポート層セキュリティ (TLS) 証明書が民間の証明機関によって発行されている場合にサーバー証明書をクライアント コンピューターのトラスト ストアにインポートする方法を説明します。

サーバー証明書の検証の詳細については、「[暗号化のサポートについて](../../connect/jdbc/understanding-ssl-support.md)」にあるサーバーの TLS 証明書の検証に関するセクションを参照してください。

## <a name="configuring-the-client-trust-store"></a>クライアントのトラスト ストアの構成

サーバー証明書を検証するには、**trustStore** 接続プロパティと **trustStorePassword** 接続プロパティを明示的に使用するか、基になる Java 仮想マシンのトラスト ストアを暗黙的に使用して、接続時にトラスト マテリアルが必要です。 接続文字列内に **trustStore** プロパティと **trustStorePassword** プロパティを設定する方法の詳細については、「[暗号化を使用した接続](connecting-with-ssl-encryption.md)」を参照してください。

**trustStore** プロパティが指定されていないか null に設定されている場合、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] では、基になる JVM のセキュリティ プロバイダー Java Secure Socket Extension (SunJSSE) を信頼します。 SunJSSE プロバイダーによって提供される既定の TrustManager を使用して、SQL Server から返される X.509 証明書を、トラスト ストアで提供されるトラスト マテリアルに対して検証します。

TrustManager は、次の順序で既定の trustStore を検索します。

- システム プロパティ "javax.net.ssl.trustStore" が定義されている場合、TrustManager は、そのシステム プロパティによって指定されているファイル名を使用して既定の trustStore ファイルを見つけようとします。
- "javax.net.ssl.trustStore" システム プロパティが指定されていない場合に、ファイル "\<java-home>/lib/security/jssecacerts" が存在する場合は、そのファイルが使用されます。
- ファイル "\<java-home>/lib/security/cacerts" が存在する場合は、そのファイルが使用されます。

詳細については、Sun Microsystems の Web サイトで SunX509 TrustManager Interface についてのドキュメントを参照してください。

Java ランタイム環境では、次のようにしてシステム プロパティ trustStore および trustStorePassword を設定できます。

```java
java -Djavax.net.ssl.trustStore=C:\MyCertificates\storeName
java -Djavax.net.ssl.trustStorePassword=storePassword
```

この場合、この JVM 上で実行されるすべてのアプリケーションが既定でこれらの設定を使用します。 アプリケーションでこの既定の設定をオーバーライドするには、接続文字列か、[SQLServerDataSource](reference/sqlserverdatasource-class.md) クラスの該当する setter メソッドで、接続プロパティの **trustStore** および **trustStorePassword** を設定する必要があります。

また、"\<java-home>/lib/security/jssecacerts" や "\<java-home>/lib/security/cacerts" などの既定のトラスト ストア ファイルを構成および管理することもできます。 そのためには、JRE (Java ランタイム環境) と共にインストールされる JAVA "keytool" ユーティリティを使用します。 "keytool" ユーティリティの詳細については、[Oracle Web サイトで keytool についてのドキュメント](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)を参照してください。

### <a name="importing-the-server-certificate-to-trust-store"></a>トラスト ストアへのサーバー証明書のインポート

サーバーでは、TLS ハンドシェイクの際にクライアントに公開キー証明書が送信されます。 公開キー証明書の発行者は証明機関 (CA) と呼ばれます。 クライアントは、その証明機関が信頼されている証明機関であることを確認する必要があります。 この確認は、信頼されている CA の公開キーを事前に把握しておくことによって行われます。 通常、JVM では、一連の信頼されている証明機関があらかじめ定義されています。

SQL Server のインスタンスの TLS 証明書が私的な証明機関によって発行されている場合は、クライアント コンピューターのトラスト ストアの信頼された証明書の一覧にその証明機関の証明書を追加する必要があります。

そのためには、JRE (Java ランタイム環境) と共にインストールされる JAVA "keytool" ユーティリティを使用します。 次のコマンド プロンプトは、"keytool" ユーティリティを使用してファイルから証明書をインポートする方法を示しています。

```java
keytool -import -v -trustcacerts -alias myServer -file caCert.cer -keystore truststore.ks
```

この例では、"caCert.cer" というファイルが証明書ファイルとして使用されています。 この証明書ファイルをサーバーから入手する必要があります。 次の手順は、サーバー証明書をファイルにエクスポートする方法を示しています。

1. [スタート] ボタン、[ファイル名を指定して実行] の順に選択し、「MMC」と入力します (MMC は Microsoft 管理コンソールの略称です)。
2. MMC で、[証明書] を開きます。
3. [個人用] を展開し、[証明書] を展開します。
4. サーバー証明書を右クリックし、[すべてのタスク] をポイントして、[エクスポート] をクリックします。
5. 証明書のエクスポート ウィザードの [ようこそ] ダイアログ ボックスで、[次へ] を選択して次のページに進みます。
6. `No, do not export the private key` が選択されていることを確認し、[次へ] を選択します。
7. DER エンコードのバイナリ X.509 (.CER) または Base-64 エンコードの X.509 (.CER) のいずれかを選択してから、[次へ] を選択します。
8. エクスポート ファイルの名前を入力します。
9. [次へ] を選択し、[完了] を選択して証明書をエクスポートします。

## <a name="see-also"></a>関連項目

[暗号化の使用](using-ssl-encryption.md)  
[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)  
