---
title: 名前空間を使用した XPath クエリの実行 (SQLXMLOLEDB)
description: SQLXMLOLEDB プロバイダーで XPath クエリを実行するときに、SQLXML 4.0 で名前空間を指定する方法について説明します。
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- SQLXMLOLEDB Provider, executing XPath queries
- namespaces property
- queries [SQLXML], SQLXMLOLEDB Provider
- XPath queries [SQLXML], namespaces
- XPath queries [SQLXML], SQLXMLOLEDB Provider
- namespaces [SQLXML], XPath queries
ms.assetid: 024a4b7d-435d-47ba-9e80-2c2f640108f5
author: MightyPen
ms.author: genemi
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: ee24d16dce8ae60ccf51a866a274b15b2737ca89
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97462913"
---
# <a name="executing-xpath-queries-with-namespaces-sqlxmloledb-provider"></a>名前空間を使用した、XPath クエリの実行 (SQLXMLOLEDB Provider)
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  XPath クエリには名前空間を使用できます。 スキーマ要素が名前空間で限定されている (対象の名前空間を含んでいる) 場合、そのスキーマに対する XPath クエリでは、この名前空間を指定する必要があります。  
  
 SQLXML 4.0 ではワイルドカード文字 (*) の使用がサポートされないため、XPath クエリは、名前空間プレフィックスを使用して指定する必要があります。 このプレフィックスを解決するには、名前空間プロパティを使用して名前空間のバインドを指定します。  
  
 次の例では、XPath クエリは、ワイルドカード文字 ( \* ) およびローカル名 () および名前空間 uri () の xpath 関数を使用して名前空間を指定します。 この XPath クエリでは、ローカル名が **Contact** で、名前空間 URI が **urn: myschema.xml: Contacts** であるすべての要素が返されます。  
  
```  
/*[local-name() = 'Contact' and namespace-uri() = 'urn:myschema:Contacts']  
```  
  
 SQLXML 4.0 では、この XPath クエリを名前空間プレフィックスと共に指定する必要があります。 たとえば、 **x:Contact** のようになります。ここで、 **x** は名前空間プレフィックスです。 次の XSD スキーマについて考えてみます。  
  
```  
<schema xmlns="http://www.w3.org/2001/XMLSchema"  
            xmlns:sql="urn:schemas-microsoft-com:mapping-schema"  
            xmlns:con="urn:myschema:Contacts"  
            targetNamespace="urn:myschema:Contacts">  
<complexType name="ContactType">  
  <attribute name="CID" sql:field="ContactID" type="ID"/>  
  <attribute name="FName" sql:field="FirstName" type="string"/>  
  <attribute name="LName" sql:field="LastName"/>   
</complexType>  
<element name="Contact" type="con:ContactType" sql:relation="Person.Contact"/>  
</schema>  
```  
  
 このスキーマでは対象の名前空間が定義されているため、このスキーマに対して "Employee" などの XPath クエリを実行するときには、クエリに名前空間を含める必要があります。  
  
 これは、上の XSD スキーマに対して XPath クエリ (x:Employee) を実行する [!INCLUDE[msCoName](../../../includes/msconame-md.md)] Visual Basic アプリケーションのサンプルです。 プレフィックスを解決するには、名前空間のプロパティを使用して名前空間のバインドを指定します。  
  
> [!NOTE]  
>  コードでは、接続文字列に [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンス名を含める必要があります。 また、この例ではデータ プロバイダーとして [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client (SQLNCLI11) を使用するよう指定していますが、これには追加ネットワーク クライアントがインストールされていることが必要です。 詳細については、「 [SQL Server Native Client のシステム要件](../../../relational-databases/native-client/system-requirements-for-sql-server-native-client.md)」を参照してください。  
  
```  
Option Explicit  
Private Sub Form_Load()  
    Dim con As New ADODB.Connection  
    Dim cmd As New ADODB.Command  
    Dim stm As New ADODB.Stream  
    con.Open "provider=SQLXMLOLEDB.4.0;Data Provider=SQLNCLI11;Data Source=SqlServerName;Initial Catalog=AdventureWorks;Integrated Security=SSPI;"  
    Set cmd.ActiveConnection = con  
    stm.Open  
    cmd.Properties("Output Stream").Value = stm  
    cmd.Properties("Output Encoding") = "utf-8"  
    cmd.Properties("Mapping schema") = "C:\DirectoryPath\con-ex.xml"  
    cmd.Properties("namespaces") = "xmlns:x='urn:myschema:Contacts'"  
    '  Debug.Print "Set Command Dialect to DBGUID_XPATH"  
    cmd.Dialect = "{ec2a4293-e898-11d2-b1b7-00c04f680c56}"  
    cmd.CommandText = "x:Contact"  
    cmd.Execute , , adExecuteStream   
    stm.Position = 0  
    Debug.Print stm.ReadText(adReadAll)  
End Sub  
```  
  
### <a name="to-test-this-application"></a>このアプリケーションをテストするには  
  
1.  サンプルの XSD スキーマをフォルダーに保存します。  
  
2.  Visual Basic 実行可能プロジェクトを作成し、プロジェクト内にコードをコピーして、 指定されたディレクトリ パスを適切に変更します。  
  
3.  次のプロジェクト参照を追加します。  
  
    ```  
    "Microsoft ActiveX Data Objects 2.8 Library"  
    ```  
  
4.  アプリケーションを実行します。  

 結果の一部を次に示します。  
  
```  
<y0:Employee xmlns:y0="urn:myschema:Contacts"   
             LName="Achong" CID="1" FName="Gustavo"/>  
<y0:Employee xmlns:y0="urn:myschema:Employees"   
             LName="Abel" CID="2" FName="Catherine"/>  
```  
  
 XML ドキュメント内に生成されるプレフィックスはその都度変わりますが、マップされる名前空間は同じです。  
  
  
