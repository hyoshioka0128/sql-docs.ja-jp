---
description: JDBC Driver for SQL Server を使用して、Java アプリケーションで TLS 暗号化を使用して接続する方法の例を紹介します。
title: 暗号化を使用した接続
ms.custom: ''
ms.date: 03/31/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: vanto
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: ec91fa8a-ab7e-4c1e-a05a-d7951ddf33b1
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: fbefb54c4af48706d04e4081d8e3a6c25ec8e21f
ms.sourcegitcommit: ebe81e2daa544f41c8ababb66a91c218ad0c2a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106177050"
---
# <a name="connecting-with-encryption"></a>暗号化を使用した接続

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

この記事の例では、Java アプリケーションでトランスポート層セキュリティ (TLS) 暗号化を使用できるようにする接続文字列プロパティの使用方法について説明します。 これらの新しい接続文字列プロパティ (**encrypt**、**trustServerCertificate**、**trustStore**、**trustStorePassword**、**hostNameInCertificate** など) の詳細については、「[接続プロパティの設定](setting-the-connection-properties.md)」を参照してください。

## <a name="configuring-the-connection"></a>接続の構成

**encrypt** プロパティが **true** に設定され、**trustServerCertificate** プロパティが **true** に設定されている場合、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の TLS 証明書が検証されません。 この設定は、テスト環境 ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インスタンスが自己署名入りの証明書しか備えていない環境など) で接続を許可する場合に一般的です。

次のコード例では、接続文字列内に **trustServerCertificate** プロパティを設定する方法を示します。

```java
String connectionUrl =
    "jdbc:sqlserver://localhost:1433;" +
     "databaseName=AdventureWorks;integratedSecurity=true;" +
     "encrypt=true;trustServerCertificate=true";
```

**encrypt** プロパティが **true** に設定され、**trustServerCertificate** プロパティが **false** に設定されている場合、[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] では [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の TLS 証明書が検証されます。 サーバー証明書の検証は、TLS ハンドシェイクの一部であり、接続先のサーバーが適切なサーバーであることが保証されます。 サーバー証明書を検証するには、**trustStore** 接続プロパティと **trustStorePassword** 接続プロパティを明示的に使用するか、または基になる Java 仮想マシン (JVM) のトラスト ストアを暗黙的に使用して、接続時にトラスト マテリアルを提供する必要があります。

**trustStore** プロパティでは、証明書の trustStore ファイルへのパス (ファイル名を含む) を指定します。このファイルには、クライアントが信頼する証明書の一覧が含まれています。 **trustStorePassword** プロパティでは、trustStore データの整合性の確認に使用するパスワードを指定します。 JVM の既定のトラスト ストアの使用に関する詳細については、「[暗号化のためのクライアントの構成](configuring-the-client-for-ssl-encryption.md)」を参照してください。

次のコード例では、接続文字列内に **trustStore** プロパティと **trustStorePassword** プロパティを設定する方法を示します。

```java
String connectionUrl =
    "jdbc:sqlserver://localhost:1433;" +
     "databaseName=AdventureWorks;integratedSecurity=true;" +
     "encrypt=true; trustServerCertificate=false;" +
     "trustStore=storeName;trustStorePassword=storePassword";
```

JDBC Driver には、サーバーのホスト名を指定するもう 1 つのプロパティである **hostNameInCertificate** が用意されています。 このプロパティの値は、証明書の subject プロパティと一致する必要があります。

次のコード例では、接続文字列内で **hostNameInCertificate** プロパティを使用する方法を示します。

```java
String connectionUrl =
    "jdbc:sqlserver://localhost:1433;" +
     "databaseName=AdventureWorks;integratedSecurity=true;" +
     "encrypt=true; trustServerCertificate=false;" +
     "trustStore=storeName;trustStorePassword=storePassword" +
     "hostNameInCertificate=hostName";
```

> [!NOTE]
> または、[SQLServerDataSource](reference/sqlserverdatasource-class.md) クラスによって提供される適切な **setter** メソッドを使用して、接続プロパティの値を設定することもできます。

**encrypt** プロパティが **true** であり、**trustServerCertificate** プロパティが **false** であり、接続文字列のサーバー名が TLS 証明書のサーバー名に一致しない場合は、次のエラーが発行されます: `The driver couldn't establish a secure connection to SQL Server by using Secure Sockets Layer (SSL) encryption. Error: "java.security.cert.CertificateException: Failed to validate the server name in a certificate during Secure Sockets Layer (SSL) initialization."`。 バージョン 7.2 以降では、ドライバーにより、TLS 証明書のサーバー名の左端のラベルでのワイルドカードのパターン マッチングがサポートされます。

## <a name="see-also"></a>関連項目

[暗号化の使用](using-ssl-encryption.md)  
[JDBC ドライバー アプリケーションのセキュリティ保護](securing-jdbc-driver-applications.md)  
