---
title: 最適なパフォーマンスを実現するための max degree of parallelism オプションの設定 | Microsoft Docs
ms.custom: ''
ms.date: 07/18/2019
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- Best Practices [Database Engine]
ms.assetid: ec908006-67ae-4674-9a61-25ea741d6197
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 00123da615c420fa3d58daae9a287ce3c13b8329
ms.sourcegitcommit: 2efb0fa21ff8093384c1df21f0e8910db15ef931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68316664"
---
# <a name="set-the-max-degree-of-parallelism-option-for-optimal-performance"></a>最適なパフォーマンスを実現するための max degree of parallelism オプションの設定
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]
  このルールでは、値の max degree of parallelism (MAXDOP) オプションが 8 より大きいかどうかを確認します。 このオプションを大きな値に設定すると、多くの場合、不要なリソース消費やパフォーマンスの低下が発生します。  
  
## <a name="best-practices-recommendations"></a>ベスト プラクティスと推奨事項  
 sp_configure を使用して max degree of parallelism オプションを 8 以下に設定してください。  
  
## <a name="for-more-information"></a>詳細情報  
 [SQL Server の max degree of parallelism 構成オプションの推奨事項とガイドライン](https://go.microsoft.com/fwlink/?linkid=117786)  
  
 [max degree of parallelism サーバー構成オプションの構成](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)  
  
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)  
  
  
