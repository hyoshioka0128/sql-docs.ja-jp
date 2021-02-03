---
description: sys.pdw_health_components (Transact-sql)
title: sys.pdw_health_components (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
ms.assetid: d5c7589b-09b0-4f12-ab84-feb3ec3fbaaa
author: ronortloff
ms.author: rortloff
monikerRange: '>= aps-pdw-2016'
ms.openlocfilehash: 84c5d002a357dd8780596d61d27b32088e659914
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97479013"
---
# <a name="syspdw_health_components-transact-sql"></a>sys.pdw_health_components (Transact-sql)
[!INCLUDE [pdw](../../includes/applies-to-version/pdw.md)]

  システムに存在するすべてのコンポーネントとデバイスに関する情報を格納します。 これには、ハードウェア、記憶装置、およびネットワークデバイスが含まれます。  
  
|列名|データ型|説明|Range|  
|-----------------|---------------|-----------------|-----------|  
|component_id|**int**|コンポーネントまたはデバイスの一意識別子。<br /><br /> このビューのキー。|NOT NULL|  
|group_id|**Int**|このコンポーネントが属する論理コンポーネントグループ。 「 [Sys.pdw_health_components (並列データウェアハウス)](../../relational-databases/system-catalog-views/sys-pdw-health-components-transact-sql.md)」を参照してください。|NOT NULL|  
|component_name|**nvarchar (255)**|コンポーネント名。|NOT NULL|  
  
## <a name="see-also"></a>参照  
 [Azure Synapse Analytics と Parallel Data Warehouse のカタログ ビュー](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)  
  
  
