---
title: RevoScaleR チュートリアル用のデータベースとアクセス許可の作成
description: R チュートリアル用の SQL Server データベースを作成する方法に関するチュートリアルチュートリアルです。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 14c07b5b2ebf30f23083921f210563bc2cb83dbf
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715532"
---
# <a name="create-a-database-and-permissions-sql-server-and-revoscaler-tutorial"></a>データベースとアクセス許可の作成 (SQL Server と RevoScaleR のチュートリアル)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このレッスンは、SQL Server で[RevoScaleR 関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)を使用する方法に関する[RevoScaleR チュートリアル](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md)の一部です。

レッスン1では、SQL Server データベースの設定と、このチュートリアルを完了するために必要なアクセス許可について説明します。 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)または別のクエリエディターを使用して、次のタスクを実行します。

> [!div class="checklist"]
> * 2つの R モデルのトレーニングとスコア付けのためのデータを格納する新しいデータベースを作成する
> * データベースオブジェクトを作成および使用するための権限を持つデータベースユーザーログインを作成する
  
## <a name="create-the-database"></a>データベースの作成

このチュートリアルでは、データとコードを格納するデータベースが必要です。 管理者でない場合は、DBA にデータベースを作成し、ログインするように依頼してください。 データの書き込みと読み取り、および R スクリプトの実行を行うためのアクセス許可が必要です。

1. SQL Server Management Studio で、R が有効なデータベースインスタンスに接続します。

2. **[データベース]** を右クリックし、 **[新しいデータベース]** をクリックします。
  
2. 新しいデータベースの名前を入力します:RevoDeepDive.
  

## <a name="create-a-login"></a>ログインを作成します
  
1. **[新しいクエリ]** をクリックして、データベース コンテキストを master データベースに変更します。
  
2. 新しい **[クエリ]** ウィンドウで、次のコマンドを実行してユーザー アカウントを作成し、このチュートリアルで使用するデータベースに割り当てます。 必要に応じてデータベースの名前を変更します。

3. ログインを確認するには、新しいデータベースを選択し、 **[セキュリティ]** 、 **[ユーザー]** の順に展開します。
  
**Windows ユーザー**
  
```sql
 -- Create server user based on Windows account
USE master
GO
CREATE LOGIN [<DOMAIN>\<user_name>] FROM WINDOWS WITH DEFAULT_DATABASE=[RevoDeepDive]

 --Add the new user to tutorial database
USE [RevoDeepDive]
GO
CREATE USER [<user_name>] FOR LOGIN [<DOMAIN>\<user_name>] WITH DEFAULT_SCHEMA=[db_datareader]
```

**SQL ログイン**

```sql
-- Create new SQL login
USE master
GO
CREATE LOGIN [DDUser01] WITH PASSWORD='<type password here>', CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF;

-- Add the new SQL login to tutorial database
USE RevoDeepDive
GO
CREATE USER [DDUser01] FOR LOGIN [DDUser01] WITH DEFAULT_SCHEMA=[db_datareader]
```

## <a name="assign-permissions"></a>アクセス許可の割り当て

このチュートリアルでは、R スクリプトと DDL 操作について説明します。これには、テーブルとストアドプロシージャの作成と削除、および SQL Server での外部プロセスでの R スクリプトの実行が含まれます。 このステップでは、アクセス許可を割り当ててこれらのタスクを許可します。

この例では、SQL ログイン (DDUser01) が想定されていますが、Windows ログインを作成した場合は、代わりにを使用します。

```sql
USE RevoDeepDive
GO

EXEC sp_addrolemember 'db_owner', 'DDUser01'
GRANT EXECUTE ANY EXTERNAL SCRIPT TO DDUser01
GO
```

## <a name="troubleshoot-connections"></a>接続のトラブルシューティング

このセクションでは、データベースを設定する過程で発生する可能性がある一般的な問題を示します。

- **データベースの接続と SQL クエリを確認するにはどうすればよいですか。**
  
    サーバーを使用して R コードを実行する前に、R の開発環境からデータベースにアクセスできることを確認したい場合があります。 [Visual Studio のサーバー エクスプローラー](https://docs.microsoft.com/previous-versions/x603htbk(v=vs.140)) と [SQL Server Management Studio](../../ssms/download-sql-server-management-studio-ssms.md) はどちらも、強力なデータベース接続と管理機能を持つ無償のツールです。
  
    新しいデータベース管理ツールをインストールしたくない場合は、コントロール パネルの [ODBC データ ソース アドミニストレーター](https://docs.microsoft.com/sql/odbc/admin/odbc-data-source-administrator?view=sql-server-2017) を使用して、SQL Server インスタンスへのテスト接続を作成できます。 データベースが正しく構成されていて、正しいユーザー名とパスワードを入力した場合は、先に作成したデータベースを表示し、既定のデータベースとして選択することができます。
  
    接続エラーの一般的な理由として、サーバーのリモート接続が有効になっておらず、名前付きパイププロトコルが有効になっていないことが挙げられます。 この記事では、その他のトラブルシューティングのヒントを確認できます。[SQL Server データベースエンジンへの接続に関するトラブルシューティングを](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)行います。
  
- **テーブル名に datareader というプレフィックスが付くのはなぜですか。**
  
    このユーザーの既定のスキーマを**db_datareader**として指定すると、このユーザーによって作成されるすべてのテーブルとその他の新しいオブジェクトには、*スキーマ*名が付加されます。 スキーマは、オブジェクトを整理するためにデータベースに追加できるフォルダーのようなものです。 スキーマでは、データベース内でのユーザーの権限も定義されています。
  
    スキーマが1つの特定のユーザー名に関連付けられている場合、ユーザーは_スキーマの所有者_になります。 オブジェクトを作成するときは、別のスキーマに作成することを明示的に要求しない限り、常に自分用のスキーマに作成されます。
  
    たとえば、 **TestData**という名前のテーブルを作成し、既定のスキーマが**db_datareader**の場合、テーブルはという名前`<database_name>.db_datareader.TestData`で作成されます。
  
    このため、テーブルが異なるスキーマに属していれば、1 つのデータベースに同じ名前の複数のテーブルを含めることができます。
   
    テーブルを検索するときにスキーマを指定しない場合、データベースサーバーは自分が所有するスキーマを探します。 そのため、自分のログインに関連付けられているスキーマ内のテーブルにアクセスするときは、スキーマ名を指定する必要はありません。
  
- **DDL 特権を持っていません。それでもチュートリアルを実行できますか。**
  
    はい。ただし、データを[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]テーブルに事前に読み込むように依頼するか、次のレッスンに進んでください。 DDL 特権を必要とする関数は、可能な限りチュートリアルで呼び出されます。

    また、アクセス許可を付与するか、外部スクリプトを実行するかを管理者に依頼してください。 リモートまたはを使用`sp_execute_external_script`して R スクリプトを実行する場合に必要です。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [RxSqlServerData を使用して SQL Server のデータ オブジェクトを作成する](../../advanced-analytics/tutorials/deepdive-create-sql-server-data-objects-using-rxsqlserverdata.md)