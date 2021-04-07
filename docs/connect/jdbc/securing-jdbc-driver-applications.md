---
title: JDBC ドライバー アプリケーションのセキュリティ保護
description: この記事では、接続文字列、ユーザー入力の検証、全般的なアプリケーション セキュリティなどを含む、一般的なセキュリティの問題について説明します。
ms.custom: ''
ms.date: 03/31/2021
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: 90724ec6-a9cb-43ef-903e-793f89410bc0
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 29bb22bccceab099c648cb07a2ca3199f5846f58
ms.sourcegitcommit: ebe81e2daa544f41c8ababb66a91c218ad0c2a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106177097"
---
# <a name="securing-jdbc-driver-applications"></a>JDBC ドライバー アプリケーションのセキュリティ保護

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

[!INCLUDE[jdbcNoVersion](../../includes/jdbcnoversion_md.md)] アプリケーションのセキュリティを強化することは重要です。 一般的なコーディングの落とし穴を避けることだけがセキュリティではありません。 データにアクセスするアプリケーションには、攻撃者に悪用される可能性がある潜在的な障害点が多数あります。 セキュリティ エラーにより、攻撃者は機密データを取得、操作、または破壊する可能性があります。 アプリケーション セキュリティのあらゆる側面を理解することが重要です。 設計段階での脅威モデリングのプロセスから、最終的な展開、そして継続的なメンテナンスまでが対象です。

このセクションの記事では、接続文字列、ユーザー入力の検証、全般的なアプリケーション セキュリティなどを含む、一般的なセキュリティの問題について説明します。

## <a name="in-this-section"></a>このセクションの内容

| [アーティクル] | 説明 |
| ------- | ----------- |
| [接続文字列のセキュリティ保護](securing-connection-strings.md) | データ ソースへの接続に使用される情報を保護する方法について説明します。 |
| [ユーザー入力の検証](validating-user-input.md) | ユーザー入力を検証する方法について説明します。 |
| [アプリケーションのセキュリティ](application-security.md) | Java のポリシー アクセス許可を使用して、JDBC ドライバー アプリケーションをセキュリティで保護する方法について説明します。 |
| [暗号化の使用](using-ssl-encryption.md) | TLS (トランスポート層セキュリティ) (以前の SSL (Secure Sockets Layer)) を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースとの通信チャネルを確立する方法について説明します。 |
| [FIPS モード](fips-mode.md) | FIPS 準拠モードで JDBC ドライバーを使用する方法について説明します。 |
  
## <a name="see-also"></a>関連項目

[JDBC ドライバーの概要](overview-of-the-jdbc-driver.md)
