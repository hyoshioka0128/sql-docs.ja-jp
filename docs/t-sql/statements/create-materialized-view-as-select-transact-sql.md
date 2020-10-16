---
description: CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)
title: CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/04/2020
ms.prod: sql
ms.prod_service: sql-data-warehouse
ms.reviewer: jrasnick
ms.technology: data-warehouse
ms.topic: language-reference
f1_keywords:
- CREATE VIEW
- VIEW_TSQL
- VIEW
- CREATE_VIEW_TSQL
- SCHEMABINDING_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- table creation [SQL Server], CREATE VIEW
- views [SQL Server], creating
- CREATE VIEW statement
- updatable partitioned views
- tables [SQL Server], virtual
- updatable views
- modifying partitioned views
- virtual tables [SQL Server]
- number of columns per view
- partitioned views [SQL Server], creating
- WITH ENCRYPTION clause
- WITH CHECK OPTION clause
- partitioned views [SQL Server], modifying
- partitioned views [SQL Server], replication
- distributed partitioned views [SQL Server]
- views [SQL Server], indexed views
- maximum number of columns per view
ms.assetid: aecc2f73-2ab5-4db9-b1e6-2f9e3c601fb9
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: =azure-sqldw-latest||=sqlallproducts-allversions
ms.openlocfilehash: bd7a5056761f6b249caea00463637c90f8ba5a49
ms.sourcegitcommit: 2f868a77903c1f1c4cecf4ea1c181deee12d5b15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671155"
---
# <a name="create-materialized-view-as-select-transact-sql"></a>CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)  

[!INCLUDE [asa](../../includes/applies-to-version/asa.md)]

この記事では、ソリューション開発用の [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] の CREATE MATERIALIZED VIEW AS SELECT T-SQL ステートメントについて説明します。 この記事には、コード例も記載されています。

Materialized View では、ビュー定義クエリから返されるデータを保持し、基になるテーブルのデータが変更されると自動的に更新されます。   これによって、複雑なクエリ (一般に結合と集計を含むクエリ) のパフォーマンスが向上すると共に、メンテナンス操作が簡単になります。   実行プランの自動一致機能により、置換するビューをオプティマイザーで検討する際、マテリアライズドビューをクエリで参照する必要はありません。  この機能により、データ エンジニアは、クエリを変更せずにクエリの応答時間を短縮するメカニズムとして、マテリアライズドビューを実装できます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```syntaxsql
CREATE MATERIALIZED VIEW [ schema_name. ] materialized_view_name
    WITH (  
      <distribution_option>
    )
    AS <select_statement>
[;]

<distribution_option> ::=
    {  
        DISTRIBUTION = HASH ( distribution_column_name )  
      | DISTRIBUTION = ROUND_ROBIN  
    }

<select_statement> ::=
    SELECT select_criteria
```

[!INCLUDE[synapse-analytics-od-unsupported-syntax](../../includes/synapse-analytics-od-unsupported-syntax.md)]
  
## <a name="arguments"></a>引数

*schema_name*     
 ビューが所属するスキーマの名前を指定します。  
  
*materialized_view_name*   
ビューの名前です。 ビュー名は、識別子のルールに従っている必要があります。 ビューの所有者名の指定は省略可能です。  

*distribution option*     
サポートされる分布は、HASH および ROUND_ROBIN のみです。

*select_statement*   
マテリアライズドビューの定義の SELECT リストでは、以下の 2 つの条件の 1 つ以上が満たされている必要があります。
- SELECT リストに集計関数が含まれています。
- マテリアライズドビューの定義で GROUP BY が使用されており、GROUP BY 内のすべての列が SELECT リストに含まれています。  GROUP BY 句では、最大 32 列使用できます。

マテリアライズドビューの定義の SELECT リストには、集計関数が必要です。  サポートされる集計には、MAX、MIN、AVG、COUNT、COUNT_BIG、SUM、VAR、STDEV が含まれます。

マテリアライズドビューの定義の SELECT リストで MIN/MAX 集計が使用される場合、以下の要件が適用されます。
 
- FOR_APPEND が必要です。  次に例を示します。
  ```sql 
  CREATE MATERIALIZED VIEW mv_test2  
  WITH (distribution = hash(i_category_id), FOR_APPEND)  
  AS
  SELECT MAX(i.i_rec_start_date) as max_i_rec_start_date, MIN(i.i_rec_end_date) as min_i_rec_end_date, i.i_item_sk, i.i_item_id, i.i_category_id
  FROM syntheticworkload.item i  
  GROUP BY i.i_item_sk, i.i_item_id, i.i_category_id
  ```

- 参照されるベース テーブルで UPDATE または DELETE が実行されると、マテリアライズドビューが無効になります。  この制限は、INSERT には適用されません。  マテリアライズドビューを再度有効にするには、REBUILD を指定して ALTER MATERIALIZED VIEW を実行します。
  
## <a name="remarks"></a>注釈

Azure データ ウェアハウスのマテリアライズドビューは、SQL Server のインデックス付きビューに似ています。マテリアライズドビューで集計関数がサポートされる点を除き、インデックス付きビューとほぼ同じ制限が共有されています (詳細については、「[Create Indexed Views (インデックス付きビューを作成する)](/sql/relational-databases/views/create-indexed-views)」 を参照してください)。   

マテリアライズドビューでは、CLUSTERED COLUMNSTORE INDEX のみがサポートされます。 

マテリアライズドビューでは、他のビューを参照できません。  

DDM 列がマテリアライズドビューの一部でない場合でも、動的データ マスク (DDM) を使用するテーブルにマテリアライズドビューを作成することはできません。  テーブル列が、アクティブなマテリアライズドビューの一部であるか、無効なマテリアライズドビューの一部である場合、DDM をこの列に追加することはできません。  

行レベルのセキュリティが有効になっているテーブルにはマテリアライズドビューを作成できません。

マテリアライズドビューは、パーティション テーブル上で作成できます。  パーティションの SPLIT/MERGE は、マテリアライズドビューのベース テーブルでサポートされていますが、パーティションの SWITCH はサポートされていません。  
 
マテリアライズドビューで参照されるテーブル上では、ALTER TABLE SWITCH はサポートされません。 ALTER TABLE SWITCH を使用する前に、マテリアライズドビューを無効にするか、ドロップします。 以下のシナリオでは、マテリアライズドビューを作成する際、このビューに新しい列を追加する必要があります。

|シナリオ|マテリアライズドビューに追加する新しい列|コメント|  
|-----------------|---------------|-----------------|
|COUNT_BIG() is missing in the SELECT list of a materialized view definition|マテリアライズドビューを作成する際に自動的に追加されます。  ユーザーによる操作は不要です。|
|SUM(a) is specified by users in the SELECT list of a materialized view definition AND 'a' is a nullable expression |ユーザーは、マテリアライズドビューの定義で式 "a" を手動で追加する必要があります。|
|AVG(a) is specified by users in the SELECT list of a materialized view definition where 'a' is an expression.|SUM(a), COUNT_BIG(a)|マテリアライズドビューを作成する際に自動的に追加されます。  ユーザーによる操作は不要です。|
|STDEV(a) is specified by users in the SELECT list of a materialized view definition where 'a' is an expression.|SUM(a), COUNT_BIG(a), SUM(square(a))|マテリアライズドビューを作成する際に自動的に追加されます。  ユーザーによる操作は不要です。 |
| | | |

マテリアライズドビューは、一度作成されると、SQL Server Management Studio 内の [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] インスタンスのビュー フォルダーの下に表示されます。

ユーザーは [SP_SPACEUSED](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql?view=azure-sqldw-latest) および [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?view=azure-sqldw-latest) を実行して、マテリアライズドビューによって使用されている領域を確認できます。  

マテリアライズドビューは、DROP VIEW でドロップできます。  ALTER MATERIALIZED VIEW を使用して、マテリアライズドビューを無効にしたり、リビルドしたりできます。   

SQL Server Management Studio 内の説明プランとグラフィカルな推定実行プランを見ると、クエリ実行の際、マテリアライズドビューがクエリ オプティマイザーで考慮されるかどうかがわかります。 SQL Server Management Studio 内のグラフィカルな推定実行プランを見ると、クエリ実行の際、マテリアライズドビューがクエリ オプティマイザーで考慮されるかどうかがわかります。

新しいマテリアライズドビューから SQL ステートメントでメリットが得られるかどうかを確認するには、`WITH_RECOMMENDATIONS` を指定して `EXPLAIN` コマンドを実行します。  詳細については、「[EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest)」を参照してください。

## <a name="permissions"></a>アクセス許可

ビューが作成されているスキーマに対する 1) REFERENCES と CREATE VIEW アクセス許可、または 2) CONTROL アクセス許可が必要です。 

  
## <a name="see-also"></a>関連項目

[Materialized View を使用したパフォーマンス チューニング](/azure/sql-data-warehouse/performance-tuning-materialized-views)   
[ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest)      
[DROP VIEW](/sql/t-sql/statements/drop-view-transact-sql?view=azure-sqldw-latest)  
[EXPLAIN &#40;Transact-SQL&#41;](/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest)   
[sys.pdw_materialized_view_column_distribution_properties &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql?view=azure-sqldw-latest)   
[sys.pdw_materialized_view_distribution_properties &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql?view=azure-sqldw-latest)   
[sys.pdw_materialized_view_mappings &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql?view=azure-sqldw-latest)   
[DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)   
[[!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] と [!INCLUDE[ssPDW](../../includes/sspdw-md.md)] カタログ ビュー](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)   
[Azure [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でサポートされているシステム ビュー](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views)   
[Azure [!INCLUDE[ssSDW](../../includes/sssdwfull-md.md)] でサポートされている T-SQL ステートメント](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)
