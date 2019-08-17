---
title: sp_create_plan_guide (TRANSACT-SQL) |Microsoft Docs
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_create_plan_guide
- sp_create_plan_guide_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_create_plan_guide
ms.assetid: 5a8c8040-4f96-4c74-93ab-15bdefd132f0
author: stevestein
ms.author: sstein
ms.openlocfilehash: 9b08de0ce5acde863d8f2683b9bc8d6f38c2fa4b
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68108633"
---
# <a name="spcreateplanguide-transact-sql"></a>sp_create_plan_guide (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-asdb-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-asdb-xxxx-xxx-md.md)]

  クエリ ヒントまたは実際のクエリ プランをデータベース内のクエリと関連付けるためのプラン ガイドを作成します。 プラン ガイドの詳細については、「 [Plan Guides](../../relational-databases/performance/plan-guides.md)」を参照してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_create_plan_guide [ @name = ] N'plan_guide_name'  
    , [ @stmt = ] N'statement_text'  
    , [ @type = ] N'{ OBJECT | SQL | TEMPLATE }'  
    , [ @module_or_batch = ]  
      {   
                    N'[ schema_name. ] object_name'  
        | N'batch_text'  
        | NULL  
      }  
    , [ @params = ] { N'@parameter_name data_type [ ,...n ]' | NULL }   
    , [ @hints = ] { N'OPTION ( query_hint [ ,...n ] )'   
                 | N'XML_showplan'  
                 | NULL }  
```  
  
## <a name="arguments"></a>引数  
 [\@名前 =] N'*plan_guide_name*'  
 プラン ガイドの名前を指定します。 プラン ガイド名は現在のデータベースに対して有効です。 *plan_guide_name*の規則に従っている必要があります[識別子](../../relational-databases/databases/database-identifiers.md)番号記号で始めることはできません (#)。 最大長*plan_guide_name*は 124 文字です。  
  
 [ \@stmt = ] N'*statement_text*'  
 プラン ガイドを作成する対象の [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントです。 ときに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]クエリ オプティマイザーがクエリに一致する*statement_text*、 *plan_guide_name*有効になります。 成功するプラン ガイドの作成を*statement_text*で指定されたコンテキストで表示する必要があります、\@type、 \@module_or_batch、および\@params パラメーター。  
  
 *statement_text*によって識別されるモジュールと、バッチ内で対応するステートメントと一致する、クエリ オプティマイザーを許可する方法を指定する必要があります\@module_or_batch と\@params します。 詳細については、「解説」セクションを参照してください。 サイズ*statement_text*サーバーの使用可能なメモリによってのみ制限されます。  
  
 [\@type = ]N'{ OBJECT | SQL | TEMPLATE }'
 エンティティの型は、 *statement_text*が表示されます。 照合するコンテキストを指定しますこの*statement_text*に*plan_guide_name*します。  
  
 OBJECT  
 示します*statement_text*のコンテキストでの表示、[!INCLUDE[tsql](../../includes/tsql-md.md)]ストアド プロシージャ、スカラー関数、複数ステートメント テーブル値関数、または[!INCLUDE[tsql](../../includes/tsql-md.md)]現在のデータベース内の DML トリガーです。  
  
 SQL  
 示す*statement_text*のスタンドアロンのステートメントまたはバッチを送信できるコンテキストでの表示[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]任意のメカニズムを通じてします。 [!INCLUDE[tsql](../../includes/tsql-md.md)] 共通言語ランタイム (CLR) オブジェクトまたは拡張ストアド プロシージャ、または EXEC N を使用して送信されるステートメント '*sql_string*'、サーバー上のバッチとして処理され、そのため、として識別する必要があります、 \@type **=**  'SQL' です。 クエリがパラメーター化をヒント SQL が指定されている場合 {FORCED |単純な} を指定することはできません、\@hintsパラメーターします。  
  
 TEMPLATE  
 示されたフォームにパラメーター化されるクエリに対してプラン ガイドが適用されることを示します*statement_text*します。 テンプレートが指定されている場合のみ、PARAMETERIZATION {FORCED | SIMPLE} クエリ ヒントで指定できます、\@hintsパラメーターします。 TEMPLATE プラン ガイドの詳細については、次を参照してください。[クエリ パラメーター化動作の指定を使用してプラン ガイドによって](../../relational-databases/performance/specify-query-parameterization-behavior-by-using-plan-guides.md)します。  
  
 [\@module_or_batch =] {N'[ *schema_name*します。 ] *object_name*' | N'*batch_text*' | NULL }  
 これで、オブジェクトの名前を指定します。 *statement_text*が表示されたら、またはバッチのテキストを*statement_text*が表示されます。 バッチのテキストは、使用を含めることはできません*データベース*ステートメント。  
  
 アプリケーションから送信されたバッチと一致するプラン ガイドの*batch_tex*t は、同じ形式で指定する必要がありますの文字を送信するときと[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]します。 この適合を容易にするために内部変換は実行されません。 詳細については、「解説」を参照してください。  
  
 [*schema_name*]。*object_name*の名前を指定します、[!INCLUDE[tsql](../../includes/tsql-md.md)]ストアド プロシージャ、スカラー関数、複数ステートメント テーブル値関数、または[!INCLUDE[tsql](../../includes/tsql-md.md)]DML トリガーを含む*statement_text*. 場合*schema_name*が指定されていない*schema_name*は現在のユーザーのスキーマを使用します。 NULL が指定されている場合と\@typeの値 ' SQL' を = \@module_or_batch がの値に設定されている\@stmt します。場合\@type = 'テンプレート **'** 、 \@module_or_batch が NULL にする必要があります。  
  
 [ \@params = ]{ N' *\@parameter_name data_type* [ , *...n* ]' | NULL }  
 埋め込まれているすべてのパラメーターの定義を指定します*statement_text*します。 \@パラメーターでは、のみ、次のいずれかが true の場合は適用されます。  
  
-   \@type = 'SQL' or 'TEMPLATE'. If 'TEMPLATE', \@params must not be NULL.    
  
-   *statement_text* sp_executesql と値を使用して送信される、 \@params パラメーターを指定または[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]内部でステートメントがパラメーター化した後に送信します。 データベース API (ODBC、OLE DB、ADO.NET など) からのパラメーター化クエリの送信は、sp_executesql または API サーバー カーソル ルーチンの呼び出しとして [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に示されるため、SQL または TEMPLATE プラン ガイドでも適合させることができます。  
  
 *\@parameter_name data_type*送信するときとまったく同じ形式で指定する必要があります[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]を sp_executesql を使用するか、またはパラメーター化してから内部的に送信します。 詳細については、「解説」を参照してください。 バッチにパラメーターが含まれていない場合は、NULL を指定する必要があります。 サイズ\@params が利用可能なサーバー メモリによってのみ制限されます。  
  
 [\@hints =] {N'OPTION (*query_hint* [、 *.n* ])' |N'*XML_showplan*' |NULL}  
 N'OPTION (*query_hint* [ , *...n* ] )  
 一致するクエリにアタッチする OPTION 句を指定します\@stmt。\@hints構文的に、SELECT ステートメントの OPTION 句と同じあり、有効な一連のクエリ ヒントを含めることができます。  
  
 N'*XML_showplan*'  
 ヒントとして適用する XML 形式のクエリ プランを指定します。  
  
 XML プラン表示を変数に割り当てることをお勧めします。XML プラン表示を変数に割り当てない場合は、XML プラン表示内の単一引用符をエスケープする必要があります。これを行うには、単一引用符の前にもう 1 つ単一引用符を追加します。 例 E を参照してください。  
  
 NULL  
 クエリの OPTION 句で指定した既存のヒントがクエリに適用されないことを示します。 詳細については、[OPTION 句 &#40;TRANSACT-SQL&#41;](../../t-sql/queries/option-clause-transact-sql.md)を参照してください。
  
## <a name="remarks"></a>コメント  
 sp_create_plan_guide の引数は、表示される順序で指定する必要があります。 **sp_create_plan_guide**のパラメーターに値を指定する場合、パラメーター名はすべて明示的に指定するか、すべて指定しないかのいずれかにする必要があります。 たとえば場合、 **\@name =** が指定されると、  **\@stmt =** 、 **\@type =** でも指定する必要があります。 同様に場合、 **\@name =** を省略してのみパラメーターの値が指定されて、他のパラメーター名も省略する必要があり、値だけを指定します。 引数の名前は、構文を理解しやすくするための説明目的のものです。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、指定したパラメーター名と、その名前が使用されている位置にあるパラメーターの名前が一致しているかどうかは確認されません。  
  
 同一のクエリとバッチまたはモジュールに対し、複数の OBJECT または SQL プラン ガイドを作成できます。 ただし、有効にできるプラン ガイドは常に 1 つだけです。  
  
 オブジェクトを作成することはできません型のプラン ガイドを\@ストアド プロシージャ、関数、または、WITH ENCRYPTION 句があるか一時的なを指定する DML トリガーが参照する module_or_batch 値。  
  
 有効、無効にする場合のどちらでも、そのプラン ガイドで参照されている関数、ストアド プロシージャ、または DML トリガーを削除または変更しようとすると、エラーが発生します。 プラン ガイドで参照され、トリガーが定義されているテーブルを削除しようとする場合もエラーが発生します。  
  
> [!NOTE]
>  プラン ガイドは、 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のすべてのエディションで使用できるわけではありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の各エディションでサポートされる機能の一覧については、「 [SQL Server 2016 の各エディションでサポートされる機能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)」を参照してください。 プラン ガイドはどのエディションでも表示できます。 また、プラン ガイドを含むデータベースは、どのエディションに対してもアタッチできます。 アップグレード済みのバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]にデータベースを復元またはアタッチした場合、プラン ガイドはまったく影響を受けません。 サーバーのアップグレード後に、各データベース内のプラン ガイドが適切かどうかを確認する必要があります。  
  
## <a name="plan-guide-matching-requirements"></a>プラン ガイドの照合要件  
 指定するプラン ガイドの\@type = 'SQL' または\@type = 'TEMPLATE' の値、クエリを正常に一致するように*batch_text*と *\@parameter_name data_type* [, *...n* ]、アプリケーションによって送信される対応と完全に同じ形式を指定する必要があります。 つまり、バッチ テキストを、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] コンパイラが受信したときとまったく同じように指定する必要があります。 実際のバッチおよびパラメーター テキストをキャプチャするには、[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] を使用する必要があります。 詳細については、次を参照してください。[を作成およびプラン ガイドをテストする SQL Server Profiler を使用して](../../relational-databases/performance/use-sql-server-profiler-to-create-and-test-plan-guides.md)します。  
  
 ときに\@type = 'SQL' と\@module_or_batch が null の場合の値に設定されている\@の値に設定されている module_or_batch \@stmt します。値は、これにより*statement_text*と完全に同じ形式を指定する必要がありますの文字を送信するときと[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]します。 この適合を容易にするために内部変換は実行されません。  
  
 ときに[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の値と一致する*statement_text*に*batch_text*と *\@parameter_name data_type* [、 *.n* ]、または\@type = **'** オブジェクト '、内の対応するクエリのテキストに*object_name*、次の文字列要素は考慮されません。  
  
-   文字列内の空白文字 (タブ、スペース、復帰、改行)  
  
-   コメント ( **--** または **/ \* \* /** )。  
  
-   末尾のセミコロン  
  
 たとえば、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]と一致できます、 *statement_text*文字列`N'SELECT * FROM T WHERE a = 10'`次*batch_text*:  
  
 `N'SELECT *`  
  
 `FROM T`  
  
 `WHERE a=10'`  
  
 ただし、同じ文字列と一致しませんこの*batch_text*:  
  
 `N'SELECT * FROM T WHERE b = 10'`  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、最初のクエリ内にある復帰、改行、空白文字を無視します。 2 つ目のクエリのシーケンス `WHERE b = 10` は、`WHERE a = 10` とは異なるものと解釈されます。 照合処理では、大文字小文字が区別されないキーワードを除き、(データベースの照合順序で大文字小文字が区別されない場合でも) 大文字小文字およびアクセントが区別されます。 キーワードの省略形は区別されません。 たとえば、キーワード `EXECUTE`、`EXEC`、および `execute` は同じものと解釈されます。  
  
## <a name="plan-guide-effect-on-the-plan-cache"></a>プラン キャッシュに対するプラン ガイドの効果  
 モジュールにプラン ガイドを作成すると、そのモジュールのクエリ プランがプラン キャッシュから削除されます。 バッチに OBJECT 型または SQL 型のプラン ガイドを作成すると、同じハッシュ値を持つバッチのクエリ プランが削除されます。 TEMPLATE 型のプラン ガイドを作成すると、単一ステートメントのバッチがデータベース内のプラン キャッシュからすべて削除されます。  
  
## <a name="permissions"></a>アクセス許可  
 OBJECT 型のプラン ガイドを作成するには、参照先オブジェクトに対する ALTER 権限が必要です。 SQL または TEMPLATE タイプのプラン ガイドを作成するには、現在のデータベースに対する ALTER 権限が必要です。  
  
## <a name="examples"></a>使用例  
  
### <a name="a-creating-a-plan-guide-of-type-object-for-a-query-in-a-stored-procedure"></a>A. ストアド プロシージャのクエリに対する OBJECT タイプのプラン ガイドを作成する  
 次の例では、アプリケーションベースのストアド プロシージャのコンテキストで実行されるクエリに適合するプラン ガイドを作成し、`OPTIMIZE FOR` ヒントをクエリに適用します。  
  
 ストアド プロシージャは次のようになります。  
  
```  
IF OBJECT_ID(N'Sales.GetSalesOrderByCountry', N'P') IS NOT NULL  
    DROP PROCEDURE Sales.GetSalesOrderByCountry;  
GO  
CREATE PROCEDURE Sales.GetSalesOrderByCountry   
    (@Country_region nvarchar(60))  
AS  
BEGIN  
    SELECT *  
    FROM Sales.SalesOrderHeader AS h   
    INNER JOIN Sales.Customer AS c ON h.CustomerID = c.CustomerID  
    INNER JOIN Sales.SalesTerritory AS t   
        ON c.TerritoryID = t.TerritoryID  
    WHERE t.CountryRegionCode = @Country_region;  
END  
GO  
```  
  
 ストアド プロシージャのクエリに対して作成されるプラン ガイドは次のようになります。  
  
```  
EXEC sp_create_plan_guide   
    @name =  N'Guide1',  
    @stmt = N'SELECT *  
              FROM Sales.SalesOrderHeader AS h   
              INNER JOIN Sales.Customer AS c   
                 ON h.CustomerID = c.CustomerID  
              INNER JOIN Sales.SalesTerritory AS t   
                 ON c.TerritoryID = t.TerritoryID  
              WHERE t.CountryRegionCode = @Country_region',  
    @type = N'OBJECT',  
    @module_or_batch = N'Sales.GetSalesOrderByCountry',  
    @params = NULL,  
    @hints = N'OPTION (OPTIMIZE FOR (@Country_region = N''US''))';  
```  
  
### <a name="b-creating-a-plan-guide-of-type-sql-for-a-stand-alone-query"></a>B. スタンドアロン クエリに対する SQL タイプのプラン ガイドを作成する  
 次の例では、sp_executesql システム ストアド プロシージャを使用するアプリケーションで送信されるバッチ内のクエリに適合するプラン ガイドを作成します。  
  
 バッチは、次のようになります。  
  
```  
SELECT TOP 1 * FROM Sales.SalesOrderHeader ORDER BY OrderDate DESC;  
```  
  
 このクエリに対して並列実行プランが生成されないようにするには、次のプラン ガイドを作成します。  
  
```  
EXEC sp_create_plan_guide   
    @name = N'Guide1',   
    @stmt = N'SELECT TOP 1 *   
              FROM Sales.SalesOrderHeader   
              ORDER BY OrderDate DESC',   
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints = N'OPTION (MAXDOP 1)';  
```  
  
### <a name="c-creating-a-plan-guide-of-type-template-for-the-parameterized-form-of-a-query"></a>C. パラメーター化形式のクエリに対する TEMPLATE タイプのプラン ガイドを作成する  
 次の例では、指定されたフォームにパラメーター化されるクエリに適合するプラン ガイドを作成し、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に対してクエリのパラメーター化を強制的に実行させます。 次の 2 つのクエリは構文的には同じですが、定数リテラル値のみが異なります。  
  
```  
SELECT * FROM AdventureWorks2012.Sales.SalesOrderHeader AS h  
INNER JOIN AdventureWorks2012.Sales.SalesOrderDetail AS d   
    ON h.SalesOrderID = d.SalesOrderID  
WHERE h.SalesOrderID = 45639;  
  
SELECT * FROM AdventureWorks2012.Sales.SalesOrderHeader AS h  
INNER JOIN AdventureWorks2012.Sales.SalesOrderDetail AS d   
    ON h.SalesOrderID = d.SalesOrderID  
WHERE h.SalesOrderID = 45640;  
```  
  
 パラメーター化形式のクエリに対するプラン ガイドは次のようになります。  
  
```  
EXEC sp_create_plan_guide   
    @name = N'TemplateGuide1',  
    @stmt = N'SELECT * FROM AdventureWorks2012.Sales.SalesOrderHeader AS h  
              INNER JOIN AdventureWorks2012.Sales.SalesOrderDetail AS d   
                  ON h.SalesOrderID = d.SalesOrderID  
              WHERE h.SalesOrderID = @0',  
    @type = N'TEMPLATE',  
    @module_or_batch = NULL,  
    @params = N'@0 int',  
    @hints = N'OPTION(PARAMETERIZATION FORCED)';  
```  
  
 この例では、 `@stmt` パラメーターの値は、パラメーター化形式のクエリになっています。 この値を取得して sp_create_plan_guide で使用できるようにするには、 [sp_get_query_template](../../relational-databases/system-stored-procedures/sp-get-query-template-transact-sql.md) システム ストアド プロシージャを使用するのが唯一信頼できる方法です。 次のスクリプトを使用すると、パラメーター化クエリを取得してそのクエリに対してプラン ガイドを作成することができます。  
  
```  
DECLARE @stmt nvarchar(max);  
DECLARE @params nvarchar(max);  
EXEC sp_get_query_template   
    N'SELECT * FROM AdventureWorks2012.Sales.SalesOrderHeader AS h  
      INNER JOIN AdventureWorks2012.Sales.SalesOrderDetail AS d   
          ON h.SalesOrderID = d.SalesOrderID  
      WHERE h.SalesOrderID = 45639;',  
    @stmt OUTPUT,   
    @params OUTPUT  
EXEC sp_create_plan_guide N'TemplateGuide1',   
    @stmt,   
    N'TEMPLATE',   
    NULL,   
    @params,   
    N'OPTION(PARAMETERIZATION FORCED)';  
```  
  
> [!IMPORTANT]  
>  sp_get_query_template に渡される `@stmt` パラメーターの定数リテラルの値は、リテラルを置き換えるパラメーターで選択されるデータ型に影響する場合があります。 この値は、プラン ガイドの適合にも影響します。 場合によっては、異なるパラメーター値範囲に対応する複数のプラン ガイドを作成する必要があります。  
  
### <a name="d-creating-a-plan-guide-on-a-query-submitted-by-using-an-api-cursor-request"></a>D. API カーソル要求を使用して送信されるクエリでプラン ガイドを作成する  
 プラン ガイドは、API サーバー カーソル ルーチンから送信されるクエリを適合できます。 これらのルーチンには、sp_cursorprepare、sp_cursorprepexec、および sp_cursoropen があります。 ADO、OLE DB、および ODBC API を使用するアプリケーションは、API サーバー カーソルを使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と頻繁に対話します。 RPC:Starting プロファイラー トレース イベントを表示して、[!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] トレース内の API サーバー カーソル ルーチンの呼び出しを参照できます。  
  
 たとえば、次のデータが、プラン ガイドに合わせて調整するクエリの RPC:Starting プロファイル トレース イベントに示されているものとします。  
  
```  
DECLARE @p1 int;  
SET @p1=-1;  
DECLARE @p2 int;  
SET @p2=0;  
DECLARE @p5 int;  
SET @p5=4104;  
DECLARE @p6 int;  
SET @p6=8193;  
DECLARE @p7 int;  
SET @p7=0;  
EXEC sp_cursorprepexec @p1 output,@p2 output,N'@P1 varchar(255),@P2 varchar(255)',N'SELECT * FROM Sales.SalesOrderHeader AS h INNER JOIN Sales.SalesOrderDetail AS d ON h.SalesOrderID = d.SalesOrderID WHERE h.OrderDate BETWEEN @P1 AND @P2',@p5 OUTPUT,@p6 OUTPUT,@p7 OUTPUT,'20040101','20050101'  
SELECT @p1, @p2, @p5, @p6, @p7;  
```  
  
 このデータを見ると、`SELECT` の呼び出しの `sp_cursorprepexec` クエリに対するプランでマージ結合を使用していることがわかりますが、ハッシュ結合を使用するとします。 `sp_cursorprepexec` を使用して送信されるクエリは、クエリ文字列およびパラメーター文字列の両方を含めて、パラメーター化されます。 `sp_cursorprepexec` の呼び出しで、表示されているとおり完全に同じであるクエリ文字列とパラメーター文字列を使用して、次のプラン ガイドを作成し、プランの選択を変更できます。  
  
```  
EXEC sp_create_plan_guide   
    @name = N'APICursorGuide',  
    @stmt = N'SELECT * FROM Sales.SalesOrderHeader AS h   
              INNER JOIN Sales.SalesOrderDetail AS d   
                ON h.SalesOrderID = d.SalesOrderID   
              WHERE h.OrderDate BETWEEN @P1 AND @P2',  
    @type = N'SQL',  
    @module_or_batch = NULL,  
    @params = N'@P1 varchar(255),@P2 varchar(255)',  
    @hints = N'OPTION(HASH JOIN)';  
```  
  
 今後アプリケーションでこのクエリを実行すると、ここで作成したプラン ガイドの影響を受け、クエリの処理にはハッシュ結合が使用されます。  
  
### <a name="e-creating-a-plan-guide-by-obtaining-the-xml-showplan-from-a-cached-plan"></a>E. キャッシュされたプランから XML プラン表示を取得してプラン ガイドを作成する  
 次の例では、単純なアドホック SQL ステートメントのプラン ガイドを作成します。 このステートメントの目的のクエリ プランは、クエリの XML プラン表示を `@hints` パラメーターで直接指定することにより、プラン ガイドで提供されます。 最初に SQL ステートメントを実行して、プラン キャッシュ内にプランを生成します。 この例では、生成されたプランが目的のプランであり、追加のクエリ チューニングが不要であると想定しています。 クエリの XML プラン表示は、 `sys.dm_exec_query_stats`、 `sys.dm_exec_sql_text`、および `sys.dm_exec_text_query_plan` の各動的管理ビューをクエリすることにより取得され、 `@xml_showplan` 変数に割り当てられます。 次に `@xml_showplan` 変数が、 `sp_create_plan_guide` パラメーターで `@hints` ステートメントに渡されます。 または、 [sp_create_plan_guide_from_handle](../../relational-databases/system-stored-procedures/sp-create-plan-guide-from-handle-transact-sql.md) ストアド プロシージャを使用して、プラン キャッシュ内のクエリ プランからプラン ガイドを作成することもできます。  
  
```  
USE AdventureWorks2012;  
GO  
SELECT City, StateProvinceID, PostalCode FROM Person.Address ORDER BY PostalCode DESC;  
GO  
DECLARE @xml_showplan nvarchar(max);  
SET @xml_showplan = (SELECT query_plan  
    FROM sys.dm_exec_query_stats AS qs   
    CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) AS st  
    CROSS APPLY sys.dm_exec_text_query_plan(qs.plan_handle, DEFAULT, DEFAULT) AS qp  
    WHERE st.text LIKE N'SELECT City, StateProvinceID, PostalCode FROM Person.Address ORDER BY PostalCode DESC;%');  
  
EXEC sp_create_plan_guide   
    @name = N'Guide1_from_XML_showplan',   
    @stmt = N'SELECT City, StateProvinceID, PostalCode FROM Person.Address ORDER BY PostalCode DESC;',   
    @type = N'SQL',  
    @module_or_batch = NULL,   
    @params = NULL,   
    @hints =@xml_showplan;  
GO  
```  
  
## <a name="see-also"></a>参照  
 [プラン ガイド](../../relational-databases/performance/plan-guides.md)   
 [sp_control_plan_guide &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-control-plan-guide-transact-sql.md)   
 [sys.plan_guides &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-plan-guides-transact-sql.md)   
 [データベース エンジン ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/database-engine-stored-procedures-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)   
 [sys.dm_exec_sql_text &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql.md)   
 [sys.dm_exec_cached_plans &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql.md)   
 [sys.dm_exec_query_stats &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql.md)   
 [sp_create_plan_guide_from_handle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-create-plan-guide-from-handle-transact-sql.md)   
 [sys.fn_validate_plan_guide &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-validate-plan-guide-transact-sql.md)   
 [sp_get_query_template &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-get-query-template-transact-sql.md)  
  
  
