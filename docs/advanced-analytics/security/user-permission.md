---
title: R および Python スクリプトの実行に対するデータベースアクセス許可の付与
description: SQL Server Machine Learning Services で R および Python スクリプトの実行に対するデータベースユーザーのアクセス許可を付与する方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/17/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 97e1fb6e2415e30f595d61dffa8a4952cfdc42d0
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715564"
---
# <a name="give-users-permission-to-sql-server-machine-learning-services"></a>SQL Server するためのアクセス許可をユーザーに付与する Machine Learning Services
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server Machine Learning Services で外部スクリプトを実行する権限をユーザーに付与し、データベースに対する読み取り、書き込み、またはデータ定義言語 (DDL) 権限を付与する方法について説明します。

詳細については、「[拡張機能フレームワークのセキュリティの概要](../../advanced-analytics/concepts/security.md#permissions)」の「アクセス許可」セクションを参照してください。

<a name="permissions-external-script"></a>

## <a name="permission-to-run-scripts"></a>スクリプトを実行する権限

自分でインストール[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]し、独自のインスタンスで R または Python スクリプトを実行する場合は、通常、管理者としてスクリプトを実行します。 そのため、データベース内のさまざまな操作とすべてのデータに対して暗黙的な権限を持っています。

ただし、ほとんどのユーザーには、このような昇格されたアクセス許可はありません。 たとえば、SQL ログインを使用してデータベースにアクセスする組織内のユーザーには、通常、昇格された権限がありません。 そのため、R または Python を使用しているユーザーごとに、言語が使用されている各データベースで外部スクリプトを実行する権限を Machine Learning Services のユーザーに付与する必要があります。 次の手順に従います。

```sql
USE <database_name>
GO
GRANT EXECUTE ANY EXTERNAL SCRIPT TO [UserName]
```

> [!NOTE]
> アクセス許可は、サポートされているスクリプト言語に固有のものではありません。 言い換えると、R スクリプトと Python スクリプトには別個のアクセス許可レベルがありません。 これらの言語に対して個別のアクセス許可を保持する必要がある場合は、R と Python を別々のインスタンスにインストールします。

<a name="permissions-db"></a> 

## <a name="grant-databases-permissions"></a>データベースの権限の許可

ユーザーがスクリプトを実行している間、ユーザーは他のデータベースからデータを読み取ることが必要になる場合があります。 ユーザーは、結果を格納したり、テーブルにデータを書き込んだりするために、新しいテーブルを作成することが必要になる場合もあります。

R または Python スクリプトを実行している Windows ユーザーアカウントまたは SQL ログインごとに、特定のデータベースに対する適切なアクセス`db_datareader`許可を持って`db_datawriter`いることを確認します。データ`db_ddladmin`の読み取り、オブジェクトのデータベースへの保存、またはオブジェクトの作成を行います。たとえば、ストアドプロシージャや、トレーニングおよびシリアル化されたデータを含むテーブルなどです。

たとえば、次[!INCLUDE[tsql](../../includes/tsql-md.md)]のステートメントは、 *ML_Samples*データベースで t-sql クエリを実行する権限を sql ログイン*MySQLLogin*に付与します。 このステートメントを実行するには、SQL ログインがサーバーのセキュリティ コンテキストに既に存在している必要があります。

```sql
USE ML_Samples
GO
EXEC sp_addrolemember 'db_datareader', 'MySQLLogin'
```

## <a name="next-steps"></a>次の手順

各ロールに含まれる権限の詳細については、「[データベースレベルのロール](../../relational-databases/security/authentication-access/database-level-roles.md)」を参照してください。