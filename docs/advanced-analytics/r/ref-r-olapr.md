---
title: olapR R 関数ライブラリ
description: SQL Server 2016 R Services の olapR 関数ライブラリの概要と、R を使用した SQL Server Machine Learning Services について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 12/04/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 507bd04140880a3c15f1e72eed49c29ade56769c
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715006"
---
# <a name="olapr-r-library-in-sql-server"></a>olapR (SQL Server の R ライブラリ)
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

**Olapr**は、SQL Server Analysis Services OLAP キューブに対して MDX クエリを実行するために使用される R 関数の Microsoft ライブラリです。 関数は、すべての MDX 操作をサポートするわけではありませんが、ディメンションに対してスライス、ダイス、ドリルダウン、ロールアップ、およびピボットを行うクエリを作成できます。 

このパッケージは、R セッションに事前に読み込まれていません。 次のコマンドを実行して、ライブラリを読み込みます。

```R
library(olapR)
```

このライブラリは、サポートされているすべてのバージョンの SQL Server で Analysis Services OLAP キューブに接続するときに使用できます。 現時点では、表形式モデルへの接続はサポートされていません。

## <a name="package-version"></a>パッケージ バージョン

現在のバージョンは、すべての Windows のみの製品およびライブラリを提供するダウンロードで1.0.0 です。

## <a name="full-reference-documentation"></a>完全なリファレンスドキュメント

**Olapr**ライブラリは複数の Microsoft 製品に配布されていますが、SQL Server または別の製品でライブラリを入手した場合でも、使用方法は同じです。 関数は同じであるため、[個々の sqlrutils 関数のドキュメント](https://docs.microsoft.com/machine-learning-server/r-reference/olapr/olapr)は、Microsoft Machine Learning Server の[R リファレンス](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)の下にある1つの場所にのみ発行されます。 製品固有の動作が存在する場合、関数のヘルプページには相違点が示されます。

## <a name="availability-and-location"></a>可用性と場所

このパッケージは、Azure 上の複数の仮想マシンイメージだけでなく、次の製品にも用意されています。 パッケージの場所は、それに応じて異なります。

製品 | Location |
--------|----------|
SQL Server Machine Learning Services (R 統合あり) | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library | 
SQL Server 2016 R Services | C:\Program Server\MSSQL13. SQLMSSQLSERVER\R_SERVICES\library
Microsoft Machine Learning Server (R Server) | C:\Program Files\Microsoft\R_SERVER\library |
Microsoft R Client | C:\Program Files\Microsoft\R Client\r (_s) ライブラリ |
Data Science Virtual Machine (Azure) | C:\Program Files\Microsoft\R Client\r (_s) ライブラリ |
SQL Server 仮想マシン (Azure) <sup>1</sup> | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library |

<sup>1</sup> R の統合は SQL Server では省略可能です。 OlapR ライブラリは、VM 構成中に Machine Learning または R 機能を追加するとインストールされます。


## <a name="see-also"></a>関連項目

[OlapR を使用して MDX クエリを作成する方法](how-to-create-mdx-queries-using-olapr.md)
