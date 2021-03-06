---
title: AffectEnum |Microsoft Docs
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.topic: conceptual
apitype: COM
f1_keywords:
- AffectEnum
helpviewer_keywords:
- AffectEnum enumeration [ADO]
ms.assetid: 1ab921a0-6c57-43b4-9291-701b2599f3e8
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: 3a26d1f9b1e8ff7cf9ed127dc78a02827952d411
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47772200"
---
# <a name="affectenum"></a>AffectEnum
操作の対象となるレコードを指定します。  
  
|定数|値|説明|  
|--------------|-----------|-----------------|  
|**adAffectAll**|3|ない場合、[フィルター](../../../ado/reference/ado-api/filter-property.md)に適用される、 **Recordset**、すべてのレコードに影響を与えます。<br /><br /> 場合、**フィルター**プロパティが文字列の条件 (など"作成者 'Smith' を =")、操作が現在」の章で表示されるレコードに影響し、します。<br /><br /> 場合、**フィルター**プロパティのメンバーに設定されて、 [FilterGroupEnum](../../../ado/reference/ado-api/filtergroupenum.md)またはブックマーク、操作の配列はのすべての行に影響を与える、 **Recordset**します。 **注:****adAffectAll** Visual Basic のオブジェクト ブラウザーには表示されません。  |  
|**呼び出します**|4|すべての兄弟章のすべてのレコードに影響を与える、**レコード セット**、いずれかを使用して非表示のものも含め**フィルター**現在適用されています。|  
|**adAffectCurrent**|1|現在のレコードのみに影響します。|  
|**adAffectGroup**|2|現在のレコードのみに影響を与えます[フィルター](../../../ado/reference/ado-api/filter-property.md)プロパティの設定。 設定する必要があります、**フィルター**プロパティを**FilterGroupEnum**値または配列の**ブックマーク**このオプションを使用します。|  
  
## <a name="adowfc-equivalent"></a>ADO と WFC と同等  
 パッケージ: **com.ms.wfc.data**  
  
|定数|  
|--------------|  
|AdoEnums.Affect.ALL|  
|AdoEnums.Affect.ALLCHAPTERS|  
|AdoEnums.Affect.CURRENT|  
|AdoEnums.Affect.GROUP|  
  
## <a name="applies-to"></a>適用対象  
  
|||  
|-|-|  
|[CancelBatch メソッド (ADO)](../../../ado/reference/ado-api/cancelbatch-method-ado.md)|[Delete メソッド (ADO Recordset)](../../../ado/reference/ado-api/delete-method-ado-recordset.md)|  
|[Resync メソッド](../../../ado/reference/ado-api/resync-method.md)|[UpdateBatch メソッド](../../../ado/reference/ado-api/updatebatch-method.md)|
