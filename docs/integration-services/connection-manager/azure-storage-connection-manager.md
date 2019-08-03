---
title: Azure Storage 接続マネージャー | Microsoft Docs
ms.custom: ''
ms.date: 05/22/2019
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.dts.designer.afpstorageconn.f1
- sql14.dts.designer.afpstorageconn.f1
ms.assetid: 68bd1d04-d20f-4357-a34e-7c9c76457062
author: janinezhang
ms.author: janinez
ms.openlocfilehash: 8a115dafb386323bc1f4738720e7576657d22543
ms.sourcegitcommit: 2efb0fa21ff8093384c1df21f0e8910db15ef931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68316686"
---
# <a name="azure-storage-connection-manager"></a>Azure Storage 接続マネージャー

[!INCLUDE[ssis-appliesto](../../includes/ssis-appliesto-ssvrpluslinux-asdb-asdw-xxx.md)]

  **Azure Storage 接続マネージャー**により、SSIS パッケージは Azure Storage アカウントに接続できます。
   
 **Azure Storage 接続マネージャー**は、[SQL Server Integration Services (SSIS) Feature Pack for Azure](../../integration-services/azure-feature-pack-for-integration-services-ssis.md) のコンポーネントです。 
  
**[SSIS 接続マネージャーの追加]** ダイアログ ボックスで **[AzureStorage]** (AzureStorage) を選択し、 **[追加]** をクリックします。  
  
使用できるプロパティは次のとおりです。

- **サービス:** 接続先のストレージ サービスを指定します。
- **アカウント名:** ストレージ アカウント名を指定します。
- **認証:** 使用する認証方法を指定します。 **AccessKey** と **ServicePrincipal** の認証がサポートされています。
    - **AccessKey:** この認証方法には、**アカウント キー**を指定します。
    - **ServicePrincipal:** この認証方法には、サービス プリンシパルの**アプリケーション ID**、**アプリケーション キー**、**テナント ID** を指定します。
      **テスト接続**が機能するためには、サービス プリンシパルには少なくともストレージ アカウントに対する**ストレージ BLOB データ閲覧者**の役割を割り当てる必要があります。
      詳細については、[この](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)ページを参照してください。
- **環境:** ストレージ アカウントをホストするクラウド環境を指定します。
