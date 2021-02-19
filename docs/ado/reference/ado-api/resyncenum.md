---
description: ResyncEnum
title: ResyncEnum |Microsoft Docs
ms.prod: sql
ms.prod_service: connectivity
ms.technology: ado
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.topic: reference
apitype: COM
f1_keywords:
- ResyncEnum
helpviewer_keywords:
- ResyncEnum enumeration [ADO]
ms.assetid: d3df2c90-e570-4c40-a79a-25b3448a009c
author: rothja
ms.author: jroth
ms.openlocfilehash: db02c56f55b577c9f74a42f43abc9a1957311c0d
ms.sourcegitcommit: 917df4ffd22e4a229af7dc481dcce3ebba0aa4d7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100040752"
---
# <a name="resyncenum"></a>ResyncEnum
再 [同期](./resync-method.md)の呼び出しによって基になる値を上書きするかどうかを指定します。  
  
|定数|値|説明|  
|--------------|-----------|-----------------|  
|**adResyncAllValues**|2|既定値。 データを上書きします。保留中の更新は取り消されます。|  
|**adResyncUnderlyingValues**|1|はデータを上書きしません。保留中の更新は取り消されません。|  
  
## <a name="adowfc-equivalent"></a>同等の ADO/WFC  
 パッケージ: **com. ms. wfc. データ**  
  
|定数|  
|--------------|  
|AdoEnums. ALLVALUES|  
|AdoEnums. UNDERLYINGVALUES|  
  
## <a name="applies-to"></a>適用対象  
 [Resync メソッド](./resync-method.md)