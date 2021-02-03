---
description: sys.external_libraries (Transact-SQL)
title: sys.external_libraries (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/25/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: machine-learning
ms.topic: reference
f1_keywords:
- external_libraries
- external_libraries_TSQL
- sys.external_libraries
- sys.external_libraries_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.external_libraries catalog view
author: dphansen
ms.author: davidph
manager: cgronlun
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 8c092f44618b07cad75d4c6be0d3aa42f22145b6
ms.sourcegitcommit: 33f0f190f962059826e002be165a2bef4f9e350c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99191555"
---
# <a name="sysexternal_libraries-transact-sql"></a>sys.external_libraries (Transact-SQL)  
[!INCLUDE [SQL Server 2017 SQL MI](../../includes/applies-to-version/sqlserver2017-asdbmi.md)]

では、R、Python、Java などの外部ランタイムに関連するパッケージライブラリの管理がサポートされています。

> [!NOTE]
> SQL Server 2017 では、R 言語と Windows プラットフォームがサポートされています。 Windows および Linux プラットフォームの R、Python、Java は SQL Server 2019 以降でサポートされています。 Azure SQL Managed Instance では、R と Python がサポートされています。

## <a name="sysexternal_libraries"></a>sys.external_libraries

カタログビュー sys.external_libraries は、データベースにアップロードされた各外部ライブラリの行を一覧表示します。

|列名 |データ型 | 説明|
|------|------|------|
|external_library_id |INT | 外部ライブラリオブジェクトの ID。 |
|name |sysname |外部ライブラリの名前。 所有者ごとにデータベース内で一意です。|
|principal_id |INT |この外部ライブラリを所有するプリンシパルの ID。 |
|language | sysname | 外部ライブラリをサポートする言語またはランタイムの名前。 有効な値は、' R '、' Python '、および ' Java ' です。 今後、追加のランタイムが追加される可能性があります。|
|scope |INT |パブリックスコープの場合は0。プライベートスコープの場合は1 |  
|scope_desc |varchar (7) |パッケージがパブリックであるかプライベートであるかを示します|

## <a name="see-also"></a>関連項目  

+ [sys.external_library_files](sys-external-library-files-transact-sql.md)  
+ [外部ライブラリの作成](../../t-sql/statements/create-external-library-transact-sql.md)  
+ [新しい R パッケージのインストール](../../machine-learning/package-management/install-additional-r-packages-on-sql-server.md)  
+ [新しい Python パッケージのインストール](../../machine-learning/package-management/install-additional-python-packages-on-sql-server.md)  
