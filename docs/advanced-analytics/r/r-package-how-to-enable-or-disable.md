---
title: リモート R パッケージの管理を有効または無効にする
description: SQL Server 2016 R Services または SQL Server Machine Learning Services (データベース内) でのリモート R パッケージ管理を有効にする
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 260109e978c8997622f24c41341e11f1e2efa7cc
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715632"
---
# <a name="enable-or-disable-remote-package-management-for-sql-server"></a>SQL Server のリモートパッケージ管理を有効または無効にする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、クライアントワークステーションまたは別の Machine Learning Server から R パッケージのリモート管理を有効にする方法について説明します。 SQL Server でパッケージ管理機能が有効になったら、クライアントで RevoScaleR コマンドを使用して、SQL Server にパッケージをインストールできます。

> [!NOTE]
> 現在、R ライブラリの管理はサポートされています。Python のサポートはロードマップにあります。

既定では、SQL Server の外部パッケージ管理機能は無効になっています。 次のセクションで説明するように、別のスクリプトを実行して機能を有効にする必要があります。

## <a name="overview-of-process-and-tools"></a>プロセスとツールの概要

SQL Server でパッケージ管理を有効または無効にするには、 **RevoScaleR**パッケージに含まれているコマンドラインユーティリティの**registerrext**を使用します。

この機能を[有効](#bkmk_enable)にするには、データベース管理者が必要な2段階のプロセスを実行します。 SQL Server インスタンスでパッケージ管理を有効にし (SQL Server インスタンスごとに1回)、SQL データベースでパッケージ管理を有効にします (SQL Server データベースごとに1回)。).

パッケージ管理機能を[無効](#bkmk_disable)にするには、multipel の手順も必要です。データベースレベルのパッケージと権限を (データベースごとに1回) 削除し、サーバーからロールを削除します (インスタンスごとに1回)。

## <a name="bkmk_enable"></a>パッケージ管理を有効にする

1. SQL Server で、管理者特権でのコマンドプロンプトを開き、ユーティリティが格納されているフォルダーに移動します。 既定の場所は`<SQLInstancePath>\R_SERVICES\library\RevoScaleR\rxLibs\x64\RegisterRExe.exe`です。

2. 次のコマンドを実行して、環境に適した引数を指定します。

    `RegisterRExt.exe /install pkgmgmt [/instance:name] [/user:username] [/password:*|password]`

    このコマンドは、パッケージ管理に必要な SQL Server コンピューター上にインスタンスレベルのオブジェクトを作成します。 また、インスタンスのスタートパッドも再起動します。

    インスタンスを指定しない場合は、既定のインスタンスが使用されます。 ユーザーを指定しない場合は、現在のセキュリティコンテキストが使用されます。 たとえば、次のコマンドは、コマンドプロンプトを開いたユーザーの資格情報を使用して、RegisterRExt のパスにあるインスタンスのパッケージ管理を有効にします。

    `REgisterRExt.exe /install pkgmgmt`

3. 特定のデータベースにパッケージ管理を追加するには、管理者特権のコマンドプロンプトから次のコマンドを実行します。

    `RegisterRExt.exe /install pkgmgmt /database:databasename [/instance:name] [/user:username] [/password:*|password]`
   
    次のコマンドでは、ユーザーのアクセス許可`rpkgs-users`を制御するために使用される、 `rpkgs-private`、、および`rpkgs-shared`のデータベースロールを含む、いくつかのデータベースアーティファクトを作成します。

    たとえば、次のコマンドは、RegisterRExt が実行されているインスタンスで、データベースのパッケージ管理を有効にします。 ユーザーを指定しない場合は、現在のセキュリティコンテキストが使用されます。

    `RegisterRExt.exe /install pkgmgmt /database:TestDB`

4. パッケージをインストールする必要がある各データベースに対して、このコマンドを繰り返します。

5. 新しいロールが正常に作成されたことを確認するには、SQL Server Management Studio でデータベースをクリックし、 **[セキュリティ]** 、 **[データベースロール]** の順に展開します。

    次のように、database_principals でクエリを実行することもできます。

    ```sql
    SELECT pr.principal_id, pr.name, pr.type_desc,   
        pr.authentication_type_desc, pe.state_desc,   
        pe.permission_name, s.name + '.' + o.name AS ObjectName  
    FROM sys.database_principals AS pr  
    JOIN sys.database_permissions AS pe  
        ON pe.grantee_principal_id = pr.principal_id  
    JOIN sys.objects AS o  
        ON pe.major_id = o.object_id  
    JOIN sys.schemas AS s  
        ON o.schema_id = s.schema_id;
    ```

この機能を有効にした後、RevoScaleR 関数を使用して、リモート R クライアントからパッケージをインストールまたはアンインストールすることができます。

## <a name="bkmk_disable"></a>パッケージ管理を無効にする

1. 管理者特権でのコマンドプロンプトで、RegisterRExt ユーティリティをもう一度実行し、データベースレベルでのパッケージ管理を無効にします。

    `RegisterRExt.exe /uninstall pkgmgmt /database:databasename [/instance:name] [/user:username] [/password:*|password]`

    このコマンドは、指定されたデータベースから、パッケージ管理に関連するデータベースオブジェクトを削除します。 また、SQL Server コンピューターのセキュリティで保護されたファイルシステムの場所からインストールされたすべてのパッケージを削除します。

2. パッケージ管理が使用された各データベースで、このコマンドを繰り返します。

3.  Optional前の手順を使用してすべてのデータベースを消去した後、管理者特権でのコマンドプロンプトから次のコマンドを実行します。

    `RegisterRExt.exe /uninstall pkgmgmt [/instance:name] [/user:username] [/password:*|password]`

    このコマンドは、インスタンスからパッケージ管理機能を削除します。 変更を表示するには、スタートパッドサービスを手動で再起動する必要がある場合があります。

## <a name="next-steps"></a>次の手順

+ [RevoScaleR を使用して新しい R パッケージをインストールする](use-revoscaler-to-manage-r-packages.md)
+ [R パッケージのインストールに関するヒント](packages-installed-in-user-libraries.md)
+ [既定のパッケージ](../package-management/default-packages.md)
