---
title: データ型の違いについて
description: Java プログラミング言語のデータ型と SQL Server のデータ型の違いと、JDBC Driver for SQL Server がどのように変換に役立つかについて説明します。
ms.custom: ''
ms.date: 08/12/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: ab8fa00f-cb16-47e2-94b8-3a76f56c2b84
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 16eea906570c9ec19e83246df5073f6ac72e2403
ms.sourcegitcommit: 66407a7248118bb3e167fae76bacaa868b134734
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728363"
---
# <a name="understanding-data-type-differences"></a>データ型の違いについて

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

Java プログラミング言語のデータ型と [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のデータ型には、異なる点が多数あります。 [!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] では、さまざまな型の変換を行うことによって、これらの違いに対応しています。  

## <a name="character-types"></a>文字型

JDBC 文字列のデータ型は、**CHAR**、**VARCHAR**、および **LONGVARCHAR** です。 JDBC ドライバーでは、JDBC 4.0 API がサポートされます。 JDBC 4.0 では、JDBC の文字列データ型として、**NCHAR**、**NVARCHAR**、および **LONGNVARCHAR** も使用できます。 これらの新しい文字列型では Java のネイティブの文字型が Unicode 形式で維持されるため、ANSI から Unicode への変換または Unicode から ANSI への変換を実行する必要がありません。  
  
| 型            | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 固定長    | [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **char** 型と **nchar** データ型は、JDBC の **CHAR** 型と **NCHAR** 型に直接マップされます。 列が `SET ANSI_PADDING ON` の場合、このサーバーが埋め込みを行う固定長の型です。 埋め込みは **nchar** に対しては常にオンですが、**char** に対しては、サーバーにより char 列の埋め込みが行われていない場合は、JDBC ドライバーが埋め込みを行います。                                                                                                                                                                                                                                                                                                                                                                                      |
| 可変長 | [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **varchar** 型と **nvarchar** 型は、それぞれ JDBC の **VARCHAR** 型と **NVARCHAR** 型に直接マップされます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Long            | [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **text** 型と **ntext** 型は、それぞれ JDBC の **LONGVARCHAR** 型と **LONGNVARCHAR** 型にマップされます。 これらの型は [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降では非推奨とされるため、代わりに大きな値の型である **varchar(max)** または **nvarchar(max)** を使用する必要があります。<br /><br /> サーバーの **text** 列と **ntext** 列に対して update\<Numeric Type> メソッドと [updateObject (int, java.lang.Object)](../../connect/jdbc/reference/updateobject-method-int-java-lang-object.md) メソッドを使用すると、エラーになります。 ただし、サーバーの **text** 列と **ntext** 列に対して、文字変換の型を指定して [setObject](../../connect/jdbc/reference/setobject-method-sqlserverpreparedstatement.md) メソッドを使用することは可能です。 |
  
## <a name="binary-string-types"></a>バイナリ文字列型

JDBC バイナリ文字列型は **BINARY**、**VARBINARY**、および **LONGVARBINARY** です。  
  
| 型            | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 固定長    | [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **binary** 型は、JDBC **BINARY** 型に直接マップされます。 列が SET ANSI_PADDING ON の場合、これはサーバーが埋め込みを行う固定長の型です。 サーバーの char 列に埋め込みが行われていない場合は、JDBC ドライバーが埋め込みを行います。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **timestamp** 型は、固定長 8 バイトの JDBC の **BINARY** 型です。 |
| 可変長 | [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **varbinary** 型は、JDBC **VARBINARY** 型にマップされます。<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では **udt** 型は、**VARBINARY** 型として JDBC にマップされます。                                                                                                                                                                                                                                 |
| Long            | [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **image** 型は、JDBC の **LONGVARBINARY** 型にマップされます。 この型は [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降では非推奨とされるため、代わりに大きな値の型である **varbinary(max)** を使用する必要があります。                                                                                                                                                                                           |
  
## <a name="exact-numeric-types"></a>真数型

JDBC の真数型は、対応する SQL Server 型に直接マップされます。  
  
| 型     | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BIT      | JDBC の **BIT** 型は、0 または 1 の単一ビットを表します。 これは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **bit** 型にマップされます。                                                                                                                                                                                                                                                                                                                                       |
| TINYINT  | JDBC の **TINYINT** 型は、1 バイトを表します。 これは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **tinyint** 型にマップされます。                                                                                                                                                                                                                                                                                                                                                 |
| SMALLINT | JDBC の **SMALLINT** 型は、符号付きの 16 ビット整数を表します。 これは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **smallint** 型にマップされます。                                                                                                                                                                                                                                                                                                                                     |
| INTEGER  | JDBC の **INTEGER** 型は、符号付きの 32 ビット整数を表します。 これは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **int** 型にマップされます。                                                                                                                                                                                                                                                                                                                                           |
| bigint   | JDBC の **BIGINT** 型は、符号付きの 64 ビット整数を表します。 これは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **bigint** 型にマップされます。                                                                                                                                                                                                                                                                                                                                         |
| NUMERIC  | JDBC の **NUMERIC** 型は、同一有効桁数の値を保持する固定長の有効桁数の 10 進値を表します。 **NUMERIC** 型は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **numeric** 型にマップされます。                                                                                                                                                                                                                                                                   |
| DECIMAL  | JDBC の **DECIMAL** 型は、指定された有効桁数以上の値を保持する固定長の有効桁数の 10 進値を表します。 **Decimal** 型は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **decimal** 型にマップされます。<br /><br /> JDBC の **DECIMAL** 型は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **money** 型と **smallmoney** 型にもマップされます。これらはそれぞれ、8 バイトと 4 バイトで格納される固定長の有効桁数の decimal 型です。 |
  
## <a name="approximate-numeric-types"></a>概数型

JDBC の概数型は、**REAL**、**DOUBLE**、および **FLOAT** です。  
  
| 型   | 説明                                                                                                                                                                                                                                                                                                   |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| real   | JDBC の **REAL** 型の有効桁数は 7 桁 (単精度) で、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **real** 型に直接マップされます。                                                                                                                                     |
| DOUBLE | JDBC の **DOUBLE** 型の有効桁数は 15 桁 (倍精度) で、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **float** 型にマップされます。 JDBC の **FLOAT** 型は **DOUBLE** のシノニムです。 **FLOAT** と **DOUBLE** は間違いやすいため、**DOUBLE** の使用をお勧めします。 |
  
## <a name="datetime-types"></a>Datetime 型

JDBC の **TIMESTAMP** 型は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **datetime** 型および **smalldatetime** 型にマップされます。 **datetime** 型は、2 つの 4 バイト整数に格納されます。 **smalldatetime** 型は同じ情報 (日付と時刻) を保持しますが、精度が低く、2 つの 2 バイトの小整数に格納されます。  
  
> [!NOTE]  
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の **timestamp** 型は、固定長のバイナリ文字列型です。 これは、JDBC の次の時刻型のいずれにもマップされません。**DATE**、**TIME**、または **TIMESTAMP**。  
  
## <a name="custom-type-mapping"></a>カスタム型のマッピング

JDBC の高度な型 (UDT、Struct など) に対応した SQLData インターフェイスを使用するカスタム型のマッピング機能は、 JDBC ドライバーに実装されていません。  
  
## <a name="see-also"></a>関連項目

[JDBC ドライバーのデータ型について](../../connect/jdbc/understanding-the-jdbc-driver-data-types.md)  
