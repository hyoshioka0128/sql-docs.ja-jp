---
title: ALTER EXTERNAL DATA SOURCE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 01/09/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER EXTERNAL DATA SOURCE
- ALTER_EXTERNAL_DATA_SOURCE
dev_langs:
- TSQL
helpviewer_keywords:
- polybase, alter external data source statement
- ALTER EXTERNAL DATA SOURCE statement
ms.assetid: a34b9e90-199d-46d0-817a-a7e69387bf5f
author: CarlRabeler
ms.author: carlrab
ms.openlocfilehash: 25df03e48d08e09033b52e4b51c11d3ecc4db4ed
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68065651"
---
# <a name="alter-external-data-source-transact-sql"></a>ALTER EXTERNAL DATA SOURCE (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2016-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2016-xxxx-xxxx-xxx-md.md)]

  外部テーブルを作成するために使用する外部データ ソースを変更します。 外部データ ソースは、Hadoop または Azure BLOB ストレージ (WASB) にすることができます。
  
## <a name="syntax"></a>構文  
  
```  
-- Modify an external data source
-- Applies to: SQL Server (2016 or later)
ALTER EXTERNAL DATA SOURCE data_source_name SET
    {   
        LOCATION = 'server_name_or_IP' [,] |
        RESOURCE_MANAGER_LOCATION = <'IP address;Port'> [,] |
        CREDENTIAL = credential_name
    }  
    [;]  

-- Modify an external data source pointing to Azure Blob storage
-- Applies to: SQL Server (starting with 2017)
ALTER EXTERNAL DATA SOURCE data_source_name
    SET
        LOCATION = 'https://storage_account_name.blob.core.windows.net'
        [, CREDENTIAL = credential_name ] 
```  
  
## <a name="arguments"></a>引数  
 data_source_name: データ ソースのユーザー定義の名前を指定します。 名前は一意である必要があります。
  
 LOCATION = 'server_name_or_IP': サーバーまたは IP アドレスの名前を指定します。
  
 RESOURCE_MANAGER_LOCATION = '\<IP address;Port>': Hadoop リソース マネージャーの場所を指定します。 指定した場合、クエリ オプティマイザーは Hadoop の計算の機能を使用して、PolyBase クエリのデータを事前処理こともできます。 これは、コストベースの判断です。 述語のプッシュダウンが呼び出されると、この Hadoop と SQL の間で転送されるデータ量が大幅に減少し、クエリのパフォーマンスが向上します。
  
 CREDENTIAL = Credential_Name: 名前付きの資格情報を指定します。 「[CREATE DATABASE SCOPED CREDENTIAL &#40;Transact-SQL&#41;](../../t-sql/statements/create-database-scoped-credential-transact-sql.md)」を参照してください。

TYPE = BLOB_STORAGE   
**適用対象:** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)]」を参照してください。
一括操作の場合のみ、`LOCATION` は Azure BLOB ストレージの有効な URL にする必要があります。 **/** 、ファイル名、共有アクセス署名パラメーターを `LOCATION` URL の末尾に入れないでください。
使用される資格情報は、`SHARED ACCESS SIGNATURE` を使用して ID として作成する必要があります。 Shared Access Signature に関する詳細については、「[Shared Access Signature (SAS) を使用](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)」を参照してください。

  
  
## <a name="remarks"></a>Remarks
 一度に変更できるソースは 1 つだけです。 同じソースを変更する複数の要求が同時に発生すると、1 つのステートメントが待機状態になります。 ただし、さまざまなソースを同時に変更できます。 このステートメントは、他のステートメントと同時実行できます。
  
## <a name="permissions"></a>アクセス許可  
 ALTER ANY EXTERNAL DATA SOURCE 権限が必須です。
 > [!IMPORTANT]  
 >  ALTER ANY EXTERNAL DATA SOURCE 権限は、あらゆる外部データ ソース オブジェクトを作成し、変更する能力をプリンシパルに与えます。そのため、データベース上のすべてのデータベース スコープ資格情報にアクセスする能力も与えます。 この権限は特権として考える必要があります。したがって、システム内の信頼できるプリンシパルにのみ与える必要があります。

  
## <a name="examples"></a>使用例  
 次の例では、既存のデータソースの場所とリソース マネージャーの場所を変更します。
  
```  
ALTER EXTERNAL DATA SOURCE hadoop_eds SET
     LOCATION = 'hdfs://10.10.10.10:8020',
     RESOURCE_MANAGER_LOCATION = '10.10.10.10:8032'
    ;
  
```  

 次の例では、既存のデータ ソースに接続できるように資格情報を変更します。
  
```  
ALTER EXTERNAL DATA SOURCE hadoop_eds SET
   CREDENTIAL = new_hadoop_user
    ;
```
