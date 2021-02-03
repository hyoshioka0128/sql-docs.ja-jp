---
title: レプリケーションの旧バージョンとの互換性 | Microsoft Docs
description: レプリケーションの旧バージョンとの互換性のために、アップグレーを行う前や、レプリケーション トポロジに複数のバージョンの SQL Server がある場合は、以下のリソースをご確認ください。
ms.custom: ''
ms.date: 03/02/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
helpviewer_keywords:
- transactional replication, backward compatibility
- backward compatibility [SQL Server replication]
- merge replication backward compatibility [SQL Server replication]
- replication [SQL Server], backward compatibility
- backward compatibility [SQL Server], replication
- snapshot replication [SQL Server], backward compatibility
- compatibility [SQL Server replication]
ms.assetid: 091c51dc-8b32-4b4f-847e-b317456c8394
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: a48ca05acd680cfd89f4438a64cac83c5d00dd0f
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97468823"
---
# <a name="replication-backward-compatibility"></a>レプリケーションの旧バージョンとの互換性
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

アップグレードを行う場合や、レプリケーション トポロジ内に複数のバージョンの SQL Server が存在する場合は、旧バージョンとの互換性を理解することが重要です。 

一般的なルールは次のとおりです: 

-   ディストリビューターは、パブリッシャーのバージョン以上であればどのバージョンでも使用できます (多くの場合、ディストリビューターはパブリッシャーと同じインスタンスです)。    
-   パブリッシャーは、ディストリビューターのバージョン以下であればどのバージョンでも使用できます。    
-   サブスクライバーのバージョンは、次のように、パブリケーションの種類によって異なります。    
    - トランザクション パブリケーションのサブスクライバーは、2 つのパブリッシャー バージョンのうちどちらでも使用できます。 例: SQL Server 2012 (11.x) パブリッシャーでは SQL Server 2014 (12.x) および SQL Server 2016 (13.x) のサブスクライバーを使用でき、SQL Server 2016 (13.x) パブリッシャーでは SQL Server 2014 (12.x) および SQL Server 2012 (11.x) のサブスクライバーを使用できます。     
    - マージ パブリケーションに対するサブスクライバーでは、バージョンのライフ サイクル サポート サイクルに従ってサポートされているパブリッシャー バージョンと等しいかそれより前のすべてのバージョンを使用できます。  


## <a name="replication-matrix"></a>レプリケーションのマトリックス
[!INCLUDE[repl matrix](../../includes/replication-compat-matrix.md)]


## <a name="additional-resources"></a>その他のリソース
 [SQL Server レプリケーションの非推奨の機能](../../relational-databases/replication/deprecated-features-in-sql-server-replication.md)  
 旧バージョンとの互換性を維持するため、[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] では引き続き使用できるが、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の今後のバージョンでは削除される予定のレプリケーション機能。  
  
 [SQL Server レプリケーションにおける重大な変更](../../relational-databases/replication/breaking-changes-in-sql-server-replication.md)  
 場合によっては、アプリケーションの修正が必要となるレプリケーション機能の変更 

 [レプリケートされたデータベースのアップグレード](../../database-engine/install-windows/upgrade-replicated-databases.md)  
 レプリケーション トポロジに参加している SQL サーバーをアップグレードする際の手順および考慮事項。 
  
  
