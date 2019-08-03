---
title: 照合順序と Unicode のサポート | Microsoft Docs
ms.custom: ''
ms.date: 06/26/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- binary collations [SQL Server]
- expression-level collations [SQL Server]
- Windows collations [SQL Server]
- globalization [SQL Server], about globalization
- supplementary character support
- code pages [SQL Server], about code pages
- collations [SQL Server], about collations
- Unicode [SQL Server], collations
- database-level collations [SQL Server]
- reading order
- column-level collations
- locales [SQL Server], about locales
- locales [SQL Server]
- code pages [SQL Server]
- SQL Server collations
- UTF-8
- UTF-16
- server-level collations [SQL Server]
ms.assetid: 92d34f48-fa2b-47c5-89d3-a4c39b0f39eb
author: stevestein
ms.author: sstein
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: af749bdb7050d9e71fdfe698fe295255a4603add
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68118485"
---
# <a name="collation-and-unicode-support"></a>Collation and Unicode Support
[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md](../../includes/appliesto-ss-asdb-asdw-pdw-md.md)]
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の照合順序により、並べ替え規則、大文字と小文字の区別、およびアクセントの区別のプロパティをデータで利用できるようになります。 **char** や **varchar** などの文字データ型に使用する照合順序は、そのデータ型で表すことのできるコード ページおよび対応する文字を指定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の新しいインスタンスをインストールしているか、データベース バックアップを復元しているか、サーバーをクライアント データベースに接続しているかに関係なく、操作するデータのロケールの要件、並べ替え順序、および大文字と小文字の区別とアクセントの区別について理解することが重要です。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスで使用可能な照合順序の一覧については、「 [sys」を参照してください。fn_helpcollations &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-helpcollations-transact-sql.md)」を参照してください。    
    
サーバー、データベース、列、または式の照合順序を選択すると、データベースのさまざまな操作の結果に影響を与える特定の特性がデータに割り当てられます。 たとえば、`ORDER BY` を使用してクエリを構築する場合、結果セットの並べ替え順序は、データベースに適用される照合順序、またはクエリの式レベルで `COLLATE` 句に指定される照合順序に依存します。    
    
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の照合順序サポートを最大限に活用するには、このトピックで定義されている用語と、それらがデータの特性にどのように関連しているかを理解する必要があります。    
    
##  <a name="Terms"></a> 照合順序の用語    
    
-   [照合順序](#Collation_Defn)    
    
-   [ロケール](#Locale_Defn)    
    
-   [コード ページ](#Code_Page_Defn)    
    
-   [並べ替え順序](#Sort_Order_Defn)    
    
###  <a name="Collation_Defn"></a> 照合順序    
照合順序は、データセット内の各文字を表すビット パターンを指定します。 また、照合順序はデータの並べ替えおよび比較を行うための規則を決定します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、単一のデータベース内で異なる照合順序を持つオブジェクトを格納できます。 非 Unicode 列の場合は、照合順序の設定によってデータのコード ページと表示可能な文字が指定されます。 非 Unicode 列の間を移動するデータは、移動元のコード ページから移動先のコード ページに変換する必要があります。    
    
[!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントの結果は、それぞれ異なる照合順序が設定されている複数のデータベースのコンテキストでステートメントが実行される場合には、データベースごとに異なります。 可能であれば、組織全体で同じ照合順序を使用してください。 これにより、それぞれの文字または Unicode 表現について、照合順序を明示的に指定する必要がなくなります。 異なる照合順序とコード ページが設定されたオブジェクトを操作する場合は、照合の優先順位の規則を考慮してクエリを作成します。 詳細については、「 [照合順序の優先順位 (Transact-SQL)](../../t-sql/statements/collation-precedence-transact-sql.md)」を参照してください。    
    
照合順序に関連するオプションは、大文字と小文字の区別、アクセントの区別、かなの区別、および文字幅の区別、バリエーションの選択の区別です。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、[UTF-8](https://www.wikipedia.org/wiki/UTF-8) エンコードのための追加のオプションが導入されています。 これらのオプションは、照合順序の名前に付加することによって指定されます。 たとえば、 `Japanese_Bushu_Kakusu_100_CS_AS_KS_WS_UTF8` という照合順序では、大文字と小文字、アクセント、かな、文字幅、および UTF-8 エンコードが区別されます。 また、 `Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS` という照合順序では、大文字小文字とアクセントが区別されず、かな、文字幅、異体字セレクターが区別され、非 Unicode エンコードが使用されます。 次の表は、これらの各オプションに関連付けられている動作を示しています。    
    
|オプション|[説明]|    
|------------|-----------------|    
|大文字と小文字を区別する (\_CS)|大文字と小文字を区別します。 このオプションを選択すると、大文字より先に小文字が並べ替えられます。 このオプションを選択しないと、照合順序で大文字と小文字が区別されません。 つまり、大文字と小文字は、並べ替えを行う際に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって同じものと見なされます。 大文字と小文字を区別しないことを明示的に選択するには、\_CI と指定します。|    
|アクセントを区別する (\_AS)|アクセントのある文字とアクセントのない文字を区別します。 たとえば、"a" と "&#xE1;" は等しくありません。 このオプションを選択しないと、照合順序でアクセントが区別されません。 つまり、アクセントのある文字とアクセントのない文字は、並べ替えを行う際に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって同じものと見なされます。 アクセントを区別しないことを明示的に選択するには、\_AI と指定します。|    
|かなを区別する (\_KS)|次の 2 種類の日本語かな文字を区別します。ひらがなとカタカナ。 このオプションを選択しないと、照合順序でかなが区別されません。 つまり、ひらがなとカタカナは、並べ替えを行う際に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって同じものと見なされます。 かなを区別しないように指定する唯一の方法は、このオプションを省略することです。|    
|文字幅を区別する (\_WS)|全角文字と半角文字を区別します。 このオプションを選択しないと、同じ文字の全角表現と半角表現は、並べ替えを行う際に [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] によって同じものと見なされます。 文字幅を区別しないように指定する唯一の方法は、このオプションを省略することです。|    
|異体字の選択を区別する (\_VSS) | [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)]から導入された日本語の照合順序 Japanese_Bushu_Kakusu_140 と Japanese_XJIS_140 で多様な表意文字のバリエーションの選択を区別します。 バリエーションのシーケンスは、基本文字と追加のバリエーションの選択で構成されます。 この \_VSS オプションを選択しない場合、照合順序で異体字の選択が区別されず、比較において異体字の選択が考慮されることはありません。 つまり、SQL Server では、並べ替えが同じになるように、バリエーションの選択が異なる同じ基本文字に基づいて構築された文字を考慮しています。 [「Unicode Ideographic Variation Database」](https://www.unicode.org/reports/tr37/)(Unicode 表意文字のバリエーション データベース) も参照してください。 <br/><br/> 異体字セレクターを区別する (\_VSS) 照合順序は、全文検索インデックスではサポートされていません。 フルテキスト検索インデックスでは、アクセントを区別する (\_AS)、かなを区別する (\_KS)、文字幅を区別する (\_WS) オプションのみがサポートされます。 SQL Server XML と CLR のエンジンでは、(\_VSS) 異体字セレクターはサポートされていません。
|UTF-8 (\_UTF8)|UTF-8 でエンコードされたデータを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に格納できるようにします。 このオプションを選択しなかった場合、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、適用可能なデータ型に対して既定の非 Unicode エンコード形式が使用されます。| 
    
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、次の照合順序のセットをサポートしています。    
    
#### <a name="windows-collations"></a>Windows 照合順序    
Windows 照合順序は、関連する Windows システム ロケールに基づく文字データを格納するための規則を定義します。 Windows 照合順序では、非 Unicode データの比較が、Unicode データと同じアルゴリズムを使用して実装されます。 基本の Windows 照合順序規則では、辞書順の並べ替えが適用される場合に使用される文字または言語と、非 Unicode 文字データの格納に使用されるコード ページを指定します。 Unicode 順の並べ替えと非 Unicode 順の並べ替えは、いずれも、特定のバージョンの Windows の文字列比較と互換性があります。 このしくみによって [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 内のデータ型に一貫性が生まれ、開発者が [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と同一の規則を使用してアプリケーションで文字列を並べ替えることが可能になります。 詳細については、「[Windows 照合順序名 &#40;Transact-SQL&#41;](../../t-sql/statements/windows-collation-name-transact-sql.md)」を参照してください。    
    
#### <a name="binary-collations"></a>バイナリ照合順序    
バイナリ照合順序では、ロケールおよびデータ型によって定義されるコーディングされた値の順序に基づいてデータを並べ替えます。 バイナリ照合順序では大文字と小文字が区別されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のバイナリ照合順序は、使用するロケールおよび ANSI コード ページを定義します。 また、バイナリ並べ替え順を実施します。 これらは比較的単純なので、バイナリ照合順序はアプリケーションのパフォーマンスを向上させるために役立ちます。 非 Unicode データ型の場合は、ANSI コード ページで定義されているコード ポイントに基づいてデータが比較されます。 Unicode データ型の場合は、Unicode コード ポイントに基づいてデータが比較されます。 Unicode データ型のバイナリ照合順序では、データを並べ替える際にロケールが考慮されません。 たとえば、Unicode データに対して Latin_1_General_BIN と Japanese_BIN を使用した場合、並べ替え結果はどちらも同じになります。    
    
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]には、古い **BIN** 照合順序と新しい **BIN2** 照合順序の 2 種類のバイナリ照合順序があります。 **BIN2** 照合順序では、すべての文字がコード ポイントに従って並び替えられます。 **BIN** 照合順序では、最初の文字のみがコード ポイントに従って並び替えられ、残りの文字はバイト値に従って並び替えられます。 (Intel プラットフォームはリトル エンディアン アーキテクチャであるため、Unicode コード文字は常にバイト スワップして格納されます)。    
    
#### <a name="sql-server-collations"></a>SQL Server 照合順序    
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序 (SQL_\*) では、以前のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] と互換性のある並べ替え順が使用されます。 非 Unicode データについては、辞書順での並べ替え規則は Windows オペレーティング システムによって提供されるどの並べ替えルーチンとも互換性はありません。 ただし、Unicode データの並べ替えは、特定のバージョンの Windows 並べ替え規則と互換性があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序では非 Unicode データと Unicode データで別々の比較規則を使用するため、基本となるデータ型によっては、同一データの比較で異なる結果が得られる場合があります。 詳細については、「[SQL Server 照合順序名 &#40;Transact-SQL&#41;](../../t-sql/statements/sql-server-collation-name-transact-sql.md)」を参照してください。    
    
> [!NOTE]    
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の英語インスタンスをアップグレードするときに、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の既存インスタンスとの互換性のために [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序 (SQL_\*) を指定することができます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスの既定照合順序がセットアップ時に定義されるため、次のいずれかに該当する場合は、照合順序の設定を注意深く指定するようにしてください。    
>     
> -   アプリケーション コードが以前の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 照合順序の動作に依存している場合。    
> -   複数の言語に対応する文字データを格納する必要がある場合。    
    
 照合順序の設定は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスの次のレベルでサポートされます。    
    
#### <a name="server-level-collations"></a>サーバーレベルの照合順序   
既定のサーバー照合順序は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセットアップ時に設定され、システム データベースおよびすべてのユーザー データベースの既定の照合順序にもなります。 なお、Unicode 専用の照合順序はサーバーレベルの照合順序としてサポートされないため、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のセットアップ時に選択することはできません。    
    
サーバーに照合順序を指定した後は、照合順序を簡単には変更できません。変更するには、すべてのデータベース オブジェクトとデータをエクスポートし、 **master** データベースを再構築してから、すべてのデータベース オブジェクトとデータをインポートする必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスの既定の照合順序を変更する代わりに、新しいデータベースまたはデータベース列の作成時に、目的の照合順序を指定することができます。    
    
#### <a name="database-level-collations"></a>データベースレベルの照合順序    
データベースを作成または修正する際には、CREATE DATABASE または ALTER DATABASE ステートメントの COLLATE 句を使用して、データベースの既定の照合順序を指定できます。 照合順序が指定されない場合、データベースにはサーバーの照合順序が割り当てられます。    
    
サーバーの照合順序を変更すること以外に、システム データベースの照合順序を変更する方法はありません。    
    
データベースの照合順序は、データベース内のすべてのメタデータで使用され、データベース内で使用されるすべての文字列型の列、一時オブジェクト、変数名、およびその他のすべての文字列の既定値になります。 ユーザー データベースの照合順序を変更する場合は、データベースのクエリが一時テーブルにアクセスするときに照合順序が競合する可能性があります。 一時テーブルは常に **tempdb** システム データベースに格納され、このデータベースではインスタンスの照合順序が使用されます。 ユーザー データベースと **tempdb** の文字データを比較するクエリは、文字データの評価で照合順序が競合すると、失敗します。 この問題は、クエリに COLLATE 句を指定することで解決できます。 詳細については、「[COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)」を参照してください。    
    
#### <a name="column-level-collations"></a>列レベルの照合順序    
テーブルを作成または変更するときに、COLLATE 句を使用して、文字列型の各列に対して照合順序を指定できます。 照合順序を指定しない場合、データベースの既定の照合順序が列に割り当てられます。    
    
#### <a name="expression-level-collations"></a>式レベルの照合順序    
式レベルの照合順序は、ステートメントの実行時に設定され、結果セットが返される方法に影響を及ぼします。 これにより、ORDER BY の並べ替え結果をロケール固有のものにすることができます。 式レベルの照合順序を実装するには、次のような COLLATE 句を使用します。    
    
```sql    
SELECT name FROM customer ORDER BY name COLLATE Latin1_General_CS_AI;    
```    
    
###  <a name="Locale_Defn"></a> ロケール    
ロケールは、場所またはカルチャに関連付けられる一連の情報です。 これには、言語の名前や ID、言語の記述に使用される文字表記、文化的慣習などがあります。 照合順序は、1 つ以上のロケールに関連付けることができます。 詳細については、「 [Microsoft によって割り当てられているロケール ID](https://msdn.microsoft.com/goglobal/bb964664.aspx)」を参照してください。    
    
###  <a name="Code_Page_Defn"></a> Code Page    
 コード ページは、特定の文字表記の順序付けられた文字のセットです。コード ページでは、数値インデックス (コード ポイント値) が各文字に関連付けられます。 Windows コード ページは、通常は *文字セット* または *charset*と呼ばれています。 コード ページは、各種の Windows システム ロケールで使用される文字セットおよびキーボード レイアウトをサポートするために使用されます。     
 
###  <a name="Sort_Order_Defn"></a> Sort Order    
 並べ替え順序は、データ値の並べ替え方法を指定します。 これは、データ比較の結果に影響を及ぼします。 データは、照合順序を使用して並べ替えられ、インデックスを使用して最適化することができます。    
    
##  <a name="Unicode_Defn"></a> Unicode のサポート    
Unicode は、コード ポイントを文字にマップするための標準です。 Unicode は世界中のすべての言語のすべての文字を処理できるようにデザインされているので、異なる文字のセットを扱うために他のコード ページを必要とすることがありません。 
   
クライアントが使用するコード ページは、オペレーティング システムの設定によって決まります。 Windows オペレーティング システムでクライアント コード ページを設定するには、コントロール パネルの **[地域と言語のオプション]** を使用します。    

非 Unicode データ型には、多くの制限が関連付けられています。 これは、Unicode に対応していないコンピューターではコード ページの使用が 1 つに制限されているためです。 Unicode コードを使用すると、必要なコード ページ変換が少なくなるので、パフォーマンスの向上が期待できます。 Unicode 照合順序は、サーバー レベルではサポートされないため、データベース、列、式の各レベルで個別に選択する必要があります。    
   
データをサーバーからクライアントに移動するとき、古いクライアント ドライバーでサーバー照合順序が認識されないことがあります。 これは、データを Unicode サーバーから非 Unicode クライアントに移動する場合に発生する可能性があります。 最善の対処方法は、クライアント オペレーティング システムをアップグレードして、基になるシステムの照合順序を更新することです。 クライアントにデータベース クライアント ソフトウェアがインストールされている場合は、データベース クライアント ソフトウェアにサービスの更新プログラムを適用する方法もあります。    
    
> [!TIP]
> また、サーバー上のデータに異なる照合順序を使用してみることもできます。 クライアントのコード ページにマップする照合順序を選択します。    

複数の言語を反映する文字データを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] から [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]) に格納する場合は、非 Unicode データ型 (**char**、**varchar**、**text**) ではなく、Unicode データ型 (**nchar**、**nvarchar**、**ntext**) を使用してください。 

> [!NOTE]
> Unicode データ型の場合、[!INCLUDE[ssde_md](../../includes/ssde_md.md)]では、UCS-2 を使うと最大 65,535 文字を表すことができ、補助文字を使った場合は Unicode の全範囲 (1,114,111 文字) を表すことができます。 補助文字の有効化について詳しくは、「[補助文字](#Supplementary_Characters)」をご覧ください。

[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 以降では、 UTF 8 対応の照合順序 (\_UTF8) を使用した場合、以前の非 Unicode データ型 (**char** と **varchar**) が Unicode (UTF-8) データ型になります。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、既存 Unicode (UTF-16) データ型 (**nchar**、**nvarchar**、および **ntext**) の動作は変わりません。 詳しくは、「[UTF-8 と UTF-16 でのストレージの相違点](#storage_differences)」をご覧ください。
       
一部の Unicode 文字の検索と並べ替えの機能を向上させるために [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] から [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]) で提供される UTF-16 照合順序を使うには (Windows 照合順序のみ)、補助文字 (\_SC) の照合順序の 1 つ、またはバージョン 140 の照合順序の 1 つを選ぶことができます。    
 
一部の Unicode 文字の検索と並べ替えの機能を向上させるために [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] で提供される UTF-8 照合順序を使うには (Windows 照合順序のみ)、UTF-8 エンコード対応の照合順序 (\_UTF8) を選択する必要があります。
 
-   UTF8 フラグは、以下に適用できます。    
    -   バージョン 90 照合順序 
        > [!NOTE]
        > 補助文字 (\_SC) または異体字セレクター (\_VSS) を区別する照合順序がこのバージョンに既に存在している場合に限られます。
    -   バージョン 100 照合順序    
    -   バージョン 140 照合順序   
    -   BIN2<sup>1</sup> バイナリ照合順序
    
-   UTF8 フラグは、以下には適用できません。    
    -   補助文字 (\_SC) または異体字セレクター (\_VSS) をサポートしていないバージョン 90 照合順序    
    -   BIN または BIN2<sup>2</sup> バイナリ照合順序    
    -   SQL\* 照合順序  
    
<sup>1</sup> [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] CTP 2.3 以降。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] CTP 3.0 では、照合順序 UTF8_BIN2 が Latin1_General_100_BIN2_UTF8 に置き換えられました。     
<sup>2</sup> [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] CTP 2.3 まで。 
    
Unicode または非 Unicode データ型の使用に関連する問題点を評価するには、使用環境におけるパフォーマンスの違いを測定するためのシナリオをテストする必要があります。 組織内のシステムで使用する照合順序を標準化し、可能であれば Unicode サーバーおよびクライアントを配置するようにしてください。    
    
さまざまな状況で、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は他のサーバーまたはクライアントとやり取りし、組織ではアプリケーションやサーバー インスタンス間で複数のデータ アクセス標準を使用する可能性があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] クライアントは次の 2 つの主要タイプのいずれかになります。    
    
-   OLE DB および Open Database Connectivity (ODBC) Version 3.7 以降のバージョンを使用する**Unicode クライアント**    
-   DB ライブラリおよび ODBC Version 3.6 以前のバージョンを使用する**非 Unicode クライアント**    
    
以下の表は、Unicode 型サーバーと非 Unicode 型サーバーの各種の組み合わせにおける多言語データの使用に関する情報を示しています。    
    
|[サーバー]|クライアント|利点または制限事項|    
|------------|------------|-----------------------------|    
|Unicode|Unicode|このシナリオでは、システム全体で Unicode データが使用されるため、最高のパフォーマンスが実現され、取得されるデータが破損から保護されます。 これは、ActiveX Data Objects (ADO)、OLE DB、および ODBC Version 3.7 以降のバージョンの場合に該当します。|    
|Unicode|非 Unicode|このシナリオで、特に新しいオペレーティング システムを実行しているサーバーと、古いバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]または古いオペレーティング システムを実行しているクライアントが接続されている場合、データをクライアント コンピューターに移動するときに制約やエラーが生じることがあります。 サーバー上の Unicode データは、非 Unicode クライアント上の対応するコード ページにマップしてデータを変換しようと試みます。|    
|非 Unicode|Unicode|これは、多言語データの使用に理想的な構成とはいえません。 Unicode データを非 Unicode サーバーに書き込むことはできません。 サーバーのコード ページ内に存在しないサーバーにデータを送信すると、問題が発生する可能性があります。|    
|非 Unicode|非 Unicode|これは、多言語データに関して非常に制限的なシナリオです。 使用できるコード ページは 1 つだけです。|    
    
##  <a name="Supplementary_Characters"></a> 補助文字    
Unicode Consortium では、各文字に一意のコード ポイント (000000 から 10FFFF の範囲の値) が割り当てられています。 最もよく使われる文字のコード ポイント値は000000 から 00FFFF の範囲で (65,535 文字)、これはメモリおよびディスク上の 8 ビットまたは 16 ビット ワードに適合します。 この範囲は、通常、基本多言語面 (BMP) として指定されます。 

ただし、Unicode Consortium では、さらに 16 個の文字の "面" が確立されており、それぞれ BMP と同じサイズです。 この定義により、Unicode では、000000 から 10FFFF までのコード ポイントで 1,114,112 個の文字 (つまり、2<sup>16</sup> * 17 文字) を表すことができます。 コード ポイント値が 00FFFF より大きい文字では、2 から 4 個の連続する 8 ビット ワード (UTF-8)、または 2 個の連続する 16 ビット ワード (UTF-16) が必要です。 BMP を越えて存在するこれらの文字は "*補助文字*" と呼ばれ、追加の連続する 8 ビット ワードまたは 16 ビット ワードは "*サロゲート ペア*" と呼ばれます。 補助文字、サロゲート、およびサロゲート ペアについて詳しくは、[Unicode 標準](http://www.unicode.org/standard/standard.html)をご覧ください。    

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、BMP 範囲 (000000 から 00FFFF) の Unicode データを格納するために、**nchar** や **nvarchar** などのデータ型が提供されています。これらは、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] では UCS-2 を使ってエンコードされます。 

[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] では、完全な Unicode 文字範囲 (000000 から 10FFFF) を表すために、**nchar**、**nvarchar**、**sql_variant** の各データ型で使用できる補助文字 (\_SC) 照合順序の新しいファミリが導入されました。 たとえば、 `Latin1_General_100_CI_AS_SC`や、日本語の照合順序を使用する場合は `Japanese_Bushu_Kakusu_100_CI_AS_SC`を使用できます。 
 
[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、新しい UTF-8 対応の照合順序 ([\_UTF8](#utf8)) を使用して、補助文字のサポートが **char** および **varchar** データ型まで拡張されています。 これらでは、完全な Unicode 文字範囲を表すこともできます。   

> [!NOTE]
> [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 以降では、すべての新しい **\_140** 照合順序で補助文字が自動的にサポートされます。

補助文字を使用する場合は、以下の点に注意してください。    
    
-   補助文字は、90 以上の照合順序バージョンで、並べ替えと比較の操作に使用できます。    
-   すべてのバージョン 100 の照合順序で、補助文字の言語的な並べ替えがサポートされています。    
-   補助文字は、データベース オブジェクトの名前など、メタデータ内で使用することはできません。    
-   補助文字 (\_SC) を含む照合順序を使用しているデータベースでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] レプリケーションを有効にすることはできません。 これは、レプリケーション用に作成されるシステム テーブルとストアド プロシージャの一部で、補助文字をサポートしていない古い **ntext** データ型が使われているためです。  

-   SC フラグは、以下に適用できます。    
    -   バージョン 90 照合順序    
    -   バージョン 100 照合順序    
    
-   SC フラグは、以下には適用できません。    
    -   バージョン 80 バージョンなしの Windows 照合順序    
    -   BIN または BIN2 バイナリ照合順序    
    -   SQL\* 照合順序    
    -   バージョン 140 照合順序 (これらは、補助文字を既にサポートしているので、SC フラグを必要としません)    
    
次の表では、一部の文字列関数と文字列演算子で、補助文字が SC 照合順序ありで使用される場合と、補助文字対応 (SCA) 照合順序なしで使用される場合の動作を比較しています。    
    
|文字列関数または演算子|補助文字対応 (SCA) 照合順序あり|SCA 照合順序なし|    
|---------------------------------|--------------------------|-----------------------------|    
|[CHARINDEX](../../t-sql/functions/charindex-transact-sql.md)<br /><br /> [LEN](../../t-sql/functions/len-transact-sql.md)<br /><br /> [PATINDEX](../../t-sql/functions/patindex-transact-sql.md)|UTF-16 サロゲート ペアは、1 つのコード ポイントとしてカウントされます。|UTF-16 サロゲート ペアは、2 つのコード ポイントとしてカウントされます。|    
|[LEFT](../../t-sql/functions/left-transact-sql.md)<br /><br /> [REPLACE](../../t-sql/functions/replace-transact-sql.md)<br /><br /> [REVERSE](../../t-sql/functions/reverse-transact-sql.md)<br /><br /> [RIGHT](../../t-sql/functions/right-transact-sql.md)<br /><br /> [SUBSTRING](../../t-sql/functions/substring-transact-sql.md)<br /><br /> [STUFF](../../t-sql/functions/stuff-transact-sql.md)|これらの関数は、各サロゲート ペアを 1 つのコード ポイントとして処理し、期待どおりに動作します。|これらの関数では、サロゲート ペアが分割され、予期しない結果が生じることがあります。|    
|[NCHAR](../../t-sql/functions/nchar-transact-sql.md)|0 ～ 0x10FFFF の範囲の指定した Unicode コード ポイント値に対応する文字を返します。 指定された値が 0 ～ 0xFFFF の範囲内にある場合、1 つの文字だけが返されます。 値が大きい場合、対応するサロゲート ペアが返されます。|0 xFFFF よりも高い値の場合は、対応するサロゲートの代わりに NULL が返されます。|    
|[UNICODE](../../t-sql/functions/unicode-transact-sql.md)|0 から 0x10FFFF の範囲の UTF-16 コード ポイントが返されます。|0 から 0xFFFF の範囲の UCS-2 コード ポイントが返されます。|    
|[1 文字に一致するワイルドカード](../../t-sql/language-elements/wildcard-match-one-character-transact-sql.md)<br /><br /> [ワイルドカード - 一致しない文字列](../../t-sql/language-elements/wildcard-character-s-not-to-match-transact-sql.md)|補助文字は、すべてのワイルドカード操作でサポートされています。|補助文字は、これらのワイルドカード操作でサポートされていません。 その他のワイルドカード演算子がサポートされます。|    
    
## <a name="GB18030"></a> GB18030 のサポート    
GB18030 は中華人民共和国が単独で中国語の文字のエンコードに使用している標準規格です。 GB18030 文字の長さは 1 バイト、2 バイト、4 バイトのいずれかです。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、クライアント側アプリケーションからサーバーに GB18030 でエンコードした文字が入力されたときに文字を認識し、内部的には Unicode 文字に変換して格納することで GB18030 文字をサポートしています。 サーバーに格納された GB18030 文字は、それ以降の操作では Unicode 文字として処理されます。 任意の中国語の照合順序を使用できますが、最新の 100 バージョンの使用をお勧めします。 すべての _100 レベルの照合順序は、GB18030 文字の言語的な並べ替えをサポートしています。 データに補助文字 (サロゲート ペア) が含まれている場合は、検索や並べ替えの機能を向上させるために、 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] で利用可能な SC 照合順序を使用できます。    
    
## <a name="Complex_script"></a> 複雑な文字表記のサポート    
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は、複雑な文字表記の入力、格納、変更、および表示をサポートできます。 複雑な文字表記には、次の種類があります。    
    
-   アラビア語テキストと英語テキストの組み合わせなど、右から左に記述されるテキストと左から右に記述されるテキストの両方の組み合わせを含む文字表記。    
-   アラビア語、インド諸語、タイ語の文字など、位置によって、または別の種類の文字と組み合わせた場合に、文字が変形する文字表記。    
-   タイ語など、単語間に区切りがないため、単語を識別するための内部辞書を必要とする言語。    
    
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を操作するデータベース アプリケーションは、複雑な文字表記をサポートするコントロールを使用する必要があります。 マネージド コードで作成される標準の Windows フォーム コントロールは、複雑な文字表記を使用できます。    

## <a name="Japanese_Collations"></a> [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] に追加された日本語照合順序
 
[!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 以降では、各種オプションの順列 (\_CS、\_AS、\_KS、\_WS、\_SS など) で、の新しい日本語照合順序がサポートされています。 

これらの照合順序を一覧表示するには、[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]に対してクエリを実行します。      

```sql 
SELECT Name, Description FROM fn_helpcollations()  
WHERE Name LIKE 'Japanese_Bushu_Kakusu_140%' OR Name LIKE 'Japanese_XJIS_140%'
``` 

すべての新しい照合順序には補助文字の組み込みサポートがあるので、どの新しい **\_140** 照合順序にも SC フラグはありません (または必要ありません)。

これらの照合順序は、[!INCLUDE[ssde_md](../../includes/ssde_md.md)] インデックス、メモリ最適化テーブル、列ストア インデックス、ネイティブ コンパイル モジュールでサポートされています。

<a name="ctp23"></a>

## <a name="utf8"></a> UTF-8 のサポート
[!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、インポートまたはエクスポートのエンコードとして、および文字列データのデータベース レベルまたは列レベルの照合順序として、広く使用されている UTF-8 文字エンコードの完全なサポートが導入されています。 UTF-8 は、**char** および **varchar** データ型で許可されており、`UTF8` サフィックスを持つようにオブジェクトの照合順序を作成するか変更すると有効になります。 たとえば、`LATIN1_GENERAL_100_CI_AS_SC` を `LATIN1_GENERAL_100_CI_AS_SC_UTF8` に変更するような場合です。 

UTF-8 は、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] で導入された補助文字をサポートする Windows 照合順序にのみ使用できます。 **nchar** と **nvarchar** では、UCS-2 または UTF-16 エンコードのみが許可され、変更されていません。

### <a name="storage_differences"></a> UTF-8 と UTF-16 でのストレージの相違点
Unicode Consortium では、各文字に一意のコード ポイント (000000 から 10FFFF の範囲の値) が割り当てられています。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、完全な範囲を表すために、UTF-8 エンコードと UTF-16 エンコードの両方を使用できます。    
-  UTF-8 エンコードでは、ASCII 範囲 (000000 – 00007F) の文字には 1 バイト、コード ポイント 000080 – 0007FF には 2 バイト、コード ポイント 000800 – 00FFFF には 3 バイト、コード ポイント 0010000 – 0010FFFF には 4 バイトが、それぞれ必要です。 
-  UTF-16 エンコードでは、コード ポイント 000000 – 00FFFF には 2 バイト、コード ポイント0010000 – 0010FFFF には 4 バイトが必要です。 

次の表では、各文字範囲およびエンコード種類に対するエンコード ストレージ バイトの概要を示します。

|コードの範囲 (16 進)|コードの範囲 (10 進)|UTF-8 でのストレージ バイト数 <sup>1</sup>|UTF-16 でのストレージ バイト数 <sup>1</sup>|    
|---------------------------------|---------------------------------|--------------------------|-----------------------------|   
|000000 – 00007F|0 - 127|1|2|
|000080 – 00009F<br />0000A0 – 0003FF<br />000400 – 0007FF|128 – 159<br />160 – 1,023<br />1,024 – 2,047|2|2|
|000800 – 003FFF<br />004000 – 00FFFF|2,048 - 16,383<br />16,384 – 65,535|3|2|
|010000 – 03FFFF <sup>2</sup><br /><br />040000 – 10FFFF <sup>2</sup>|65,536 – 262,143 <sup>2</sup><br /><br />262,144 – 1,114,111 <sup>2</sup>|4|4|

<sup>1</sup> ストレージ バイト数は、それぞれのデータ型のディスク上でのストレージ サイズではなく、エンコード済みのバイト長を示します。 ディスク上のストレージ サイズについて詳しくは、「[nchar および nvarchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md)」と「[char および varchar](../../t-sql/data-types/char-and-varchar-transact-sql.md)」をご覧ください。

<sup>2</sup> [補助文字](#Supplementary_Characters)のコード ポイント範囲。

> [!TIP]   
> [CHAR(*n*) および VARCHAR(*n*)](../../t-sql/data-types/char-and-varchar-transact-sql.md)、または[NCHAR(*n*) および NVARCHAR(*n*)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) では *n* は文字数を定義すると考えるのが一般的です。 これは、CHAR (10) 列の例では、0 から 127 の範囲の 10 個の ASCII 文字を Latin1_General_100_CI_AI などの照合順序を使用して格納できるためで、この範囲内の各文字が 1 バイトのみを使用するためです。    
> ただし、[CHAR(*n*) および VARCHAR(*n*)](../../t-sql/data-types/char-and-varchar-transact-sql.md) では、*n* は **bytes** (0-8,000) の文字列サイズを定義し、[NCHAR(*n*) および NVARCHAR(*n*)](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) では *n* は**byte-pairs** (0-4,000) の文字列の長さを定義します。 *n* は、格納できる文字数を定義しません。

上記のように、使われている文字セットによっては、適切な Unicode エンコードとデータ型を選択することで、ストレージを大幅に節約したり、現在のストレージの占有領域を増やしたりする可能性があります。 たとえば、Latin1_General_100_CI_AI_SC_UTF8 など、UTF-8 対応のラテン語の照合順序を使用する場合、`CHAR(10)` 列に 10 バイトが格納され、0-127 の範囲に 10 個の ASCII 文字を保持できますが、128-2047 の範囲では 5 文字のみ、2048-65535 の範囲には 3 文字のみ保持できます。 これに対して、`NCHAR(10)` 列には 10 バイトペア (20 バイト) が格納されるため、0-65535 の範囲に 10 文字を保持できます。  

データベースまたは列に UTF-8 または UTF-16 のどちらのエンコードを使うかを選択する前に、格納される文字列データの分布を考慮してください。
-  主に 0-127 の ASCII 範囲の場合は (英語など)、各文字に UTF-8 では 1 バイト、UTF-16 では 2 バイトが必要です。 UTF-8 を使う方がストレージに関してメリットがあります。 0-127 の範囲の ASCII 文字の既存の列データ型を、UTF-8 対応の照合順序を使って `NCHAR(10)` から `CHAR(10)` に変更すると、必要なストレージが 50% 削減されます。 このように減るのは、`NCHAR(10)` を保存するには 20 バイト必要であるのに対し、`CHAR(10)` では同じ Unicode 文字列表現に 10 バイトしか必要ないためです。    
-  ASCII の範囲の上の、ほぼすべてのラテン語系スクリプト、およびギリシャ語、キリル語、コプト語、アルメニア語、ヘブライ語、アラビア語、シリア語、Tāna、N’Ko では、UTF-8 および UTF-16 のどちらでも、1 文字あたり 2 バイトが必要です。 これらの場合は、対応するデータ型で大きなストレージの違いはありません (たとえば、**char** または **nchar** を使う場合)。
-  主に東アジア言語のスクリプト (韓国語、中国語、日本語) の場合は、各文字に UTF-8 では 3 バイト、UTF-16 では 2 バイトが必要です。 UTF-16 を使う方がストレージに関してメリットがあります。 
-  010000 から 10FFFF の範囲の文字には、UTF-8 と UTF-16 のどちらでも 4 バイト必要です。 これらの場合は、対応するデータ型でストレージの違いはありません (たとえば、**char** または **nchar** を使う場合)。

その他の考慮事項については、「[国際化に対応した Transact-SQL ステートメントの記述](../../relational-databases/collations/write-international-transact-sql-statements.md)」をご覧ください。

##  <a name="Related_Tasks"></a> 関連タスク    
    
|タスク|トピック|    
|----------|-----------|    
|SQL Server のインスタンスの照合順序を設定または変更する方法について説明します。|[サーバーの照合順序の設定または変更](../../relational-databases/collations/set-or-change-the-server-collation.md)|    
|ユーザー データベースの照合順序を設定または変更する方法について説明します。|[データベースの照合順序の設定または変更](../../relational-databases/collations/set-or-change-the-database-collation.md)|    
|データベース内の列の照合順序を設定または変更する方法について説明します。|[列の照合順序の設定または変更](../../relational-databases/collations/set-or-change-the-column-collation.md)|    
|サーバー、データベース、または列のレベルでの照合順序の情報を取得する方法について説明します。|[照合順序情報の表示](../../relational-databases/collations/view-collation-information.md)|    
|Transact-SQL ステートメントを、ある言語から別の言語に容易に移行したり、複数の言語を簡単にサポートしたりできるように記述する方法について説明します。|[国際化に対応した Transact-SQL ステートメントの記述](../../relational-databases/collations/write-international-transact-sql-statements.md)|    
|エラー メッセージの言語と、日付、時刻、通貨データを使用および表示する際の設定を変更する方法について説明します。|[セッション言語の設定](../../relational-databases/collations/set-a-session-language.md)|    
    
##  <a name="Related_Content"></a> 関連コンテンツ    
[SQL Server ベスト プラクティス照合順序の変更](https://go.microsoft.com/fwlink/?LinkId=113891)    
[Unicode 文字形式を使用したデータのインポートまたはエクスポート &#40;SQL Server&#41;](../../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)        
[国際化に対応した Transact-SQL ステートメントの記述](../../relational-databases/collations/write-international-transact-sql-statements.md)     
[SQL Server ベスト プラクティス Unicode への移行](https://go.microsoft.com/fwlink/?LinkId=113890) - 今後は維持されません   
[Unicode コンソーシアムの Web サイト](https://go.microsoft.com/fwlink/?LinkId=48619)   
[Unicode 標準](http://www.unicode.org/standard/standard.html)      
    
## <a name="see-also"></a>参照    
[包含データベースの照合順序](../../relational-databases/databases/contained-database-collations.md)     
[フルテキスト インデックス作成時の言語の選択](../../relational-databases/search/choose-a-language-when-creating-a-full-text-index.md)     
[sys.fn_helpcollations &#40;Transact-SQL&#41;](../../relational-databases/system-functions/sys-fn-helpcollations-transact-sql.md)    
    
 
