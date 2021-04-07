---
description: アプリケーションを FIPS 140 に準拠させるために JDBC Driver for SQL Server を FIPS モードで使用する方法について説明します。
title: JDBC の FIPS モード
ms.custom: ''
ms.date: 03/21/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: genemi
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: f11527e1b6e5917ed7b74624dc5e92ed39c27047
ms.sourcegitcommit: ebe81e2daa544f41c8ababb66a91c218ad0c2a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106177003"
---
# <a name="fips-mode"></a>FIPS モード

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

SQL Server 用 Microsoft JDBC ドライバーは、*FIPS 140 準拠* となるように構成された JVM での実行をサポートします。

## <a name="prerequisites"></a>前提条件

- FIPS 構成の JVM
- 適切な TLS/SSL 証明書
- 適切なポリシー ファイル
- 適切な構成パラメーター

## <a name="fips-configured-jvm"></a>FIPS 構成の JVM

一般に、アプリケーションでは、FIPS に準拠している暗号化プロバイダーを使用するように `java.security` ファイルを構成できます。 FIPS 140 準拠の構成方法については、JVM 固有のドキュメントを参照してください。

FIPS 構成として承認されたモジュールを確認するには、「[Validated Modules in the Cryptographic Module Validation Program](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Validated-Modules)」 (暗号化モジュール検証プログラムで検証済みのモジュール) を参照してください。

ベンダーによっては、FIPS を満たすように JVM を構成するその他の手順を用意している場合があります。

## <a name="appropriate-tls-certificate"></a>適切な TLS 証明書

FIPS モードで SQL Server に接続するには、有効な TLS/SSL 証明書が必要です。 FIPS を有効にするクライアント マシン (JVM) 上の Java キーストアにインストールまたはインポートします。

### <a name="importing-tls-certificate-in-java-keystore"></a>Java キーストアに TLS 証明書をインポートする

FIPS では、ほとんどの場合、PKCS またはプロバイダー固有の形式で証明書 (.cert) をインポートする必要があります。
次のスニペットを使用して、TLS/SSL 証明書をインポートし、適切なキーストア形式を使用して作業ディレクトリに格納します。 _TRUST\_STORE\_PASSWORD_ は、Java キーストアのパスワードです。

```java
public void saveGenericKeyStore(
        String provider,
        String trustStoreType,
        String certName,
        String certPath
        ) throws KeyStoreException, CertificateException,
            NoSuchAlgorithmException, NoSuchProviderException,
            IOException
{
    KeyStore ks = KeyStore.getInstance(trustStoreType, provider);
    FileOutputStream os = new FileOutputStream("./MyTrustStore_" + trustStoreType);
    ks.load(null, null);
    ks.setCertificateEntry(certName, getCertificate(certPath));
    ks.store(os, TRUST_STORE_PASSWORD.toCharArray());
    os.flush();
    os.close();
}

private Certificate getCertificate(String pathName)
        throws FileNotFoundException, CertificateException
{
    FileInputStream fis = new FileInputStream(pathName);
    CertificateFactory cf = CertificateFactory.getInstance("X.509");
    return cf.generateCertificate(fis);
}
```

次の例では、BouncyCastle プロバイダーを使用して、Azure TLS/SSL 証明書を PKCS12 形式でインポートしています。 このスニペットを使用すると、証明書は、_MyTrustStore\_PKCS12_ という名前の作業ディレクトリにインポートされます。

`saveGenericKeyStore(BCFIPS, PKCS12, "SQLAzure SSL Certificate Name", "SQLAzure.cer");`

## <a name="appropriate-policy-files"></a>適切なポリシー ファイル

一部の FIPS プロバイダーには、無制限のポリシー jars が必要です。 このような場合、Sun / Oracle については、[JRE 8](https://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html) または [JRE 7](https://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html) の Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files をダウンロードします。

## <a name="appropriate-configuration-parameters"></a>適切な構成パラメーター

JDBC ドライバーを FIPS 準拠モードで実行するには、次の表に示すように接続プロパティを構成します。

### <a name="properties"></a>Properties

|プロパティ|Type|Default|説明|Notes|
|---|---|---|---|---|
|`encrypt`|boolean ["true / false"]|"false"|FIPS 対応 JVM の場合、暗号化プロパティを **true** にする必要があります。||
|`TrustServerCertificate`|boolean ["true / false"]|"false"|FIPS の場合、ユーザーは、証明書チェーンを検証する必要があるため、このプロパティには値 **"false"** を使用する必要があります。 ||
|`trustStore`|String|null|証明書をインポートした Java キーストア ファイルのパス。 証明書をシステムにインストールする場合、何も渡す必要はありません。 ドライバーは、cacerts ファイルまたは jssecacerts ファイルを使用します。||
|`trustStorePassword`|String|null|trustStore データの整合性を確認するために使用するパスワードです。||
|`fips`|boolean ["true / false"]|"false"|FIPS 対応 JVM の場合、このプロパティを **true** にする必要があります。|6\.1.4 で追加 (安定リリース 6.2.2)|
|`fipsProvider`|String|null|JVM で構成される FIPS プロバイダー。 たとえば、BCFIPS、SunPKCS11-NSS など。 |6\.1.2 で追加 (安定リリース 6.2.2)、6.4.0 で非推奨。詳細については、[こちら](https://github.com/Microsoft/mssql-jdbc/pull/460)を参照してください。|
|`trustStoreType`|String|JKS|FIPS モードの場合、信頼ストアの種類 (PKCS12 または FIPS プロバイダーで定義された種類) を設定します。 |6\.1.2 で追加 (安定リリース 6.2.2)|
