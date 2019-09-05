---
title: sysmergearticles (Transact-SQL) |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sysmergearticles
- sysmergearticles_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysmergearticles system table
ms.assetid: e9b1648e-4660-4688-9f56-18b2baf7228c
author: stevestein
ms.author: sstein
ms.openlocfilehash: 6d2208097f09df33f175acb2535310e447853de4
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68029790"
---
# <a name="sysmergearticles-transact-sql"></a>sysmergearticles (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  ローカル データベースに定義されているマージ アーティクルごとに 1 行のデータを格納します。 このテーブルは、パブリケーション データベース内に保存されます。  
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|アーティクルの名前。|  
|**type**|**tinyint**|アーティクルの種類。次のいずれかになります。<br /><br /> **10**テーブルを = です。<br /><br /> **32** = ストアド プロシージャ (スキーマのみ)。<br /><br /> **64** = ビューまたはインデックス付きビュー (スキーマのみ)。<br /><br /> **128** = ユーザー定義関数 (スキーマのみ)。<br /><br /> **160** = シノニム (スキーマのみ)。|  
|**objid**|**int**|オブジェクト識別子です。|  
|**sync_objid**|**int**|同期データセットを表すビューのオブジェクト ID。|  
|**view_type**|**tinyint**|ビューの種類。<br /><br /> **0** = ビュー以外はすべてのベース オブジェクトを使用しています。<br /><br /> **1** = パーマネント ビュー。<br /><br /> **2** = 一時ビュー。|  
|**artid**|**uniqueidentifier**|指定したアーティクルの一意な ID 番号です。|  
|**description**|**nvarchar (255)**|アーティクルの簡単な説明。|  
|**pre_creation_command**|**tinyint**|サブスクリプション データベースにアーティクルが作成されるときに実行される既定の操作。<br /><br /> **0 =** なし - サブスクライバーで、テーブルが既に存在する場合操作はありません。<br /><br /> **1** = drop - テーブルを再作成する前に削除します。<br /><br /> **2** = 削除-サブセット フィルターの WHERE 句に基づいて削除します。<br /><br /> **3** = Truncate のと同じ**2**が行ではなくページを削除します。 ただし、WHERE 句は使用しません。|  
|**pubid**|**uniqueidentifier**|現在のアーティクルが属するパブリケーションの ID。|  
|**nickname**|**int**|アーティクル識別用のニックネーム マップ。|  
|**column_tracking**|**int**|アーティクルに対して列追跡が実装されているかどうかを示します。|  
|**status**|**tinyint**|アーティクルの状態。次のいずれかになります。<br /><br /> **1** = Unsynced - テーブルをパブリッシュする初期処理スクリプトは、次回、スナップショット エージェントの実行を実行します。<br /><br /> **2** = active - テーブルをパブリッシュする初期処理スクリプトが実行されています。<br /><br /> **5** = New_inactive - 追加します。<br /><br /> **6** = New_active - 追加します。|  
|**conflict_table**|**sysname**|現在のアーティクルに関する競合レコードが含まれているローカル テーブルの名前。 このテーブルは情報用のみとして提供されており、その内容は、カスタム競合回避ルーチンを使用して変更や削除ができます。または、管理者が直接変更したり、削除することもできます。|  
|**creation_script**|**nvarchar (255)**|アーティクルの作成スクリプト。|  
|**conflict_script**|**nvarchar (255)**|アーティクルの競合スクリプト。|  
|**article_resolver**|**nvarchar (255)**|アーティクルのカスタム行レベル競合回避モジュール。|  
|**ins_conflict_proc**|**sysname**|競合を書き込むに使用するプロシージャ**conflict_table**します。|  
|**insert_proc**|**sysname**|同期化の際に行を挿入するため、既定の競合回避モジュールが使用するプロシージャ。|  
|**update_proc**|**sysname**|同期化の際に行を更新するため、既定の競合回避モジュールが使用するプロシージャ。|  
|**select_proc**|**sysname**|マージ エージェントがアーティクル用の列と行をロックおよび検索する場合に使用する、自動生成されるストアド プロシージャの名前。|  
|**metadata_select_proc**|**sysname**|マージ レプリケーション システム テーブル内にあるメタデータへのアクセスで使用される、自動生成ストアド プロシージャの名前。|  
|**delete_proc**|**sysname**|同期化の際に行を削除するため、既定の競合回避モジュールが使用するプロシージャ。|  
|**schema_option**|**binary(8)**|値がサポートされている*schema_option*を参照してください[sp_addmergearticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addmergearticle-transact-sql.md)します。|  
|**destination_object**|**sysname**|サブスクライバーで作成されるテーブルの名前。|  
|**destination_owner**|**sysname**|目的のオブジェクトの所有者名。|  
|**resolver_clsid**|**nvarchar (50)**|カスタム競合回避モジュールの ID。|  
|**subset_filterclause**|**nvarchar(1000)**|アーティクルのフィルター句。|  
|**missing_col_count**|**int**|見つからない列の数。|  
|**missing_cols**|**varbinary (128)**|見つからない列のビットマップ。|  
|**excluded_cols**|**varbinary (128)**|アーティクルがサブスクライバーに送信されるときに、そのアーティクルから除外される列のビットマップ。|  
|**excluded_col_count**|**int**|除外される列数。|  
|**columns**|**varbinary (128)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**deleted_cols**|**varbinary (128)**|ソース テーブルから削除された列のビットマップ。|  
|**resolver_info**|**nvarchar (255)**|カスタム競合回避モジュールによって必要な追加情報の記憶域。|  
|**view_sel_proc**|**nvarchar(290)**|マージ エージェントが、動的にフィルター選択されたパブリケーションでアーティクルを最初に作成するとき、および任意のフィルター選択されたパブリケーションで変更された行を列挙するときに使用するストアド プロシージャの名前。|  
|**gen_cur**|**int**|アーティクルのベース テーブルへのローカルな変更に対して生成される番号。|  
|**vertical_partition**|**int**|列のフィルター選択がテーブル アーティクルで有効かどうかを示します。 **0**垂直方向のフィルター処理がないことを示します、すべての列をパブリッシュします。|  
|**identity_support**|**int**|ID 範囲の自動処理が有効かどうかを示します。 **1** id 範囲処理が有効になっていることを意味し、 **0**サポートの範囲は id がないことを意味します。|  
|**before_image_objid**|**int**|追跡テーブルのオブジェクト ID。 パブリケーションが作成されたときに、追跡テーブルが特定のキー列の値を含む *@keep_partition_changes*  =  **true**します。|  
|**before_view_objid**|**int**|ビュー テーブルのオブジェクト ID。 ビューが存在するテーブルでは、行の削除または更新前に、その行が特定のサブスクライバーに属していたかどうかが追跡されます。 パブリケーションが作成された場合にのみ適用されます *@keep_partition_changes*  =  **true です。**|  
|**verify_resolver_signature**|**int**|マージ レプリケーションで競合回避モジュールを使用する前に、デジタル署名を確認するかどうかを示します。<br /><br /> **0** = 署名がないことを確認します。<br /><br /> **1** = 署名が信頼できる発行元があるかどうかを確認することを確認します。|  
|**allow_interactive_resolver**|**bit**|アーティクルに対する対話型の競合回避モジュールの使用が有効かどうかを示します。 **1**インタラクティブ競合回避モジュールが、情報の記事で使用されていることを指定します。|  
|**fast_multicol_updateproc**|**bit**|1 つの UPDATE ステートメントで同じ行の複数の列に対して変更を適用するように、マージ エージェントが有効になっているかどうかを示します。<br /><br /> **0**列ごとに個別の更新の変更の問題を = です。<br /><br /> **1** = 1 つのステートメントで複数の列を更新する UPDATE ステートメントを発行します。|  
|**check_permissions**|**int**|マージ エージェントがパブリッシャーに変更を適用するときに確認されるテーブルレベル権限のビットマップ。 *check_permissions*これらの値のいずれかの。<br /><br /> **0x00 =** 権限は確認されません。<br /><br /> **0x10 =** サブスクライバー側で Insert をアップロードする前に、パブリッシャー側で権限を確認します。<br /><br /> **0x20 =** サブスクライバーで行われた更新プログラムをアップロードする前に、パブリッシャー側で権限を確認します。<br /><br /> **0x40 =** サブスクライバー側で Delete をアップロードする前に、パブリッシャー側で権限を確認します。|  
|**maxversion_at_cleanup**|**int**|メタデータがクリーンアップされる最上位世代。|  
|**processing_order**|**int**|マージ パブリケーション内のアーティクルの処理順序を示します値が**0**アーティクルが順序付けられたがないことと、最低から最高値の順序でアーティクルが処理で示されます。 2 つのアーティクルの値が同じ場合、それらは同時に処理されます。 詳細については、「[Specify Merge Replication properties](../../relational-databases/replication/merge/specify-merge-replication-properties.md)」 (マージ レプリケーションのプロパティの指定) を参照してください。|  
|**upload_options**|**tinyint**|クライアント サブスクリプションを使用したサブスクライバー側での更新に対する制限を定義します。次のいずれかの値になります。<br /><br /> **0** = クライアント サブスクリプションを使用したサブスクライバーで行われる更新に制限はありません。 すべての変更がパブリッシャーにアップロードされます。<br /><br /> **1** = 変更は、クライアント サブスクリプションを使用したサブスクライバーで許可されますが、パブリッシャーにはアップロードされません。<br /><br /> **2** = クライアント サブスクリプションを使用したサブスクライバーでの変更は許可されません。<br /><br /> 詳細については、「[ダウンロード専用アーティクルを使用したマージ レプリケーションのパフォーマンス最適化](../../relational-databases/replication/merge/optimize-merge-replication-performance-with-download-only-articles.md)」を参照してください。|  
|**published_in_tran_pub**|**bit**|マージ パブリケーション内のアーティクルが、トランザクション パブリケーションでもパブリッシュされるかどうかを示します。<br /><br /> **0** = アーティクルはトランザクション アーティクルでパブリッシュされません。<br /><br /> **1** = アーティクルはトランザクション アーティクルでもパブリッシュされます。|  
|**lightweight**|**bit**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**procname_postfix**|**nchar(32)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**well_partitioned_lightweight**|**bit**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**before_upd_view_objid**|**int**|追加されます。|  
|**delete_tracking**|**bit**|削除がレプリケートされるかどうかを示します。<br /><br /> **0** = 削除はレプリケートされません<br /><br /> **1** = マージ レプリケーションの既定の動作は、削除がレプリケートされます。<br /><br /> ときに、値の*delete_tracking*は**0**、パブリッシャー、サブスクライバーで削除された行を手動で削除する必要があり、パブリッシャー側で削除された行はサブスクライバー側で手動で削除する必要があります。<br /><br /> 注:値**0**未集約になります。|  
|**compensate_for_errors**|**bit**|同期中にエラーが発生したときに、補正アクションが行われるかどうかを示します。<br /><br /> **0** = 補正アクションが無効になります。<br /><br /> **1**に補正アクションは、マージ レプリケーションの既定の動作は、これらの変更を元に戻す、サブスクライバーまたはパブリッシャーが常に適用できない変更を = です。<br /><br /> 注:値**0**未集約になります。|  
|**pub_range**|**bigint**|パブリッシャーの ID 範囲の大きさ。|  
|**range**|**bigint**|調整の際にサブスクライバーに割り当てられる、連続する ID 値の大きさ。|  
|**threshold**|**int**|ID 範囲のしきい値のパーセンテージ。|  
|**stream_blob_columns**|**bit**|バイナリ ラージ オブジェクト列をレプリケートするときにデータ ストリームの最適化が使用されるかどうかを示します。 **1**最適化が試行されたことを意味します。|  
|**preserve_rowguidcol**|**bit**|レプリケーションで既存の rowguid 列が使用されるかどうかを示します。 値**1**既存の ROWGUIDCOL 列が使用されることを意味します。 **0**レプリケーション ROWGUIDCOL 列を追加することを意味します。|  
  
## <a name="see-also"></a>参照  
 [レプリケーション テーブル &#40;Transact-SQL&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [レプリケーション ビュー &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)   
 [sp_addmergearticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addmergearticle-transact-sql.md)   
 [sp_changemergearticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changemergearticle-transact-sql.md)   
 [sp_helpmergearticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-helpmergearticle-transact-sql.md)  
  
  
