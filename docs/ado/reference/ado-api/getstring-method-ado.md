---
title: GetString メソッド (ADO) |Microsoft Docs
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.topic: conceptual
apitype: COM
f1_keywords:
- Recordset20::raw_GetString
- Recordset20::GetString
helpviewer_keywords:
- GetString method [ADO]
ms.assetid: 92452940-b2a7-456e-94fc-3780c71da33c
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: 1570918c423291b6c4fdd212fcb82f518dfb766e
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47708060"
---
# <a name="getstring-method-ado"></a>GetString メソッド (ADO)
返します、 [Recordset](../../../ado/reference/ado-api/recordset-object-ado.md)を文字列として。  
  
## <a name="syntax"></a>構文  
  
```  
  
Variant = recordset.GetString(StringFormat, NumRows, ColumnDelimiter, RowDelimiter, NullExpr)  
```  
  
## <a name="return-value"></a>戻り値  
 返します、 **Recordset**文字列値として**バリアント**(BSTR)。  
  
#### <a name="parameters"></a>パラメーター  
 *StringFormat*  
 A [StringFormatEnum](../../../ado/reference/ado-api/stringformatenum.md)値を指定する方法、 **Recordset**文字列に変換する必要があります。 *RowDelimiter*、 *ColumnDelimiter*、および*NullExpr*パラメーターのみを使用する*StringFormat*の**adClipString**します。  
  
 *NumRows*  
 任意。 変換する行の数、 **Recordset**します。 場合*NumRows*が指定されていない、または内の行の合計数より大きい場合、**レコード セット**、し、すべての行で、**レコード セット**変換されます。  
  
 *ColumnDelimiter*  
 任意。 それ以外の場合、タブ文字を指定した場合、列の間で使用する区切り記号。  
  
 *RowDelimiter*  
 任意。 それ以外の場合、復帰改行文字を指定した場合、行の間で使用される区切り記号。  
  
 *NullExpr*  
 任意。 それ以外の場合、空の文字列を指定した場合、null 値の代わりに使用される式。  
  
## <a name="remarks"></a>コメント  
 文字列には、行のデータが、スキーマ データが保存されます。 そのため、 **Recordset**この文字列を使用してを再度開くことはできません。  
  
 このメソッドは、RDO **GetClipString**メソッド。  
  
## <a name="applies-to"></a>適用対象  
 [Recordset オブジェクト (ADO)](../../../ado/reference/ado-api/recordset-object-ado.md)  
  
## <a name="see-also"></a>参照  
 [GetString メソッドの例 (VB)](../../../ado/reference/ado-api/getstring-method-example-vb.md)
