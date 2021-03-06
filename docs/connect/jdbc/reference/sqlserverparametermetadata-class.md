---
title: SQLServerParameterMetaData クラス |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 546290e0-9411-4a2b-aa36-61251e70e9cf
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: 6d24d63765f31f6c3218d8ed161af77298326ab7
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MTE75
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47797234"
---
# <a name="sqlserverparametermetadata-class"></a>SQLServerParameterMetaData クラス
[!INCLUDE[Driver_JDBC_Download](../../../includes/driver_jdbc_download.md)]

  準備されたステートメントのパラメーターのメタデータを表します。  
  
 **パッケージ:** com.microsoft.sqlserver.jdbc  
  
 **拡張:** java.lang.Object  
  
 **実装:** java.sql.ParameterMetaData  
  
## <a name="syntax"></a>構文  
  
```  
  
public class SQLServerParameterMetaData  
```  
  
## <a name="remarks"></a>Remarks  
 パラメーターのメタデータを取得するために、準備されたステートメントは SET FMT ONLY を指定して実行されます。 呼び出し可能ステートメントで sp_sproc_columns が呼び出され、プロシージャ パラメーターの名前とメタデータが取得されます。  
  
## <a name="see-also"></a>参照  
 [SQLServerParameterMetaData のメンバー](../../../connect/jdbc/reference/sqlserverparametermetadata-members.md)   
 [JDBC Driver API リファレンス](../../../connect/jdbc/reference/jdbc-driver-api-reference.md)  
  
  
