---
title: '[ポリシーのインポート] ダイアログ ボックス | Microsoft Docs'
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- sql13.swb.dmf.importpolicy.f1
ms.assetid: 78ab5f6e-2f13-4788-937e-8892ef4e2345
author: VanMSFT
ms.author: vanto
manager: craigg
ms.openlocfilehash: cca73d0d0ad9574f592f529a26518c18a6332c77
ms.sourcegitcommit: ef6e3ec273b0521e7c79d5c2a4cb4dcba1744e67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2018
ms.locfileid: "51512487"
---
# <a name="import-policies-dialog-box"></a>[ポリシーのインポート] ダイアログ ボックス
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]
  このダイアログ ボックスを使用すると、XML ファイルとして保存された 1 つ以上のポリシー (およびその参照された条件) を [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] の現在のインスタンスにインポートできます。  
  
## <a name="options"></a>[変数]  
 **[インポートするファイル]**  
 XML ファイルからポリシーをインポートするには、ファイルのパスと名前を入力するか、参照ボタン (**[...]**) を使用します。  
  
 **[インポートされるアイテムと重複部分を置き換える]**  
 この [!INCLUDE[ssDE](../../includes/ssde-md.md)] インスタンスに同じ名前のポリシーまたは条件が既に存在する場合、その既存のポリシーまたは条件が上書きされます。 依存ポリシーを使用している条件は、依存ポリシーも上書きしない限り上書きできません。 このオプションを選択していない場合は、同じ条件式を使用している既存の条件によってエラーが発生することはありません。  
  
 **[ポリシーの状態]**  
 インポートしたポリシーの状態を選択します。  
  
-   **[インポート時にポリシーの状態を保存します]**  
  
-   **[インポート時にすべてのポリシーを有効にします]**  
  
-   **[インポート時にすべてのポリシーを無効にします]**  
  
## <a name="see-also"></a>参照  
 [ポリシー ベースの管理を使用したサーバーの管理](../../relational-databases/policy-based-management/administer-servers-by-using-policy-based-management.md)   
 [ポリシー ベースの管理ポリシーのインポート](../../relational-databases/policy-based-management/import-a-policy-based-management-policy.md)   
 [ポリシー ベースの管理ポリシーのエクスポート](../../relational-databases/policy-based-management/export-a-policy-based-management-policy.md)  
  
  
