---
title: MaxRecords プロパティの例 (VB) |Microsoft Docs
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.topic: conceptual
dev_langs:
- VB
helpviewer_keywords:
- MaxRecords property [ADO], Visual Basic example
ms.assetid: 630a3be4-7a87-41cf-997e-8bb50d89db1e
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: c687f2b7a11dba37c05412c03cf14da4a3daa543
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47684190"
---
# <a name="maxrecords-property-example-vb"></a>MaxRecords プロパティの例 (VB)
この例では、 [MaxRecords](../../../ado/reference/ado-api/maxrecords-property-ado.md)プロパティを開き、[レコード セット](../../../ado/reference/ado-api/recordset-object-ado.md)で最も高価な 10 のタイトルを含む、***タイトル***テーブル。  
  
```  
'BeginMaxRecordsVB  
  
    'To integrate this code  
    'replace the data source and initial catalog values  
    'in the connection string  
  
Public Sub Main()  
    On Error GoTo ErrorHandler  
  
    Dim rstTitles As ADODB.Recordset  
    Dim Cnxn As ADODB.Connection  
    Dim strCnxn As String  
    Dim strSQLTitles As String  
  
    ' Open a connection  
    Set Cnxn = New ADODB.Connection  
    strCnxn = "Provider='sqloledb';Data Source='MySqlServer';" & _  
        "Initial Catalog='Pubs';Integrated Security='SSPI';"  
    Cnxn.Open strCnxn  
  
    ' Open recordset containing the 10 most expensive  
    ' titles in the Titles table  
    Set rstTitles = New ADODB.Recordset  
    rstTitles.MaxRecords = 10  
  
    strSQLTitles = "SELECT Title, Price FROM Titles ORDER BY Price DESC"  
    rstTitles.Open strSQLTitles, strCnxn, adOpenStatic, adLockReadOnly, adCmdText  
  
    ' Display the contents of the recordset  
    Debug.Print "Top Ten Titles by Price:"  
  
    Do Until rstTitles.EOF  
        Debug.Print "  " & rstTitles!Title & " - " & rstTitles!Price  
        rstTitles.MoveNext  
    Loop  
  
    ' clean up  
    rstTitles.Close  
    Cnxn.Close  
    Set rstTitles = Nothing  
    Set Cnxn = Nothing  
    Exit Sub  
  
ErrorHandler:  
    ' clean up  
    If Not rstTitles Is Nothing Then  
        If rstTitles.State = adStateOpen Then rstTitles.Close  
    End If  
    Set rstTitles = Nothing  
  
    If Not Cnxn Is Nothing Then  
        If Cnxn.State = adStateOpen Then Cnxn.Close  
    End If  
    Set Cnxn = Nothing  
  
    If Err <> 0 Then  
        MsgBox Err.Source & "-->" & Err.Description, , "Error"  
    End If  
End Sub  
'EndMaxRecordsVB  
```  
  
## <a name="see-also"></a>参照  
 [MaxRecords プロパティ (ADO)](../../../ado/reference/ado-api/maxrecords-property-ado.md)   
 [Recordset オブジェクト (ADO)](../../../ado/reference/ado-api/recordset-object-ado.md)
