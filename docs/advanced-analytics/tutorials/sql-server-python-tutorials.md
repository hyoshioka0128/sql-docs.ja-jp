---
title: SQL Server 2017 Python チュートリアルの概要
description: SQL Server 2017 でのデータベース内分析に関する Python チュートリアルの概要を説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 12/18/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 65dd2197d9fb079da116908871d931ad19370cf5
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714768"
---
# <a name="sql-server-2017-python-tutorials"></a>SQL Server 2017 Python チュートリアル
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、 [SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)でのデータベース内分析の Python チュートリアルについて説明します。 

+ ストアドプロシージャで Python コードをラップして実行する方法について説明します。
+ Python ベースのモデルをシリアル化し、SQL Server データベースに保存します。
+ リモートとローカルの計算コンテキスト、およびそれらを使用するタイミングについて説明します。
+ データサイエンスと機械学習のタスクについては、Microsoft Python モジュールを参照してください。

<a name="bkmk_pythontutorials"></a>

## <a name="python-quickstarts-and-tutorials"></a>Python のクイックスタートとチュートリアル

| リンク | 説明 |
|------|-------------|
| [クイック スタート:SQL Server の "Hello world" Python スクリプト](quickstart-python-run-using-t-sql.md) | T-sql で Python を呼び出す方法の基本について説明します。 |
| [クイック スタート:ストアドプロシージャを使用した Python モデルの作成、トレーニング、および使用 SQL Server](quickstart-python-train-score-in-tsql.md) | ストアドプロシージャへの Python コードの埋め込み、入力、およびストアドプロシージャの実行のしくみについて説明します。 |
| [チュートリアル: Revoscalepy を使用してモデルを作成する](use-python-revoscalepy-to-create-model.md) | SQL Server のコンピューティングコンテキストを使用してリモート Python ターミナルからコードを実行する方法を示します。 Python のツールと環境についてよく理解している必要があります。 新しい**revoscalepy**ライブラリから**rxLinMod**を使用してモデルを作成するサンプルコードが用意されています。 |
| [チュートリアル: SQL 開発者向けのデータベース内 Python analytics について学習する](sqldev-in-database-python-for-sql-developers.md) | このエンドツーエンドチュートリアルでは、T-sql ストアドプロシージャを使用して完全な Python ソリューションを構築するプロセスについて説明します。 すべての Python コードが含まれています。|

<a name ="bkmk_samples"></a>

## <a name="code-samples"></a>コード サンプル

SQL Server 開発チームが提供するこれらのサンプルとデモは、実際のアプリケーションで埋め込み分析を使用する方法を示しています。

| リンク | 説明 |
|------|-------------|
| [Python と SQL Server を使用した予測モデルの作成](https://microsoft.github.io/sql-ml-tutorials/python/rentalprediction/) | Ski レンタル企業が機械学習を使用して将来のレンタルを予測する方法について説明します。これにより、ビジネスプランやスタッフが将来の需要に対応できるようになります。 |
| [Python と SQL Server を使用して顧客のクラスタリングを実行する](https://microsoft.github.io/sql-ml-tutorials/python/customerclustering/) | Kmeans アルゴリズムを使用して顧客のクラスタリングを実行する方法について説明します。 |

## <a name="see-also"></a>関連項目

+ [SQL Server するための Python 拡張機能](../concepts/extension-python.md)
+ [SQL Server Machine Learning Services チュートリアル](machine-learning-services-tutorials.md)
