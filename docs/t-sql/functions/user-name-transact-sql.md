---
title: USER_NAME (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- USER_NAME
- USER_NAME_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- usernames [SQL Server]
- IDs [SQL Server], databases
- USER_NAME function
- users [SQL Server], database username
- names [SQL Server], database users
- identification numbers [SQL Server], databases
- database usernames [SQL Server]
ms.assetid: ab32d644-4228-449a-9ef0-5a975c305775
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e407ab5ac5e69c78d19a1022210ca8da5e470d64
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67927593"
---
# <a name="username-transact-sql"></a>USER_NAME (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-all-md](../../includes/tsql-appliesto-ss2008-all-md.md)]

  指定した ID 番号から、データベース ユーザー名を返します。  
  
 ![記事のリンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "記事のリンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
USER_NAME ( [ id ] )  
```  
  
## <a name="arguments"></a>引数  
 *id*  
 データベース ユーザーに関連付けられている識別番号を指定します。 *id* は **int** です。かっこが必要です。  
  
## <a name="return-types"></a>戻り値の型  
 **nvarchar (256)**  
  
## <a name="remarks"></a>Remarks  
 *id* を省略した場合は、現在のコンテキストの現在のユーザーであると見なされます。 パラメーターに "NULL" という語が含まれていると、NULL が返されます。 EXECUTE AS ステートメントの実行後に *id* を指定せずに USER_NAME を呼び出した場合、USER_NAME では権限を借用したユーザーの名前が返されます。 Windows プリンシパルがグループのメンバーシップを使ってデータベースにアクセスした場合、USER_NAME はグループではなく Windows プリンシパルの名前を返します。  
  
## <a name="examples"></a>使用例  
  
### <a name="a-using-username"></a>A. USER_NAME を使用する  
 次の例では、ユーザー ID `13` のユーザー名を返します。  
  
```  
SELECT USER_NAME(13);  
GO  
```  
  
### <a name="b-using-username-without-an-id"></a>B. ID を指定せずに USER_NAME を使用する  
 次の例では、ID を指定せずに、現在のユーザーの名前を検索します。  
  
```  
SELECT USER_NAME();  
GO  
```  
  
 次に、ユーザーが sysadmin 固定サーバー ロールのメンバーである場合の結果セットを示します。  
  
 ```
------------------------------  
dbo  
  
(1 row(s) affected)
```  
  
### <a name="c-using-username-in-the-where-clause"></a>C. WHERE 句で USER_NAME を使用する  
 次の例では、`sysusers` の行を検索します。検索される名前は、ユーザー識別番号 `USER_NAME` に対してシステム関数 `1` を適用した結果と同じになります。  
  
```  
SELECT name FROM sysusers WHERE name = USER_NAME(1);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
name  
------------------------------  
dbo  
  
(1 row(s) affected)
```  
  
### <a name="d-calling-username-during-impersonation-with-execute-as"></a>D. EXECUTE AS での権限借用中に USER_NAME を呼び出す  
 次の例では、権限借用中の `USER_NAME` の動作を示します。  
  
```  
SELECT USER_NAME();  
GO  
EXECUTE AS USER = 'Zelig';  
GO  
SELECT USER_NAME();  
GO  
REVERT;  
GO  
SELECT USER_NAME();  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
DBO  
Zelig  
DBO
```  
  
## <a name="examples-includesssdwfullincludessssdwfull-mdmd-and-includesspdwincludessspdw-mdmd"></a>例: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] および [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="e-using-username-without-an-id"></a>E. ID を指定せずに USER_NAME を使用する  
 次の例では、ID を指定せずに、現在のユーザーの名前を検索します。  
  
```  
SELECT USER_NAME();  
```  
  
 これは現在のログイン ユーザーの結果セットです。  
  
```  
------------------------------   
User7                              
```  
  
### <a name="f-using-username-in-the-where-clause"></a>F. WHERE 句で USER_NAME を使用する  
 次の例では、`sysusers` の行を検索します。検索される名前は、ユーザー識別番号 `USER_NAME` に対してシステム関数 `1` を適用した結果と同じになります。  
  
```  
SELECT name FROM sysusers WHERE name = USER_NAME(1);  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
name                             
------------------------------   
User7                              
```  
  
## <a name="see-also"></a>参照  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [CURRENT_TIMESTAMP &#40;Transact-SQL&#41;](../../t-sql/functions/current-timestamp-transact-sql.md)   
 [CURRENT_USER &#40;Transact-SQL&#41;](../../t-sql/functions/current-user-transact-sql.md)   
 [SESSION_USER &#40;Transact-SQL&#41;](../../t-sql/functions/session-user-transact-sql.md)   
 [システム関数 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-for-transact-sql.md)   
 [SYSTEM_USER &#40;Transact-SQL&#41;](../../t-sql/functions/system-user-transact-sql.md)  
  
