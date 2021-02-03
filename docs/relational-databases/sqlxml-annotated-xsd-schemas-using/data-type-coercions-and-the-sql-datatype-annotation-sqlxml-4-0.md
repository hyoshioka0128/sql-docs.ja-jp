---
title: 'Sql: datatype (SQLXML) を使用したデータ型の変換'
description: 'SQLXML 4.0 で xsd: type 属性と sql: datatype 属性を使用して、XSD データ型と SQL Server データ型の間のマッピングを制御する方法について説明します。'
ms.date: 03/17/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- mapping data types [SQLXML]
- type attribute
- sql:datatype
- data types [SQLXML], converting
- annotated XSD schemas, mapping data types
- xsd:type
- datatype annotation
- converting data types [SQLXML]
- data types [SQLXML], mapping data types
- XSD schemas [SQLXML], mapping data types
ms.assetid: db192105-e8aa-4392-b812-9d727918c005
author: MightyPen
ms.author: genemi
ms.reviewer: ''
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 0e457aa6c1d1e16e96682f19898e22813254e9ef
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97461803"
---
# <a name="data-type-conversions-and-the-sqldatatype-annotation-sqlxml-40"></a>データ型の変換と sql: datatype 注釈 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  Xsd スキーマでは、 **xsd: type** 属性は、要素または属性の xsd データ型を指定します。 XSD スキーマを使用したデータベースからのデータの抽出では、指定されているデータ型を使用して、データが書式設定されます。  
  
 スキーマで XSD 型を指定するだけでなく、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **sql: datatype** 注釈を使用して Microsoft データ型を指定することもできます。 Xsd **: type** 属性と **sql: datatype** 属性は、xsd データ型とデータ型の間のマッピングを制御し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
## <a name="xsdtype-attribute"></a>xsd:type 属性  
 **Xsd: type** 属性を使用して、列にマップする属性または要素の XML データ型を指定できます。 **Xsd: type** は、サーバーから返されるドキュメントと、実行される XPath クエリに影響します。 **Xsd: type** を含むマッピングスキーマに対して xpath クエリを実行すると、xpath はクエリを処理するときに、指定されたデータ型を使用します。 XPath で **xsd: type** を使用する方法の詳細については、次を参照してください。 [xsd データ型から xpath データ型へのマッピング &#40;SQLXML 4.0&#41;](../../relational-databases/sqlxml-annotated-xsd-schemas-using/mapping-xsd-data-types-to-xpath-data-types-sqlxml-4-0.md)です。  
  
 返されるドキュメントでは、すべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型は文字列表記に変換されます。 また、データ型によっては追加の変換が必要です。 次の表に、さまざまな **xsd: type** 値に使用される変換の一覧を示します。  
  
|XSD データ型|SQL Server の変換|  
|-------------------|---------------------------|  
|Boolean|CONVERT(bit, COLUMN)|  
|Date|LEFT(CONVERT(nvarchar(4000), COLUMN, 126), 10)|  
|decimal|CONVERT(money, COLUMN)|  
|id/idref/idrefs|id-prefix + CONVERT(nvarchar(4000), COLUMN, 126)|  
|nmtoken/nmtokens|id-prefix + CONVERT(nvarchar(4000), COLUMN, 126)|  
|時刻|SUBSTRING(CONVERT(nvarchar(4000), COLUMN, 126), 1+CHARINDEX(N'T', CONVERT(nvarchar(4000), COLUMN, 126)), 24)|  
|その他すべて|追加の変換はありません。|  
  
> [!NOTE]  
>  によって返される値の中には、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **xsd: type** を使用して指定された XML データ型との互換性がない場合があります (たとえば、"XYZ" を **decimal** データ型に変換するなど)。または、値がそのデータ型の範囲を超えている (たとえば、-10万が **unsignedshort** xsd 型に変換される) 互換性のない型を変換しようとすると、無効な XML ドキュメントが生成されるか、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エラーになる可能性があります。  
  
## <a name="mapping-from-sql-server-data-types-to-xsd-data-types"></a>SQL Server データ型から XSD データ型へのマッピング  
 次の表は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型から XSD データ型への明確なマッピングです。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 型がわかっている場合は、XSD スキーマで指定できる、対応する XSD 型をこの表で確認してください。  
  
|SQL Server のデータ型|XSD データ型|  
|--------------------------|-------------------|  
|**bigint**|**long**|  
|**[バイナリ]**|**base64Binary**|  
|**bit**|**boolean**|  
|**char**|**string**|  
|**datetime**|**dateTime**|  
|**decimal**|**decimal**|  
|**float**|**double**|  
|**image**|**base64Binary**|  
|**int**|**int**|  
|**money**|**decimal**|  
|**nchar**|**string**|  
|**ntext**|**string**|  
|**nvarchar**|**string**|  
|**numeric**|**decimal**|  
|**real**|**float**|  
|**smalldatetime**|**dateTime**|  
|**smallint**|**short**|  
|**smallmoney**|**decimal**|  
|**sql_variant**|**string**|  
|**sysname**|**string**|  
|**text**|**string**|  
|**timestamp**|**dateTime**|  
|**tinyint**|**unsignedByte**|  
|**varbinary**|**base64Binary**|  
|**varchar**|**string**|  
|**uniqueidentifier**|**string**|  
  
## <a name="sqldatatype-annotation"></a>sql:datatype 注釈  
 **Sql: datatype** 注釈は、データ型を指定するために使用されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] この注釈は、次の場合に指定する必要があります。  
  
-    [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] XSD **datetime**、 **date**、または **time** 型から datetime 列に一括読み込みを実行しています。 この場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **sql: datatype = "dateTime"** を使用して、列のデータ型を識別する必要があります。 この規則はアップデートグラムにも当てはまります。  
  
-   Uniqueidentifier 型の列に一括読み込みを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 行い、XSD 値は、中かっこ ({および}) を含む GUID です。 **Sql: datatype = "uniqueidentifier"** を指定すると、列に挿入される前に、値から中かっこが削除されます。 **Sql: datatype** が指定されていない場合、値は中かっこで送信され、挿入または更新は失敗します。  
  
-   XML データ型 **base64Binary** は、さまざまな [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型 (**binary**、 **image**、または **varbinary**) にマップされます。 XML データ型の **base64Binary** を特定のデータ型にマップするには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **sql: datatype** 注釈を使用します。 この注釈では、属性のマップ先列の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データ型を明示的に指定できます。 これは、データをデータベースに格納する場合に便利で、 **Sql: datatype** 注釈を指定することにより、明示的なデータ型を識別でき [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。  
  
 一般に、スキーマには **sql: datatype** を指定することをお勧めします。  
  
## <a name="examples"></a>例  
 次の例を使用した実際のサンプルを作成するには、特定の条件を満たす必要があります。 詳細については、「 [SQLXML の例を実行するための要件](../../relational-databases/sqlxml/requirements-for-running-sqlxml-examples.md)」を参照してください。  
  
### <a name="a-specifying-xsdtype"></a>A. xsd:type を指定する  
 この例では、スキーマで **xsd: type** 属性を使用して指定した xsd の **日付** 型が、結果の XML ドキュメントに影響を与える方法を示します。 このスキーマでは、AdventureWorks データベース内の Sales.SalesOrderHeader テーブルの XML ビューが提供されます。  
  
```  
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"   
            xmlns:sql="urn:schemas-microsoft-com:mapping-schema">  
  <xsd:element name="Order" sql:relation="Sales.SalesOrderHeader">  
     <xsd:complexType>  
       <xsd:attribute name="SalesOrderID" type="xsd:string" />   
       <xsd:attribute name="CustomerID"   type="xsd:string" />   
       <xsd:attribute name="OrderDate"    type="xsd:date" />   
       <xsd:attribute name="DueDate"  />   
       <xsd:attribute name="ShipDate"  type="xsd:time" />   
     </xsd:complexType>  
  </xsd:element>  
</xsd:schema>  
```  
  
 この XSD スキーマには、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] から日付値を返す 3 つの属性が含まれています。 それぞれの属性は次のとおりです。  
  
-   **Orderdate** 属性に **xsd: type = date** を指定します。 orderdate 属性に対してによって返される値の日付部分 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が表示されます。   
  
-   **ShipDate** 属性で **xsd: type = time** を指定します。 ShipDate 属性に対してによって返される値の時刻部分 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が表示されます。   
  
-   **DueDate** 属性に **xsd: type** が指定されていません。によって返されたのと同じ値 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が表示されます。  
  
##### <a name="to-test-a-sample-xpath-query-against-the-schema"></a>スキーマに対してサンプル XPath クエリをテストするには  
  
1.  上のスキーマのコードをコピーして、テキスト ファイルに貼り付け、 xsdType.xml として保存します。  
  
2.  次のテンプレートをコピーして、テキスト ファイルに貼り付け、 xsdType.xml を保存したディレクトリに xsdTypeT.xml として保存します。  
  
    ```  
    <ROOT xmlns:sql="urn:schemas-microsoft-com:xml-sql">  
      <sql:xpath-query mapping-schema="xsdType.xml">  
        /Order  
      </sql:xpath-query>  
    </ROOT>  
    ```  
  
     マッピング スキーマ (xsdType.xml) に指定するディレクトリ パスは、テンプレートを保存するディレクトリに対する相対パスです。 次のように、絶対パスを指定することもできます。  
  
    ```  
    mapping-schema="C:\SqlXmlTest\xsdType.xml"  
    ```  
  
3.  SQLXML 4.0 テスト スクリプト (sqlxml4test.vbs) を作成し、それを使用してテンプレートを実行します。  

     詳細については、「ADO を使用した [SQLXML 4.0 クエリの実行](../../relational-databases/sqlxml/using-ado-to-execute-sqlxml-4-0-queries.md)」を参照してください。  
  
 次に結果セットの一部を示します。  
  
```  
<ROOT xmlns:sql="urn:schemas-microsoft-com:xml-sql">  
  <Order SalesOrderID="43659"   
         CustomerID="676"   
         OrderDate="2001-07-01"   
         DueDate="2001-07-13T00:00:00"   
         ShipDate="00:00:00" />   
  <Order SalesOrderID="43660"   
         CustomerID="117"   
         OrderDate="2001-07-01"   
         DueDate="2001-07-13T00:00:00"   
         ShipDate="00:00:00" />   
 ...  
</ROOT>  
```  
  
 これは、これと同等の XDR スキーマです。  
  
```  
<?xml version="1.0" ?>  
<Schema xmlns="urn:schemas-microsoft-com:xml-data"  
        xmlns:dt="urn:schemas-microsoft-com:datatypes"  
        xmlns:sql="urn:schemas-microsoft-com:xml-sql">  
  
<ElementType name="Order" sql:relation="Sales.SalesOrderHeader">  
    <AttributeType name="SalesOrderID" />  
    <AttributeType name="CustomerID"  />  
    <AttributeType name="OrderDate" dt:type="date" />  
    <AttributeType name="DueDate" />  
    <AttributeType name="ShipDate" dt:type="time" />  
  
    <attribute type="SalesOrderID" sql:field="OrderID" />  
    <attribute type="CustomerID" sql:field="CustomerID" />  
    <attribute type="OrderDate" sql:field="OrderDate" />  
    <attribute type="DueDate" sql:field="DueDate" />  
    <attribute type="ShipDate" sql:field="ShipDate" />  
</ElementType>  
</Schema>  
```  
  
### <a name="b-specifying-sql-data-type-using-sqldatatype"></a>B. sql:datatype を使用して SQL データ型を指定する  
 実際のサンプルについては、「 [XML 一括読み込みの例 &#40;SQLXML 4.0&#41;](../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/bulk-load-xml/xml-bulk-load-examples-sqlxml-4-0.md)」を参照してください。 この例では、"{" および "}" を含む GUID 値の一括読み込みが行われます。 この例のスキーマでは、データ型を uniqueidentifier として識別する **sql: datatype** を指定して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] います。 この例では、 **sql: datatype** がスキーマで指定されている必要があることを示します。  
  
  
