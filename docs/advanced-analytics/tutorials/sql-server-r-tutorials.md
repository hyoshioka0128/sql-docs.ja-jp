---
title: SQL Server R のチュートリアルの概要
description: SQL Server database analytics の R 言語チュートリアルの概要。
ms.prod: sql
ms.technology: machine-learning
ms.date: 12/18/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: ff1027a3a791ef0151e61982445cafff7be40329
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715423"
---
# <a name="sql-server-r-language-tutorials"></a>SQL Server R 言語のチュートリアル
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、 [SQL Server 2016 r Services](../install/sql-r-services-windows-install.md)または[SQL Server Machine Learning Services](../install/sql-machine-learning-services-windows-install.md)でのデータベース内分析の r 言語チュートリアルについて説明します。

+ ストアドプロシージャで R コードをラップして実行する方法について説明します。
+ R ベースのモデルをシリアル化し、SQL Server データベースに保存します。
+ リモートとローカルの計算コンテキスト、およびそれらを使用するタイミングについて説明します。
+ データサイエンスと機械学習のタスクについては、Microsoft R ライブラリを参照してください。

<a name="bkmk_sqltutorials"></a>

## <a name="r-quickstarts-and-tutorials"></a>R のクイックスタートとチュートリアル

| リンク | 説明 |
|------|-------------|
| [クイック スタート:T-sql での R の使用](rtsql-using-r-code-in-transact-sql-quickstart.md) | まず、いくつかのクイックスタートでは、SQL Server Management Studio などの T-sql クエリエディターを使用して R 関数を呼び出すための基本的な構文を紹介します。 |
| [チュートリアル: データ科学者向けのデータベース内 R 分析について学習する](../tutorials/walkthrough-data-science-end-to-end-walkthrough.md) | SQL Server を初めて使用する R 開発者向けに、このチュートリアルでは SQL Server で一般的なデータサイエンスタスクを setvalue する方法について説明します。 データを読み込んで視覚化し、モデルをトレーニングして SQL Server に保存し、予測分析にモデルを使用します。 |
| [チュートリアル: SQL 開発者向けのデータベース内 R 分析について学習する](../tutorials/sqldev-in-database-r-for-sql-developers.md) | ツールのみ[!INCLUDE[tsql](../../includes/tsql-md.md)]を使用して、完全な R ソリューションを構築してデプロイします。 ソリューションを運用環境に移行することに重点を置いています。 ストアド プロシージャに R コードをラップし、R モデルを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベースに保存し、パラメーター化された呼び出しを R モデルに行い、予測を実行する方法について学習します。 |
| [チュートリアル: RevoScalepR の詳細](deepdive-data-science-deep-dive-using-the-revoscaler-packages.md) | RevoScaleR パッケージで関数を使用する方法について説明します。 R と SQL Server 間でデータを移動し、特定のタスクに合わせて計算コンテキストを切り替えます。 モデルとプロットを作成し、開発環境とデータベースサーバーの間で移動します。 |

<a name ="bkmk_samples"></a>

## <a name="code-samples"></a>コード サンプル

| リンク | 説明 |
|------|-------------|
| [R と SQL Server を使用した予測モデルの作成](https://microsoft.github.io/sql-ml-tutorials/R/rentalprediction) | Ski レンタル企業が機械学習を使用して将来のレンタルを予測する方法について説明します。これにより、ビジネスプランやスタッフが将来の需要に対応できるようになります。 |
| [R と SQL Server を使用して顧客のクラスタリングを実行する](https://microsoft.github.io/sql-ml-tutorials/R/customerclustering/) | 教師なし learning を使用して、売上データに基づいて顧客をセグメント化します。 |

## <a name="see-also"></a>関連項目

+ [R 拡張機能を SQL Server](../concepts/extension-r.md)
+ [SQL Server Machine Learning Services チュートリアル](machine-learning-services-tutorials.md)

