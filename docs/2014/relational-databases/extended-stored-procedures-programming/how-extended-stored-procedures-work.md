---
title: 拡張ストアド プロシージャのしくみ |Microsoft Docs
ms.custom: ''
ms.date: 03/09/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- database-engine
- docset-sql-devref
ms.topic: reference
helpviewer_keywords:
- extended stored procedures [SQL Server], about extended stored procedures
ms.assetid: 6e946d8c-3268-4b59-8a1c-1637909cd701
author: rothja
ms.author: jroth
manager: craigg
ms.openlocfilehash: 7ceb2fd3c9069d762d5e0a317a256fcd92dbe186
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48184742"
---
# <a name="how-extended-stored-procedures-work"></a>拡張ストアド プロシージャのしくみ
    
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 代わりに CLR Integration を使用してください。  
  
 拡張ストアド プロシージャが動作するプロセスは次のとおりです。  
  
1.  表形式データ ストリーム (TDS) または、クライアント アプリケーションからの簡易オブジェクト アクセス プロトコル (SOAP) 形式で要求を送信するクライアントは、拡張ストアド プロシージャを実行するとき[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]します。  
  
2.  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は拡張ストアド プロシージャに関連付けられた DLL を検索し、対象の DLL がまだロードされていない場合はロードします。  
  
3.  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、要求されている拡張ストアド プロシージャ (DLL 内に関数として実装されている) を呼び出します。  
  
4.  拡張ストアド プロシージャは拡張ストアド プロシージャ API を介してサーバーに結果セットを渡し、パラメーターを返します。  
  
## <a name="see-also"></a>参照  
 [データベース エンジン拡張ストアド プロシージャ プログラミング](../database-engine-extended-stored-procedure-programming.md)  
  
  
