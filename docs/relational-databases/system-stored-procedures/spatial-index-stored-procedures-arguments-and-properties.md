---
title: ストアド プロシージャの引数と空間インデックスのプロパティ |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- spatial indexes [SQL Server], stored procedures
ms.assetid: ee26082b-c0ed-40ff-b5ad-f5f6b00f0475
author: stevestein
ms.author: sstein
ms.openlocfilehash: 7adc7ed5677fdc511f0c8801a5ab56b55d4b9cde
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67950406"
---
# <a name="spatial-index-stored-procedures---arguments-and-properties"></a>空間インデックス ストアド プロシージャの引数とプロパティ
[!INCLUDE[tsql-appliesto-ss2012-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2012-xxxx-xxxx-xxx-md.md)]

  このトピックでは、空間インデックス ストアド プロシージャの引数とプロパティについて説明します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
 特定の空間インデックス ストアド プロシージャの構文は、次のトピックを参照してください。  
  
-   [sp_help_spatial_geometry_index &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geometry-index-transact-sql.md)  
  
-   [sp_help_spatial_geometry_index_xml &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geometry-index-xml-transact-sql.md)  
  
-   [sp_help_spatial_geography_index &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geography-index-transact-sql.md)  
  
-   [sp_help_spatial_geography_index_xml &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geography-index-xml-transact-sql.md)  
  
## <a name="arguments"></a>引数  
`[ @tabname = ] 'tabname'` 空間インデックスが指定されているテーブルの修飾付きまたは修飾なしの名前です。  
  
 引用符は、修飾されたテーブルが指定されている場合にのみ必要です。 データベース名を含む、完全修飾名が指定されている場合、データベース名は、現在のデータベースの名前である必要があります。 *tabname $* は**nvarchar**(776) で、既定値はありません。  
  
`[ @indexname = ] 'indexname'` 指定された空間インデックスの名前です。 *indexname*は**sysname**既定値はありません。  
  
`[ @verboseoutput = ] 'verboseoutput'` プロパティの名前と返される値の範囲です。  
  
 0 = 主要プロパティ  
  
 \>0 = すべてのプロパティ  
  
 *verboseoutput*は**tinyint**既定値はありません。  
  
`[ @query_sample = ] 'query_sample'` インデックスの有用性をテストするために使用できる代表のクエリ サンプルです。 代表のオブジェクトまたはクエリ ウィンドウがあります。 *query_sample*は**geometry**既定値はありません。  
  
`[ @xml_output = ] 'xml_output'` XML フラグメントで結果を返す出力パラメーターが設定されます。 *xml_output*は**xml**既定値はありません。  
  
## <a name="properties"></a>Properties  
 設定 **\@verboseoutput** = 0; 次の表に示すように、コア プロパティを返す **\@verboseoutput** > 0 を空間インデックスのすべてのプロパティを返します。  
  
 **Base_Table_Rows**  
 ベース テーブルの行の数。 値は**bigint**します。  
  
 **Bounding_Box_xmin**  
 X の最小の境界ボックスのプロパティの空間インデックスの**geometry**型。 このプロパティの値は NULL です**geography**型。 値は**float**します。  
  
 **Bounding_Box_ymin**  
 境界ボックスのプロパティの空間インデックスの Y の最小値**geometry**型。 このプロパティの値は NULL です**geography**型。 値は**float**します。  
  
 **Bounding_Box_xmax**  
 境界ボックスのプロパティの空間インデックスの X の最大値**geometry**型。 このプロパティの値は NULL です**geography**型。 値は**float**します。  
  
 **Bounding_Box_ymax**  
 境界ボックスのプロパティの空間インデックスの Y の最大値**geometry**型。 このプロパティの値は NULL です**geography**型。 値は**float**します。  
  
 **Grid_Size_Level_1**  
 空間インデックスのレベル 1 グリッド密度です。  
  
 低値の 16  
  
 MEDIUM の場合は 64。  
  
 HIGH の場合は 256。  
  
 値は**int**します。  
  
 **Grid_Size_Level_2**  
 空間インデックスのグリッド密度をレベル 2:  
  
 低値の 16  
  
 MEDIUM の場合は 64。  
  
 HIGH の場合は 256。  
  
 値は**int**します。  
  
 **Grid_Size_Level_3**  
 空間インデックスのグリッド密度をレベル 3:  
  
 低値の 16  
  
 MEDIUM の場合は 64。  
  
 HIGH の場合は 256。  
  
 値は**int**します。  
  
 **Grid_Size_Level_4**  
 空間インデックスのレベル 4 のグリッド密度。  
  
 低値の 16  
  
 MEDIUM の場合は 64。  
  
 HIGH の場合は 256。  
  
 値は**int**します。  
  
 **Cells_Per_Object**  
 オブジェクト (インデックス プロパティ) ごとのセルの数。 値は**int**します。  
  
 **Total_Primary_Index_Rows**  
 インデックス内の行の数。 値は**bigint**します。  
  
 **Total_Primary_Index_Pages**  
 インデックス内のページ数。 値は**bigint**します。  
  
 **Average_Number_Of_Index_Rows_Per_Base_Row**  
 インデックス行数/ベース テーブルの行の番号します。 値は**bigint**します。  
  
 **Total_Number_Of_ObjectCells_In_Level0_For_QuerySample**  
 代表のクエリ サンプルがの境界ボックス外にあるかどうかを示す、 **geometry**インデックスと、ルート セル (レベル 0 のセル)。 この値は、0 (レベル 0 のセル外) または 1 のいずれかです。 レベル 0 のセル内にある場合、調査対象のインデックスは、クエリ サンプルに適切なインデックスではありません。 これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_ObjectCells_In_Level0_In_Index**  
 レベル 0 でテセレーションされるインデックス付きオブジェクトのセル インスタンスの数 (ルート セルでは、境界ボックスの外**geometry**)。 これはコア プロパティです。 値は**bigint**します。  
  
 **Geometry**インデックスの場合、これが発生、インデックスの境界ボックスがデータ ドメインより小さい場合。 クエリ ウィンドウが境界外部分的になる場合、セカンダリ フィルターのボックスし、インデックスのパフォーマンスが低下レベル 0 のオブジェクトの多数があります (たとえば、 **Total_Number_Of_ObjectCells_In_Level0_For_QuerySample**は 1)。 境界ボックス内でクエリ ウィンドウが少なくなると、レベル 0 のオブジェクト数が高するときに、インデックスのパフォーマンスが実際に向上します。  
  
 NULL および空のインスタンスは、レベル 0 でカウントされますが、パフォーマンスには影響しません。 レベル 0 は、NULL と、ベース テーブルの空のインスタンス数のセルになります。 **Geography**インデックス レベル 0 は NULL として数のセルと +1 に空のインスタンスのセルがあります、クエリ サンプルが 1 としてカウントされるためです。  
  
 **Total_Number_Of_ObjectCells_In_Level1_In_Index**  
 レベル 1 の精度でテセレーションされるインデックス付きオブジェクトのセル インスタンスの数。 これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_ObjectCells_In_Level2_In_Index**  
 レベル 2 の精度でテセレーションされるインデックス付きオブジェクトのセル インスタンスの数。 これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_ObjectCells_In_Level3_In_Index**  
 レベル 3 の精度でテセレーションされるインデックス付きオブジェクトのセル インスタンスの数。 これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_ObjectCells_In_Level4_In_Index**  
 レベル 4 の精度でテセレーションされるインデックス付きオブジェクトのセル インスタンスの数。 これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_interior_ObjectCells_In_Level1_In_Index**  
 テセレーションでオブジェクトによって完全に覆われるセルの数は、レベル 1 およびそのため、内部オブジェクトにします。 (Cell_attributevalue は 2 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_interior_ObjectCells_In_Level2_In_Index**  
 テセレーションでオブジェクトによって完全に覆われるセルの数は、レベル 2 と、そのため、内部オブジェクトにします。 (Cell_attribute 値は 2 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_interior_ObjectCells_In_Level3_In_Index**  
 テセレーションでオブジェクトによって完全に覆われるセルの数は、レベル 3 およびそのため、内部オブジェクトにします。 (Cell_attribute 値は 2 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_interior_ObjectCells_In_Level4_In_Index**  
 テセレーションでオブジェクトによって完全に覆われるセルの数は、レベル 4 と、そのため、内部オブジェクトにします。 (Cell_attribute 値は 2 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_intersecting_ObjectCells_In_Level1_In_Index**  
 テセレーション レベル 1 のオブジェクトと交差するセルの数。 (Cell_attribute 値は 1 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_intersecting_ObjectCells_In_Level2_In_Index**  
 テセレーション レベル 2 のオブジェクトと交差するセルの数。 (Cell_attribute 値は 1 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_intersecting_ObjectCells_In_Level3_In_Index**  
 テセレーション レベル 3 のオブジェクトと交差するセルの数。 (Cell_attribute 値は 1 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_intersecting_ObjectCells_In_Level4_In_Index**  
 テセレーション レベル 4 のオブジェクトと交差するセルの数。 (Cell_attribute 値は 1 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_Border_ObjectCells_In_Level0_For_QuerySample**  
 クエリ サンプルが手を加えることが、境界ボックス外には、ルート セル 0 かどうかを示します。 これはコア プロパティです。 値は**bigint**します。  
  
> [!NOTE]  
>  この情報は、境界ボックス密接に不足しているオブジェクトがあるかどうかを特定するのに役立つはのみです。  
  
 **Total_Number_Of_Border_ObjectCells_In_Level0_In_Index**  
 境界ボックスに接しているレベル 0 のオブジェクトの数。 (Cell_attribute 値は 0 です)。値は**bigint**します。  
  
 **Total_Number_Of_Border_ObjectCells_In_Level1_In_Index**  
 テセレーション レベル 1 グリッド セルの境界に接するオブジェクト セルの数。 (Cell_attribute 値は 0 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_Border_ObjectCells_In_Level2_In_Index**  
 テセレーション レベル 2 のグリッド セルの境界に接するオブジェクト セルの数。 (Cell_attribute 値は 0 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_Border_ObjectCells_In_Level3_In_Index**  
 テセレーション レベル 3 のグリッド セルの境界に接するオブジェクト セルの数。 (Cell_attribute 値は 0 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Total_Number_Of_Border_ObjectCells_In_Level4_In_Index**  
 テセレーション レベル 4 のグリッド セルの境界に接するオブジェクト セルの数 (Cell_attribute 値は 0 です)。これはコア プロパティです。 値は**bigint**します。  
  
 **Interior_To_Total_Cells_Normalized_To_Leaf_Grid_Percentage**  
 合計の割合リーフ セルを含むグリッドの領域 (リーフ セルの合計) がオブジェクトで対応します。  
  
 たとえば、合計で 100 のリーフ セルに相当する領域をカバーする、4 つの異なるグリッド レベルで 10 個のセルにオブジェクトがテセレーションします。 オブジェクトによって完全に覆われる 3 つの内部セルがあるとします。 3 つの内部セルで覆われる領域は、42 のリーフ セルと同じです。 したがって、覆われている領域の割合では、42% です。 これは、インデックス内のオブジェクトがどの程度細分化の効果的な手段です。  
  
 値は**float**します。  
  
 **Intersecting_To_Total_Cells_Normalized_To_Leaf_Grid_Percentage**  
 同じ**Interior_To_Total_Cells_Normalized_To_Leaf_Grid_Percentage**こと部分的にカバーされたセルを除いて、します。 値は**float**します。  
  
 **Border_To_Total_Cells_Normalized_To_Leaf_Grid_Percentage**  
 同じ**Interior_To_Total_Cells_Normalized_To_Leaf_Grid_Percentage**こと境界のセルを除いて。 値は**float**します。  
  
 **Average_Cells_Per_Object_Normalized_To_Leaf_Grid**  
 リーフ グリッドに正規化されたオブジェクトごとの平均セル数。 これにより、オブジェクトの空間サイズ、つまりオブジェクトの大きさがわかります。 値は**float**します。  
  
 **Average_Objects_PerLeaf_GridCell**  
 インデックスのスパースの度合い。 リーフ セルごとのオブジェクトの平均数。 値は**float**します。  
  
 **Number_Of_SRIDs_Found**  
 インデックスおよび列内の一意な SRID の数。 値は**int**します。  
  
 列には複数の SRID を含めることができ、異なる SRID のオブジェクトは交差しないので、SRID の数はインデックスの選択度を表します。  
  
 **Width_Of_Cell_In_Level1**  
 インデックス作成グリッド内のセルの幅のプロパティです。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Width_Of_Cell_In_Level2**  
 インデックス作成グリッド内のセルの幅のプロパティです。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Width_Of_Cell_In_Level3**  
 インデックス作成グリッド内のセルの幅のプロパティです。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Width_Of_Cell_In_Level4**  
 インデックス作成グリッド内のセルの幅のプロパティです。 測定単位はインデックスで指定され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Height_Of_Cell_In_Level1**  
 インデックス作成グリッド内のセルの高さのプロパティ。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Height_Of_Cell_In_Level2**  
 インデックス作成グリッド内のセルの高さのプロパティ。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Height_Of_Cell_In_Level3**  
 インデックス作成グリッド内のセルの高さのプロパティ。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Height_Of_Cell_In_Level4**  
 インデックス作成グリッド内のセルの高さのプロパティ。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Area_Of_Cell_In_Level1**  
 インデックス作成グリッド内のセルの領域のプロパティ。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Area_Of_Cell_In_Level2**  
 インデックス作成グリッド内のセルの領域のプロパティ。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Area_Of_Cell_In_Level3**  
 インデックス作成グリッド内のセルの領域のプロパティ。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **Area_Of_Cell_In_Level4**  
 インデックス作成グリッド内のセルの領域のプロパティ。 測定単位は、インデックスによって提供され、インデックス付きデータの SRID によって異なります。 値は**float**します。  
  
 **CellArea_To_BoundingBoxArea_Percentage_In_Level1**  
 レベル 1 のセルによる境界ボックスのカバレッジの割合。 値は**float**します。  
  
 **CellArea_To_BoundingBoxArea_Percentage_In_Level2**  
 レベル 2 のセルによる境界ボックスのカバレッジの割合。 値は**float**します。  
  
 **CellArea_To_BoundingBoxArea_Percentage_In_Level3**  
 レベル 3 のセルによる境界ボックスのカバレッジの割合。 値は**float**します。  
  
 **CellArea_To_BoundingBoxArea_Percentage_In_Level4**  
 レベル 4 のセルによる境界ボックスの使用割合。 値は**float**します。  
  
 **Number_Of_Rows_Selected_By_Primary_Filter**  
 プライマリ フィルターによって選択された行数。 これはコア プロパティです。 値は**bigint です。**  
  
 **Number_Of_Rows_Selected_By_Internal_Filter**  
 内部フィルターによって選択された行数。 これらの行について、セカンダリ フィルターは呼び出されません。 これはコア プロパティです。 値は**bigint です。**  
  
 返される数はのみに適用されます**STintersects**します。  
  
 **Number_Of_Times_Secondary_Filter_Is_Called**  
 セカンダリ フィルターの呼び出し回数。 これはコア プロパティです。 値は**bigint です。**  
  
 **Percentage_Of_Rows_NotSelected_By_Primary_Filter**  
 ベース テーブル内に N 行あり、P 行がプライマリ フィルターによって選択されたとすると、割合として (N-P)/N を返します。 これはコア プロパティです。 値は**浮動小数点数。**  
  
 **Percentage_Of_Primary_Filter_Rows_Selected_By_internal_Filter**  
 P 行がプライマリ フィルターによって選択され、S 行が内部フィルターによって選択されたとすると、割合として S/P を返します。 この割合が大きくなると、インデックスで、パフォーマンス コストが高いセカンダリ フィルターを使用せずに済むようになります。 これはコア プロパティです。 値は**浮動小数点数。**  
  
 **Number_Of_Rows_Output**  
 クエリで出力された行数。 これはコア プロパティです。 値は**bigint です。**  
  
 **Internal_Filter_Efficiency**  
 O が出力される行の数の場合は、割合として S/O を返します。 これはコア プロパティです。 値は**浮動小数点数。**  
  
 **Primary_Filter_Efficiency**  
 P 行がプライマリ フィルターと O で選択されている場合は、出力行の数に対する割合としてこの returnsO P/です。 プライマリのフィルターのために、セカンダリ フィルターのある偽陽性の数の効率性が高いほど処理します。 これはコア プロパティです。 値は**浮動小数点数。**  
  
## <a name="permissions"></a>アクセス許可  
 ユーザーのメンバーである必要があります、**パブリック**ロール。 サーバーとオブジェクトの読み取りアクセス許可が必要です。 これは、すべての空間インデックス ストアド プロシージャに適用されます。  
  
## <a name="remarks"></a>コメント  
 NULL 値を含むプロパティは、返されるセットに含まれません。  
  
## <a name="examples"></a>使用例  
 例については、次のトピックを参照してください。  
  
-   [sp_help_spatial_geometry_index &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geometry-index-transact-sql.md)  
  
-   [sp_help_spatial_geometry_index_xml &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geometry-index-xml-transact-sql.md)  
  
-   [sp_help_spatial_geography_index &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geography-index-transact-sql.md)  
  
-   [sp_help_spatial_geography_index_xml &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geography-index-xml-transact-sql.md)  
  
## <a name="requirements"></a>必要条件  
  
## <a name="see-also"></a>関連項目  
 [空間インデックス ストアド プロシージャ&#40;TRANSACT-SQL&#41;](https://msdn.microsoft.com/library/1be0f34e-3d5a-4a1f-9299-bd482362ec7a)   
 [sp_help_spatial_geometry_index &#40;TRANSACT-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-spatial-geometry-index-transact-sql.md)   
 [空間インデックスの概要](../../relational-databases/spatial/spatial-indexes-overview.md)   
 [XQuery の基礎](../../xquery/xquery-basics.md)   
 [XQuery 言語リファレンス &#40;SQL Server&#41;](../../xquery/xquery-language-reference-sql-server.md)  
  
  
