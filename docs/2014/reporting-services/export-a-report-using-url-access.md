---
title: URL アクセスを使用してレポートをエクスポートする | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- reporting-services-native
ms.topic: conceptual
helpviewer_keywords:
- formats [Reporting Services], URL rendering
- URL access [Reporting Services], rendering formats
ms.assetid: 6a3b7fc3-3d91-4d12-8371-42ea12e74517
author: markingmyname
ms.author: maghan
manager: craigg
ms.openlocfilehash: b90f1ad3e67aafa2ee8b967f8a04ddb16daf4e53
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48132909"
---
# <a name="export-a-report-using-url-access"></a>URL アクセスを使用してレポートをエクスポートする
  使用してレポートを表示する形式を指定することができます必要に応じて、 *Rs:format*パラメーター。 たとえば、ネイティブ モードのレポート サーバーから直接レポートの PDF コピーを取得するには、次の URL を使用します。  
  
```  
http://myrshost/ReportServer?/myreport&rs:Format=PDF  
```  
  
 また、SharePoint 統合モードのレポート サーバーから取得するには、次の URL を使用します。  
  
```  
http://myspsite/subsite/_vti_bin/reportserver?http://myspsite/subsite/myrereport.rdl&rs:Format=PDF  
```  
  
 このパラメーターの有効値は、アクセス先レポート サーバーにインストールされているレポート表示拡張機能によって異なります。 一般的な拡張機能は HTML4.0、MHTML、IMAGE、EXCEL、WORD、CSV、PDF、XML、および NULL です。 指定した表示拡張機能がレポート サーバーにインストールされていない場合は、レポートが表示されず、エラーが生成されてブラウザーに表示されます。  
  
 *Format* パラメーターを URL の一部として含めない場合は、レポート サーバーがブラウザーを検出し、適切な HTML 形式でレポートを表示します。  
  
## <a name="see-also"></a>参照  
 [URL アクセス&#40;SSRS&#41;](url-access-ssrs.md)   
 [URL アクセス パラメーター リファレンス](url-access-parameter-reference.md)  
  
  
