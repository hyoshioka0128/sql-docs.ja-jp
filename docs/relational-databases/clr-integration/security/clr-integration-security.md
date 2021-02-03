---
title: CLR 統合のセキュリティ |Microsoft Docs
description: SQL Server .NET Framework CLR セキュリティとの統合により、オブジェクト間のアクセスが管理されます。 オブジェクトに対して実行されるセキュリティチェックは、関連する呼び出しによって異なります。
ms.custom: ''
ms.date: 07/22/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: clr
ms.topic: reference
helpviewer_keywords:
- security [CLR integration]
- authorization [CLR integration]
- common language runtime [SQL Server], security
- database objects [CLR integration], security
ms.assetid: 05d7a471-c5d5-4730-b903-e4edc8157bb4
author: rothja
ms.author: jroth
ms.openlocfilehash: 7774d83d959bccea3a1de1d7d0a06660d52c191b
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641909"
---
# <a name="clr-integration-security"></a>CLR 統合のセキュリティ

[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] CLR (共通言語ランタイム) と [!INCLUDE[dnprdnshort](../../../includes/dnprdnshort-md.md)] を統合したセキュリティ モデルは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 内で使用されるさまざまな種類の CLR オブジェクトと CLR 以外のオブジェクトとの間のアクセスを管理し、セキュリティで保護します。 これらのオブジェクトは、[!INCLUDE[tsql](../../../includes/tsql-md.md)] ステートメントやサーバー内で使用される別の CLR オブジェクトから呼び出すことができます。 オブジェクト間の呼び出しをリンクと呼びます。 このようなオブジェクトに対して実行されるセキュリティ チェックの種類は、関連するリンクの種類によって異なります。  
  
 CLR 統合のセキュリティ モデルは、次のことを目標にしています。  
  
-   既定で、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に対するマネージド ユーザー コードの実行によって、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の整合性や安定性が損なわれることのないようにする。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の堅牢性を侵害する可能性のある操作の実行は、レベルの高い適切な権限によって保護されるようにする。  
  
-   マネージド ユーザー コードは、データベース内のユーザー データや他のユーザー コードに対して、未承認のアクセスを行わない。 ユーザー定義コードは、そのコードを呼び出したユーザー セッションのセキュリティ コンテキストで実行する。実行には、そのセキュリティ コンテキストにおける適切な特権を使用する。  
  
-   ユーザー コードからサーバー外部のリソースへのアクセスを禁止するための制御機能を備える。ユーザー コードの使用は、ローカル データのアクセスおよびコンピューティングに限定する。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] プロセスで実行することによって、ユーザー定義コードがシステム リソースへの未承認のアクセス手段を獲得してはならない。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のユーザーベースのセキュリティ モデルと CLR のコード アクセスベースのセキュリティ モデルが統合されました。 このセクションでは、このようにセキュリティ モデルを組み合わせたアプローチによるメリットの一部を紹介します。  
  
 次の表に、このセクションの各トピックの一覧を示します。  
  
 [CLR 統合のコード アクセス セキュリティ](../../../relational-databases/clr-integration/security/clr-integration-code-access-security.md)  
 マネージド コードのコード アクセス セキュリティ (CAS) モデルについて説明します。  
  
 [ホスト保護属性と CLR 統合プログラミング](../../../relational-databases/clr-integration-security-host-protection-attributes/host-protection-attributes-and-clr-integration-programming.md)  
 SAFE アセンブリと EXTERNAL_ACCESS アセンブリで許可されていないホスト保護属性 (HPA) の値に関する情報を提供します。  
  
 [CLR 統合のセキュリティのリンク]()  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 内のユーザー コードが相互に呼び出すしくみを説明します。  
  
 [権限借用と CLR 統合のセキュリティ](../data-access/impersonation-and-credentials-for-connections.md)  
 権限借用を使用してマネージド コードが外部リソースにアクセスするしくみを説明します。  
  
 マネージド メソッドが別のアセンブリに含まれるクラスのメソッドを起動する際に生じる問題について説明します。  
  
 [アプリケーション ドメインと CLR 統合のセキュリティ](/previous-versions/sql/2014/database-engine/dev-guide/allowing-partially-trusted-callers?view=sql-server-2014&preserve-view=true)  
 アセンブリがアプリケーション ドメインに読み込まれるしくみを説明します。  
  
## <a name="see-also"></a>参照  
 [CLR 統合アセンブリの管理](../../../relational-databases/clr-integration/assemblies/managing-clr-integration-assemblies.md)  
  
