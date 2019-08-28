---
title: sp_mergecleanupmetadata (TRANSACT-SQL) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sp_mergecleanupmetadata_TSQL
- sp_mergecleanupmetadata
helpviewer_keywords:
- sp_mergecleanupmetadata
ms.assetid: 892f8628-4cbe-4cc3-b959-ed45ffc24064
author: stevestein
ms.author: sstein
ms.openlocfilehash: ebdc7b55cde31198007e05de1603df7134ed3bfc
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68020004"
---
# <a name="spmergecleanupmetadata-transact-sql"></a>sp_mergecleanupmetadata (TRANSACT-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  バージョンを実行しているサーバーを含むレプリケーション トポロジでのみ使用する必要があります[!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]より前のバージョン[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)]Service Pack 1 **。sp_mergecleanupmetadata**内のメタデータをクリーンアップすることができます、 **MSmerge_genhistory**、 **MSmerge_contents**と**MSmerge_tombstone**システム テーブル。 このストアド プロシージャは、パブリッシャー側でパブリケーション データベースについて実行されます。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_mergecleanupmetadata [ [ @publication = ] 'publication' ]  
    [ , [ @reinitialize_subscriber = ] 'reinitialize_subscriber' ]  
```  
  
## <a name="arguments"></a>引数  
`[ @publication = ] 'publication'` パブリケーションの名前です。 *パブリケーション*は**sysname**、既定値は **%** 、すべてのパブリケーションのメタデータをクリーンアップします。 明示的に指定されている場合、パブリケーションが既に存在します。  
  
`[ @reinitialize_subscriber = ] 'subscriber'` サブスクライバーを再初期化するかどうかを指定します。 *サブスクライバー*は**nvarchar (5)** 、できる**TRUE**または**FALSE**、既定値は**TRUE**します。 場合**TRUE**サブスクリプションが再初期化のマークを付けます。 場合**FALSE**サブスクリプションが再初期化のマークされていません。  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または**1** (失敗)  
  
## <a name="remarks"></a>コメント  
 **sp_mergecleanupmetadata**のバージョンを実行しているサーバーを含むレプリケーション トポロジでのみ使用する必要があります[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]より前のバージョン[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)]Service Pack 1。 のみが含まれるトポロジ[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)]Service Pack 1 またはそれ以降は、自動保有期間に基づくメタデータのクリーンアップを使用する必要があります。 このストアド プロシージャを実行する場合は、実行するコンピューター上で、きわめて大きいログファイルが必要であり、作成される可能性があることに注意してください。  
  
> [!CAUTION]
>  後**sp_mergecleanupmetadata**既定に格納されているメタデータが存在するパブリケーションのサブスクライバーのすべてのサブスクリプションで実行される**MSmerge_genhistory**、 **MSmerge_contents**と**MSmerge_tombstone**マークは、再初期化のサブスクライバーの保留中の変更は失われ、現在のスナップショットに不使用とマークされている場合は。  
> 
> [!NOTE]
>  かどうか、データベースに対する複数のパブリケーションが存在し、それらのパブリケーションのいずれかが無期限のパブリケーションの保有期間を使用して ( **@retention** =**0**)、実行中**sp_mergecleanupmetadata**クリーンアップ、マージ レプリケーション変更の追跡データベースのメタデータがありません。 このため、無期限のパブリケーション保有期間は注意して使用してください。  
  
 これを実行するストアド プロシージャをときに、サブスクライバーを再初期化を設定するかどうかを選択、 **\@reinitialize_subscriber** パラメーターを**TRUE** (既定値) または**FALSE**. 場合**sp_mergecleanupmetadata**を実行すると、 **\@reinitialize_subscriber** パラメーターに設定**TRUE**サブスクリプションがいた場合でも、サブスクライバーでスナップショットが再適用されます初期スナップショット (たとえば場合、スナップショット データとスキーマが手動で適用またはサブスクライバーに既に存在していた) なしで作成します。 パラメーターを設定する**FALSE**パブリケーションが再初期化されていない場合、パブリッシャーとサブスクライバーでデータが同期されていることを確認する必要がありますので注意が必要ですで使用する必要があります。  
  
 値に関係なく **\@reinitialize_subscriber** 、 **sp_mergecleanupmetadata**マージ パブリッシャーまたは再パブリッシュ サブスクライバーに変更をアップロードしようとしているプロセスを継続的ながある場合は失敗ストアド プロシージャが呼び出された時刻。  
  
 **Executing sp_mergecleanupmetadata with @reinitialize_subscriber = TRUE:**  
  
1.  推奨必須ではありませんが、パブリケーションおよびサブスクリプション データベースにすべての更新プログラムを停止すること。 更新を続行する場合、パブリケーションが再初期化が、データの収束は保持されます、前回のマージ以降にサブスクライバー側で行われた更新は失われます。  
  
2.  マージ エージェントを実行してマージを実行します。 使用することをお勧め、 **-Validate**マージ エージェントを実行すると、各サブスクライバーでエージェントのコマンド ライン オプション。 連続モードのマージを実行している場合は、次を参照してください。*連続モードのマージに関する注意事項*このセクションで後述します。  
  
3.  すべてのマージが完了すると、実行**sp_mergecleanupmetadata**します。  
  
4.  実行**sp_reinitmergepullsubscription**名前付きまたは匿名プル サブスクリプションを使用してデータを収束させることを確認するすべてのサブスクライバーにします。  
  
5.  連続モードのマージを実行している場合は、次を参照してください。*連続モードのマージに関する注意事項*このセクションで後述します。  
  
6.  すべてのレベルで関係するすべてのマージ パブリケーションに対して、スナップショット ファイルを再生成します。 最初にスナップショットを再生成せずにマージしようとすると、スナップショットを再生成するように要求されます。  
  
7.  パブリケーション データベースをバックアップします。 そのためにはエラーには、パブリケーション データベースの復元後のマージが失敗する可能性があります。  
  
 **Executing sp_mergecleanupmetadata with @reinitialize_subscriber = FALSE**  
  
1.  停止**すべて**パブリケーションおよびサブスクリプション データベースを更新します。  
  
2.  マージ エージェントを実行してマージを実行します。 使用することをお勧め、 **-Validate**マージ エージェントを実行すると、各サブスクライバーでエージェントのコマンド ライン オプション。 連続モードのマージを実行している場合は、次を参照してください。*連続モードのマージに関する注意事項*このセクションで後述します。  
  
3.  すべてのマージが完了すると、実行**sp_mergecleanupmetadata**します。  
  
4.  連続モードのマージを実行している場合は、次を参照してください。*連続モードのマージに関する注意事項*このセクションで後述します。  
  
5.  すべてのレベルで関係するすべてのマージ パブリケーションに対して、スナップショット ファイルを再生成します。 最初にスナップショットを再生成せずにマージしようとすると、スナップショットを再生成するように要求されます。  
  
6.  パブリケーション データベースをバックアップします。 そのためにはエラーには、パブリケーション データベースの復元後のマージが失敗する可能性があります。  

[!INCLUDE[freshInclude](../../includes/paragraph-content/fresh-note-steps-feedback.md)]

 **連続モードのマージに関する注意事項**  
  
 連続モードのマージを実行している場合は、いずれかが必要です。  
  
-   マージ エージェントを停止し、せずに別のマージを実行、 **-Continuous**パラメーターを指定します。  
  
-   パブリケーションを非アクティブ化**sp_changemergepublication**にパブリケーションの状態をポーリングしている連続モードのマージが失敗することを確認します。  
  
    ```  
    EXEC central..sp_changemergepublication @publication = 'dynpart_pubn', @property = 'status', @value = 'inactive'  
    ```  
  
 完了したときの実行手順 3. **sp_mergecleanupmetadata**、停止した方法に基づいて、連続モードのマージを再開します。 次のいずれかの操作を行います。  
  
-   追加、 **-Continuous**マージ エージェントのパラメーター。  
  
-   パブリケーションを再アクティブ化**sp_changemergepublication** します。  
  
    ```  
    EXEC central..sp_changemergepublication @publication = 'dynpart_pubn', @property = 'status', @value = 'active'  
    ```  
  
## <a name="permissions"></a>アクセス許可  
 メンバーのみ、 **sysadmin**固定サーバー ロールまたは**db_owner**固定データベース ロールが実行できる**sp_mergecleanupmetadata**します。  
  
 このストアド プロシージャを使用するには、パブリッシャーが [!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)] を実行していることが必要です。 いずれかのサブスクライバーを実行する必要があります[!INCLUDE[ssVersion2000](../../includes/ssversion2000-md.md)]または[!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 7.0 では、Service Pack 2。  
  
## <a name="see-also"></a>参照  
 [MSmerge_genhistory &#40;Transact-SQL&#41;](../../relational-databases/system-tables/msmerge-genhistory-transact-sql.md)   
 [MSmerge_contents &#40;Transact-SQL&#41;](../../relational-databases/system-tables/msmerge-contents-transact-sql.md)   
 [MSmerge_tombstone &#40;Transact-SQL&#41;](../../relational-databases/system-tables/msmerge-tombstone-transact-sql.md)  
  
  
