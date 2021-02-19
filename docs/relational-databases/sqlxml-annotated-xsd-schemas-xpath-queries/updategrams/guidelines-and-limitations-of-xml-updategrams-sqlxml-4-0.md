---
title: アップデートグラムのガイドラインと制限事項 (SQLXML)
description: SQLXML 4.0 で XML アップデートグラムを使用する場合のガイドラインと制限事項について説明します。
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: xml
ms.topic: reference
helpviewer_keywords:
- updategrams [SQLXML], about updategrams
ms.assetid: b5231859-14e2-4276-bc17-db2817b6f235
author: MightyPen
ms.author: genemi
ms.custom: seo-lt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 71bd56145c0ef26736a7b567db22b783ea258e1c
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97479193"
---
# <a name="guidelines-and-limitations-of-xml-updategrams-sqlxml-40"></a>XML アップデートグラムのガイドラインと制限 (SQLXML 4.0)
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  XML アップデートグラムを使用する場合は、次の点に注意してください。  
  
-   とブロックのペアが1つだけである挿入操作にアップデートグラムを使用している場合は、 **\<before>** **\<after>** ブロックを **\<before>** 省略できます。 逆に、削除操作の場合は、 **\<after>** ブロックを省略できます。  
  
-   タグに複数のおよびブロックを含むアップデートグラムを使用している場合は、 **\<before>** **\<after>** との **\<sync>** **\<before>** **\<after>** ペアを形成するために、ブロックとブロックの両方を指定する必要があり **\<before>** **\<after>** ます。  
  
-   アップデートグラムの更新は、XML スキーマで提供される XML ビューに適用されます。 したがって、既定のマッピングが正しく行われるようにするには、アップデートグラムでスキーマ ファイル名を指定するか、ファイル名を指定しない場合は、要素名と属性名がデータベースのテーブル名と列名に一致している必要があります。  
  
-   SQLXML 4.0 を使用する場合、アップデートグラムのすべての列値は、子要素の XML ビューを構成するために提供される XDR スキーマまたは XSD スキーマで明示的にマップする必要があります。 この動作は、以前のバージョンの SQLXML とは異なります。これは、 **sql: relationship** 注釈で外部キーの一部として暗黙的に指定されている場合に、スキーマでマップされていない列の値を許可します。 この変更は、子要素への主キー値の割り当てには影響しません。SQLXML 4.0 では、子要素に対して明示的に値が指定されていない場合でも、値が割り当てられます。  
  
-   アップデートグラムを使用してバイナリ列 (image データ型など) のデータを変更する場合は、データ型 (たとえば、 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] **sql: datatype = "image"**) と XML データ型 (たとえば、 **dt: type = "binhex"** や **dt: type = "binbase64**) を指定する必要があるマッピングスキーマを指定する必要があります。 アップデートグラムでバイナリ列のデータを指定する必要があります。マッピングスキーマで指定されている **sql: url エンコード** の注釈は、アップデートグラムによって無視されます。  
  
-   XSD スキーマを記述する場合、 **sql: relation** 注釈または **sql: field** 注釈に指定する値に、空白文字 (たとえば、"order details" テーブル名) などの特殊文字が含まれている場合は、この値を角かっこで囲む必要があります (たとえば、"[order details]")。  
  
-   アップデートグラムの使用で、チェーン リレーションシップはサポートされません。 たとえば、テーブル A とテーブル C が、テーブル B を使用するチェーン リレーションシップで関連付けられている場合は、アップデートグラムの実行時に次のエラーが発生します。  
  
    ```  
    There is an inconsistency in the schema provided.  
    ```  
  
     スキーマとアップデートグラムの両方が正しく、有効な形式であっても、チェーン リレーションシップが存在すると、このエラーが発生します。  
  
-   アップデートグラムでは、 **イメージ** の種類のデータを更新中にパラメーターとして渡すことは許可されていません。  
  
-   **Text/ntext** および image のようなバイナリラージオブジェクト (BLOB) 型は、updategrams を使用するときにのブロックでは使用しない **\<before>** でください。これには、同時実行制御で使用するためのものが含まれます。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] で、BLOB 型の比較の制限によって問題が発生する可能性があります。 たとえば、 **text** データ型の列を比較するには、WHERE 句で LIKE キーワードを使用します。ただし、データのサイズが8K を超える BLOB 型の場合、比較は失敗します。  
  
-   **Ntext** データ内の特殊文字は、BLOB 型の比較の制限により、SQLXML 4.0 で問題が発生する可能性があります。 たとえば、アップデートグラムのブロックで "[Serializable]" を使用 **\<before>** すると、 **ntext** 型の列の同時実行制御チェックに失敗し、次の SQLOLEDB エラー説明が返されます。  
  
    ```  
    Empty update, no updatable rows found   Transaction aborted  
    ```  
  
## <a name="see-also"></a>参照  
 [SQLXML 4.0&#41;&#40;アップデートグラムのセキュリティに関する考慮事項 ](../../../relational-databases/sqlxml-annotated-xsd-schemas-xpath-queries/security/updategram-security-considerations-sqlxml-4-0.md)  
  
  
