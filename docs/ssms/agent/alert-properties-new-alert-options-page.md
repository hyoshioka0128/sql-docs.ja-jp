---
description: '[警告のプロパティ] - [新しい警告] ([オプション] ページ)'
title: '[警告のプロパティ] - [新しい警告] ([オプション] ページ)'
ms.custom: seo-lt-2019
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
f1_keywords:
- sql13.ag.alert.options.f1
ms.assetid: 6e4f41aa-832d-46ba-b6b5-cf1d3b15d33f
author: markingmyname
ms.author: maghan
ms.reviewer: ''
monikerRange: = azuresqldb-mi-current || >= sql-server-2016 || = sqlallproducts-allversions
ms.openlocfilehash: 590cf63cb3e49ace877807f121d74e3753ac17d7
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88372268"
---
# <a name="alert-properties---new-alert-options-page"></a>[警告のプロパティ] - [新しい警告] ([オプション] ページ)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

> [!IMPORTANT]  
> 現在、[Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) によって、すべてではありませんが、ほとんどの SQL Server エージェントの機能がサポートされています。 詳細については、[Azure SQL Managed Instance と SQL Server の T-SQL の相違点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)に関するページを参照してください。

このページを使用すると、[!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントの警告のオプションを表示および変更できます。  

## <a name="options"></a>オプション  
**電子メール**  
イベントからのエラー テキストがある場合は、それを電子メール通知に含めます。  
  
**ポケットベル**  
イベントからのエラー テキストがある場合は、それをポケットベル通知に含めます。  
  
**Net send**  
イベントからのエラー テキストがある場合は、それを Net Send 通知に含めます。  
  
**[送信する付加通知メッセージ]**  
通知メッセージに含める追加のテキストを入力します。  
  
**[応答の遅延]**  
反復的に発生するイベントの遅延を指定します。 イベントの中には、短時間に頻繁に発生するものがあります。 そのようなイベントに対しては、その発生を認識するだけで、すべてのイベントについて応答を返さないようにする場合があります。 このオプションは、タイムアウトを指定するために使用します。遅延を指定した場合、イベントに対して警告が返された後、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントは、イベントが遅延中に発生するかどうかに関係なく、再び応答するまでに指定の遅延を待ちます。  
  
**Minutes**  
遅延を分単位で指定します。 イベントが発生するごとに応答するには、0 分 0 秒を指定します。  
  
**Seconds**  
遅延を秒単位で指定します。 イベントが発生するごとに応答するには、0 分 0 秒を指定します。  
  
## <a name="see-also"></a>参照  
[警告](../../ssms/agent/alerts.md)  
  
