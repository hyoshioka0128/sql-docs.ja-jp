---
title: システム構成チェッカーの検査パラメーター | Microsoft Docs
ms.custom: ''
ms.date: 09/05/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- installing SQL Server, system configuration checks
- failed system configuration checks [SQL Server]
- verifying configuration before installation
- SCC [SQL Server]
- system configuration checker
- scanning computer before installation [SQL Server]
- checking configuration before installation
- status information [SQL Server], system configuration checker
- parameters [SQL Server], system configuration checker
- configuration checkers [SQL Server]
- Setup [SQL Server], system configuration checker
ms.assetid: 8e712c15-6bfa-4d71-b303-9526101e5594
author: MashaMSFT
ms.author: mathoma
monikerRange: '>=sql-server-2016||=sqlallproducts-allversions'
manager: craigg
ms.openlocfilehash: 869461d6054d3db263f96604c16bae39b63293a5
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47622080"
---
# <a name="check-parameters-for-the-system-configuration-checker"></a>システム構成チェッカーの検査パラメーター

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セットアップの実行中、システム構成チェッカー (SCC) は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインストール先コンピューターをスキャンします。 SCC は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インストールの成功を妨げる条件がないかどうかを調べます。 セットアップで [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] インストール ウィザードが起動する前に、SCC は各項目の状態を取得します。 次に、必要な条件と取得した結果を比較し、ブロックの問題解決に関するガイダンスを提供します。  
  
システム構成チェッカーは、実行された各ルールの簡単な記述と、実行ステータスを含むレポートを生成します。 システム構成チェッカーのレポートは %programfiles%\Microsoft SQL Server\140\Setup Bootstrap\Log\\\<YYYYMMDD_HHMM>\\\. に配置されます。    
  
詳細については、次のリンクを参照してください。

- [SQL Server のインストールに必要なハードウェアおよびソフトウェア](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)   
- [SQL Server インストールにおけるセキュリティの考慮事項](../../sql-server/install/security-considerations-for-a-sql-server-installation.md)   
- [サポートされているバージョンとエディションのアップグレード](../../database-engine/install-windows/supported-version-and-edition-upgrades.md)  
  
  
