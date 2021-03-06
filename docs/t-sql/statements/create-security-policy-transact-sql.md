---
title: CREATE SECURITY POLICY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/10/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SECURITY_POLICY_TSQL
- CREATE SECURITY
- SECURITY
- CREATE_SECURITY_POLICY_TSQL
- CREATE_SECURITY_TSQL
- SECURITY POLICY
- SECURITY_TSQL
- CREATE SECURITY POLICY
dev_langs:
- TSQL
helpviewer_keywords:
- RLS
- CREATE SECURITY POLICY statement
- Row-Level Security
ms.assetid: d6ab70ee-0fa2-469c-96f6-a3c16d673bc8
author: CarlRabeler
ms.author: carlrab
manager: craigg
ms.openlocfilehash: 01417c90e5583b8b2276f05ec92347758689d284
ms.sourcegitcommit: 2429fbcdb751211313bd655a4825ffb33354bda3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2018
ms.locfileid: "52532483"
---
# <a name="create-security-policy-transact-sql"></a>セキュリティ ポリシー (TRANSACT-SQL) の作成します。
[!INCLUDE[tsql-appliesto-ss2016-asdb-xxxx-xxx-md](../../includes/tsql-appliesto-ss2016-asdb-xxxx-xxx-md.md)]

  行レベルのセキュリティのセキュリティ ポリシーを作成します。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```     
CREATE SECURITY POLICY [schema_name. ] security_policy_name    
    { ADD [ FILTER | BLOCK ] } PREDICATE tvf_schema_name.security_predicate_function_name   
      ( { column_name | expression } [ , ...n] ) ON table_schema_name. table_name    
      [ <block_dml_operation> ] , [ , ...n] 
    [ WITH ( STATE = { ON | OFF }  [,] [ SCHEMABINDING = { ON | OFF } ] ) ]  
    [ NOT FOR REPLICATION ] 
[;]  
  
<block_dml_operation>  
    [ { AFTER { INSERT | UPDATE } }   
    | { BEFORE { UPDATE | DELETE } } ]  
```  
  
## <a name="arguments"></a>引数  
 *security_policy_name*  
 セキュリティ ポリシーの名前。 セキュリティ ポリシー名は識別子のルールに準拠し、データベースおよびそのスキーマで一意でなければなりません。  
  
 *schema_name*  
 セキュリティ ポリシーが属するスキーマの名前。 *schema_name* はスキーマのバインドのために必要です。  
  
 [フィルター | ブロック]  
 対象のテーブルにバインドされている関数のセキュリティの述語の型。 サイレント モードで、フィルター述語には、読み取り操作に使用できる行がフィルター処理します。 ブロックは述語述語の関数に違反しているブロックの書き込み操作では明示的にします。  
  
 *tvf_schema_name.security_predicate_function_name*  
 述語として使用され、ターゲット テーブルに対するクエリで実施されるインライン テーブル値関数。 特定のテーブルの特定の DML 操作に対して定義できるセキュリティ述語の数は 1 つです。 インライン テーブル値関数は、SCHEMABINDING オプションを使用して作成する必要があります。  
  
 { *column_name* | *expression* }  
 セキュリティ述語関数のパラメーターとして使用される列名または式。 ターゲット テーブルの任意の列を使用できます。 [expression](../../t-sql/language-elements/expressions-transact-sql.md) には、定数、組み込みのスカラー関数、演算子、およびターゲット テーブルの列のみを含めることができます。 関数の各パラメーターに列名または式を指定する必要があります。  
  
 *table_schema_name.table_name*  
 セキュリティ述語の適用先となるターゲット テーブル。 複数のセキュリティを無効になっているポリシーの特定の DML 操作では、1 つのテーブルを対象にできますが、任意の時点で、1 つのみを有効にすることができます。  
  
 *\<block_dml_operation>* ブロック述語が適用される特定の DML 操作。 結局、DML 操作が実行される (INSERT または UPDATE) 後に、述語、行の値に評価することを指定します。 前に、DML の操作が実行される (更新または削除) する前に、述語は、行の値に評価することを指定します。 操作が指定されていない場合、述語は、すべての操作に適用されます。  
  
 [ STATE = { ON | **OFF** } ]  
 セキュリティ ポリシーによるターゲット テーブルに対するセキュリティ述語の実施を有効または無効にします。 作成されているセキュリティ ポリシーが有効になっているが指定されていない場合。  
  
 [SCHEMABINDING = {ON |OFF}]  
 SCHEMABINDING オプションを使用して、ポリシー内のすべての述語関数を作成する必要があるかどうかを示します。 既定では、schemabinding を指定してすべての関数を作成する必要があります。  
  
 NOT FOR REPLICATION  
 レプリケーション エージェントがターゲット オブジェクトを変更するときにセキュリティ ポリシーを実行すべきではないことを示します。 詳細については、「[同期中にトリガと制約の動作を制御する方法 &#40;レプリケーション Transact-SQL プログラミング&#41;](../../relational-databases/replication/control-behavior-of-triggers-and-constraints-in-synchronization.md)」を参照してください。  
  
 [*table_schema_name*.] *table_name*  
 セキュリティ述語の適用先となるターゲット テーブル。 無効な複数のセキュリティ ポリシーは単一テーブルをターゲットにできますが、有効にできるのはどの時点でも 1 つだけです。  
  
## <a name="remarks"></a>Remarks  
 メモリ最適化テーブルで述語関数を使用する場合、**SCHEMABINDING** を含め、**WITH NATIVE_COMPILATION** コンパイル ヒントを使う必要があります。  
  
 ブロックの述語は、対応する DML 操作を実行した後に評価されます。 そのため、READ UNCOMMITTED のクエリでは、ロールバックは一時的な値を確認できます。  
  
## <a name="permissions"></a>アクセス許可  
 スキーマに対する ALTER ANY SECURITY POLICY 権限と ALTER 権限が必要です。  
  
 また、追加される各述語に関しては次のアクセス許可も必要になります。  
  
-   述語として使用している関数に関する SELECT および REFERENCES 権限。  
  
-   ポリシーにバインドしているターゲット テーブルに対する REFERENCES 権限。  
  
-   引数として使用しているターゲット テーブルのすべての列に対する REFERENCES 権限。  
  
## <a name="examples"></a>使用例  
 以下の例は、 **CREATE SECURITY POLICY** 構文の使用法を示しています。 詳細なセキュリティ ポリシーのシナリオ例については、「[行レベルのセキュリティ](../../relational-databases/security/row-level-security.md)」をご覧ください。  
  
### <a name="a-creating-a-security-policy"></a>A. セキュリティ ポリシーを作成する  
 以下の構文は、Costomer テーブルにフィルター述語を使用してセキュリティ ポリシーを作成し、そのセキュリティ ポリシーを無効な状態のままにします。  
  
```  
CREATE SECURITY POLICY [FederatedSecurityPolicy]   
ADD FILTER PREDICATE [rls].[fn_securitypredicate]([CustomerId])   
ON [dbo].[Customer];  
```  
  
### <a name="b-creating-a-policy-that-affects-multiple-tables"></a>B. 複数のテーブルに影響を及ぼすポリシーを作成する  
 以下の構文は、3 つの異なるテーブルに 3 つのフィルター述部を使用してセキュリティ ポリシーを作成し、そのセキュリティ ポリシーを有効にします。  
  
```  
CREATE SECURITY POLICY [FederatedSecurityPolicy]   
ADD FILTER PREDICATE [rls].[fn_securitypredicate1]([CustomerId])   
    ON [dbo].[Customer],  
ADD FILTER PREDICATE [rls].[fn_securitypredicate1]([VendorId])   
    ON [dbo].[ Vendor],  
ADD FILTER PREDICATE [rls].[fn_securitypredicate2]([WingId])   
    ON [dbo].[Patient]  
WITH (STATE = ON);  
```  
  
### <a name="c-creating-a-policy-with-multiple-types-of-security-predicates"></a>C. 複数の種類のセキュリティの述語でポリシーを作成します。  
 フィルター述語とブロックの述語の両方を Sales テーブルに追加します。  
  
```  
CREATE SECURITY POLICY rls.SecPol  
    ADD FILTER PREDICATE rls.tenantAccessPredicate(TenantId) ON dbo.Sales,  
    ADD BLOCK PREDICATE rls.tenantAccessPredicate(TenantId) ON dbo.Sales AFTER INSERT;  
```  
  
## <a name="see-also"></a>参照  
 [行レベルのセキュリティ](../../relational-databases/security/row-level-security.md)   
 [ALTER SECURITY POLICY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-security-policy-transact-sql.md)   
 [DROP SECURITY POLICY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-security-policy-transact-sql.md)   
 [sys.security_policies &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-security-policies-transact-sql.md)   
 [sys.security_predicates &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-security-predicates-transact-sql.md)  
  
  

