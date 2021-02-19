---
title: SQL Server 2017 Reporting Services で非推奨の機能 | Microsoft Docs
description: この記事では、SQL Server Reporting Services の次のバージョンで非推奨となる機能について説明します。
ms.date: 11/20/2019
ms.prod: reporting-services
ms.prod_service: reporting-services-native
ms.technology: reporting-services
ms.topic: conceptual
helpviewer_keywords:
- Reporting Services, backward compatibility
- deprecated features [Reporting Services]
- HTML OWC rendering extension [Reporting Services]
- Report Server Web service, endpoints
ms.assetid: 3876c01e-f81d-4cce-9104-5106a8c369e6
author: maggiesMSFT
ms.author: maggies
monikerRange: '>= sql-server-2017'
ms.openlocfilehash: 553432d2e79069da19eacdd193be9b3b3e7d498c
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425317"
---
# <a name="deprecated-features-in-sql-server-2017-reporting-services"></a>SQL Server 2017 Reporting Services で非推奨の機能

[!INCLUDE [ssrs-appliesto](../includes/ssrs-appliesto.md)] [!INCLUDE [ssrs-appliesto-2017](../includes/ssrs-appliesto-2017.md)] [!INCLUDE [ssrs-appliesto-not-pbirs](../includes/ssrs-appliesto-not-pbirs.md)]

ある機能が非推奨とされた場合、次のようになります。

- その機能は保守管理状態にあり、それ以外では利用されていません。 新しい変更は行われません。新しい機能との相互運用性に関する変更もありません。
- Microsoft は、アップグレードを容易にする目的で、今後のリリースから非推奨機能を外さないように努めます。 ただし、その機能が将来の技術革新を制限してしまうような場合は、Reporting Services からそれを永久的に外すことをまれに選択することがあります。
- 新しい開発作業に非推奨機能を使用することはお勧めしません。

## <a name="features-deprecated-in-the-next-version-of-sql-server"></a>SQL Server の次のバージョンで非推奨となっている機能

SQL Server の次のバージョンでは、以下の SQL Server 2017 Reporting Services 機能は非推奨となります。 新規の開発作業ではこれらの機能を使用しないようにし、現在これらの機能を使用しているアプリケーションはできるだけ早く修正してください。

> [!NOTE]
> この一覧は、SQL Server 2016 Reporting Services (13. x) の一覧と同じです。 SQL Server 2017 Reporting Services (14.x) で提供が終了または中止されることが新たに発表された機能はありません。


| **カテゴリ** | **非推奨の機能** | **代替** |
| --- | --- | --- |
| レポート サーバー | HTML 4.0 レンダラー | HTML 5 レンダラー |

## <a name="see-also"></a>参照

[SQL Server Reporting Services (SSRS) で廃止された機能](discontinued-functionality-to-sql-server-reporting-services-in-sql-server.md)

その他の質問 [Reporting Services のフォーラムに質問してみてください](https://go.microsoft.com/fwlink/?LinkId=620231)
