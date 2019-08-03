---
title: R パッケージ マネージャーの使用
description: Install. パッケージなどの標準の R コマンドを使用して、SQL Server 2016 R Services または SQL Server Machine Learning Services (データベース内) に新しい R パッケージを追加します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 75ef22eb7e06fa1f8d4d2a0d9c754959f1bb1ae4
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715019"
---
# <a name="use-r-package-managers-to-install-r-packages-on-sql-server"></a>R パッケージマネージャーを使用して SQL Server に R パッケージをインストールする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

標準の R ツールを使用すると SQL Server 2016 または SQL Server 2017 のインスタンスに新しいパッケージをインストールできます。この場合、コンピューターのポート80が開いており、管理者権限を持っている必要があります。

> [!IMPORTANT] 
> 必ず、現在のインスタンスに関連付けられている既定のライブラリにパッケージをインストールしてください。 ユーザーディレクトリにパッケージをインストールしないでください。

この手順では RGui を使用しますが、昇格されたアクセスをサポートする Rgui またはその他の R コマンドラインツールを使用することもできます。

## <a name="install-a-package-using-rgui"></a>RGui を使用してパッケージをインストールする

1. [インスタンスライブラリの場所を決定](../package-management/default-packages.md)します。 R ツールがインストールされているフォルダーに移動します。 たとえば、SQL Server 2017 の既定のインスタンスの既定のパスは次のようになります。`C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\bin\x64`

1. RGui .exe を右クリックし、 **[管理者として実行]** を選択します。 必要なアクセス許可がない場合は、データベース管理者に連絡し、必要なパッケージの一覧を提供してください。

1. パッケージ名がわかっている場合は、コマンドラインから次のように入力できます。`install.packages("the_package-name")`パッケージ名には二重引用符が必要です。

1. ミラーサイトの使用を確認するメッセージが表示されたら、その場所に適したサイトを選択します。

ターゲットパッケージが他のパッケージに依存している場合は、R インストーラーによって自動的に依存関係がダウンロードされ、自動的にインストールされます。

SQL Server の複数のインスタンスがある場合 (SQL Server 2016 R Services のサイドバイサイドインスタンスや SQL Server Machine Learning Services など)、両方のコンテキストでパッケージを使用する場合は、インスタンスごとにインストールを個別に実行します。 パッケージをインスタンス間で共有することはできません。

## <a name = "bkmk_offlineInstall"></a>R ツールを使用したオフラインインストール

サーバーがインターネットにアクセスできない場合は、パッケージを準備するために追加の手順が必要になります。 インターネットにアクセスできないサーバーに R パッケージをインストールするには、次のことを行う必要があります。

+ 依存関係を事前に分析します。
+ インターネットにアクセスできるコンピューターにターゲットパッケージをダウンロードします。
+ 同じコンピューターに必要なパッケージをダウンロードし、すべてのパッケージを1つのパッケージアーカイブに配置します。
+ アーカイブを zip 形式にしていない場合は、zip 圧縮します。
+ パッケージアーカイブをサーバー上の場所にコピーします。
+ アーカイブファイルをソースとして指定するターゲットパッケージをインストールします。

> [!IMPORTANT] 
>  インストールを開始する**前に**、すべての依存関係を分析し、必要な**すべて**のパッケージをダウンロードしてください。 このプロセスには[miniCRAN](https://mran.microsoft.com/package/miniCRAN)を使用することをお勧めします。 この R パッケージは、インストールするパッケージの一覧を取得し、依存関係を分析し、zip 形式のファイルをすべて取得します。 miniCRAN は、サーバーコンピューターにコピーできる単一のリポジトリを作成します。 詳細については、「 [miniCRAN を使用してローカルパッケージリポジトリを作成する](create-a-local-package-repository-using-minicran.md)」を参照してください。

この手順では、必要なすべてのパッケージを zip 形式で準備し、それらをサーバーにコピーする準備ができていることを前提としています。

1. パッケージの zip 形式のファイル、または zip 形式のすべてのパッケージを含む完全なリポジトリをサーバーがアクセスできる場所にコピーします。

2. インスタンスの R ツールがインストールされているサーバー上のフォルダーを開きます。 たとえば、SQL Server 2016 R Services が搭載されたシステムで Windows コマンドプロンプトを使用している場合`C:\Program Files\MSSQL13.MSSQLSERVER\R_SERVICES\bin\x64`は、に切り替えます。

3. RGui または Rgui を右クリックし、 **[管理者として実行]** を選択します。

4. R コマンド`install.packages`を実行し、パッケージまたはリポジトリの名前と、zip 形式のファイルの場所を指定します。

    ```R
    install.packages("C:\\Temp\\Downloaded packages\\mynewpackage.zip", repos=NULL)
    ```

    このコマンドは、ディレクトリ`mynewpackage` `C:\Temp\Downloaded packages`にコピーを保存し、ローカルコンピューターにパッケージをインストールすると仮定して、ローカルの zip 形式のファイルから R パッケージを抽出します。 パッケージに依存関係がある場合、インストーラーはライブラリ内の既存のパッケージを確認します。 依存関係を含むリポジトリを作成した場合は、必要なパッケージもインストーラーによってインストールされます。

    必要なパッケージがインスタンスライブラリに存在せず、zip 形式のファイルに見つからない場合、ターゲットパッケージのインストールは失敗します。

## <a name="see-also"></a>関連項目

+ [新しい R パッケージのインストール](install-additional-r-packages-on-sql-server.md)
+ [新しい Python パッケージのインストール](../python/install-additional-python-packages-on-sql-server.md)
+ [チュートリアル、サンプル、ソリューション](../tutorials/machine-learning-services-tutorials.md)
