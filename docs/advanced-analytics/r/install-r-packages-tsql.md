---
title: T-sql (EXTERNAL LIBRARY の作成) を使用して R パッケージをインストールする
description: SQL Server 2016 R Services または SQL Server Machine Learning Services (データベース内) に新しい R パッケージを追加します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/12/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 1acaae39d71abd1cbd68781c0edec76308b85760
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715734"
---
# <a name="use-t-sql-create-external-library-to-install-r-packages-on-sql-server"></a>T-sql (CREATE EXTERNAL LIBRARY) を使用して SQL Server に R パッケージをインストールする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、machine learning が有効になっている SQL Server のインスタンスに新しい R パッケージをインストールする方法について説明します。 選択する方法は複数あります。 T-sql の使用は、R に慣れていないサーバー管理者に最適です。

**適用対象:** [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)]  [!INCLUDE[rsql-productnamenew-md](../../includes/rsql-productnamenew-md.md)]

[CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)ステートメントを使用すると、R または Python コードを直接実行することなく、インスタンスまたは特定のデータベースにパッケージまたはパッケージのセットを追加できます。 ただし、この方法では、パッケージの準備と追加のデータベースアクセス許可が必要です。

+ すべてのパッケージは、インターネットからの要求に応じてダウンロードするのではなく、ローカルの zip 形式のファイルとして使用できる必要があります。

+ すべての依存関係を名前とバージョンで識別し、zip ファイルに含める必要があります。 ダウンストリームパッケージの依存関係など、必要なパッケージが使用できない場合、ステートメントは失敗します。 

+ **Db_owner**であるか、データベースロールで CREATE EXTERNAL LIBRARY 権限を持っている必要があります。 詳細については、「 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)」を参照してください。

## <a name="download-packages-in-archive-format"></a>アーカイブ形式でパッケージをダウンロードする

1つのパッケージをインストールする場合は、パッケージを zip 形式でダウンロードします。

パッケージの依存関係により、複数のパッケージをインストールする方が一般的です。 パッケージに他のパッケージが必要な場合は、インストール中にすべてのパッケージが相互にアクセスできることを確認する必要があります。 パッケージの完全なコレクションと、パッケージの依存関係を分析するための[igraph](https://igraph.org/r/)をアセンブルするために、 [miniCRAN](https://andrie.github.io/miniCRAN/)を使用して[ローカルリポジトリを作成](create-a-local-package-repository-using-minicran.md)することをお勧めします。 間違ったバージョンのパッケージをインストールしたり、パッケージの依存関係を省略したりすると、CREATE EXTERNAL LIBRARY ステートメントが失敗する可能性があります。 

## <a name="copy-the-file-to-a-local-folder"></a>ローカルフォルダーにファイルをコピーする

すべてのパッケージを含む zip 形式のファイルをサーバー上のローカルフォルダーにコピーします。 サーバー上のファイルシステムへのアクセス権がない場合は、バイナリ形式を使用して完全なパッケージを変数として渡すこともできます。 詳細については、「 [CREATE EXTERNAL LIBRARY](../../t-sql/statements/create-external-library-transact-sql.md)」を参照してください。

## <a name="run-the-statement-to-upload-packages"></a>ステートメントを実行してパッケージをアップロードする

管理者特権を持つアカウントを使用して**クエリ**ウィンドウを開きます。

T-sql ステートメント`CREATE EXTERNAL LIBRARY`を実行して、圧縮されたパッケージコレクションをデータベースにアップロードします。

たとえば、次のステートメント名は、パッケージソースとして、 **randomForest**パッケージを含む miniCRAN リポジトリとその依存関係を含んでいます。 

```sql
CREATE EXTERNAL LIBRARY randomForest
FROM (CONTENT = 'C:\Temp\Rpackages\randomForest_4.6-12.zip')
WITH (LANGUAGE = 'R');
```

任意の名前を使用することはできません。外部ライブラリ名には、パッケージの読み込み時または呼び出し時に使用するものと同じ名前を指定する必要があります。

## <a name="verify-package-installation"></a>パッケージのインストールの確認

ライブラリが正常に作成された場合は、ストアドプロシージャ内でパッケージを呼び出すことによって、SQL Server でパッケージを実行できます。
    
```sql
EXEC sp_execute_external_script
@language =N'R',
@script=N'library(randomForest)'
```

## <a name="see-also"></a>関連項目

+ [パッケージ情報の取得](../package-management/installed-package-information.md)
+ [R チュートリアル](../tutorials/sql-server-r-tutorials.md)
