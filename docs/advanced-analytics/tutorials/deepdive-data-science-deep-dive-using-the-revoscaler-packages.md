---
title: RevoScaleR 関数の詳細チュートリアル-SQL Server Machine Learning
description: このチュートリアルでは、SQL Server Machine Learning R 統合を使用して RevoScaleR functions を呼び出す方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/27/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 4db5debf4ba71f29a8870c8674a5422e9ffd334a
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714882"
---
# <a name="tutorial-use-revoscaler-r-functions-with-sql-server-data"></a>チュートリアル:SQL Server データでの RevoScaleR R 関数の使用
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)は、データサイエンスと機械学習のワークロードに対して分散型および並列処理を提供する Microsoft R パッケージです。 SQL Server での R 開発では、 **RevoScaleR**は、データソースオブジェクトの作成、コンピューティングコンテキストの設定、パッケージの管理などの機能を備えたコア組み込みパッケージの1つです。また、データのインポートから視覚化と分析。 SQL Server の Machine Learning アルゴリズムは、 **RevoScaleR**データソースに依存しています。 **RevoScaleR**の重要性を考えると、その関数を呼び出すタイミングと方法を知っておくことが不可欠なスキルです。 

このマルチパートチュートリアルでは、データサイエンスに関連するタスクのためのさまざまな**RevoScaleR**関数について紹介します。 このプロセスでは、リモートの計算コンテキストを作成する方法、ローカルとリモートの計算コンテキストの間でデータを移動する方法、およびリモート SQL Server で R コードを実行する方法について説明します。 また、ローカルとリモートサーバーの両方でデータを分析してプロットする方法と、モデルを作成して配置する方法についても説明します。

## <a name="prerequisites"></a>必須コンポーネント

+ R 機能を使用した[SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md) 、または[SQL Server R Services (データベース内)](../install/sql-r-services-windows-install.md)
  
+ [データベース権限](../security/user-permission.md)と SQL Server データベースユーザーログイン

+ [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

+ RStudio などの IDE、または R に付属する組み込みの RSTUDIO ツール

ローカルとリモートの両方の計算コンテキストを切り替えるには、2つのシステムが必要です。 ローカルは、通常、データサイエンスワークロードのための十分なパワーを備えた開発ワークステーションです。 この場合、Remote は R 機能が有効になっている SQL Server ます。 

コンピューティングコンテキストの切り替えは、ローカルとリモートの両方のシステムで同じバージョンの**RevoScaleR**を持つ必要があります。 ローカルワークステーションでは、Microsoft R Client をインストールすることによって、 **RevoScaleR**パッケージと関連プロバイダーを取得できます。

クライアントとサーバーを同じコンピューターに配置する必要がある場合は、"リモート" クライアントから R スクリプトを送信するために、2つ目の Microsoft R ライブラリのセットを必ずインストールしてください。 SQL Server インスタンスのプログラムファイルにインストールされている R ライブラリは使用しないでください。 具体的には、1台のコンピューターを使用している場合は、クライアントとサーバーの操作をサポートするために、両方の場所に**RevoScaleR**ライブラリが必要です。

+ C:\Program Files\Microsoft\R Client\R_SERVER\library\RevoScaleR 
+ C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library\RevoScaleR

クライアント構成の手順については、「 [R 開発用のデータサイエンスクライアントのセットアップ](../r/set-up-a-data-science-client.md)」を参照してください。


## <a name="r-development-tools"></a>R 開発ツール

R 開発者は、通常、Ide を使用して R コードを作成およびデバッグします。 推奨事項をいくつか以下に示します。

- **R Tools for Visual Studio**(RTVS) は、Intellisense、デバッグ、Microsoft R のサポートを提供する無料のプラグインです。これは、R Server と SQL Server Machine Learning Services の両方で使用できます。 ダウンロードするには、「 [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/)」を参照してください。

- **RStudio** は R 開発用の最も一般的な環境の 1 つです。 詳細については、「[https://www.rstudio.com/products/RStudio/](https://www.rstudio.com/products/RStudio/)」を参照してください。

- SQL Server または R クライアントに R をインストールすると、基本的な R ツール (R、RTerm .exe、Rterm .exe) も既定でインストールされます。 IDE をインストールしない場合は、組み込みの R ツールを使用して、このチュートリアルのコードを実行できます。

ローカルコンピューターとリモートコンピューターの両方で**RevoScaleR**が必要であることを思い出してください。 このチュートリアルは、Microsoft R ライブラリがない RStudio またはその他の環境の汎用インストールを使用して完了することはできません。 詳しくは、「 [データ サイエンス クライアントのセットアップ](../r/set-up-a-data-science-client.md)」を参照してください。

## <a name="summary-of-tasks"></a>タスクの概要

+ 最初に、CSV ファイルまたは XDF ファイルからデータを取得します。 RevoScaleR パッケージの関数を[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]使用して、データをにインポートします。
+ モデルのトレーニングとスコア付けは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]コンピューティングコンテキストを使用して実行されます。 
+ **RevoScaleR** functions を使用して[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 、スコア付けの結果を保存する新しいテーブルを作成します。
+ サーバーとローカル計算コンテキストの両方でプロットを作成します。
+ インスタンスで R を実行し[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]て、データベース内のデータに対してモデルをトレーニングします。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]
+ データのサブセットを抽出し、それを XDF ファイルとして保存して、ローカルワークステーションで分析に再利用できるようにします。
+ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]データベースへの ODBC 接続を開いて、スコアリング用の新しいデータを取得します。 スコアリングはローカルワークステーションで実行されます。
+ カスタム R 関数を作成し、サーバーコンピューティングコンテキストで実行してシミュレーションを実行します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [レッスン 1:データベースとアクセス許可の作成](deepdive-work-with-sql-server-data-using-r.md)