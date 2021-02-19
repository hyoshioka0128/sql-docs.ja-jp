---
title: Microsoft SQL Server のドライバー履歴 | Microsoft Docs
description: このページでは、SQL Server に接続するための Microsoft の履歴上のデータ接続テクノロジについて説明します。
ms.custom: ''
ms.date: 12/08/2020
ms.prod: sql
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: dee1514230f3e0ce0f0ba4c0d3af904cc90c9720
ms.sourcegitcommit: d983ad60779d90bb1c89a34d7b3d6da18447fdd8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933811"
---
# <a name="driver-history-for-microsoft-sql-server"></a>Microsoft SQL Server のドライバー履歴

このページでは、SQL Server に接続するための Microsoft の履歴上のデータ接続テクノロジについて説明します。

## <a name="odbc"></a>ODBC

SQL Server 用 Microsoft ODBC ドライバーには 3 つの異なる世代があります。 最初の "SQL Server" ODBC ドライバーは、[Windows Data Access Components](#microsoft-or-windows-data-access-components) の一部として現在でも出荷されています。 このドライバーは新規の開発にはお勧めしません。 SQL Server 2005 以降では、[SQL Server Native Client](#sql-server-native-client) に ODBC インターフェイスが組み込まれており、これは SQL Server 2005 から SQL Server 2012 までに付属する ODBC ドライバーです。 このドライバーも新規の開発にはお勧めしません。 SQL Server 2012 より後は、[Microsoft ODBC Driver for SQL Server](#microsoft-odbc-driver-for-sql-server) が、今後最新のサーバー機能で更新されるドライバーになります。

### <a name="sql-server-native-client"></a>SQL Server Native Client

SQL Server Native Client は、OLE DB と ODBC の両方に使用されるスタンドアロン ライブラリです。 SQL Server Native Client (多くの場合、SNAC と略記されます) は、SQL Server 2005 から 2012 までに組み込まれていました。 SQL Server Native Client は、SQL Server 2005 から SQL Server 2012 までに導入された新機能を利用する必要があるアプリケーションに使用できます。 (Microsoft/Windows Data Access Components は、SQL Server のこれらの新機能に対して更新されません。)SQL Server 2012 より後の新機能については、SQL Server Native Client は更新されません。 今後の新しい SQL Server 機能を利用する場合は、Microsoft ODBC Driver for SQL Server または Microsoft OLE DB Driver for SQL Server に切り替えてください。

SQL Server Native Client の完全なドキュメントについては、[SQL Server Native Client のドキュメント](../relational-databases/native-client/sql-server-native-client-programming.md)を参照してください。

### <a name="microsoft-odbc-driver-for-sql-server"></a>Microsoft SQL Server 用 ODBC Driver

SQL Server 2012 より後では、SQL Server のプライマリ ODBC ドライバーは、Microsoft ODBC Driver for SQL Server として開発およびリリースされています。 詳細については、[Microsoft ODBC Driver for SQL Server のドキュメント](./odbc/microsoft-odbc-driver-for-sql-server.md)を参照してください。

## <a name="ole-db"></a>OLE DB (OLE DB)

SQL Server 向けの Microsoft OLE DB プロバイダーには 3 つの世代があります。 最初の "Microsoft OLE DB Provider for SQL Server" (SQLOLEDB) は、現在でも [Windows Data Access Components](#microsoft-or-windows-data-access-components) の一部として同梱されています。 このプロバイダーは新しい機能で更新されないため、新しい開発にこのドライバーを使用することはお勧めしません。 SQL Server 2005 以降では、[SQL Server Native Client](#sql-server-native-client) に OLE DB プロバイダー インターフェイス (SQLNCLI) が組み込まれており、これは SQL Server 2005 から SQL Server 2017 までに付属する OLE DB プロバイダーです。 [2011 年に非推奨として発表済み](/archive/blogs/sqlnativeclient/microsoft-is-aligning-with-odbc-for-native-relational-data-access)であり、新規開発にこのドライバーを使用することはお勧めしません。 その後 2017 年に、OLE DB データ アクセス テクノロジは[非推奨が取り消しとなり、2018 年の新しい計画リリースが発表](/archive/blogs/sqlnativeclient/announcing-the-new-release-of-ole-db-driver-for-sql-server)されました。 新しい OLE DB プロバイダーは、"Microsoft OLE DB Driver for SQL Server" (MSOLEDBSQL) と呼ばれ、現在も保守およびサポートされています。

## <a name="adonet"></a>ADO.NET

ADO.NET は、任意の種類のデータ ソース (リレーショナルと非リレーショナルの両方が対象) にアクセスするためのインターフェイスを定義するクラスのセットです。 ADO.NET は Microsoft .NET Framework で導入され、.NET での改善および保守が継続されています。 SqlClient ライブラリは、SQL Server および Azure SQL データ ソースへの接続を提供する ADO.NET データ プロバイダーです。

### <a name="systemdatasqlclient"></a>System.Data.SqlClient

System.Data.SqlClient は、.NET Framework および .NET Core の一部として含められています。 2019 年までは、通常の機能更新プログラムが配信されていました。 [将来の .Net Core、.NET Framework](https://devblogs.microsoft.com/dotnet/net-core-is-the-future-of-net/)、および [.NET 全般](https://devblogs.microsoft.com/dotnet/introducing-net-5/)についての発表に伴い、SqlClient の開発では、.NET 外部のパッケージへのシフトが必要になりました。 System.Data.SqlClient は引き続きサポートされていますが、機能更新プログラムは配信されないため、新規開発にはお勧めしません。

### <a name="microsoftdatasqlclient"></a>Microsoft.Data.SqlClient

[2019 年に導入された](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/) Microsoft SqlClient Data Provider for SQL Server は、.NET Framework、.NET Core、.NET Standard をターゲットとしたアプリケーションをサポートする ADO.NET データ プロバイダーです。 Microsoft.Data.SqlClient 名前空間の詳細については、「[Microsoft ADO.NET for SQL Server](ado-net/microsoft-ado-net-sql-server.md)」を参照してください。

## <a name="jdbc"></a>JDBC

### <a name="microsoft-jdbc-driver-for-sql-server"></a>SQL Server 用 Microsoft JDBC ドライバー

2000 年に導入された SQL Server 用 Microsoft JDBC ドライバーは、継続的に改善および保守されています。 2016 年にオープンソース化されました。 このドライバーのダウンロード方法などの最新情報については、「[JDBC ドライバーの概要](./jdbc/overview-of-the-jdbc-driver.md)」を参照してください。

## <a name="php"></a>PHP

### <a name="microsoft-drivers-for-php-for-sql-server"></a>Microsoft SQL Server 用 Drivers for PHP

2009 年にオープンソース プロジェクトとして導入された Microsoft SQL Server 用 Drivers for PHP は、継続的に改善および保守されています。 PHP ドライバーのダウンロード方法などの最新情報については、「[Microsoft SQL Server 用 Drivers for PHP](./php/microsoft-php-driver-for-sql-server.md)」を参照してください。

## <a name="nodejs"></a>Node.js

### <a name="microsoft-driver-for-nodejs-for-sql-server"></a>Microsoft SQL Server 用 Driver for Node.js

Microsoft SQL Server 用 Driver for Node.js を使用すると、Microsoft Windows および Microsoft Azure の node.js アプリケーションが Microsoft SQL Server および Microsoft Azure SQL Database にアクセスできます。 このドライバーには、開発の取り組みの重点が置かれなくなっています。 Microsoft SQL Server 用 Driver for Node.js を使用して新しいアプリケーションを作成することはお勧めしません。

Microsoft SQL Server 用 Driver for Node.js の詳細については、「[Windows Azure / node-sqlserver](https://github.com/Azure/node-sqlserver)」を参照してください。

### <a name="tedious"></a>Tedious

Microsoft は現在、JavaScript を使用した SQL Server への接続のために、Node.js のオープンソースの Tedious モジュールに貢献し、サポートしています。 詳細については、「[Node.js Driver for SQL Server](./node-js/node-js-driver-for-sql-server.md)」を参照してください。

## <a name="microsoft-or-windows-data-access-components"></a>Microsoft または Windows Data Access Components

Microsoft/Windows Data Access Components (MDAC/WDAC) は、アプリケーションの下位互換性を確保するために Windows に付属しサポートされており、現在の SQL Server テクノロジ スタックには含まれていません。 MDAC/WDAC のコンポーネントには新しい機能が追加されないため、新しいアプリケーションの開発には使用しないことをお勧めします。

このドキュメントでは、テクノロジと製品に基づいて、MDAC/WDAC スタックを次のコンポーネントに分割できます。

* **ADO** (ADOMD および ADOX を含む)
* **OLE DB** (OLE DB Core Services、SQL Server OLE DB プロバイダー、Oracle OLE DB プロバイダー、OLE DB Provider for ODBC Drivers、データ シェイプ プロバイダー、およびリモート データ プロバイダーを含む)
* **ODBC** (ODBC ドライバー マネージャー、SQL ODBC ドライバー、Oracle ODBC ドライバーを含む)

### <a name="mdacwdac-components"></a>MDAC/WDAC コンポーネント

MDAC/WDAC には、次のコンポーネントが含まれています。

* **ODBC:** Microsoft Open Database Connectivity (ODBC) インターフェイスは、アプリケーションがさまざまな種類のデータベース管理システム (DBMS) からデータにアクセスできるようにする C プログラミング言語のインターフェイスです。 この API を使用するアプリケーションは、リレーショナル データ ソースへのアクセスのみに制限されます。
* **OLE DB:** OLE DB は、さまざまな種類のデータ ストアのデータにアクセスするための一連の COM インターフェイスです。 OLE DB プロバイダーは、データベース、ファイル システム、メッセージ ストア、ディレクトリ サービス、ワークフロー、およびドキュメント ストアのデータにアクセスするために存在します。
* **ADO:** ActiveX データ オブジェクト (ADO) は、高水準のプログラミング モデルを提供します。 ADO は、OLE DB または ODBC への直接コーディングよりも若干パフォーマンスが劣りますが、簡単に学習して使用することができます。 これは、Microsoft Visual Basic Scripting Edition (VBScript) や Microsoft JScript などのスクリプト言語から使用できます。
* **ADOMD:** ADO 多次元 (ADOMD) は、Microsoft Analysis Services プロバイダーとも呼ばれる Microsoft OLAP プロバイダーなどの多次元データ プロバイダーで使用します。 MDAC 2.0 以降、主な機能強化は行われていません。
* **ADOX:** ADO Extensions for DDL and Security (ADOX) を使用すると、データベース、テーブル、インデックス、またはストアド プロシージャの定義を作成および変更できます。 ADOX はすべてのプロバイダーで使用できます。 Microsoft Jet OLE DB Provider は ADOX の完全なサポートを提供していますが、Microsoft SQL Server OLE DB プロバイダーは制限付きサポートを提供しています。
* **Microsoft SQL Server ネットワーク ライブラリ:** SQL Server ネットワーク ライブラリを使用すると、SQLOLEDB と SQLODBC が SQL Server データベースと通信できます。 MDAC/WDAC リリースでは、次の SQL Server ネットワーク ライブラリが非推奨となりました。Banyan Vines、AppleTalk、ServerNet、IPX/SPX、Giganet、RPC。 TCP/IP と名前付きパイプは引き続きサポートされ、64 ビットの Windows オペレーティング システムで使用できます。
* **MSDASQL:** Microsoft OLE DB Provider for ODBC (MSDASQL) を使用すると、OLE DB と ADO (内部的に OLEDB を使用する) 上で構築されるアプリケーションが、ODBC ドライバーを通じてデータ ソースにアクセスできます。 MSDASQL は、データベースではなく ODBC に接続する OLEDB プロバイダーです。 これは、データ ソースに直接の OLE DB プロバイダーが存在しない場合に、OLE DB から ODBC ドライバーへのブリッジとして機能することを意味します。 MSDASQL は Windows オペレーティング システムに付属しており、Windows Server 2008 と Vista SP1 が、このテクノロジの 64 ビット バージョンを含む最初の Windows リリースでした。

### <a name="deprecated-mdacwdac-components"></a>非推奨の MDAC/WDAC コンポーネント

これらのコンポーネントは MDAC/WDAC の現在のリリースでもサポートされていますが、今後のリリースで削除される可能性があります。 新しいアプリケーションを開発するときは、これらのコンポーネントを使用しないことをお勧めします。 また、既存のアプリケーションをアップグレードまたは変更する場合は、これらのコンポーネントに対する依存関係を削除してください。

* **SQLOLEDB:** Microsoft SQL Server へのアクセスをサポートする Microsoft OLE DB Provider for SQL Server (SQLOLEDB) は、非推奨となりました。 SQL Server の今後のバージョンへの接続は、サポートされない可能性があります。 SQL Server 7 より前のバージョンに接続する機能は、Windows 7 より後のオペレーティング システムから削除されます。 新しいアプリケーションでは、新しい SQL Server 機能をサポートする Microsoft OLE DB Driver for SQL Server (MSOLEDBSQL) を使用する必要があります。 パフォーマンス、信頼性、およびサポート性を向上させるために、既存のアプリケーションも Microsoft OLE DB Driver for SQL Server に移行する必要があります。 詳細については、「[MDAC から OLE DB Driver for SQL Server へのアプリケーションの更新](oledb/applications/updating-an-application-to-oledb-driver-for-sql-server-from-mdac.md)」を参照してください。
* **SQLODBC:** Microsoft SQL Server へのアクセスをサポートする Microsoft SQL Server ODBC Driver (SQLODBC) は、非推奨となりました。 SQL Server の今後のバージョンへの接続は、サポートされない可能性があります。 SQL Server 7 より前のバージョンに接続する機能は、Windows 7 より後のオペレーティング システムから削除されます。 新しいアプリケーションでは、新しい SQL Server 機能をサポートする Microsoft ODBC Driver for SQL Server on Windows を使用する必要があります。 パフォーマンス、信頼性、およびサポート性を向上させるために、既存のアプリケーションも Microsoft ODBC Driver for SQL Server に移行する必要があります。 関連情報については、「[MDAC から SQL Server Native Client へのアプリケーションの更新](../relational-databases/native-client/applications/updating-an-application-to-sql-server-native-client-from-mdac.md)」を参照してください。
* **Microsoft Jet データベース エンジン 4.0:** バージョン 2.6 以降では、MDAC に Jet コンポーネントが含まれなくなりました。 つまり、MDAC 2.6、2.7、2.8 には、Microsoft Jet、Microsoft Jet OLE DB Provider、ODBC Desktop Database Drivers、または Jet Data Access Objects (DAO) が含まれていません。 

  64 ビット バージョンの Jet データベース エンジン、Jet OLEDB Driver、Jet ODBC Drivers、または Jet DAO はありません。 詳細については、[サポート技術情報の記事 957570](https://support.microsoft.com/kb/957570) を参照してください。 64 ビット バージョンの Windows では、32 ビットの Jet は Windows WOW64 サブシステムで実行されます。 WOW64 の詳細については、[MSDN WOW64 のドキュメント](/windows/desktop/WinProg64/wow64-implementation-details)を参照してください。 ネイティブの 64 ビット アプリケーションは、WOW64 で実行されている 32 ビットの Jet ドライバーと通信できません。

  リレーショナル データ ストアを必要とする、Microsoft Access 以外の新しいアプリケーションを開発するときは、Microsoft Jet ではなく [Microsoft SQL Server Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express) を使用することをお勧めします。 これらの新しいまたは変換された Jet アプリケーションは、プライマリではないデータ ストレージに対して Microsoft Office 2003 以前のファイル (.mdb および .xls) を使用する目的で、引き続き Jet を使用できます。 ただし、これらのアプリケーションについては、Jet から Microsoft Access データベース エンジンに移行することを計画する必要があります。 Office 2003 (.mdb および .xls) または Office 2007 (*.accdb、*.xlsm、*.xlsx、*.xlsb) ファイル形式の既存のファイルに対する読み取りと書き込みを行うことができる [Microsoft Access データベース エンジンをダウンロード](https://www.microsoft.com/download/details.aspx?id=54920)できます。

  > [!IMPORTANT]
  > 具体的な使用制限については、2007 Office System 使用許諾契約書をお読みください。

  > [!NOTE]
  > また SQL Server アプリケーションは、2007 Office System ドライバーを使用して、SQL Server の異種データ接続と Integration Services 機能から、2007 Office システムおよびそれ以前のファイルにもアクセスできます。 さらに、64 ビットの SQL Server アプリケーションは、64 ビットの Windows で 32 ビットの SQL Server Integration Services (SSIS) を使用して、32 ビットの Jet および 2007 Office System ファイルにアクセスできます。

* **データ整形用の Microsoft OLE DB Provider (MSDADS):** MSDADS を使用すると、アプリケーション内のキー、フィールド、または行セットの間に階層関係を作成できます。 MDAC 2.1 以降、主な機能強化は行われていません。 このプロバイダーは非推奨となりました。 MSDADS の代わりに XML を使用することをお勧めします。
* **Oracle ODBC および Oracle OLE DB:** Microsoft Oracle ODBC Driver (Oracle ODBC) および Microsoft OLE DB Provider for Oracle (Oracle OLE DB) を使用すると、Oracle データベース サーバーにアクセスできます。 これらは Oracle Call Interface (OCI) バージョン 7 を使用して構築され、Oracle 7 の完全なサポートを提供します。 また、Oracle 7 エミュレーションを使用して、Oracle 8 データベースの制限付きサポートを提供しています。 Oracle では、OCI バージョン 7 の呼び出しを使用するアプリケーションはサポートされなくなりました。 これらのテクノロジは非推奨になりました。 Oracle データ ソースを使用している場合は、Oracle 提供のドライバーおよびプロバイダーに移行する必要があります。
* **Remote Data Services (RDS):** RDS は、インターネットまたはイントラネットを介してリモートの ADO レコード セット オブジェクトにアクセスするための独自の Microsoft メカニズムです。 RDS は非推奨となっています。MDAC 2.1 以降、RDS に主な機能強化は行われていません。 Microsoft は、広範な SOAP 機能を備え、RDS コンポーネントの代わりとなる .NET Framework をリリースしました。 Windows 7 より後のオペレーティング システムから、すべての RDS サーバー コンポーネントが削除されます。
* **Jet Replication Objects (JRO):** JRO は非推奨となっています。 JRO は、Jet ( *.mdb) データベースと共に ADO 内で使用されて、Jet データベース (.mdb) を作成および圧縮し、Jet レプリケーション管理を実行します。MDAC 2.7 が、その最後のリリースになります。JRO は 64 ビットの Windows オペレーティング システムでは使用できません。JRO は Microsoft Access 2007 ファイル形式 (* .accdb) ではサポートされません。
* **16 ビット ODBC のサポート:** 16 ビット アプリケーションを使用している場合は、32 ビット アプリケーションに移行する必要があります。 16 ビットの機能は非推奨となり、64 ビットのオペレーティング システムから削除されます。 詳細については、[サポート技術情報の記事 896458](https://support.microsoft.com/kb/896458) を参照してください。
* **OLEDB Simple Provider (MSDAOSP):** OLEDB Simple Provider は、単純なデータで OLE DB プロバイダーをすばやく構築するためのフレームワークを提供します。 MSDAOSP は非推奨となっています。
* **ODBC カーソル ライブラリ:** ODBC カーソル ライブラリ (ODBCCR32.dll) では、クライアント側の制限付きデータ カーソルを提供します。 ODBC カーソル ライブラリは非推奨となりました。お使いのアプリケーションでは、代替としてサーバー側のカーソルの実装を使用できます。
* **OLE DB プロセス外インターフェイス リモート処理:** OLEDB インターフェイス リモート処理 (msdaps.dll) は、OLE DB プロバイダーがプロセス外で実行することを可能にする試みでした。 OLEDB プロセス外インターフェイス リモート処理は非推奨となっています。
* **AppleTalk および Banyan Vines SQL ネットワーク ライブラリ:** Banyan Vines、AppleTalk、ServerNet、IPX/SPX、Giganet、および RPC SQL ネットワーク ライブラリは非推奨となっています。 これらのテクノロジのいずれかを使用している場合は、TCP/IP や名前付きパイプなどの他のネットワーク ライブラリのいずれかを使用するようにアプリケーションを変更する必要があります。

### <a name="mdacwdac-releases"></a>MDAC/WDAC リリース

以下に、過去の MDAC/WDAC リリースのサポート可能性のシナリオを、最も古いものから順に示します。

* **MDAC 1.5、MDAC 2.0、および MDAC 2.1:** これらのバージョンの MDAC は、Microsoft Windows NT オプション パック、Microsoft Windows プラットフォーム SDK、または MDAC Web サイトからリリースされた独立したリリースでした。 これらのバージョンの MDAC はサポートされなくなりました。
* **MDAC 2.5:** このバージョンの MDAC は、Windows 2000 オペレーティング システムに含まれていました。 MDAC 2.5 の Service Pack は、対応する Windows 2000 Service Pack に含まれていました。
* **MDAC 2.6:** MDAC 2.6 RTM、SP1、および SP2 は、それぞれ Microsoft SQL Server 2000 RTM、SP1、SP2 に含まれていました。 また、これらの MDAC Service Pack は、Microsoft SQL Server 2000 Service Pack のリリース スケジュールに従って MDAC Web サイトにリリースされました。 このバージョンの MDAC とその Service Pack は、Windows 2000、Windows Millennium Edition、Windows NT、Windows 95、および Windows 98 プラットフォームにインストールできます。 このバージョンの MDAC はサポートされなくなりました。
* **MDAC 2.7:** このバージョンの MDAC は、Microsoft Windows XP RTM および SP1 オペレーティング システムに含まれていました。 このバージョンの MDAC とその Service Pack は、Windows 2000、Windows Millennium、Windows NT、および Windows 98 プラットフォームにインストールできます。 このバージョンは、オペレーティング システムまたはその Service Pack を使用してのみ Windows XP プラットフォームにインストールできます。 このバージョンの MDAC はサポートされなくなりました。
* **MDAC 2.8:** このバージョンの MDAC は、Windows Server 2003 および Windows XP SP2 以降に含まれていました。 このバージョンの MDAC とその Service Pack は、Windows 2000 にもインストールできます。

  * 32 ビット バージョンの MDAC 2.8 も、お客様への Windows Server 2003 のリリースと同時に、MDAC Web サイトにリリースされました。
  * 64 ビット バージョンの MDAC 2.8 は、64 ビット バージョンの Windows Server 2003 および Windows XP と一緒にリリースされました。

* **Windows Data Access Components (WDAC):** MDAC は、Windows Vista および Windows Server 2008 以降では、名前が WDAC (Windows Data Access Components) に変更されています。 WDAC はオペレーティング システムの一部として含まれており、再配布用に個別に使用することはできません。 WDAC のサービス性は、オペレーティング システムのライフサイクルに左右されます。

  32 ビットおよび 64 ビット バージョンの WDAC は、それぞれ 32 ビットおよび 64 ビット バージョンの Windows オペレーティング システムでリリースされます。

## <a name="obsolete-data-access-technologies"></a>古いデータ アクセス テクノロジ

古いテクノロジとは、いくつかの製品リリースで機能強化または更新されておらず、今後の製品リリースから除外されるテクノロジです。 新しいアプリケーションを作成するときは、これらのテクノロジを使用しないでください。 これらのテクノロジを使用して作成された既存のアプリケーションを変更する場合は、それらのアプリケーションを ADO.NET または他の現在のテクノロジに移行することを検討してください。

以下のコンポーネントは古いと見なされます。

* **DB-Library:** DB-Library は、C API を含む SQL Server 固有のプログラミング モデルです。 SQL Server 6.5 以降、DB-Library に機能強化は行われていません。 最後のリリースは SQL Server 2000 のものであり、64 ビットの Windows オペレーティング システムには移植されません。
* **埋め込み SQL (E-SQL):** E-SQL は、Transact-SQL ステートメントを Visual C コードに埋め込むことができる、SQL Server 固有のプログラミング モデルです。 SQL Server 6.5 以降、E-SQL に機能強化は行われていません。 最後のリリースは SQL Server 2000 のものであり、64 ビットの Windows オペレーティング システムには移植されません。
* **Data Access Objects (DAO):** DAO は、JET (Access) データベースへのアクセスを提供します。 この API は、Microsoft Visual Basic、Microsoft Visual C++、およびスクリプト言語から使用できます。 これは Microsoft Office 2000 および Office XP に含まれていました。 DAO 3.6 が、このテクノロジの最終バージョンです。 これは 64 ビットの Windows オペレーティング システムでは使用できません。
* **リモート データ オブジェクト (RDO):** RDO は、リモート ODBC リレーショナル データ ソースにアクセスするために特別に設計されています。これにより、複雑なアプリケーション コードを必要とせずに ODBC を容易に使用できるようになりました。 これは Microsoft Visual Basic バージョン 4、5、および 6 に含まれていました。 RDO バージョン 2.0 が、このテクノロジの最終バージョンでした。

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]
