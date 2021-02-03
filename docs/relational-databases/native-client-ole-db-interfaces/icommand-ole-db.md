---
description: ICommand (Native Client OLE DB プロバイダー)
title: ICommand (Native Client OLE DB プロバイダー) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- ICommand [SQL Server Native Client]
ms.assetid: 5e24b3a0-0658-44fc-b653-f4c52f9eb328
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f261d8fa00650be277f145c43c13af0ab7c03fa9
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97483193"
---
# <a name="icommand-native-client-ole-db-provider"></a>ICommand (Native Client OLE DB プロバイダー)
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  このトピックでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client に固有の OLE DB の動作について説明します。  
  
## <a name="icommandexecute"></a>ICommand::Execute  
 列のサイズより大きなデータを挿入すると、通常はエラーが発生します。 ただし、S_OK が返され、*dwStatus* が DBSTATUS_S_TRUNCATED に設定される場合もあります。 これは通常、パラメーターを使用してデータを挿入するときに、列のサイズがデータを保持するのに十分ではなく、 **ICommandWithParameters:: SetParameterInfo** が呼び出されていない場合に発生します。  
  
## <a name="see-also"></a>参照  
 [インターフェイス &#40;OLE DB&#41;](./sql-server-native-client-ole-db-interfaces.md)  
  
