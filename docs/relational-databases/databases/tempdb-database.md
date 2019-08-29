---
title: tempdb データベース | Microsoft Docs
description: このトピックでは、SQL Server と Azure SQL Database で tempdb データベースを構成し、使用する方法について説明します。
ms.custom: P360
ms.date: 08/21/2019
ms.prod: sql
ms.prod_service: database-engine
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- temporary tables [SQL Server], tempdb database
- tempdb database [SQL Server], about tempdb
- temporary stored procedures [SQL Server]
- tempdb database [SQL Server]
ms.assetid: ce4053fb-e37a-4851-b711-8e504059a780
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
monikerRange: =azuresqldb-current||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 8197b243bc0789da9acb0e94069585d8619d5fa0
ms.sourcegitcommit: 5e838bdf705136f34d4d8b622740b0e643cb8d96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69653773"
---
# <a name="tempdb-database"></a>tempdb データベース

[!INCLUDE[appliesto-ss-asdb-xxxx-xxx-md](../../includes/appliesto-ss-asdb-xxxx-xxx-md.md)]

**tempdb** システム データベースは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスまたは SQL Database に接続しているすべてのユーザーが使用できるグローバル リソースです。 Tempdb で保持するもの:  
  
- グローバルまたはローカルな一時テーブルおよびインデックス、一時ストアド プロシージャ、テーブル変数、テーブル値関数で返されるテーブル、カーソルなど、明示的に作成された一時的な**ユーザー オブジェクト**。  
- データベース エンジンによって作成された**内部オブジェクト**。 たとえば、次のオブジェクトにアクセスできます。
  - スプール、カーソル、並べ替え、および一時的なラージ オブジェクト (LOB) 記憶域の中間結果を格納する作業テーブル。
  - ハッシュ結合操作またはハッシュ集計操作用の作業ファイル
  - インデックスの作成または再構築などの操作 (SORT_IN_TEMPDB を指定した場合) や、GROUP BY、ORDER BY、または UNION クエリにおける並べ替えの中間結果

  > [!NOTE]
  > 各内部オブジェクトでは、少なくとも 9 つのページが使用されます (IAM ページと 8 ページ分のエクステント)。 ページとエクステントの詳細については、「[ページとエクステント](../../relational-databases/pages-and-extents-architecture-guide.md#pages-and-extents)」を参照してください。
  > [!IMPORTANT]
  > Azure SQL Database 単一データベースおよびエラスティック プールは、tempdb に保存され、データベース レベルまで調べられるグローバル一時テーブルとグローバル一時ストアド プロシージャをサポートしています。 グローバル一時テーブルとグローバル一時ストアド プロシージャは、同じ Azure SQL データベース内ですべてのユーザーのセッションで共有されます。 他の Azure SQL データベースからのユーザー セッションは、グローバル一時テーブルにアクセスできません。 詳細については、「[Database scoped global temporary tables (Azure SQL Database)](../../t-sql/statements/create-table-transact-sql.md#database-scoped-global-temporary-tables-azure-sql-database)」(データベース スコープ グローバル一時テーブル (Azure SQL Database)) を参照してください。 [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)) では、SQL Server と同じ一時オブジェクトがサポートされます。
  > Azure SQL Database 単一データベースおよびエラスティック プールでは、master データベースと tempdb データベースのみが適用されます。 詳しくは、「[Azure SQL Database サーバーとは](https://docs.microsoft.com/azure/sql-database/sql-database-servers-databases#what-is-an-azure-sql-database-server)」をご覧ください。 Azure SQL Database 単一データベースおよびエラスティック プールのコンテキストでの tempdb の説明については、[Azure SQL Database 単一データベースおよびエラスティック プール内の tempdb データベース](#tempdb-database-in-sql-database)に関するページをご覧ください。 Azure SQL Database Managed Instance の場合、すべてのシステム データベースが適用されます。

- **バージョン ストア**。これは行のバージョン管理を使用する機能のサポートに必要なデータ行を保持するデータ ページのコレクションです。 共通バージョン ストアとオンライン インデックス構築用のバージョン ストアの 2 つのバージョン ストアがあります。 バージョン ストアに保持される内容:
  - 行のバージョン管理を伴う READ COMMITTED 分離トランザクションまたはスナップショット分離トランザクションを使用するデータベースで、データ変更トランザクションによって生成される行バージョン。  
  - オンライン インデックス操作、複数のアクティブな結果セット (MARS)、AFTER トリガーなどの機能に対してデータ変更トランザクションによって生成される行バージョン。  
  
トランザクションをロールバックできるように、**tempdb** のログ記録は最小限に抑えられます。 **tempdb** は [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が起動されるたびに再作成され、システムが常にデータベースのクリーンなコピーで起動されるようにします。 一時テーブルと一時ストアド プロシージャは、切断時に自動的に削除され、システムのシャットダウン時にアクティブな接続はありません。 そのため、 **tempdb** には、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のあるセッションから別のセッションに保存されるものは一切含まれません。 **tempdb**では、バックアップ操作と復元操作は実行できません。  
  
## <a name="physical-properties-of-tempdb-in-sql-server"></a>SQL Server の tempdb の物理プロパティ

次の表は、SQL Server の **tempdb** のデータ ファイルとログ ファイルの初期構成値 (Model データベースの既定値に基づく) の一覧です。 これらのファイルのサイズは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のエディションによって多少異なる場合があります。  
  
|ファイル|論理名|物理名|初期サイズ|ファイル拡張|  
|----------|------------------|-------------------|------------------|-----------------|  
|プライマリ データ|tempdev|tempdb.mdf|8 MB|ディスクがいっぱいになるまで 64 MB ずつ自動拡張|  
|セカンダリ データ ファイル*|temp#|tempdb_mssql_#.ndf|8 MB|ディスクがいっぱいになるまで 64 MB ずつ自動拡張|  
|Log|templog|templog.ldf|8 MB|最大 2 TB まで 64 MB ずつ自動拡張|  
  
\* コンピューター上の (論理) プロセッサの数に依存するファイル数です。 一般的なルールとして、論理プロセッサの数が 8 以下の場合、論理プロセッサと同じ数のデータ ファイルを使用します。 論理プロセッサの数が 8 より大きい場合、8 つのデータ ファイルを使用し、競合が続く場合、競合が許容できるレベルに減少するまでデータ ファイルの数を 4 の倍数分ずつ増やすか、ワークロード/コードを変更します。

> [!NOTE]
> データ ファイルの数の既定値は、 [KB 2154845](https://support.microsoft.com/kb/2154845/)の一般的なガイドラインに基づいています。  
  
### <a name="moving-the-tempdb-data-and-log-files-in-sql-server"></a>SQL Server の tempdb のデータ ファイルとログ ファイルの移動

**tempdb** データ ファイルとログ ファイルを移動するには、「 [システム データベースの移動](../../relational-databases/databases/move-system-databases.md)」を参照してください。  
  
### <a name="database-options-for-tempdb-in-sql-server"></a>SQL Server の tempdb のデータベース オプション

**tempdb** データベースの各データベース オプションの既定値とそのオプションを変更できるかどうかを次の表に示します。 これらのオプションの現在の設定を表示するには、 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) カタログ ビューを使用します。  
  
|データベース オプション|既定値|変更可否|  
|---------------------|-------------------|---------------------|  
|ALLOW_SNAPSHOT_ISOLATION|OFF|はい|  
|ANSI_NULL_DEFAULT|OFF|はい|  
|ANSI_NULLS|OFF|はい|  
|ANSI_PADDING|OFF|はい|  
|ANSI_WARNINGS|OFF|はい|  
|ARITHABORT|OFF|はい|  
|AUTO_CLOSE|OFF|いいえ|  
|AUTO_CREATE_STATISTICS|ON|はい|  
|AUTO_SHRINK|OFF|いいえ|  
|AUTO_UPDATE_STATISTICS|ON|はい|  
|AUTO_UPDATE_STATISTICS_ASYNC|OFF|はい|  
|CHANGE_TRACKING|OFF|いいえ|  
|CONCAT_NULL_YIELDS_NULL|OFF|はい|  
|CURSOR_CLOSE_ON_COMMIT|OFF|はい|  
|CURSOR_DEFAULT|GLOBAL|はい|  
|データベース可用性オプション|ONLINE<br /><br /> MULTI_USER<br /><br /> READ_WRITE|いいえ<br /><br /> いいえ<br /><br /> いいえ|  
|DATE_CORRELATION_OPTIMIZATION|OFF|はい|  
|DB_CHAINING|ON|いいえ|  
|ENCRYPTION|OFF|いいえ|  
|MIXED_PAGE_ALLOCATION|OFF|いいえ|  
|NUMERIC_ROUNDABORT|OFF|はい|  
|PAGE_VERIFY|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の新規インストールの場合は CHECKSUM<br /><br /> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のアップグレードの場合は NONE|はい|  
|PARAMETERIZATION|SIMPLE|はい|  
|QUOTED_IDENTIFIER|OFF|はい|  
|READ_COMMITTED_SNAPSHOT|OFF|いいえ|  
|RECOVERY|SIMPLE|いいえ|  
|RECURSIVE_TRIGGERS|OFF|はい|  
|Service Broker のオプション|ENABLE_BROKER|はい|  
|TRUSTWORTHY|OFF|いいえ|  
  
これらのデータベース オプションの説明は、「[ALTER DATABASE の SET オプション (Transact-SQL)](../../t-sql/statements/alter-database-transact-sql-set-options.md)」を参照してください。  
  
## <a name="tempdb-database-in-sql-database"></a>SQL Database の Tempdb データベース

### <a name="tempdb-sizes-for-dtu-based-service-tiers"></a>DTU に基づくサービス層の tempdb のサイズ

|SLO|最大 Tempdb データ ファイル サイズ (GB)|tempdb データ ファイルの数|最大 tempdb データ サイズ (GB)|
|---|---:|---:|---:|
|Basic|13|1|13|
|S0|13|1|13|
|S1|13|1|13|
|S2|13|1|13|
|S3|32|1|32
|S4|32|2|64|
|S6|32|3|96|
|S7|32|6|192|
|S9|32|12|384|
|S12|32|12|384|
|P1|13|12|156|
|P2|13|12|156|
|P4|13|12|156|
|P6|13|12|156|
|P11|13|12|156|
|P15|13|12|156|
|Premium エラスティック プール (すべての DTU 構成)|13|12|156|
|Standard エラスティック プール (S0-S2)|13|12|156|
|Standard エラスティック プール (S3 以降) |32|12|384|
|Basic エラスティック プール (すべての DTU 構成)|13|12|156|
||||

### <a name="tempdb-sizes-for-vcore-based-service-tiers"></a>vCore に基づくサービス層の tempdb のサイズ

[仮想コアベースのリソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits)に関するページを参照してください。

## <a name="restrictions"></a>制限

**tempdb** データベースでは、次の操作は実行できません。  
  
- ファイル グループの追加
- データベースのバックアップまたは復元
- 照合順序の変更。 既定の照合順序はサーバーの照合順序です
- データベース所有者の変更。 **tempdb** は **sa**が所有します
- データベース スナップショットを作成する
- データベースの削除
- データベースからの **guest** ユーザーの削除
- 変更データ キャプチャの有効化
- データベース ミラーリングへの参加
- プライマリ ファイル グループ、プライマリ データ ファイル、またはログ ファイルの削除
- データベースまたはプライマリ ファイル グループの名前変更
- DBCC CHECKALLOC の実行
- DBCC CHECKCATALOG の実行
- データベースの OFFLINE への設定
- データベースまたはプライマリ ファイル グループの READ_ONLY への設定
  
## <a name="permissions"></a>アクセス許可

すべてのユーザーが tempdb 内に一時オブジェクトを作成できます。 ユーザーは追加の権限を付与されない限り、自分で作成したオブジェクトにしかアクセスできません。 ユーザーが tempdb を使用できないように tempdb への接続アクセス許可を取り消すことはできますが、一部のルーチン処理で tempdb を使用する必要があるためお勧めしません。  

## <a name="optimizing-tempdb-performance-in-sql-server"></a>SQL Server の tempdb のパフォーマンスの最適化

tempdb データベースのサイズと物理的な配置場所は、システムのパフォーマンスに影響を与えることがあります。 たとえば、tempdb 用に定義されているサイズが小さすぎると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを再起動するたびに、tempdb のサイズがワークロードのサポートに必要なサイズまで自動的に拡張されるので、システムの処理負荷の一部が占有されます。

可能な場合は、[データベースのファイルの瞬時初期化](../../relational-databases/databases/database-instant-file-initialization.md)を使用して、データ ファイル拡張操作のパフォーマンスを向上します。

すべての tempdb ファイルに対する領域をあらかじめ割り当てるには、環境における一般的なワークロードに十分に対応できる大きさの値にファイル サイズを設定します。 事前に割り当てることで、tempdb が頻繁に拡張されることなく、パフォーマンスに影響が出ません。 tempdb データベースは自動拡張が行われるように設定する必要がありますが、これは想定外の例外に対してディスク領域を増加するために使用する必要があります。

各[ファイル グループ](../../relational-databases/databases/database-files-and-filegroups.md#filegroups)内でデータ ファイルのサイズは等しくする必要があります。これは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がより多くの空き領域を持つファイル内の割り当てを優先する比例配分アルゴリズムを使用していることによります。 サイズの等しい複数のデータ ファイルに tempdb を分割すると、tempdb を使用する操作において効率の高い並列処理を実現できます。

tempdb データベース ファイルの拡張単位が小さすぎることのないように、ファイル拡張の増分値を妥当なサイズに設定します。 tempdb に書き込まれたデータ量と比較してファイルの拡張単位が小さすぎると、tempdb を頻繁に拡張する必要が生じ、パフォーマンスに影響が出る場合があります。

現在の tempdb のサイズと拡張パラメーターを確認するには、次のクエリを使用します。

```sql
 SELECT name AS FileName,
    size*1.0/128 AS FileSizeInMB,
    CASE max_size
        WHEN 0 THEN 'Autogrowth is off.'
        WHEN -1 THEN 'Autogrowth is on.'
        ELSE 'Log file grows to a maximum size of 2 TB.'
    END,
    growth AS 'GrowthValue',
    'GrowthIncrement' =
        CASE
            WHEN growth = 0 THEN 'Size is fixed.'
            WHEN growth > 0 AND is_percent_growth = 0
                THEN 'Growth value is in 8-KB pages.'
            ELSE 'Growth value is a percentage.'
        END
FROM tempdb.sys.database_files;
GO
```

高速な I/O サブシステムに tempdb データベースを配置します。 直接アタッチされたディスクが多数ある場合は、ディスク ストライピングを使用します。 I/O ボトルネックが発生しない限り、tempdb データ ファイルの個々またはグループを必ずしも別々のディスクまたはスピンドルに配置する必要はありません。

ユーザー データベースによって使用されるディスクとは異なるディスクに tempdb データベースを配置します。

## <a name="performance-improvements-in-tempdb-for-sql-server"></a>SQL Server の tempdb でのパフォーマンスの強化

[!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 以降では、**tempdb** のパフォーマンスが次の方法でさらに最適化されています。  
  
- 一時テーブルとテーブル変数はキャッシュされます。 キャッシュを使用することで、一時オブジェクトを削除および作成する操作を非常に高速に実行でき、ページ割り当ての競合が減少します。  
- 割り当てページのラッチ プロトコルが改善され、使用される UP (更新) ラッチの回数が減っています。  
- **tempdb** のログ記録オーバーヘッドが減らされ、**tempdb** ログ ファイルのディスク I/O 帯域幅消費が減りました。  
- セットアップによって、新しいインスタンスのインストール中に複数の tempdb データ ファイルが追加されます。 この操作を実行するには、 **[データベース エンジンの構成]** セクションの新しい UI 入力コントロールまたはコマンドライン パラメーター /SQLTEMPDBFILECOUNT を使用します。 既定では、セットアップ時に、論理プロセッサ数または 8 のいずれか小さい方と同数の tempdb データ ファイルが追加されます。  
- 複数の **tempdb** データ ファイルがある場合は、拡張設定に応じて、すべてのファイルが同時に同量ずつ自動拡張されます。 トレース フラグ 1117 は必須ではなくなりました。  
- **tempdb** 内のすべての割り当てで単一エクステントが使用されます。 [トレース フラグ 1118](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md) は必須ではなくなりました。  
- プライマリ ファイル グループの場合は、AUTOGROW_ALL_FILES プロパティがオンで、プロパティは変更できません。

tempdb でのパフォーマンスの向上の詳細については、次のブログ記事を参照してください。

[TEMPDB - ファイル、トレース フラグ、更新プログラム](https://blogs.msdn.microsoft.com/sql_server_team/tempdb-files-and-trace-flags-and-updates-oh-my/)

## <a name="memory-optimized-tempdb-metadata"></a>メモリ最適化 tempdb メタデータ

tempdb メタデータの競合は、従来から、SQL Server 上で実行されている多くのワークロードのスケーラビリティに対するボトルネックになっていました。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、[メモリ内データベース](../in-memory-database.md)機能ファミリの一部として、メモリ最適化 tempdb メタデータという新機能が導入されています。この機能により、効果的にこのボトルネックが除去され、tempdb が多用されるワークロードに対して新たなレベルのスケーラビリティが実現されます。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、一時テーブルのメタデータの管理に関連するシステム テーブルを、ラッチ フリーの非持続的メモリ最適化テーブルに移動できます。  [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、[メモリ内データベース](../in-memory-database.md)機能ファミリの一部として、メモリ最適化 tempdb メタデータという新機能が導入されています。この機能により、効果的にこのボトルネックが除去され、tempdb が多用されるワークロードに対して新たなレベルのスケーラビリティが実現されます。 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] では、一時テーブルのメタデータの管理に関連するシステム テーブルを、ラッチ フリーの非持続的メモリ最適化テーブルに移動できます。この新しい機能にオプトインするには、次のスクリプトを使用します。

```sql
ALTER SERVER CONFIGURATION SET MEMORY_OPTIMIZED TEMPDB_METADATA = ON 
```

この構成の変更を有効にするには、サービスの再起動が必要です。

この実装には、注意すべき重要な制限がいくつかあります。

1. 機能のオンとオフの切り替えは、動的ではありません。 tempdb の構造を根本的に変更する必要があるため、この機能を有効または無効にするには再起動が必要です。
2. 1 つのトランザクションで複数のデータベース内のメモリ最適化テーブルにアクセスすることはできません。  つまり、ユーザー データベースにメモリ最適化テーブルが含まれるすべてのトランザクションでは、同じトランザクション内の tempdb システム ビューにアクセスできなくなります。  ユーザー データベース内のメモリ最適化テーブルと同じトランザクションで tempdb システム ビューにアクセスしようとした場合、次のエラーを受け取ります。
    ```
    A user transaction that accesses memory optimized tables or natively compiled modules cannot access more than one user database or databases model and msdb, and it cannot write to master.
    ```
    例:
    ```
    BEGIN TRAN
    SELECT *
    FROM tempdb.sys.tables  -----> Creates a user In-Memory OLTP Transaction on Tempdb
    INSERT INTO <user database>.<schema>.<mem-optimized table>
    VALUES (1)  ----> Attempts to create user In-Memory OLTP transaction but will fail
    COMMIT TRAN
    ```
3. メモリ最適化テーブルに対するクエリではロックと分離のヒントがサポートされていないため、メモリ最適化 tempdb カタログ ビューに対するクエリでは、ロックと分離のヒントは適用されません。 SQL Server 内の他のシステム カタログ ビューと同じように、システム ビューに対するすべてのトランザクションは、READ COMMITTED (または、このケースでは READ COMMITTED SNAPSHOT) の分離になります。
4. メモリ最適化 tempdb メタデータが有効になっていると、一時テーブルの列ストア インデックスで問題が発生する場合あります。 このプレビュー リリースでは、メモリ最適化 tempdb メタデータを使用するときは、一時テーブルで列ストア インデックスを使用しないことをお勧めします。

[!INCLUDE[freshInclude](../../includes/paragraph-content/fresh-note-steps-feedback.md)]

> [!NOTE] 
> これらの制限は、tempdb システム ビューを参照するときにのみ適用されます。ユーザー データベース内のメモリ最適化テーブルにアクセスするときは、必要であれば、同じトランザクションで一時テーブルを作成することができます。

次の T-SQL コマンドを使用して、tempdb がメモリ最適化かどうかを確認できます。
```
SELECT SERVERPROPERTY('IsTempdbMetadataMemoryOptimized')
```

## <a name="capacity-planning-for-tempdb-in-sql-server"></a>SQL Server の tempdb に使用するディスク領域の計画

SQL Server 運用環境での tempdb の適切なサイズを判断するには、多くの要因が関係します。 この記事で前述されているように、これらの要因には既存のワークロードや使用されている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の機能などがあります。 SQL Server のテスト環境で次のタスクを実行して、既存のワークロードを分析することをお勧めします。

- tempdb に自動拡張を設定する。
- 個々のクエリまたはワークロード トレース ファイルを実行し、tempdb 領域の使用を監視する。
- インデックスの再構築などのインデックス メンテナンス操作を実行し、tempdb 領域を監視する。
- 前の手順の使用領域値を使用してワークロード全体の使用量を予測し、予測される同時処理に合わせてこの値を調整し、それに応じて tempdb のサイズを設定する。

## <a name="how-to-monitor-tempdb-use"></a>tempdb の使用状況を監視する方法

tempdb のディスク領域が不足すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の運用環境で重大な障害が発生したり、実行中のアプリケーションの操作を完了できなくなったりする場合があります。 [sys.dm_db_file_space_usage](../../relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql.md) 動的管理ビューを使用して、tempdb ファイルで使用されているディスク領域を監視できます。

```sql
 -- Determining the Amount of Free Space in tempdb
SELECT SUM(unallocated_extent_page_count) AS [free pages],
  (SUM(unallocated_extent_page_count)*1.0/128) AS [free space in MB]
FROM sys.dm_db_file_space_usage;

-- Determining the Amount Space Used by the Version Store
SELECT SUM(version_store_reserved_page_count) AS [version store pages used],
  (SUM(version_store_reserved_page_count)*1.0/128) AS [version store space in MB]
FROM sys.dm_db_file_space_usage;

-- Determining the Amount of Space Used by Internal Objects
SELECT SUM(internal_object_reserved_page_count) AS [internal object pages used],
  (SUM(internal_object_reserved_page_count)*1.0/128) AS [internal object space in MB]
FROM sys.dm_db_file_space_usage;

-- Determining the Amount of Space Used by User Objects
SELECT SUM(user_object_reserved_page_count) AS [user object pages used],
  (SUM(user_object_reserved_page_count)*1.0/128) AS [user object space in MB]
FROM sys.dm_db_file_space_usage;
 ```

また、tempdb のページの割り当てや割り当て解除の状態をセッション レベルまたはタスク レベルで監視するには、[sys.dm_db_session_space_usage](../../relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql.md) 動的管理ビューと [sys.dm_db_task_space_usage](../../relational-databases/system-dynamic-management-views/sys-dm-db-task-space-usage-transact-sql.md) 動的管理ビューを使用できます。 これらのビューを使用すると、tempdb のディスク領域を大量に使用している大きなクエリ、一時テーブル、またはテーブル変数を特定できます。 さらに、tempdb で使用できる空き領域と tempdb を使用しているリソースの監視に使用できるいくつかのカウンターもあります。 詳細については、次のセクションを参照してください。

```sql
-- Obtaining the space consumed by internal objects in all currently running tasks in each session
SELECT session_id,
  SUM(internal_objects_alloc_page_count) AS task_internal_objects_alloc_page_count,
  SUM(internal_objects_dealloc_page_count) AS task_internal_objects_dealloc_page_count
FROM sys.dm_db_task_space_usage
GROUP BY session_id;

-- Obtaining the space consumed by internal objects in the current session for both running and completed tasks
SELECT R2.session_id,
  R1.internal_objects_alloc_page_count
  + SUM(R2.internal_objects_alloc_page_count) AS session_internal_objects_alloc_page_count,
  R1.internal_objects_dealloc_page_count
  + SUM(R2.internal_objects_dealloc_page_count) AS session_internal_objects_dealloc_page_count
FROM sys.dm_db_session_space_usage AS R1
INNER JOIN sys.dm_db_task_space_usage AS R2 ON R1.session_id = R2.session_id
GROUP BY R2.session_id, R1.internal_objects_alloc_page_count,
  R1.internal_objects_dealloc_page_count;;
```

## <a name="related-content"></a>関連コンテンツ

- [インデックスの SORT_IN_TEMPDB オプション](../../relational-databases/indexes/sort-in-tempdb-option-for-indexes.md)  
- [システム データベース](../../relational-databases/databases/system-databases.md)  
- [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md)  
- [sys.master_files](../../relational-databases/system-catalog-views/sys-master-files-transact-sql.md)  
- [データベース ファイルの移動](../../relational-databases/databases/move-database-files.md)  
  