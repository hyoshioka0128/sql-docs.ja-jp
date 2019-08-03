---
title: R スクリプトエラーとトラブルシューティング
ms.prod: sql
ms.technology: machine-learning
ms.date: 05/31/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 10ec78bf8627bfef3232dfc72d7ef7f638604b15
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715750"
---
# <a name="r-scripting-errors-in-sql-server"></a>SQL Server での R スクリプトエラー
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server で R コードを実行する際のいくつかのスクリプトエラーについて説明します。 この一覧は包括的なものではありません。 パッケージは多数あり、同じパッケージのバージョンによってエラーが異なる場合があります。 [Machine Learning Server フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?category=MicrosoftR)では、R Services (データベース内)、Microsoft R Client、および Microsoft R Server で使用される機械学習コンポーネントをサポートするスクリプトエラーを投稿することをお勧めします。

## <a name="valid-script-fails-in-t-sql-or-in-stored-procedures"></a>T-sql またはストアドプロシージャで有効なスクリプトが失敗する

ストアドプロシージャで R コードをラップする前に、外部 IDE または RTerm や Rterm などの R ツールのいずれかで R コードを実行することをお勧めします。 これらのメソッドを使用すると、R によって返される詳細なエラーメッセージを使用して、コードをテストおよびデバッグできます。

ただし、外部 IDE やユーティリティで完全に動作するコードは、ストアドプロシージャや SQL Server の計算コンテキストでは実行できない場合があります。 この問題が発生した場合は、パッケージが SQL Server で動作しないと見なす前に、さまざまな問題を探す必要があります。

1. スタートパッドが実行されているかどうかを確認します。

2. メッセージを確認して、入力データまたは出力データに互換性のないデータ型またはサポートされていないデータ型の列が含まれるかどうかを確認します。 たとえば、SQL データベースに対するクエリでは、Guid または行 Guid が返されることがよくありますが、どちらもサポートされていません。 詳細については、「 [R ライブラリとデータ型](r/r-libraries-and-data-types.md)」を参照してください。

3. 個々の R 関数のヘルプページを参照して、SQL Server 計算コンテキストですべてのパラメーターがサポートされているかどうかを確認します。 ScaleR のヘルプを表示するには、インラインの R ヘルプコマンドを使用するか、[パッケージのリファレンス](https://docs.microsoft.com/r-server/r-reference/revoscaler/revoscaler)を参照してください。

R ランタイムが機能していても、スクリプトからエラーが返された場合は、R Tools for Visual Studio などの専用の R 開発環境でスクリプトをデバッグすることをお勧めします。

また、R とデータベースエンジンの間でデータを移動するときに発生する可能性のあるデータ型に関する問題を修正するために、スクリプトを確認し、少し書き直しておくことをお勧めします。 詳細については、「 [R ライブラリとデータ型](r/r-libraries-and-data-types.md)」を参照してください。

また、sqlrutils パッケージを使用して、ストアドプロシージャとしてより簡単に使用できる形式で R スクリプトをバンドルすることもできます。 詳細については、以下をご覧ください。
* [sqlrutils パッケージ](r/ref-r-sqlrutils.md)
* [Sqlrutils を使用してストアドプロシージャを作成する](r/how-to-create-a-stored-procedure-using-sqlrutils.md)

## <a name="script-returns-inconsistent-results"></a>スクリプトは一貫性のない結果を返します

R スクリプトは、いくつかの理由により、SQL Server のコンテキストで異なる値を返すことがあります。

- データが SQL Server と R の間で渡される場合、暗黙的な型変換は一部のデータ型に対して自動的に実行されます。詳細については、「 [R ライブラリとデータ型](r/r-libraries-and-data-types.md)」を参照してください。

- ビットが因子であるかどうかを判断します。 たとえば、32ビットおよび64ビットの浮動小数点ライブラリでは、算術演算の結果に違いがあることがよくあります。

- 任意の操作で Nan が生成されたかどうかを確認します。 これにより、結果が無効になる可能性があります。

- ゼロに近い数値の逆数を取得すると、小さい違いが増幅される可能性があります。

- 丸め誤差が累積されると、0ではなく0未満の値が発生する可能性があります。

## <a name="implied-authentication-for-remote-execution-via-odbc"></a>ODBC を使用したリモート実行の暗黙の認証

RevoScaleR 関数を使用し[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]て R コマンドを実行するためにコンピューターに接続した場合、データをサーバーに書き込む ODBC 呼び出しを使用すると、エラーが発生することがあります。 このエラーは、Windows 認証を使用している場合にのみ発生します。

その理由は、R Services に対して作成されたワーカーアカウントには、サーバーに接続するためのアクセス許可がないためです。 そのため、ODBC 呼び出しをユーザーに代わって実行することはできません。 Sql ログインでは、資格情報が R クライアントから SQL Server インスタンスに明示的に渡され、次に ODBC に渡されるため、SQL ログインでは問題は発生しません。 ただし、SQL ログインの使用は、Windows 認証を使用する場合よりも安全性が低くなります。

リモートで開始されるスクリプトから Windows 資格情報を安全に渡すには、SQL Server 資格情報をエミュレートする必要があります。 このプロセスは_暗黙の認証_と呼ばれます。 この作業を行うには、SQL Server コンピューターで R または Python スクリプトを実行するワーカーアカウントに適切なアクセス許可が必要です。

1. R [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]コードを実行するインスタンスで管理者としてを開きます。

2. 次のスクリプトを実行します。 既定値を変更した場合は、ユーザーグループ名を編集し、コンピューター名とインスタンス名を必ず編集してください。

    ```sql
    USE [master]
    GO
    
    CREATE LOGIN [computername\\SQLRUserGroup] FROM WINDOWS WITH
    DEFAULT_DATABASE=[master], DEFAULT_LANGUAGE=[language]
    GO
    ```

## <a name="avoid-clearing-the-workspace-while-youre-running-r-in-a-sql-compute-context"></a>SQL コンピューティングコンテキストで R を実行しているときにワークスペースをクリアしないようにする

ワークスペースのクリアは、R コンソールで作業するときは一般的ですが、SQL の計算コンテキストで意図しない結果が生じる可能性があります。

`revoScriptConnection`は、R ワークスペース内のオブジェクトであり、SQL Server から呼び出された R セッションに関する情報が含まれています。 ただし、r コードにワークスペースをクリアするコマンド ( `rm(list=ls())`など) が含まれている場合は、r ワークスペースのセッションとその他のオブジェクトに関するすべての情報もクリアされます。

回避策として、SQL Server で R を実行しているときに、変数とその他のオブジェクトの区別を解除しないようにしてください。 **Remove**関数を使用すると、特定の変数を削除できます。

```R
remove('name1', 'name2', ...)
```

複数の変数を削除する場合は、一時変数の名前をリストに保存し、定期的にガベージコレクションを実行することをお勧めします。



## <a name="next-steps"></a>次のステップ

[Machine Learning Services のトラブルシューティングと既知の問題](machine-learning-troubleshooting-faq.md)

[Machine learning のトラブルシューティングのためのデータ収集](data-collection-ml-troubleshooting-process.md)

[アップグレードとインストールに関してよく寄せられる質問](r/upgrade-and-installation-faq-sql-server-r-services.md)

[データベースエンジン接続のトラブルシューティング](../database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine.md)
