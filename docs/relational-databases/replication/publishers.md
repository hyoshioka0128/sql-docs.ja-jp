---
description: ディストリビューターのプロパティ
title: パブリッシャー | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
f1_keywords:
- sql13.rep.configuredistributionwizard.enablepublishers.f1
ms.assetid: 116cd6a5-32ac-4273-81a2-d184408e0f07
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: 6aa802cc14b494a3892fea71d2ddbc99f42c8749
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97479833"
---
# <a name="publishers"></a>ディストリビューターのプロパティ
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]
  他のパブリッシャーにこのディストリビューターの使用許可を与えることができます。 パブリッシャーがサーバーをパブリッシャーのリモート ディストリビューターとして使用しても、そのサーバーがパブリッシャーにならないことに注意してください。 パブリッシャーに接続し、パブリッシュするための構成を行って、このサーバーをディストリビューターとして選択する必要があります。 パブリケーションの新規作成ウィザードを使用して、パブリッシャーを構成し、ディストリビューターを選択できます。  
  
 パブリッシャーとして選択したサーバーでは、このウィザードの **[ディストリビューション データベース]** ページで指定したディストリビューション データベースが使用されます。 他のディストリビューション データベースを使用する場合には、この時点でパブリッシャーを有効にしないでください。 代わりに、ディストリビューションの構成ウィザードの終了後に、 **[ディストリビューターのプロパティ]** ダイアログ ボックスを使用してパブリッシャーを追加します。  
  
## <a name="options"></a>Options  
 **[パブリッシャー]**  
 このディストリビューターの使用を許可するサーバーを選択します。 その他のプロパティを表示し、設定するには、[パブリッシャー] の横にあるプロパティ ボタン (**[...]**) をクリックします。  
  
 **追加**  
 許可するサーバーが一覧に表示されていない場合には、**[追加]** をクリックして、使用できるパブリッシャーの一覧に [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] パブリッシャーまたは Oracle パブリッシャーを追加します。  
  
## <a name="see-also"></a>参照  
 [[ディストリビューションの構成]](../../relational-databases/replication/configure-distribution.md)   
 [パブリッシングとディストリビューションの構成](../../relational-databases/replication/configure-publishing-and-distribution.md)   
 [View and Modify Distributor and Publisher Properties (ディストリビューターとパブリッシャーのプロパティの表示および変更)](../../relational-databases/replication/view-and-modify-distributor-and-publisher-properties.md)   
 [パブリケーションを作成する](../../relational-databases/replication/publish/create-a-publication.md)  
  
  
