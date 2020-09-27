---
title: クラス ファイルから Java jar ファイルを作成する
description: SQL Server 言語拡張を使用して Java コードを実行するとき、クラス ファイルを jar ファイルにパッケージ化します。
author: dphansen
ms.author: davidph
ms.date: 11/05/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: language-extensions
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: c324fca6893af35cafe6e9f65eccd0e8a0e478f9
ms.sourcegitcommit: 9b41725d6db9957dd7928a3620fe4db41eb51c6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88180284"
---
# <a name="create-a-java-jar-file-from-class-files"></a>クラス ファイルから Java jar ファイルを作成する
[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

[SQL Server 言語拡張](../language-extensions-overview.md)を使用して Java コードを実行するとき、クラス ファイルを jar ファイルにパッケージ化する方法について説明します。 ファイルをパッケージ化することをお勧めします。

## <a name="create-a-jar-file"></a>jar ファイルを作成する

クラス ファイルから jar を作成するには、クラス ファイルが格納されているフォルダーに移動し、次のコマンドを実行します。

```cmd
jar -cf <MyJar.jar> *.class
```

`jar.exe` のパスがシステム パス変数の一部であることを確認します。 または、JDK フォルダーの `/bin` 以下にある jar の完全なパスを指定します。 次に例を示します。

```cmd
C:\Users\MyUser\Desktop\jdk1.8.0_201\bin\jar -cf <MyJar.jar> *.class
```

## <a name="next-steps"></a>次のステップ

+ [SQL Server 言語拡張で Java ランタイムを呼び出す方法](../how-to/call-java-from-sql.md)