---
title: ファイルシステムからの R パッケージの同期
description: SQL Server の R ライブラリを、ファイルシステムにインストールされている新しいバージョンで更新します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 4e0b6133bcecc553934cd657785ea9ee765c6fd9
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715077"
---
# <a name="r-package-synchronization-for-sql-server"></a>SQL Server の R パッケージの同期
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server 2017 に含まれているバージョンの RevoScaleR には、ファイルシステムと、パッケージが使用されるインスタンスおよびデータベース間で R パッケージのコレクションを同期する機能が含まれています。

この機能は、SQL Server データベースに関連付けられている R パッケージコレクションのバックアップを容易にするために用意されています。 この機能を使用すると、管理者はデータベースだけでなく、データ科学者がそのデータベースで使用していた R パッケージも復元できます。

この記事では、パッケージの同期機能について説明します。また、 [rxSyncPackages](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsyncpackages)関数を使用して次のタスクを実行する方法について説明します。

+ SQL Server データベース全体のパッケージの一覧を同期する

+ 個々のユーザーまたはユーザーのグループによって使用されるパッケージを同期する

+ ユーザーが別の SQL Server に移動した場合は、ユーザーの作業中のデータベースのバックアップを作成し、新しいサーバーに復元することができます。ユーザー用のパッケージは、R の必要に応じて、新しいサーバーのファイルシステムにインストールされます。

たとえば、次のような場合にパッケージ同期を使用できます。

+ DBA は SQL Server のインスタンスを新しいコンピューターに復元し、ユーザーに R クライアントから接続し、を実行`rxSyncPackages`してパッケージを更新および復元するように要求しました。

+ ファイルシステム上の R パッケージが破損していると考えら`rxSyncPackages`れるので、SQL Server で実行します。

## <a name="requirements"></a>必要条件

パッケージの同期を使用するには、適切なバージョンの Microsoft R または Machine Learning Server が必要です。 この機能は、Microsoft R バージョン9.1.0 以降で提供されています。 

また、サーバーで[パッケージ管理機能](r-package-how-to-enable-or-disable.md)を有効にする必要があります。

### <a name="determine-whether-your-server-supports-package-management"></a>サーバーでパッケージ管理がサポートされているかどうかを判断する

この機能は SQL Server 2017 CTP 2 以降で使用できます。

この機能を SQL Server 2016 のインスタンスに追加するには、最新バージョンの Microsoft R を使用するようにインスタンスをアップグレードします。詳細については、「 [SqlBindR .exe を使用して SQL Server R Services をアップグレードする](../install/upgrade-r-and-python.md)」を参照してください。

### <a name="enable-the-package-management-feature"></a>パッケージ管理機能を有効にする

パッケージの同期を使用するには、SQL Server インスタンスと個々のデータベースで新しいパッケージ管理機能が有効になっている必要があります。 詳細については、「 [SQL Server のパッケージ管理の有効化または無効化](r-package-how-to-enable-or-disable.md)」を参照してください。

1. サーバー管理者は、SQL Server インスタンスの機能を有効にします。
2. 管理者は、各データベースについて、データベースロールを使用して R パッケージをインストールまたは共有する権限を個々のユーザーに付与します。

この処理が完了したら、 [rxInstallPackages](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxinstallpackages)などの RevoScaleR 関数を使用して、データベースにパッケージをインストールできます。  ユーザーおよびユーザーが使用できるパッケージの情報は、SQL Server インスタンスに格納されます。 

パッケージ管理機能を使用して新しいパッケージを追加するたびに、SQL Server のレコードとファイルシステムの両方が更新されます。 この情報は、データベース全体のパッケージ情報を復元するために使用できます。

### <a name="permissions"></a>アクセス許可

+ パッケージ同期関数を実行するユーザーは、SQL Server インスタンスのセキュリティプリンシパルであり、パッケージが含まれているデータベースである必要があります。

+ 関数の呼び出し元は、次のいずれかのパッケージ管理ロールのメンバーである必要があります: **rpkgs-shared**または**rpkgs-private**。

+ **共有**とマークされているパッケージを同期するには、その関数を実行しているユーザーが、 **rpkgs-shared**ロールのメンバーシップを持っている必要があります。また、移動されるパッケージは、共有スコープライブラリにインストールされている必要があります。

+ **プライベート**とマークされたパッケージを同期するには、パッケージの所有者または管理者が関数を実行し、パッケージがプライベートである必要があります。

+ 他のユーザーの代わりにパッケージを同期するには、所有者が**db_owner**データベースロールのメンバーである必要があります。

## <a name="how-package-synchronization-works"></a>パッケージの同期のしくみ

パッケージの同期を使用するには、 [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)の新しい関数である[rxSyncPackages](https://docs.microsoft.com/r-server/r-reference/revoscaler/rxsyncpackages)を呼び出します。 

を`rxSyncPackages`呼び出すたびに、SQL Server インスタンスとデータベースを指定する必要があります。 次に、同期するパッケージの一覧を表示するか、パッケージのスコープを指定します。

1. `RxInSqlServer`関数を使用して SQL Server 計算コンテキストを作成します。 計算コンテキストを指定しない場合は、現在のコンピューティングコンテキストが使用されます。

2. 指定された計算コンテキストで、インスタンス上のデータベースの名前を指定します。 パッケージはデータベースごとに同期されます。

3. スコープ引数を使用して、同期するパッケージを指定します。

    **プライベート**スコープを使用する場合は、指定された所有者が所有するパッケージのみが同期されます。 **共有**スコープを指定すると、データベース内のすべての非プライベートパッケージが同期されます。 
    
    **プライベート**スコープまたは**共有**スコープを指定せずに関数を実行すると、すべてのパッケージが同期されます。

4. コマンドが正常に実行された場合は、指定されたスコープと所有者を使用して、ファイルシステム内の既存のパッケージがデータベースに追加されます。

    ファイルシステムが破損している場合は、データベースに保持されている一覧に基づいてパッケージが復元されます。

    ターゲットデータベースでパッケージ管理機能が使用できない場合は、次のエラーが発生します。"パッケージ管理機能が SQL Server で有効になっていないか、バージョンが古すぎます"

### <a name="example-1-synchronize-all-package-by-database"></a>例 1. すべてのパッケージをデータベース別に同期

この例では、ローカルファイルシステムから新しいパッケージを取得し、データベース [TestDB] にパッケージをインストールします。 所有者が指定されていないため、一覧には、プライベートスコープと共有スコープにインストールされているすべてのパッケージが含まれます。

```R
connectionString <- "Driver=SQL Server;Server=myServer;Database=TestDB;Trusted_Connection=True;"
computeContext <- RxInSqlServer(connectionString = connectionString )
rxSyncPackages(computeContext=computeContext, verbose=TRUE)
```

### <a name="example-2-restrict-synchronized-packages-by-scope"></a>例 2。 同期されたパッケージをスコープ別に制限する

次の例では、指定されたスコープ内のパッケージのみを同期します。

```R
#Shared scope
rxSyncPackages(computeContext=computeContext, scope="shared", verbose=TRUE)

#Private scope
rxSyncPackages(computeContext=computeContext, scope="private", verbose=TRUE)
```

### <a name="example-3-restrict-synchronized-packages-by-owner"></a>例 3. 同期されたパッケージを所有者別に制限する

次の例では、特定のユーザーに対してインストールされたパッケージのみを同期する方法を示します。 この例では、ユーザーは SQL ログイン名*user1*によって識別されます。

```R
rxSyncPackages(computeContext=computeContext, scope="private", owner = "user1", verbose=TRUE))
```

## <a name="related-resources"></a>関連リソース

[SQL Server の R パッケージの管理](install-additional-r-packages-on-sql-server.md)
