---
title: Python プログラミング言語拡張機能
description: SQL Server Machine Learning Services の Python コード実行と組み込み Python ライブラリについて説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/05/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 61a1a5629d4f0488b5f75a08578c39f2e68f2c7d
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715870"
---
# <a name="python-language-extension-in-sql-server"></a>SQL Server の Python 言語拡張機能
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

Python 拡張機能は、リレーショナルデータベースエンジンにアドオン Machine Learning Services SQL Server の一部です。 Python の実行環境、python 3.5 ランタイムとインタープリターを使用した Anaconda ディストリビューション、標準ライブラリとツール、Python 用の Microsoft 製品ライブラリ (大規模な分析と Microsoft [ml](../python/ref-py-microsoftml.md)用の[revoscalepy](../python/ref-py-revoscalepy.md) ) を追加します。機械学習アルゴリズム。 

Python 統合は[SQL Server Machine Learning Services](../what-is-sql-server-machine-learning.md)としてインストールされます。

Python 3.5 ランタイムとインタープリターのインストールにより、標準的な Python ソリューションとのほぼ完全な互換性が保証されます。 Python は SQL Server とは別のプロセスで実行されるので、データベース操作が損なわれないようにすることができます。

## <a name="python-components"></a>Python コンポーネント

SQL Server には、オープンソースパッケージと専用パッケージの両方が含まれています。 セットアップによってインストールされる Python ランタイムは、Python 3.5 の Anaconda 4.2 です。 Python ランタイムは、SQL ツールとは別にインストールされ、機能拡張フレームワークのコアエンジンプロセスの外部で実行されます。 Python を使用した Machine Learning Services のインストールの一環として、GNU パブリックライセンスの条項に同意する必要があります。 

SQL Server は Python 実行可能ファイルを変更しませんが、そのバージョンが、独自のパッケージがビルドおよびテストされているバージョンであるため、セットアップによってインストールされた Python のバージョンを使用する必要があります。 Anaconda 分布でサポートされているパッケージの一覧については、「連続分析サイト:[Anaconda パッケージの一覧](https://docs.continuum.io/anaconda/packages/pkg-docs)。

特定のデータベースエンジンインスタンスに関連付けられている Anaconda ディストリビューションは、インスタンスに関連付けられているフォルダーにあります。 たとえば、既定のインスタンスに Machine Learning Services と Python を含む SQL Server 2017 データベースエンジンをインストールした場合は`C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES`、「」を参照してください。

Microsoft が並行して分散したワークロードに対して追加する Python パッケージには、次のライブラリが含まれます。

| ライブラリ | 説明 |
|---------|-------------|
| [**revoscalepy**](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) | では、データソースオブジェクトとデータの探索、操作、変換、および視覚化がサポートされています。 リモートコンピューティングコンテキストの作成、および**rxLinMod**などのさまざまなスケーラブルな機械学習モデルの作成をサポートしています。 詳細については、「 [revoscalepy module with SQL Server](../python/ref-py-revoscalepy.md)」を参照してください。  |
| [**microsoftml**](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) | 速度と精度のために最適化された機械学習アルゴリズムに加え、テキストとイメージを操作するためのインライン変換も含まれています。 詳細については、「 [SQL Server を使用した microsoft ml モジュール](../python/ref-py-microsoftml.md)」を参照してください。 |

Microsoft ml と revoscalepy は密結合されています。microsoft ml で使用されるデータソースは、revoscalepy オブジェクトとして定義されます。 Revoscalepy を microsoft ml に転送する場合の計算コンテキストの制限。 つまり、ローカル操作ではすべての機能を使用できますが、リモートの計算コンテキストに切り替えるには RxInSqlServer が必要です。

## <a name="using-python-in-sql-server"></a>SQL Server での Python の使用

Python コードに**revoscalepy**モジュールをインポートし、他の python 関数と同様に、モジュールから関数を呼び出します。

サポートされているデータソースには、ODBC データベース、SQL Server、および他のソースとデータを交換するための XDF ファイル形式、または R ソリューションがあります。 Python の入力データは表形式である必要があります。 すべての Python 結果は、**パンダ**のデータフレームの形で返される必要があります。

サポートされているコンピューティングコンテキストには、ローカルまたはリモートの SQL Server 計算コンテキストがあります。 リモート計算コンテキストとは、ワークステーションなどの1台のコンピューターで起動するコード実行を指しますが、その後、スクリプトの実行をリモートコンピューターに切り替えます。 コンピューティングコンテキストを切り替えるには、両方のシステムの revoscalepy ライブラリが同じである必要があります。

ローカルの計算コンテキストでは、データベースエンジンのインスタンスと同じサーバー上で Python コードを実行することができます。これには、T-sql 内のコードまたはストアドプロシージャへの埋め込みが含まれます。 ローカルの Python IDE からコードを実行し、リモートの計算コンテキストを定義することで、SQL Server コンピューターでスクリプトを実行することもできます。

## <a name="execution-architecture"></a>実行アーキテクチャ

次の図は、サポートされている各シナリオにおける、SQL Server コンポーネントと Python ランタイムの相互作用を示しています。各シナリオでは、データベース内でのスクリプトの実行と Python ターミナルからのリモート実行 (SQL Server 計算コンテキストを使用) です。

### <a name="python-scripts-executed-in-database"></a>データベースで実行される Python スクリプト

Python の "内部" SQL Server を実行する場合は、Python スクリプトを特殊なストアドプロシージャ[sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)内にカプセル化する必要があります。

スクリプトをストアドプロシージャに埋め込んだ後、ストアドプロシージャ呼び出しを行うことができるすべてのアプリケーションは、Python コードの実行を開始できます。  その後 SQL Server は、次の図に示すようにコードの実行を管理します。

![script-in-db-python](../../advanced-analytics/python/media/script-in-db-python2.png)

1. Python ランタイムの要求は、ストアドプロシージャに渡され`@language='Python'`たパラメーターによって示されます。 SQL Server は、この要求をスタートパッドサービスに送信します。
2. スタートパッドサービスが適切なランチャーを起動します。この例では、Python ランチャーです。
3. Python ランチャーが外部の Python35 プロセスを開始します。
4. BxlServer は、データの交換と作業結果の格納を管理するために Python ランタイムと調整します。
5. SQL サテライトは、SQL Server に関連するタスクとプロセスに関する通信を管理します。
6. BxlServer は、SQL サテライトを使用して状態と結果を SQL Server に伝えます。
7. SQL Server の結果を取得し、関連するタスクとプロセスを終了します。

### <a name="python-scripts-executed-from-a-remote-client"></a>リモートクライアントから実行される Python スクリプト

次の条件が満たされている場合は、ラップトップなどのリモートコンピューターから Python スクリプトを実行して、SQl Server コンピューターのコンテキストで実行することができます。

+ スクリプトを適切にデザインする
+ リモートコンピューターには、Machine Learning Services によって使用される機能拡張ライブラリがインストールされています。 リモートの計算コンテキストを使用するには、 [revoscalepy](../python/ref-py-revoscalepy.md)パッケージが必要です。

次の図は、スクリプトがリモートコンピューターから送信された場合の全体的なワークフローの概要を示しています。

![remote-sqlcc-from-python](../../advanced-analytics/python/media/remote-sqlcc-from-python3.png)

1. **Revoscalepy**でサポートされている関数の場合、Python ランタイムはリンク関数を呼び出し、その関数は BxlServer を呼び出します。
2. BxlServer は Machine Learning Services (データベース内) に含まれており、Python ランタイムとは別のプロセスで実行されます。
3. BxlServer は接続ターゲットを決定し、ODBC を使用して接続を開始し、Python スクリプトの接続文字列の一部として指定された資格情報を渡します。
4. BxlServer は、SQL Server インスタンスへの接続を開きます。
5. 外部スクリプトランタイムが呼び出されると、スタートパッドサービスが呼び出され、その後、適切なランチャー (この場合は、Python ランチャー) が開始されます。 その後、python コードの処理は、T-sql のストアドプロシージャから Python コードが呼び出された場合と同様のワークフローで処理されます。
6. Python ランチャーは、SQL Server コンピューターにインストールされている Python のインスタンスを呼び出します。
7. 結果が BxlServer に返されます。
8. SQL サテライトは、関連するジョブオブジェクトの SQL Server とクリーンアップとの通信を管理します。
9. SQL Server は、結果をクライアントに渡します。

## <a name="see-also"></a>関連項目

+ [SQL Server の revoscalepy モジュール](../python/ref-py-revoscalepy.md)
+ [revoscalepy 関数リファレンス](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package) 
+ [SQL Server の機能拡張フレームワーク](extensibility-framework.md)
+ [SQL Server の R および machine learning 拡張機能](extension-r.md)