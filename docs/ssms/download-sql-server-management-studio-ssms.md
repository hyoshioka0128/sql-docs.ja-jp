---
title: SQL Server Management Studio (SSMS) のダウンロード | Microsoft Docs
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: sstein
ms.technology: ssms
ms.topic: conceptual
keywords:
- ssms のインストール、ssms のダウンロード、最新の ssms
- SQL Server Management Studio
- ssms.exe
- sql man studio
- sql management studio
- sql management studio インストール
- ダウンロード sql management studio
- ms sql management studio
- install sql management studio
- ssms download
- sql server ssms
- ssms express
ms.assetid: adafeeef-4255-4924-8042-02f503d599ca
author: dnethi
ms.author: dinethi
ms.custom: ''
ms.date: 07/26/2019
ms.openlocfilehash: 46174db6dc0008dbeb9490cc96cf41cdef1bc3ed
ms.sourcegitcommit: a1ddeabe94cd9555f3afdc210aec5728f0315b14
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123105"
---
# <a name="download-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) のダウンロード

[!INCLUDE[appliesto-ss-asdb-asdw-xxx-md.md](../includes/appliesto-ss-asdb-asdw-xxx-md.md)]

SQL Server Management Studio (SSMS) は、SQL Server から Azure SQL Database まで、SQL インフラストラクチャを管理するための統合環境です。 SSMS には、SQL Server とデータベースのインスタンスを構成、監視、および管理するためのツールが備わっています。 SSMS を使用して、ご利用のアプリケーションで使われるデータ層コンポーネントを配置、監視、アップグレードしたり、クエリとスクリプトを作成したりすることができます。

SSMS を使用すると、データベースとデータ ウェアハウスがローカル コンピューターやクラウドなど、どこにあっても、クエリ、設計、および管理ができます。

SSMS は無料です。

## <a name="download-ssms-182"></a>SSMS 18.2 のダウンロード

**SSMS 18.2 をご利用いただけるようになりました。これは "*SQL Server Management Studio*" の GA (一般提供) 最新版であり、[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] に対応しています。**

**[![ダウンロード](../ssdt/media/download.png) SQL Server Management Studio 18.2 のダウンロード](https://go.microsoft.com/fwlink/?linkid=2099720)**

SSMS 18.2 は SSMS の GA (一般提供) 最新版です。 以前の GA バージョンの SSMS 18 がインストールされている場合、SSMS 18.2 をインストールすると、18.2 にアップグレードされます。 以前の "*プレビュー*" 版の SSMS 18.x がインストールされている場合は、それをアンインストールしてから SSMS 18.2 をインストールしてください。

**バージョン情報**

- リリース番号:18.2  
- ビルド番号:15.0.18142.0  
- リリース日:2019 年 7 月 25 日  

コメントや提案がある場合、または問題を報告する場合、SSMS チームに連絡する最適な方法は [UserVoice](https://aka.ms/sqlfeedback) です。

SSMS 18.x のインストールでは、17.x 以前のバージョンの SSMS がアップグレードまたは置き換えられることはありません。 SSMS 18.x は以前のバージョンとは別にサイド バイ サイドでインストールされるので、両方のバージョンを使用できます。

コンピューターに SSMS のサイド バイ サイドのインストールが含まれている場合は、特定のニーズに応じて適切なバージョンを起動してください。 最新バージョンには、**Microsoft SQL Server Management Studio 18** というラベルが付いています。

## <a name="available-languages-ssms-182"></a>使用できる言語 (SSMS 18.2)

SSMS の今回のリリースは、次の言語でインストールできます。

SQL Server Management Studio 18.2:  
[中国語 (簡体字)](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x804) | [中国語 (繁体字)](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x404) | [英語 (米国)](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x409) | [フランス語](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x40c) | [ドイツ語](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x407) | [イタリア語](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x410) | [日本語](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x411) | [韓国語](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x412) | [ポルトガル語 (ブラジル)](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x416) | [ロシア語](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x419) | [スペイン語](https://go.microsoft.com/fwlink/?linkid=2099720&clcid=0x40a)

> [!NOTE]
> SQL Server PowerShell モジュールは、PowerShell ギャラリーで入手できる独立したインストールです。 詳細については、「[SQL Server PowerShell モジュールのダウンロード](download-sql-server-ps-module.md)」を参照してください。

## <a name="new-in-this-release-ssms-182"></a>このリリースの新機能 (SSMS 18.2)

|  新しい項目  |  詳細  |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Always Encrypted | Azure の構成証明をサポートするように、エンクレーブ プロバイダーを更新しました。 |
| Intellisense/エディター | データ分類に対するサポートを追加しました。  |
| OPTIMIZE_FOR_SEQUENTIAL_KEY | SSMS インデックス ダイアログ - 新しいインデックス オプション OPTIMIZE_FOR_SEQUENTIAL_KEY を追加しました。 |
| OPTIMIZE_FOR_SEQUENTIAL_KEY | Intellisense サポートを追加しました。 |
| クエリの実行または結果 | 指定したクエリでその実行を完了したときに、追跡するメッセージに "完了時刻" を追加しました。 |
| クエリの実行または結果  | より多くのデータを表示し (結果をテキストで表示)、セルに格納することを許可します (結果をグリッドに表示)。 SSMS では、どちらも最大 200 万文字まで許可されるようになりました (それぞれ 256 および 64,000 から増加)。 これにより、ユーザーがグリッドのセルから 43,680 文字を超えて取得できないという問題にも対処しました。 |
| プラン表示 | インライン スカラー UDF 機能が有効な場合、QueryPlan に新しい属性を追加しました (ContainsInlineScalarTsqlUdfs)。 |
| Integration Services (SSIS) | Azure の SSIS パッケージ スケジューラに対するパフォーマンスの最適化 |
|  |  |

このリリースの新機能の詳細については、[SSMS のリリース ノート](release-notes-ssms.md)に関するページをご覧ください。

## <a name="supported-sql-offerings-ssms-182"></a>サポートされる SQL 製品 (SSMS 18.2)

* このバージョンの SSMS は、すべての[サポート対象バージョンである SQL Server 2008 から [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)]](https://support.microsoft.com/lifecycle?C2=1044) で動作し、Azure SQL Database および Azure SQL Data Warehouse で最新のクラウド機能と連携するための最大レベルのサポートを提供します。
* また、SSMS 18.x は、SSMS 17.x、SSMS 16.x または SQL Server 2014 SSMS 以前とサイドバイサイドでインストールできます。
* SQL Server Integration Services (SSIS) - SSMS バージョン 17.x 以降では、レガシ SQL Server Integration Services サービスへの接続はサポートされません。 以前のバージョンのレガシ Integration Services に接続するには、SQL Server のバージョンに対応する SSMS のバージョンを使用します。 たとえば、レガシ SQL Server 2016 Integration Services サービスに接続する場合は、SSMS 16.x を使用します。 SSMS 17.x および SSMS 16.x は、同じコンピューターにサイドバイサイドでインストールすることができます。 SQL Server 2012 のリリース以降では、SSIS カタログ データベースである SSISDB を使用して、Integration Services パッケージの格納、管理、実行、監視を行うことをお勧めします。 詳細については、「[SSIS カタログ](../integration-services/catalog/ssis-catalog.md)」をご覧ください。

## <a name="supported-operating-systems-ssms-182"></a>サポートされるオペレーティング システム (SSMS 18.2)

SSMS の今回のリリースでは、最新の Service Pack を使用した次の 64 ビット プラットフォームがサポートされます。

- Windows 10 (64 ビット) <sup>*</sup>
- Windows 8.1 (64 ビット)
- Windows Server 2019 (64 ビット)
- Windows Server 2016 (64 ビット) <sup>*</sup>
- Windows Server 2012 R2 (64 ビット)
- Windows Server 2012 (64 ビット)
- Windows Server 2008 R2 (64 ビット)

<sup>*</sup> バージョン 1607 (10.0.14393) 以降が必要

> [!NOTE]
> SSMS は Windows 上でのみ実行されます。 Windows 以外のプラットフォーム上で実行されるツールが必要な場合は、Azure Data Studio をご覧ください。 Azure Data Studio は、macOS、Linux、さらに Windows 上で実行される新しいクロスプラットフォーム ツールです。 詳しくは、[Azure Data Studio](../azure-data-studio/what-is.md) に関する記事をご覧ください。

## <a name="release-notes-ssms-182"></a>リリース ノート (SSMS 18.2)

このリリースには、[既知の問題](release-notes-ssms.md#known-issues-181)がいくつかあります。

このリリースの詳細については、[SSMS のリリース ノート](release-notes-ssms.md)に関するページをご覧ください。

## <a name="previous-ssms-releases"></a>以前のリリースの SSMS

[SQL Server Management Studio の以前のリリース](../ssms/release-notes-ssms.md#previous-ssms-releases)

[!INCLUDE[get-help-sql-tools](../includes/paragraph-content/get-help-sql-tools.md)]

## <a name="see-also"></a>参照

- [チュートリアル: SQL Server Management Studio](tutorials/tutorial-sql-server-management-studio.md)
- [SQL Server Management Studio のドキュメント](sql-server-management-studio-ssms.md)
- [追加の更新プログラムとサービス パック](https://technet.microsoft.com/sqlserver/ff803383.aspx)
- [SQL Server Data Tools (SSDT) のダウンロード](../ssdt/download-sql-server-data-tools-ssdt.md)

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]
