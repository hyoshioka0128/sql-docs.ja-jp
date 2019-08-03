---
title: R プログラミング言語拡張機能
description: SQL Server R Services または SQL Server Machine Learning Services での R コードの実行と組み込み R ライブラリについて説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: fa39240da51d0b7a9269777f751944104d703d59
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715241"
---
# <a name="r-language-extension-in-sql-server"></a>SQL Server の R 言語拡張機能
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

R 拡張機能は、リレーショナルデータベースエンジンにアドオン Machine Learning Services SQL Server の一部です。 R 実行環境、標準ライブラリとツールを使用した base R ディストリビューション、および Microsoft R ライブラリが追加されます。[RevoScaleR](../r/ref-r-revoscaler.md)は、大規模な分析、機械学習アルゴリズム用の[microsoft ml](../r/ref-r-microsoftml.md) 、SQL Server のデータまたは R コードにアクセスするためのその他のライブラリを対象としています。

R 統合は、 [SQL Server R Services](../r/sql-server-r-services.md)と[SQL Server Machine Learning Services](../what-is-sql-server-machine-learning.md)で利用できます。

## <a name="r-components"></a>R コンポーネント

SQL Server には、オープンソースパッケージと専用パッケージの両方が含まれています。 Base R ライブラリは、Microsoft のオープンソース R の配布によってインストールされます。Microsoft R Open (MRO)。 R の現在のユーザーは、R コードを移植し、SQL Server の外部プロセスとして実行できる必要があります。変更はほとんどありません。 MRO は、SQL ツールとは別にインストールされ、機能拡張フレームワークのコアエンジンプロセスの外部で実行されます。 インストール中に、オープンソースライセンスの条項に同意する必要があります。 その後、R の他のオープンソースディストリビューションの場合と同様に、変更を加えずに標準の R パッケージを実行できます。 

SQL Server では、基本の R 実行可能ファイルは変更されませんが、セットアップによってインストールされたバージョンの R を使用する必要があります。これは、独自のパッケージがビルドおよびテストされているバージョンであるためです。 CRAN によって取得される可能性のある R の基本分布と MRO の違いについては、「 [r 言語と Microsoft r の製品および機能との相互運用性](https://docs.microsoft.com/r-server/what-is-r-server-interoperability)」を参照してください。

セットアップによってインストールされた R base package ディストリビューションは、インスタンスに関連付けられているフォルダーにあります。 たとえば、SQL Server の既定のインスタンスに R Services をインストールした場合、既定`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES\library`では、r ライブラリがこのフォルダーに配置されます。 同様に、既定のインスタンスに関連付けられている R ツールは、既定`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES\bin`ではこのフォルダーに配置されます。

Microsoft によって並列ワークロードと分散ワークロードに追加される R パッケージには、次のライブラリが含まれます。

| ライブラリ | 説明 |
|---------|-------------|
| [**RevoScaleR**](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) | では、データソースオブジェクトとデータの探索、操作、変換、および視覚化がサポートされています。 リモートコンピューティングコンテキストの作成、および**rxLinMod**などのさまざまなスケーラブルな機械学習モデルの作成をサポートしています。 これらの API は、大きすぎてメモリに収まらないデータ セットの分析と複数のコアまたはプロセッサでの分散計算を実行するように最適化されています。 RevoScaleR パッケージでは、分析に使用されるデータをより高速に移動および格納するための XDF ファイル形式もサポートされています。 XDF 形式は、カラム型ストレージを使用する移植可能な形式であり、テキスト、SPSS、ODBC 接続などのさまざまなソースからデータを読み込んで操作するために使用できます。 |
| [**MicrosoftML**](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) | 速度と精度のために最適化された機械学習アルゴリズムに加え、テキストとイメージを操作するためのインライン変換も含まれています。 詳細については、 [SQL Server の「Microsoft ml](../r/ref-r-microsoftml.md)」を参照してください。 | 

## <a name="using-r-in-sql-server"></a>SQL Server での R の使用

基本関数を使用して R のスクリプトを作成できますが、複数の処理を利用するには、 **RevoScaleR**および**microsoft Ml**モジュールを r コードにインポートし、その関数を呼び出して並列に実行するモデルを作成する必要があります。 
 
サポートされているデータソースには、ODBC データベース、SQL Server、および他のソースとデータを交換するための XDF ファイル形式、または R ソリューションがあります。 入力データは表形式である必要があります。 すべての R 結果は、データフレームの形式で返される必要があります。

サポートされているコンピューティングコンテキストには、ローカルまたはリモートの SQL Server 計算コンテキストがあります。 リモート計算コンテキストとは、ワークステーションなどの1台のコンピューターで起動するコード実行を指しますが、その後、スクリプトの実行をリモートコンピューターに切り替えます。 コンピューティングコンテキストを切り替えるには、両方のシステムの RevoScaleR ライブラリが同じである必要があります。

必要に応じて、ローカルの計算コンテキストでは、データベースエンジンインスタンスと同じサーバー上で R コードを実行します。これには、T-sql 内のコード、またはストアドプロシージャへの埋め込みが含まれます。 ローカルの R IDE からコードを実行し、リモートの計算コンテキストを定義することで、SQL Server コンピューターでスクリプトを実行することもできます。

## <a name="execution-architecture"></a>実行アーキテクチャ

次の図は、サポートされている各シナリオにおける SQL Server コンポーネントと R ランタイムの相互作用を示しています。各シナリオでは、データベース内でのスクリプトの実行と R コマンドラインからのリモート実行 (SQL Server 計算コンテキストを使用) です。

### <a name="r-scripts-executed-from-sql-server-in-database"></a>データベース内 SQL Server から実行された R スクリプト

"内部" SQL Server から実行される R コードは、ストアドプロシージャを呼び出すことによって実行されます。 したがって、R コードの実行は、ストアド プロシージャを呼び出す任意のアプリケーションによって開始できます。  その後 SQL Server は、次の図にまとめられている R コードの実行を管理します。

![rsql_indb780-01](../r/media/script_in-db-r.png)

1. ストアド プロシージャ ([sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)) に渡されたパラメーター _@language='R'_ によって、R ランタイムに対する要求が示されます。 SQL Server は、この要求をスタートパッドサービスに送信します。
2. スタート パッド サービスが適切なランチャーを起動します (この場合は RLauncher)。
3. RLauncher が外部の R プロセスを起動します。
4. BxlServer は R ランタイムと連携して、SQL Server のデータ交換を管理し、作業結果を格納します。
5. SQL サテライトは、SQL Server に関連するタスクとプロセスに関する通信を管理します。
6. BxlServer は、SQL サテライトを使用して状態と結果を SQL Server に伝えます。
7. SQL Server の結果を取得し、関連するタスクとプロセスを終了します。

### <a name="r-scripts-executed-from-a-remote-client"></a>リモート クライアントから実行された R スクリプト

Microsoft R をサポートするリモートのデータサイエンスクライアントから接続する場合は、RevoScaleR 関数を使用して SQL Server のコンテキストで R 関数を実行できます。 これは、上記のワークフローとは異なるものです。次の図に概要を示します。

![rsql_fromR2db-01](../r/media/remote-sqlcc-from-r2.png)

1. RevoScaleR 関数の場合、R ランタイムはリンク関数を呼び出し、その関数は BxlServer を呼び出します。
2. BxlServer は Microsoft R と共に提供されているもので、R ランタイムとは別個のプロセスで実行されます。
3. BxlServer は接続のターゲットを決定し、ODBC を使用して接続を開始して、R データ ソース オブジェクト内の接続文字列の一部として提供された資格情報を渡します。
4. BxlServer は、SQL Server インスタンスへの接続を開きます。
5. R 呼び出しの場合は、スタートパッドサービスが呼び出されます。これにより、適切なランチャー (RLauncher) が開始されます。 その後の R コードの処理は、T-SQL から R コードを実行する場合のプロセスと同様です。
6. RLauncher により、SQL Server コンピューターにインストールされている R ランタイムのインスタンスが呼び出されます。
7. 結果が BxlServer に返されます。
8. SQL サテライトは、関連するジョブオブジェクトの SQL Server とクリーンアップとの通信を管理します。
9. SQL Server は、結果をクライアントに渡します。

## <a name="see-also"></a>関連項目

+ [SQL Server の機能拡張フレームワーク](extensibility-framework.md)
+ [SQL Server の Python および machine learning 拡張機能](extension-python.md)