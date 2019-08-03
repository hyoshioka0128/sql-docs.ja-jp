---
title: SQL Server に R が存在することを確認するためのクイックスタート
description: R と Machine Learning Services が SQL Server に存在することを確認するためのクイックスタート。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/04/2019
ms.topic: quickstart
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 072a6f34a7cb91505d77356d6ec3835915c310d0
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715401"
---
# <a name="quickstart-verify-r-exists-in-sql-server"></a>クイック スタート: SQL Server に R が存在することを確認する 
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server には、データサイエンス分析のための R 言語サポートが常駐 SQL Server データに含まれています。 R スクリプトは、オープンソース R 関数、サードパーティ製 R ライブラリ、または大規模な予測分析のための[RevoScaleR](../r/revoscaler-overview.md)などの組み込みの Microsoft R ライブラリで構成されています。

スクリプトの実行は、次のいずれかの方法を使用して、ストアドプロシージャを使用します。

+ 組み込みの[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)ストアドプロシージャ。入力パラメーターとして R スクリプトを渡します。
+ 作成した[カスタムストアドプロシージャ](sqldev-in-database-r-for-sql-developers.md)に R スクリプトをラップします。

このクイックスタートでは、 [SQL Server Machine Learning Services](../what-is-sql-server-machine-learning.md)または[SQL Server 2016 R Services](../r/sql-server-r-services.md)がインストールおよび構成されていることを確認します。

## <a name="prerequisites"></a>必須コンポーネント

この演習では、次のいずれかが既にインストールされている SQL Server のインスタンスにアクセスする必要があります。

+ R 言語がインストールされている[SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)
+ [SQL Server 2016 R Services](../install/sql-r-services-windows-install.md)

SQL Server インスタンスは、Azure 仮想マシンまたはオンプレミスに配置できます。 外部スクリプト機能が既定で無効になっているため、[外部スクリプトを有効](../install/sql-machine-learning-services-windows-install.md#bkmk_enableFeature)にし、開始する前に**SQL Server Launchpad サービス**が実行されていることを確認する必要がある場合があることに注意してください。

また、SQL クエリを実行するためのツールも必要です。 SQL Server インスタンスに接続し、T-sql クエリまたはストアドプロシージャを実行できる限り、任意のデータベース管理ツールまたはクエリツールを使用して R スクリプトを実行できます。 このクイックスタートでは、 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)を使用します。

## <a name="verify-r-exists"></a>R の存在を確認する

SQL Server インスタンスで Machine Learning Services (R) が有効になっていること、およびインストールされている R のバージョンを確認できます。 次の手順に従います。

1. SQL Server Management Studio を開き、SQL Server インスタンスに接続します。

2. 次のコードを実行します。 

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

3. R `print`関数は、バージョンを **[メッセージ]** ウィンドウに返します。 次の出力例では、SQL Server に R バージョン3.3.3 がインストールされていることがわかります。

    **結果**

    ```text
    platform       x86_64-w64-mingw32          
    arch           x86_64                      
    os             mingw32                     
    system         x86_64, mingw32             
    status                                     
    major          3                           
    minor          3.3                         
    year           2017                        
    month          03                          
    day            06                          
    svn rev        72310                       
    language       R                           
    version.string R version 3.3.3 (2017-03-06)
    nickname       Another Canoe               
    ```

このクエリからエラーが発生した場合は、インストールの問題をすべて除外します。 外部コードライブラリを使用できるようにするには、インストール後の構成が必要です。 「 [Install SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md) 」または「 [SQL Server 2016 R Services のインストール](../install/sql-r-services-windows-install.md)」を参照してください。 同様に、スタートパッドサービスが実行されていることを確認します。

環境によっては、SQL Server に接続するための R ワーカー アカウントの有効化、追加のネットワーク ライブラリのインストール、リモートでのコード実行の有効化、すべてを構成した後のインスタンスの再起動が必要な場合があります。 詳細については、「 [R Services のインストールとアップグレード](../r/upgrade-and-installation-faq-sql-server-r-services.md)に関する FAQ」を参照してください。

## <a name="list-r-packages"></a>R パッケージの一覧表示

Microsoft では、SQL Server インスタンスに Machine Learning Services と共に事前にインストールされた多数の R パッケージを提供しています。 バージョン、依存関係、ライセンス、ライブラリパスの情報など、インストールされている R パッケージの一覧を表示するには、次の手順に従います。

1. SQL Server インスタンスで次のスクリプトを実行します。

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. 出力は R の`installed.packages()`からのものであり、結果セットとして返されます。

    **結果**

    ![R でインストールされたパッケージ](./media/rsql-installed-packages.png)

## <a name="next-steps"></a>次のステップ

これで、インスタンスが R を使用する準備ができたことを確認できました。基本的な R の相互作用について詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [クイック スタート:SQL Server の "Hello world" R スクリプト](quickstart-r-run-using-tsql.md)
