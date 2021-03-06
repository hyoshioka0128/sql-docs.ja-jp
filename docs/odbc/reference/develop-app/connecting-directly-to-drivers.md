---
title: ドライバーに直接接続する |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- connecting to driver [ODBC], SQLDriverConnect
- connecting to data source [ODBC], SqlDriverConnect
- SQLDriverConnect function [ODBC], connecting directly to drivers
- connecting to driver [ODBC], drivers
ms.assetid: f86e198f-a088-4401-9106-aa62a0eb8f6e
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: 3f5818d67659769ae104b3e98248c26f5b9fe8a7
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47803420"
---
# <a name="connecting-directly-to-drivers"></a>ドライバーに直接接続する
説明したとおり[データ ソースまたはドライバーを選択する](../../../odbc/reference/develop-app/choosing-a-data-source-or-driver.md)、このセクションで前述した一部のアプリケーションは、データ ソースをまったく使用しません。 代わりに、ドライバーに直接接続します。 **SQLDriverConnect**アプリケーションがデータ ソースを指定せずに、ドライバーに直接接続する方法を提供します。 概念的には、一時的なデータ ソースは、実行時に作成されます。  
  
 ドライバーに直接接続する場合、アプリケーションを指定します、**ドライバー**の代わりに、接続文字列キーワードで、 **DSN**キーワード。 値、**ドライバー**キーワードは、ドライバーの説明、によって返される**SQLDrivers**します。 たとえば、ドライバーは Paradox ドライバーの説明があるため、データ ファイルを含むディレクトリの名前が必要です。 このドライバーに接続するには、アプリケーションは次の接続文字列のいずれかで使用可能性があります。  
  
```  
DRIVER={Paradox Driver};Directory=C:\PARADOX;  
DRIVER={Paradox Driver};  
```  
  
 最初の文字列で、ドライバーはその他の情報を必要はありません。 2 番目の文字列で、ドライバーは、データ ファイルを含むディレクトリの名前を要求する必要があります。
