---
title: R 言語と Python スクリプトの拡張性アーキテクチャ
description: 外部コードは、リレーショナルデータに対して R および Python スクリプトを実行するためのデュアルアーキテクチャを使用して、SQL Server データベースエンジンをサポートします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 49c45fa39cd271140ba78c2b1b32ee8a2f9c1a7a
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715254"
---
# <a name="extensibility-architecture-in-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services の機能拡張アーキテクチャ 
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server には、サーバー上で R や Python などの外部スクリプトを実行するための機能拡張フレームワークがあります。 スクリプトは、言語ランタイム環境でコアデータベースエンジンの拡張機能として実行されます。 

## <a name="background"></a>背景情報

拡張フレームワークは、R ランタイムをサポートするために SQL Server 2016 で導入されました。 SQL Server 2017 以降では、Python がサポートされています。

拡張性フレームワークの目的は、SQL Server とデータサイエンス言語 (R、Python など) の間にインターフェイスを提供し、データサイエンスソリューションを運用環境に移行するときの混乱を軽減し、開発中に公開されたデータを保護することです。process. SQL Server によって管理されるセキュリティで保護されたフレームワーク内で信頼できるスクリプト言語を実行することで、データベース管理者はセキュリティを維持しながら、データ科学者が企業データにアクセスできるようになります。

次の図は、拡張可能なアーキテクチャの営業案件と利点を視覚的に示しています。

  ![SQL Server との統合の目標](../media/ml-service-value-add.png "Machine Learning Services 値の追加")

ストアドプロシージャを呼び出すと、R または Python スクリプトを実行できます。結果はテーブル結果として直接 SQL Server に返されます。これにより、SQL クエリを送信して結果を処理できるアプリケーションから、機械学習を生成したり、使用したりすることが簡単になります。

+ 外部スクリプトの実行には、SQL Server データセキュリティが適用されます。外部スクリプトを実行するユーザーは、SQL クエリでも同等に使用できるデータにのみアクセスできます。 権限が不十分であるためにクエリが失敗した場合、同じユーザーによって実行されるスクリプトも同じ理由で失敗します。 SQL Server セキュリティは、テーブル、データベース、およびインスタンスレベルで適用されます。 データベース管理者は、ユーザーアクセス、外部スクリプトによって使用されるリソース、およびサーバーに追加された外部コードライブラリを管理できます。  

+ スケールと最適化の機会には、データベースプラットフォーム (列ストアインデックス、[リソース](../../advanced-analytics/r/resource-governance-for-r-services.md)管理) を通じて得られるメリットと、データサイエンスモデルで R および Python 用の Microsoft ライブラリを使用する場合の拡張機能固有の利点があります。 R はシングルスレッドであるのに対し、RevoScaleR 関数はマルチスレッドであり、ワークロードを複数のコアに分散することができます。

+ 配置では SQL Server の方法論を使用します。外部スクリプト、embedded SQL、または T-sql クエリをラップするストアドプロシージャは、予測などの関数を呼び出して、サーバーに保持されている予測モデルの結果を返します。

+ 特定のツールや Ide でスキルを確立した R と Python の開発者は、これらのツールでコードを記述し、SQL Server にコードを移植できます。

## <a name="architecture-diagram"></a>アーキテクチャの図

このアーキテクチャは、外部スクリプトが SQL Server とは別のプロセスで実行されるように設計されていますが、SQL Server のデータと操作に対する要求のチェーンを内部で管理するコンポーネントがあります。 SQL Server のバージョンによっては、サポートされている言語拡張機能に R と Python があります。 

  ![コンポーネントのアーキテクチャ](../media/generic-architecture.png "コンポーネントのアーキテクチャ")

コンポーネントには、言語固有のランチャー (R または Python)、インタープリターとライブラリを読み込むための言語およびライブラリ固有のロジックを呼び出す**スタートパッド**サービスが含まれています。 ランチャーは、言語の実行時間と独自のモジュールを読み込みます。 たとえば、コードに RevoScaleR 関数が含まれている場合、RevoScaleR インタープリターが読み込まれます。 **Bxlserver**と**SQL サテライト**は、SQL Server による通信とデータ転送を管理します。

<a name="launchpad"></a>

## <a name="launchpad"></a>スタート パッド

[!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)]は、フルテキストクエリを処理するために、フルテキストインデックス作成とクエリサービスが個別のホストを起動するのと同様に、外部スクリプトを管理および実行するサービスです。 スタートパッドサービスは、Microsoft によって発行された、または Microsoft によって認定された、パフォーマンスとリソース管理の要件を満たしている、信頼できるランチャーだけを起動できます。

| 信頼できるランチャー | 拡張子 | SQL Server のバージョン |
|-------------------|-----------|---------------------|
| R 言語用の RLauncher .dll | [R 拡張機能](extension-r.md) | SQL Server 2016 以降 |
| Python ランチャー .dll (Python 3.5 用) | [Python 拡張機能](extension-python.md) | SQL Server 2017 以降 |

[!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)] サービスは、独自のユーザー アカウント下で実行されます。 スタートパッドを実行するアカウントを変更する場合は、必ず SQL Server 構成マネージャーを使用して、変更内容が関連ファイルに書き込まれるようにしてください。

SQL Server Machine Learning Services [!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)]を追加したデータベースエンジンインスタンスごとに個別のサービスが作成されます。 データベースエンジンのインスタンスごとに1つのスタートパッドサービスがあるため、外部スクリプトをサポートする複数のインスタンスがある場合は、スタートパッドサービスを使用できます。 データベースエンジンのインスタンスは、それに対して作成されたスタートパッドサービスにバインドされます。 ストアドプロシージャまたは T-sql での外部スクリプトのすべての呼び出しでは、同じインスタンスに対して作成されたスタートパッドサービスを呼び出して、SQL Server サービスになります。

サポートされている特定の言語でタスクを実行するために、スタートパッドは、セキュリティで保護されたワーカーアカウントをプールから取得し、サテライトプロセスを開始して外部ランタイムを管理します。 各サテライトプロセスは、スタートパッドのユーザーアカウントを継承し、スクリプトの実行中にそのワーカーアカウントを使用します。 スクリプトで並列プロセスを使用する場合は、同じ1つのワーカーアカウントで作成されます。

## <a name="bxlserver-and-sql-satellite"></a>BxlServer と SQL サテライト

**Bxlserver**は、Microsoft によって提供される、SQL Server と Python または R 間の通信を管理する実行可能ファイルです。外部スクリプトセッションを格納するために使用される Windows ジョブオブジェクトを作成し、各外部スクリプトジョブに対してセキュリティで保護された作業フォルダーを準備します。また、SQL サテライトを使用して、外部ランタイムと SQL Server 間のデータ転送を管理します。 ジョブの実行中に[プロセスエクスプローラー](https://technet.microsoft.com/sysinternals/processexplorer.aspx)を実行すると、BxlServer の1つまたは複数のインスタンスが表示される場合があります。

実際には、BxlServer は、SQL Server と連携してデータを転送し、タスクを管理する言語ランタイム環境に関連しています。 BXL はバイナリ交換言語を表し、SQL Server と外部プロセス間で効率的にデータを移動するために使用されるデータ形式を表します。 BxlServer は、Microsoft R Client や Microsoft R Server などの関連製品の重要な部分でもあります。

**SQL サテライト**は、データベースエンジンに含まれる拡張 API で、C またはC++を使用して実装された外部コードまたは外部ランタイムをサポートします。

BxlServer は、次のタスクに SQL サテライトを使用します。

+ 入力データの読み取り
+ 出力データの書き込み
+ 入力引数の取得
+ 出力引数の書き込み
+ エラー処理
+ STDOUT と STDERR のクライアントへの書き戻し

SQL サテライトは、SQL Server と外部スクリプト言語間の高速データ転送に最適化されたカスタムデータ形式を使用します。 型変換を実行し、SQL Server と外部スクリプトランタイム間の通信中に入力データセットと出力データセットのスキーマを定義します。

SQL サテライトは、windows 拡張イベント (Xevent) を使用して監視できます。 詳細については、「Python 用の R および[拡張イベント](../../advanced-analytics/python/extended-events-for-python.md)[の拡張イベント](../../advanced-analytics/r/extended-events-for-sql-server-r-services.md)」を参照してください。

## <a name="communication-channels-between-components"></a>コンポーネント間の通信チャネル

このセクションでは、コンポーネントとデータプラットフォーム間の通信プロトコルについて説明します。

+ **TCP/IP**

  既定では、SQL Server と SQL サテライト間の内部通信で TCP/IP が使用されます。

+ **名前付きパイプ**

  SQL サテライトを介した BxlServer と SQL Server の内部データ転送では、独自の圧縮データ形式を使用してパフォーマンスを向上させます。 データは、名前付きパイプを使用して、言語の実行時刻と BXL 形式の BxlServer との間で交換されます。

+ **ODBC**

  外部データサイエンスクライアントとリモート SQL Server インスタンス間の通信には ODBC が使用されます。 スクリプトジョブを SQL Server に送信するアカウントには、インスタンスに接続し、外部スクリプトを実行するための両方の権限が必要です。

  また、タスクによっては、アカウントに次のアクセス許可が必要になる場合があります。

  + ジョブによって使用されるデータの読み取り
  + テーブルへのデータの書き込み: たとえば、結果をテーブルに保存する場合
  + データベースオブジェクトを作成します。たとえば、外部スクリプトを新しいストアドプロシージャの一部として保存する場合などです。

  リモートクライアントから実行されるスクリプトの計算コンテキストとして SQL Server が使用され、実行可能ファイルが外部ソースからデータを取得する必要がある場合は、書き戻しに ODBC が使用されます。 SQL Server は、リモートコマンドを発行しているユーザーの id を現在のインスタンス上のユーザーの id にマップし、そのユーザーの資格情報を使用して ODBC コマンドを実行します。 この ODBC 呼び出しを実行するために必要な接続文字列は、クライアント コードから取得されます。

+ **RODBC (R のみ)** 

  追加の ODBC 呼び出しは、**RODBC** を使用してスクリプト内で実行できます。 RODBC は、リレーショナルデータベースのデータにアクセスするために使用される一般的な R パッケージです。ただし、通常は、SQL Server で使用される同等のプロバイダーよりもパフォーマンスが低下します。 多くの R スクリプトでは、分析に使用する "セカンダリ" データセットを取得するために、RODBC への埋め込み呼び出しが使用されます。 たとえば、モデルをトレーニングするストアド プロシージャでは、モデル トレーニング用のデータを取得するために SQL クエリが定義される場合もありますが、追加要素を取得したり、検索を実行したり、テキスト ファイルや Excel などの外部ソースから新しいデータを取得する場合には、埋め込みの RODBC 呼び出しが使用されます。

  次のコードは、R スクリプトに埋め込まれた RODBC 呼び出しを示したものです。

    ```R
    library(RODBC);
    connStr <- paste("Driver=SQL Server;Server=", instance_name, ";Database=", database_name, ";Trusted_Connection=true;", sep="");
    dbhandle <- odbcDriverConnect(connStr)
    OutputDataSet <- sqlQuery(dbhandle, "select * from table_name");
    ```

+ **その他のプロトコル**

  "チャンク" で動作したり、リモートクライアントにデータを転送したりする必要があるプロセスは、 [Xdf ファイル形式](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-xdf)を使用することもできます。 実際のデータ転送は、エンコードされた blob を使用して行われます。

## <a name="see-also"></a>参照

+ [SQL Server の R 拡張機能](extension-r.md)
+ [SQL Server の Python 拡張機能](extension-python.md)