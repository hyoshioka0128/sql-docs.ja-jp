---
title: 参照変換エディター (詳細 ページ) |Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- integration-services
ms.topic: conceptual
f1_keywords:
- sql12.dts.designer.lookuptransformation.advanced.f1
helpviewer_keywords:
- Lookup Transformation Editor
ms.assetid: f3395c65-0320-47f9-8d83-daaa082d8713
author: douglaslms
ms.author: douglasl
manager: craigg
ms.openlocfilehash: aa56e9366fdc60a5b411e19b200706f0404e7312
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48209732"
---
# <a name="lookup-transformation-editor-advanced-page"></a>[参照変換エディター] ([詳細設定] ページ)
  **[参照変換エディター]** ダイアログ ボックスの **[詳細設定]** ページを使用して、部分キャッシュを構成し、参照変換用 SQL ステートメントを変更します。  
  
 参照変換の詳細については、「 [Lookup Transformation](data-flow/transformations/lookup-transformation.md)」を参照してください。  
  
## <a name="options"></a>および  
 **[キャッシュ サイズ (32 ビット)]**  
 32 ビット コンピューター用のキャッシュ サイズを MB 単位で調整します。 既定値は 5 MB です。  
  
 **[キャッシュ サイズ (64 ビット)]**  
 64 ビット コンピューター用のキャッシュ サイズを MB 単位で調整します。 既定値は 5 MB です。  
  
 **[エントリが一致しない行のキャッシュを有効にする]**  
 一致するエントリが参照データセットにない行をキャッシュします。  
  
 **[キャッシュからの割り当て]**  
 一致するエントリが参照データセットにない行に対して割り当てるキャッシュの割合を指定します。  
  
 **[SQL ステートメントを変更する]**  
 参照データセットを生成するために使用される SQL ステートメントを変更します。  
  
> [!NOTE]  
>  このページで指定するオプションの SQL ステートメントは、**[参照変換エディター]** の **[接続]** ページで指定したテーブル名をオーバーライドおよび置換します。 詳細については、次を参照してください。[参照変換エディター&#40;接続ページ&#41;](../../2014/integration-services/lookup-transformation-editor-connection-page.md)します。  
  
 **[パラメーターの設定]**  
 **[クエリ パラメーターの設定]** ダイアログ ボックスを使用して、入力列をパラメーターにマップします。  
  
## <a name="external-resources"></a>外部リソース  
 blogs.msdn.com のブログ「 [キャッシュ モードの参照](http://go.microsoft.com/fwlink/?LinkId=219518) 」  
  
## <a name="see-also"></a>参照  
 [参照変換エディター &#40;[全般] ページ&#41;](general-page-of-integration-services-designers-options.md)   
 [参照変換エディター&#40;接続 ページ&#41;](../../2014/integration-services/lookup-transformation-editor-connection-page.md)   
 [[参照変換エディター] &#40;[列] ページ&#41;](../../2014/integration-services/lookup-transformation-editor-columns-page.md)   
 [参照変換エディター&#40;エラー出力 ページ&#41;](../../2014/integration-services/lookup-transformation-editor-error-output-page.md)   
 [Integration Services のエラーおよびメッセージのリファレンス](../../2014/integration-services/integration-services-error-and-message-reference.md)   
 [あいまい参照変換](data-flow/transformations/fuzzy-lookup-transformation.md)  
  
  
