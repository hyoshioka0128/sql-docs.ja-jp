---
description: MSSQL_ENG018456
title: MSSQL_ENG018456 | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
helpviewer_keywords:
- MSSQL_ENG018456 error
ms.assetid: 3daa8144-d81f-445a-b6c3-4bb3e9fd1526
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: 6eb620604dde5c83c45d1ac946b600fc987e2d9d
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97469093"
---
# <a name="mssql_eng018456"></a>MSSQL_ENG018456
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]
    
## <a name="message-details"></a>メッセージの詳細  
  
|属性|値|  
|-|-|  
|製品名|SQL Server|  
|イベント ID|18456|  
|イベント ソース|MSSQLSERVER|  
|コンポーネント|[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]|  
|シンボル名||  
|メッセージ テキスト|ユーザー '%.*ls'.%.\*ls はログインできませんでした|  
  
## <a name="explanation"></a>説明  
 ログインの試行が失敗するたびに、エラー MSSQL_ENG018456 が発生します。 エラー メッセージにアカウント **distributor_admin** が含まれている場合 ("ユーザー 'distributor_admin' はログインできませんでした。" となっている場合)、問題はレプリケーションによって使用されているアカウントに関するものです。 レプリケーションによってリモート サーバー **repl_distributor** が作成され、これによってディストリビューターとパブリッシャーの間の通信が可能になります。 ログイン **distributor_admin** は、このリモート サーバーに関連付けられており、有効なパスワードを持つ必要があります。  
  
## <a name="user-action"></a>ユーザーの操作  
 このアカウントに対するパスワードを指定したことを確認します。 詳細については、「[ディストリビューターのセキュリティ保護](../../relational-databases/replication/security/secure-the-distributor.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [エラーとイベントのリファレンス &#40;レプリケーション&#41;](../../relational-databases/replication/errors-and-events-reference-replication.md)  
  
  
