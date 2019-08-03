---
title: R での OLAP キューブからのデータの使用
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: ec50ee1b10a51e16b72d7ffc110448dcf016a13f
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714971"
---
# <a name="using-data-from-olap-cubes-in-r"></a>R での OLAP キューブからのデータの使用
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

**Olapr**パッケージは、Machine Learning Server および SQL Server で使用するために Microsoft によって提供される R パッケージであり、MDX クエリを実行して OLAP キューブからデータを取得できます。 このパッケージでは、リンクサーバーを作成したり、フラット化された行セットをクリーンアップしたりする必要はありません。R から直接 OLAP データを取得できます。

この記事では、多次元キューブデータベースを初めて使用する場合の OLAP および MDX の概要と共に、API について説明します。

> [!IMPORTANT]
> Analysis Services のインスタンスは、従来の多次元キューブまたはテーブルモデルのいずれかをサポートできますが、インスタンスは両方の種類のモデルをサポートできません。 したがって、キューブに対して MDX クエリを作成する前に、Analysis Services インスタンスに多次元モデルが含まれていることを確認してください。

## <a name="what-is-an-olap-cube"></a>OLAP キューブとは

OLAP はオンライン分析処理では短時間です。 OLAP ソリューションは、時間の経過と共に重要なビジネスデータをキャプチャして格納するために広く使用されています。 OLAP データは、多様なツール、ダッシュボード、視覚エフェクトでビジネス分析に使用されます。 詳細については、「[オンライン分析処理](https://en.wikipedia.org/wiki/Online_analytical_processing)」を参照してください。

Microsoft には[Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services)が用意されています。この機能を使用すると、_キューブ_または_テーブルモデル_の形式で OLAP データを設計、配置、およびクエリできます。 キューブは多次元データベースです。 _ディメンション_は、データのファセット、または R の要因に似ています。ディメンションを使用して、集計または分析するデータの特定のサブセットを特定します。 たとえば、time は重要なディメンションであるため、多くの OLAP ソリューションには、データをスライスして集計するときに使用する複数のカレンダーが既定で定義されています。 

OLAP データベースは、パフォーマンス上の理由から、事前に集計 (または_集計_) を事前に計算し、より高速な取得のために保存します。 集計は、数値データに適用できる数式を表す*メジャー*に基づいています。 ディメンションを使用してデータのサブセットを定義し、そのデータに対してメジャーを計算します。 たとえば、メジャーを使用して、複数の四半期を差し引いた特定の製品ラインの売上合計を計算し、特定の供給業者の平均出荷コストを報告したり、支払い済みの年度累計を報告したりすることができます。

MDX (多次元式の場合) は、キューブのクエリに使用される言語です。 MDX クエリには、通常、1つ以上のディメンションと1つ以上のメジャーを含むデータ定義が含まれていますが、MDX クエリはかなり複雑になり、ローリングウィンドウ、累積平均、合計、順位、またはパーセンタイルが含まれます。 

MDX クエリの作成を開始する際に役立つ可能性があるその他の用語を次に示します。

+ *スライス*は、1つのディメンションの値を使用して、キューブのサブセットを取得します。

+ *ダイス* は、複数のディメンションに対して値の範囲を指定してサブキューブを作成することです。

+ *ドリルダウン* は、概要から詳細情報に移動することです。

+ *ドリルアップ* は、集計の詳細情報から概要に移動することです。

+ *ロールアップ* は、ディメンションに関するデータを要約することです。

+ *ピボット* は、キューブまたはデータの選択を回転させることです。

## <a name="how-to-use-olapr-to-create-mdx-queries"></a>OlapR を使用して MDX クエリを作成する方法

次の記事では、キューブに対してクエリを作成または実行するための構文の詳細な例を示します。

+ [R を使用して MDX クエリを作成する方法](../../advanced-analytics/r/how-to-create-mdx-queries-using-olapr.md)

## <a name="olapr-api"></a>olapR API

**olapR** パッケージでは、MDX クエリを作成する方法が 2 つサポートされています。

- **MDX ビルダーを使用します。** パッケージ内の R 関数を使用して、キューブを選択し、軸とスライサーを設定することにより、単純な MDX クエリを生成します。 これは、従来の OLAP ツールにアクセスできない場合や、MDX 言語に関する深い知識がない場合に、有効な MDX クエリを作成するための簡単な方法です。

    MDX は複雑になる可能性があるため、この方法を使用してすべての MDX クエリを作成することはできません。 ただし、この API では、スライス、ダイス、ドリルダウン、ロールアップ、ピボット (N ディメンション) など、最も一般的で便利な操作の大部分がサポートされています。

+ **整形式の MDX をコピーして貼り付けます。** 任意の MDX クエリを手動で作成して貼り付けます。 このオプションは、既存の MDX クエリを再利用する場合、または構築するクエリが複雑すぎて**Olapr**が処理できない場合に最適です。

    SSMS や Excel などの任意のクライアントユーティリティを使用して MDX を構築した後、クエリ文字列を保存します。 この MDX 文字列を、 **Olapr**パッケージの*SSAS クエリハンドラー*の引数として指定します。 プロバイダーは、指定された Analysis Services サーバーにクエリを送信し、結果を R に戻します。 

MDX クエリを作成する方法、または既存の MDX クエリを実行する方法の例については、「 [R を使用して mdx クエリを作成する方法](../../advanced-analytics/r/how-to-create-mdx-queries-using-olapr.md)」を参照してください。

## <a name="known-issues"></a>既知の問題

ここでは、 **Olapr**パッケージに関する既知の問題と一般的な質問をいくつか紹介します。

### <a name="tabular-model-support"></a>テーブルモデルのサポート

テーブルモデルを含む Analysis Services のインスタンスに接続する場合、関数は`explore` 、戻り値が TRUE の成功を報告します。 ただし、テーブルモデルオブジェクトは多次元オブジェクトとは異なり、多次元データベースの構造はテーブルモデルの構造とは異なります。

DAX (データ分析式) は通常、テーブルモデルで使用される言語ですが、MDX について既によく理解している場合は、テーブルモデルに対して有効な MDX クエリをデザインできます。 OlapR コンストラクターを使用して、テーブルモデルに対する有効な MDX クエリを作成することはできません。

ただし、MDX クエリは、テーブルモデルからデータを取得する非効率的な方法です。 R で使用するためにテーブルモデルからデータを取得する必要がある場合は、代わりに次の方法を検討してください。

+ モデルで DirectQuery を有効にし、SQL Server のリンクサーバーとしてサーバーを追加します。 
+ テーブルモデルがリレーショナルデータマート上に構築されている場合は、ソースから直接データを取得します。

### <a name="how-to-determine-whether-an-instance-contains-tabular-or-multidimensional-models"></a>インスタンスにテーブルモデルまたは多次元モデルが含まれているかどうかを確認する方法

1つの Analysis Services インスタンスには、1種類のモデルのみを含めることができますが、複数のモデルを含めることができます。 テーブルモデルと多次元モデルの間には、データの格納と処理の方法を制御する基本的な違いがあるためです。 たとえば、テーブルモデルはメモリに格納され、列ストアインデックスを利用して非常に高速な計算を実行します。 多次元モデルでは、データはディスクに格納され、集計は事前に定義され、MDX クエリを使用して取得されます。

SQL Server Management Studio などのクライアントを使用して Analysis Services に接続する場合、サポートされているモデルの種類を一目で確認できます。そのためには、データベースのアイコンを参照してください。

また、サーバーのプロパティを表示および照会して、インスタンスでサポートされているモデルの種類を確認することもできます。 **サーバーモード**プロパティでは、_多次元_または_テーブル_の2つの値がサポートされます。

2種類のモデルに関する一般的な情報については、次の記事を参照してください。

+ [多次元モデルとテーブルモデルの比較](https://docs.microsoft.com/sql/analysis-services/comparing-tabular-and-multidimensional-solutions-ssas)

サーバーのプロパティのクエリの詳細については、次の記事を参照してください。

+ [OLE DB for OLAP Schema 行セット](https://docs.microsoft.com/bi-reference/schema-rowsets/ole-db-olap/ole-db-for-olap-schema-rowsets)

### <a name="writeback-is-not-supported"></a>書き戻しはサポートされていません

カスタム R 計算の結果をキューブに書き戻すことはできません。

一般に、キューブで書き戻しが有効になっている場合でも、制限付きの操作のみがサポートされ、追加の構成が必要になることがあります。 このような操作には MDX を使用することをお勧めします。

+ [書き込み許可ディメンション](https://docs.microsoft.com/sql/analysis-services/multidimensional-models-olap-logical-dimension-objects/write-enabled-dimensions)
+ [書き込み許可パーティション](https://docs.microsoft.com/sql/analysis-services/multidimensional-models-olap-logical-cube-objects/partitions-write-enabled-partitions)
+ [セルデータへのカスタムアクセスの設定](https://docs.microsoft.com/sql/analysis-services/multidimensional-models/grant-custom-access-to-cell-data-analysis-services)

### <a name="long-running-mdx-queries-block-cube-processing"></a>実行時間の長い MDX クエリは、キューブの処理をブロックします。

**Olapr**パッケージは読み取り操作のみを実行しますが、実行時間の長い MDX クエリでは、キューブが処理されないようにするロックを作成できます。 返されるデータの量を把握できるように、常に MDX クエリを事前にテストしてください。

ロックされているキューブに接続しようとすると、SQL Server データウェアハウスに到達できないというエラーが表示されることがあります。 推奨される解決策としては、リモート接続の有効化、サーバーまたはインスタンス名の確認などがあります。ただし、以前に開いていた接続の可能性を考慮してください。

SSAS 管理者は、開いているセッションを識別して終了することによって、ロックの問題を回避できます。 タイムアウトプロパティは、サーバーレベルで MDX クエリに適用して、実行時間の長いすべてのクエリを強制的に終了することもできます。

## <a name="resources"></a>リソース

OLAP または MDX クエリを初めて使用する場合は、次の Wikipedia の記事を参照してください。 

+ [OLAP キューブ](https://en.wikipedia.org/wiki/OLAP_cube)
+ [MDX クエリ](https://en.wikipedia.org/wiki/MultiDimensional_eXpressions)
