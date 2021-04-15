---
description: SQLXML における DiffGram のガイドラインと制限
title: SQLXML における DiffGram のガイドラインと制限
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- DiffGrams [SQLXML], about DiffGrams
ms.assetid: cf8689c4-2a63-4d05-b202-21b5ff187d7f
author: rothja
ms.author: jroth
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 737cbcef2cead359c4f4cfbab0a9d06e58405826
ms.sourcegitcommit: 9142bb6b80ce22eeda516b543b163eb9918bc72e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107491862"
---
# <a name="guidelines-and-limitations-of-diffgrams-in-sqlxml"></a>SQLXML における DiffGram のガイドラインと制限
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]
  SQLXML 4.0 で DiffGram を使用するときには、次の点に注意してください。  
  
-   **Text/ntext** および image のようなバイナリラージオブジェクト (BLOB) 型は、diffgram を使用するときに、のブロックでは使用しない **\<diffgr:before>** でください。これには、同時実行制御で使用するためのものが含まれます。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] で、BLOB 型の比較の制限によって問題が発生する可能性があります。 たとえば、 **text** データ型の列を比較するには、WHERE 句で LIKE キーワードを使用します。ただし、データのサイズが8K を超える BLOB 型の場合、比較は失敗します。  
  
-   **Ntext** データ内の特殊文字は、BLOB 型の比較の制限により、SQLXML 4.0 で問題が発生する可能性があります。 たとえば、DiffGram のブロックで "[Serializable]" を使用 **\<diffgr:before>** すると、 **ntext** 型の列の同時実行制御チェックでは失敗し、次の SQLOLEDB エラー説明が返されます。  
  
    ```  
    Empty update, no updatable rows found   Transaction aborted  
    ```  
  
  
