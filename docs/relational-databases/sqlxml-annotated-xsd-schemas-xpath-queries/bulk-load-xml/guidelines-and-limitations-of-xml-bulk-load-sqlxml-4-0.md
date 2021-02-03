---
title: XML 一括読み込みのガイドラインと制限 (SQLXML)
description: SQLXML 4.0 での XML 一括読み込みの使用に関するガイドラインと制限事項について説明します。
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- XML Bulk Load [SQLXML], about XML Bulk Load
- bulk load [SQLXML], about bulk load
ms.assetid: c5885d14-c7c1-47b3-a389-455e99a7ece1
author: MightyPen
ms.author: genemi
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: b17b1990f168326ae884b4db4f1ff22a63b24e21
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97439691"
---
# <a name="guidelines-and-limitations-of-xml-bulk-load-sqlxml-40"></a>XML 一括読み込みのガイドラインと制限 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  XML 一括読み込みを使用する場合は、次のガイドラインと制限に留意してください。  
  
-   インライン スキーマはサポートされません。  
  
     インライン スキーマがソース XML ドキュメントにある場合、XML 一括読み込みでそのスキーマは無視されます。 XML 一括読み込みには、XML データの外部にあるマッピング スキーマを指定してください。 ノードでは、 **xmlns = "x:schema"** 属性を使用してマッピングスキーマを指定することはできません。  
  
-   XML ドキュメントが適切な形式であるかどうかはチェックされますが、検証は行われません。  
  
     Xml 一括読み込みでは、xml ドキュメントが適切な形式であるかどうかを確認し、xml が World Wide Web コンソーシアムの XML 1.0 勧告の構文要件に準拠しているかどうかを確認します。 ドキュメントが適切な形式でない場合、XML 一括読み込みの処理は取り消され、エラーが返されます。 ただし、ドキュメントがフラグメントの場合 (ドキュメントに単一のルート要素がない場合) だけは、XML 一括読み込みでドキュメントが読み込まれます。  
  
     XML 一括読み込みでは、XML データ ファイル内で定義または参照されている XML-Data または DTD スキーマに関して、ドキュメントの検証は行われません。 さらに、XML 一括読み込みでは、指定されるマッピング スキーマに対して XML データ ファイルは検証されません。  
  
-   XML prolog 情報は無視されます。  
  
     Xml 一括読み込みでは、XML ドキュメント内の要素の前後にあるすべての情報が無視さ \<root> れます。 たとえば、XML 宣言、内部 DTD 定義、外部 DTD 参照、コメントなどは無視されます。  
  
-   マッピング スキーマで、2 つのテーブル (たとえば Customer と CustOrder) 間の主キー/外部キーのリレーションシップを定義する場合は、主キーがあるテーブルを先に記述する必要があります。 外部キー列があるテーブルは、後に記述します。 その理由は、スキーマ内でテーブルが識別される順序が、データベースに読み込まれる順序と同じであるためです。たとえば、次の XDR スキーマでは、要素が要素の前に記述されているため、XML 一括読み込みで使用されるとエラーが発生し **\<Order>** **\<Customer>** ます。 CustOrder の CustomerID 列は、Cust テーブル内の CustomerID 主キー列を参照する外部キー列です。  
  
    ```  
    <?xml version="1.0" ?>  
    <Schema xmlns="urn:schemas-microsoft-com:xml-data"   
            xmlns:dt="urn:schemas-microsoft-com:xml:datatypes"    
            xmlns:sql="urn:schemas-microsoft-com:xml-sql" >  
  
        <ElementType name="Order" sql:relation="CustOrder" >  
          <AttributeType name="OrderID" />  
          <AttributeType name="CustomerID" />  
          <attribute type="OrderID" />  
          <attribute type="CustomerID" />  
        </ElementType>  
  
       <ElementType name="CustomerID" dt:type="int" />  
       <ElementType name="CompanyName" dt:type="string" />  
       <ElementType name="City" dt:type="string" />  
  
       <ElementType name="root" sql:is-constant="1">  
          <element type="Customers" />  
       </ElementType>  
       <ElementType name="Customers" sql:relation="Cust"   
                         sql:overflow-field="OverflowColumn"  >  
          <element type="CustomerID" sql:field="CustomerID" />  
          <element type="CompanyName" sql:field="CompanyName" />  
          <element type="City" sql:field="City" />  
          <element type="Order" >   
               <sql:relationship  
                   key-relation="Cust"  
                    key="CustomerID"  
                    foreign-key="CustomerID"  
                    foreign-relation="CustOrder" />  
          </element>  
       </ElementType>  
    </Schema>  
    ```  
  
-   **Sql: overflow フィールド** 注釈を使用してオーバーフロー列が指定されていない場合、Xml 一括読み込みでは、xml ドキュメント内に存在するデータは無視されますが、マッピングスキーマには記述されていません。  
  
     XML 一括読み込みでは、XML データ ストリーム内に既知のタグが検出されると常に、指定のマッピング スキーマが適用されます。 XML ドキュメントに存在していてもスキーマに記述されていないデータは無視されます。 たとえば、要素を記述するマッピングスキーマがあるとし **\<Customer>** ます。 XML データファイルには、 **\<AllCustomers>** すべての要素を囲むルートタグ (スキーマでは説明されていません) があり **\<Customer>** ます。  
  
    ```  
    <AllCustomers>  
      <Customer>...</Customer>  
      <Customer>...</Customer>  
       ...  
    </AllCustomers>  
    ```  
  
     この場合、XML 一括読み込みでは要素が無視され、 **\<AllCustomers>** 要素でマッピングが開始さ **\<Customer>** れます。 XML ドキュメントに存在していてもスキーマに記述されていない要素は無視されます。  
  
     要素を含む別の XML ソースデータファイルについて考えてみ **\<Order>** ます。 この要素はマッピング スキーマには記述されていません。  
  
    ```  
    <AllCustomers>  
      <Customer>...</Customer>  
        <Order> ... </Order>  
        <Order> ... </Order>  
         ...  
      <Customer>...</Customer>  
        <Order> ... </Order>  
        <Order> ... </Order>  
         ...  
      ...  
    </AllCustomers>  
    ```  
  
     XML 一括読み込みでは、これらの要素は無視さ **\<Order>** れます。 ただし、スキーマで **sql: overflow-field** 注釈を使用して、列をオーバーフロー列として識別すると、XML 一括読み込みではこの列にすべての未使用データが格納されます。  
  
-   CDATA セクションとエンティティ参照は、データベースに保存される前に、同等の文字列に変換されます。  
  
     この例では、CDATA セクションによって要素の値がラップさ **\<City>** れます。 XML 一括読み込みでは、データベースに要素を挿入する前に、文字列値 ("NY") が抽出され **\<City>** ます。  
  
    ```  
    <City><![CDATA[NY]]> </City>  
    ```  
  
     XML 一括読み込みでは、エンティティ参照は保持されません。  
  
-   マッピング スキーマで属性に既定値が指定されており、その属性が XML ソース データに含まれていない場合、XML 一括読み込みでは既定値が使用されます。  
  
     次のサンプル XDR スキーマでは、 **HireDate** 属性に既定値が割り当てられています。  
  
    ```  
    <?xml version="1.0" ?>  
    <Schema xmlns="urn:schemas-microsoft-com:xml-data"   
            xmlns:dt="urn:schemas-microsoft-com:xml:datatypes"    
            xmlns:sql="urn:schemas-microsoft-com:xml-sql" >  
       <ElementType name="root" sql:is-constant="1">  
          <element type="Customers" />  
       </ElementType>  
  
       <ElementType name="Customers" sql:relation="Cust3" >  
          <AttributeType name="CustomerID" dt:type="int"  />  
          <AttributeType name="HireDate"  default="2000-01-01" />  
          <AttributeType name="Salary"   />  
  
          <attribute type="CustomerID" sql:field="CustomerID" />  
          <attribute type="HireDate"   sql:field="HireDate"  />  
          <attribute type="Salary"     sql:field="Salary"    />  
       </ElementType>  
    </Schema>  
    ```  
  
     この XML データでは、2番目の要素に **HireDate** 属性がありません **\<Customers>** 。 XML 一括読み込みで2番目の要素がデータベースに挿入されるときに、 **\<Customers>** スキーマで指定されている既定値が使用されます。  
  
    ```  
    <ROOT>  
      <Customers CustomerID="1" HireDate="1999-01-01" Salary="10000" />  
      <Customers CustomerID="2" Salary="10000" />  
    </ROOT>  
    ```  
  
-   **Sql: url エンコード** の注釈はサポートされていません。  
  
     XML データ入力に URL を指定して、その場所からデータの一括読み込みを行うことはできません。  
  
     マッピング スキーマで指定されているテーブルは、新しく作成されます (データベースは存在する必要があります)。 データベースに1つ以上のテーブルが既に存在する場合、SGDropTables プロパティは、これらの既存のテーブルを削除して再作成するかどうかを決定します。  
  
-   SchemaGen プロパティ (たとえば、SchemaGen = true) を指定した場合、マッピングスキーマで指定されているテーブルが作成されます。 ただし、SchemaGen では、次の1つの例外を除き、これらのテーブルに対する制約 (主キー/外部キーの制約など) は作成されません。リレーションシップの主キーを構成する XML ノードが XML 型の ID (つまり、XSD の **type = "xsd: ID"** ) を持つように定義されていて、SchemaGen に対して SGUseID プロパティが True に設定されている場合、id 型のノードから作成される主キーだけでなく、マッピングスキーマリレーションシップから主キー/外部キーのリレーションシップが  
  
-   SchemaGen は、リレーショナルスキーマを生成するために XSD スキーマファセットと拡張機能を使用しません [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 。  
  
-   一括読み込みで SchemaGen プロパティ (SchemaGen = true など) を指定した場合、指定されている (共有名のビューではなく) テーブルだけが更新されます。  
  
-   SchemaGen は、注釈付き XSD からリレーショナルスキーマを生成するための基本的な機能のみを提供します。 ユーザーは必要に応じて、生成されたテーブルを手動で変更する必要があります。  
  
-   テーブル間に複数のリレーションシップが存在する場合、SchemaGen は、2つのテーブル間に含まれるすべてのキーを含む単一のリレーションシップを作成しようとします。 この制限は、[!INCLUDE[tsql](../../../includes/tsql-md.md)] エラーの原因となることがあります。  
  
-   データベースに XML データの一括読み込みを行う場合は、マッピング スキーマ内に、データベース列にマップされる属性または子要素が 1 つ以上存在している必要があります。  
  
-   XML 一括読み込みを使用して日付値を挿入する場合、値は (-)CCYY-MM-DD((+-)TZ) の形式で指定する必要があります。 これは日付の標準の XSD 形式です。  
  
-   一部のプロパティ フラグは、他のプロパティ フラグと互換性がありません。 たとえば、一括読み込みでは、 **Ignoreduplicatekeys = true** を **Keepidentity = false** と共に使用することはできません。 **Keepidentity = false** の場合、一括読み込みでは、サーバーがキー値を生成する必要があります。 テーブルには、キーに対する **IDENTITY** 制約が必要です。 サーバーは重複するキーを生成しません。つまり、 **Ignoreduplicatekeys** が **true** に設定されている必要はありません。 **Ignoreduplicatekeys** は、受信データから行を含むテーブルに主キーの値をアップロードする場合にのみ **true** に設定し、主キーの値が競合する可能性がある場合にのみ設定する必要があります。  
  
  
