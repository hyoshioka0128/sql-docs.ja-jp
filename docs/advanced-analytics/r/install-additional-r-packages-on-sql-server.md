---
title: 新しい R 言語パッケージをインストールする
description: SQL Server 2016 R Services または SQL Server Machine Learning Services (データベース内) に新しい R パッケージを追加する
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 1048dc6ef0a43c5fa41dd5398a5b3dced4a5ebe8
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715105"
---
# <a name="install-new-r-packages-on-sql-server"></a>SQL Server に新しい R パッケージをインストールする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、machine learning が有効になっている SQL Server のインスタンスに新しい R パッケージをインストールする方法について説明します。 新しい R パッケージをインストールするには、使用している SQL Server のバージョン、およびサーバーがインターネットに接続されているかどうかによって、複数の方法があります。 新しいパッケージをインストールするには、次の方法を使用できます。

| アプローチ                           | アクセス許可               | リモート/ローカル |
|------------------------------------|---------------------------|--------------|
| [従来の R パッケージマネージャーを使用する](use-r-package-managers-on-sql-server.md)  | 管理 | Local |
| [RevoScaleR の使用](use-revoscaler-to-manage-r-packages.md) |  管理者が有効になっているデータベースロール後 | both|
| [T-sql を使用する (外部ライブラリの作成)](install-r-packages-tsql.md) | 管理者が有効になっているデータベースロール後 | both 

## <a name="who-installs-permissions"></a>インストールするユーザー (アクセス許可)

R パッケージライブラリは、物理的に SQL Server インスタンスの Program Files フォルダーにあり、アクセスが制限された安全なフォルダーに配置されます。 この場所に書き込むには、管理者権限が必要です。

管理者以外はパッケージをインストールできますが、そのためには、初期インストールで使用できない追加の構成と機能が必要です。 非管理者パッケージのインストールには、次の2つの方法があります。RevoScaleR 9.0.1 以降を使用しているか、CREATE EXTERNAL LIBRARY (SQL Server 2017 のみ) を使用しています。 SQL Server 2017 では、 **dbo_owner** 、または CREATE EXTERNAL LIBRARY 権限を持つ別のユーザーが、現在のデータベースに R パッケージをインストールできます。

R 開発者は、中央に配置されているライブラリが制限されていない場合に、必要なパッケージのユーザーライブラリを作成することに慣れています。 この方法は、SQL Server データベースエンジンインスタンスで R コードを実行する場合に問題になります。 SQL Server、そのライブラリが同じコンピューター上にある場合でも、外部ライブラリからパッケージを読み込むことはできません。 SQL Server で実行されている R コードでは、インスタンスライブラリのパッケージのみを使用できます。

通常、ファイルシステムアクセスはサーバーで制限されています。管理者権限があり、サーバー上のユーザードキュメントフォルダーへのアクセス権がある場合でも、SQL Server で実行する外部スクリプトランタイムは、既定のインスタンスの外部にインストールされたパッケージにアクセスできません。ライブラリ. 

## <a name="considerations-for-package-installation"></a>パッケージのインストールに関する注意点

新しいパッケージをインストールする前に、特定のパッケージで有効になっている機能が SQL Server 環境に適しているかどうかを検討してください。 セキュリティが強化された SQL Server 環境では、次のことを避けることをお勧めします。

+ ネットワークアクセスを必要とするパッケージ
+ 管理者特権でのファイルシステムアクセスが必要なパッケージ
+ パッケージは、web 開発や、内部での実行によるメリットがないその他のタスクに使用され SQL Server

## <a name="offline-installation-no-internet-access"></a>オフラインインストール (インターネットアクセスなし)

一般に、実稼働データベースをホストするサーバーは、インターネット接続をブロックします。 このような環境に新しい R または Python パッケージをインストールするには、事前にパッケージと依存関係を準備し、オフラインインストールのためにファイルをサーバー上のフォルダーにコピーする必要があります。

すべての依存関係を識別することは複雑です。 R の場合は、MiniCRAN を使用して[ローカルリポジトリを作成](create-a-local-package-repository-using-minicran.md)し、完全に定義されたリポジトリを分離 SQL Server インスタンスに転送することをお勧めします。

または、これらの手順を手動で実行することもできます。

1. すべてのパッケージの依存関係を識別します。 
2. サーバーに必要なパッケージが既にインストールされているかどうかを確認します。 パッケージがインストールされている場合は、バージョンが正しいことを確認します。
3. パッケージとすべての依存関係を別のコンピューターにダウンロードします。
4. サーバーからアクセス可能なフォルダーにファイルを移動します。
5. サポートされているインストールコマンドまたは DDL ステートメントを実行して、インスタンスライブラリにパッケージをインストールします。

### <a name="download-the-package-as-a-zipped-file"></a>パッケージを zip 形式のファイルとしてダウンロードする

インターネットにアクセスできないサーバーにインストールする場合は、オフラインインストール用の zip 形式のファイルの形式でパッケージのコピーをダウンロードする必要があります。 **パッケージを解凍しないでください。**

たとえば、次の手順では、コンピューターがインターネットにアクセスできると仮定して、 [FISHalyseR](https://bioconductor.org/packages/release/bioc/html/FISHalyseR.html)パッケージの正しいバージョンを Bioconductor から取得するようになりました。

1.  **[パッケージ アーカイブ]** ボックスの一覧で、 **Windows バイナリ** バージョンを見つけます。

2.  へのリンクを右クリックします。ZIP ファイルを選択し、 **[ターゲットを名前を付けて保存]** を選択します。

3.  Zip 形式のパッケージが格納されているローカルフォルダーに移動し、 **[保存]** をクリックします。

    このプロセスにより、パッケージのローカル コピーが作成されます。 

4. ダウンロードエラーが発生した場合は、別のミラーサイトを試してください。

5. パッケージのアーカイブがダウンロードされたら、パッケージをインストールするか、インターネットにアクセスできないサーバーに zip 圧縮されたパッケージをコピーすることができます。

> [!TIP]
> バイナリをダウンロードするのではなく、パッケージを誤ってインストールした場合は、ダウンロードした zip ファイルのコピーもコンピューターに保存されます。 パッケージがインストールされている状態メッセージを監視して、ファイルの場所を確認します。 この zip ファイルは、インターネットにアクセスできないサーバーにコピーできます。
> 
> ただし、このメソッドを使用してパッケージを取得する場合、依存関係は含まれません。 


## <a name="side-by-side-installation-with-standalone-r-or-python-servers"></a>スタンドアロンの R または Python サーバーとのサイドバイサイドインストール

R と Python の機能はいくつかの Microsoft 製品に含まれており、これらはすべて同じコンピューターに共存する可能性があります。

データベース内分析に加えて SQL Server 2017 Microsoft Machine Learning Server (スタンドアロン) または SQL Server 2016 R Server (スタンドアロン) をインストールした場合 (SQL Server Machine Learning Services と SQL Server 2016 R Services)、コンピューターはすべての R ツールとライブラリの複製による、それぞれの R のインストール。

R_SERVER ライブラリにインストールされたパッケージは、スタンドアロンサーバーによってのみ使用され、SQL Server (データベース内) インスタンスからはアクセスできません。 SQL Server でデータベース`R_SERVICES`内で使用するパッケージをインストールする場合は、常にライブラリを使用します。 パスの詳細については、「[パッケージライブラリの場所](../package-management/default-packages.md)」を参照してください。

## <a name="see-also"></a>関連項目

+ [新しい Python パッケージのインストール](../python/install-additional-python-packages-on-sql-server.md)
+ [チュートリアル、サンプル、ソリューション](../tutorials/machine-learning-services-tutorials.md)
