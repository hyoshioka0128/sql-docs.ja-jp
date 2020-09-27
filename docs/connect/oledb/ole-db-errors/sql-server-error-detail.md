---
title: SQL Server エラーの詳細 (OLE DB ドライバー)
description: OLE DB Driver for SQL Server のプロバイダー固有のエラー インターフェイス ISQLServerErrorInfo について説明します。このインターフェイスは SQL Server エラーの詳細を返します。
ms.custom: ''
ms.date: 06/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: connectivity
ms.topic: reference
helpviewer_keywords:
- OLE DB Driver for SQL Server, errors
- errors [OLE DB], error details
- IErrorRecords interface
- IErrorInfo interface
- OLE DB error handling, error details
- ISQLServerErrorInfo interface
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: f532b124fa23a65a71480b4bcb1bf31272a3081e
ms.sourcegitcommit: c95f3ef5734dec753de09e07752a5d15884125e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88862226"
---
# <a name="sql-server-error-detail"></a>SQL Server エラーの詳細
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

[!INCLUDE[Driver_OLEDB_Download](../../../includes/driver_oledb_download.md)]

  OLE DB Driver for SQL Server では、プロバイダー固有のエラー インターフェイス [ISQLServerErrorInfo](https://docs.microsoft.com/sql/connect/oledb/ole-db-interfaces/isqlservererrorinfo-geterrorinfo-ole-db?view=sql-server-ver15) が定義されます。 このインターフェイスにより、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] エラーの詳細が返されるので、コマンドの実行や行セットの操作が失敗したときに役立ちます。  
  
 **ISQLServerErrorInfo** インターフェイスにアクセスする方法は 2 つあります。  
  
 次のコード サンプルに示すように、コンシューマーは **IErrorRecords::GetCustomerErrorObject** を呼び出して **ISQLServerErrorInfo** ポインターを取得できます。 (**ISQLErrorInfo** を取得する必要はありません)。**ISQLErrorInfo** と **ISQLServerErrorInfo** は、どちらもカスタム OLE DB エラー オブジェクトです。**ISQLServerErrorInfo** は、プロシージャ名や行番号などの詳細情報を含むサーバー エラーの情報を取得するために使用するインターフェイスです。  
  
```  
// Get the SQL Server custom error object.  
if(FAILED(hr=pIErrorRecords->GetCustomErrorObject(  
   nRec, IID_ISQLServerErrorInfo,  
   (IUnknown**)&pISQLServerErrorErrorInfo)))  
```  
  
 **ISQLServerErrorInfo** ポインターを取得するもう 1 つの方法は、既に取得した **ISQLErrorInfo** ポインターの **QueryInterface** メソッドを呼び出すことです。 ただし、**ISQLServerErrorInfo** には **ISQLErrorInfo** から取得できる情報のスーパーセットが含まれているので、**GetCustomerErrorObject** から直接 **ISQLServerErrorInfo** を取得する方が効果的です。  
  
 **ISQLServerErrorInfo** インターフェイスでは、メンバー関数 [ISQLServerErrorInfo::GetErrorInfo](../../oledb/ole-db-interfaces/isqlservererrorinfo-geterrorinfo-ole-db.md) が公開されます。 この関数では、SSERRORINFO 構造体へのポインターと文字列バッファーへのポインターが返されます。 どちらのポインターが参照するメモリも、コンシューマーが **IMalloc::Free** メソッドを使用して割り当て解除する必要があります。  
  
 コンシューマーでは SSERRORINFO 構造体のメンバーが次のように解釈されます。  
  
|メンバー|説明|  
|------------|-----------------|  
|*pwszMessage*|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] エラー メッセージです。 このエラー メッセージは、**IErrorInfo::GetDescription** で返される文字列と同じです。|  
|*pwszServer*|セッションの [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスの名前。|  
|*pwszProcedure*|該当する場合は、エラーが発生したプロシージャの名前です。 該当しない場合は空文字列です。|  
|*lNative*|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のネイティブ エラー番号です。 **ISQLErrorInfo::GetSQLInfo** の *plNativeError* パラメーターで返される値と同じです。|  
|*bState*|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] エラー メッセージの状態です。|  
|*bClass*|[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] エラー メッセージの重大度です。|  
|*wLineNumber*|該当する場合は、ストアド プロシージャでエラーが発生した行番号です。|  
  
## <a name="see-also"></a>参照  
 [エラー](../../oledb/ole-db-errors/errors.md)   
 [RAISERROR &#40;Transact-SQL&#41;](../../../t-sql/language-elements/raiserror-transact-sql.md)  
  
  
