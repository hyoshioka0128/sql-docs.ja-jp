---
title: Linux 上の SQL Server 2019 のリリース ノート
description: この記事には、Linux で実行されている SQL Server 2019 のリリース ノートとサポートされている機能が含まれています。 リリース ノートは、最新のリリースと以前のいくつかのリリースに含まれています。
author: VanMSFT
ms.author: vanto
ms.date: 08/21/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
monikerRange: '>= sql-server-linux-ver15  || >= sql-server-ver15 || = sqlallproducts-allversions'
ms.openlocfilehash: af3b6f82e3b76e2dd2b11403bccf4b3e0885912e
ms.sourcegitcommit: cbbb210c0315f9e2be2b9cd68db888ac53429814
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69890904"
---
# <a name="release-notes-for-sql-server-2019-on-linux"></a>Linux 上の SQL Server 2019 のリリース ノート

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx-linuxonly.md](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx-linuxonly.md)]

次のリリース ノートは、Linux で実行されている SQL Server 2019 に適用されます。 この記事は、リリースごとのセクションに分けられています。 各リリースには、Linux パッケージのダウンロードへのリンクに加えて、CU の変更について説明するサポート記事へのリンクが含まれています。

> [!TIP]
> SQL Server 2019 の Linux 新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md?view=sql-server-ver15#sql-server-on-linux)に関するページを参照してください。

## <a name="supported-platforms"></a>サポートされているプラットフォーム

| プラットフォーム | [ファイル システム] | インストール ガイド |
|-----|-----|-----|
| Red Hat Enterprise Linux 7.3、7.4、7.5、または 7.6 Server | XFS または EXT4 | [インストール ガイド](quickstart-install-connect-red-hat.md) | 
| SUSE Enterprise Linux Server v12 SP2 | XFS または EXT4 | [インストール ガイド](quickstart-install-connect-suse.md) |
| Ubuntu 16.04LTS | XFS または EXT4 | [インストール ガイド](quickstart-install-connect-ubuntu.md) | 
| Windows、Mac、または Linux 上の Docker エンジン 1.8+ | なし | [インストール ガイド](quickstart-install-connect-docker.md) | 

> [!TIP]
> 詳細については、SQL Server on Linux の[システム要件](sql-server-linux-setup.md#system)を確認してください。 SQL Server 2017 の最新のサポート ポリシーについては、「[Microsoft SQL Server のテクニカル サポート ポリシー](https://support.microsoft.com/help/4047326/support-policy-for-microsoft-sql-server)」を参照してください。

## <a name="tools"></a>ツール

SQL Server を対象とする既存のクライアント ツールの多くは、Linux で実行されている SQL Server をシームレスにターゲットにすることができます。 一部のツールには、Linux で適切に動作させるための特定のバージョン要件がある場合があります。 SQL Server ツールの完全な一覧については、[SQL Server 用の SQL ツールとユーティリティ](../tools/overview-sql-tools.md)に関するページを参照してください。

## <a name="release-history"></a>リリース履歴

SQL Server 2019 プレビューのリリース履歴の一覧を次の表に示します。

| リリース                   | バージョン       | リリース日 |
|---------------------------|---------------|--------------|
| [リリース候補](#rc)  | 15.0.1900.25  | 2019-8-21    |
| [CTP 3.2](#CTP32)         | 15.0.1800.32  | 2019-7-24    |
| [CTP 3.1](#CTP31)         | 15.0.1700.37  | 2019-6-26    |
| [CTP 3.0](#CTP30)         | 15.0.1600.8   | 2019-5-22    |
| [CTP 2.5](#CTP25)         | 15.0.1500.28  | 2019-4-24    |
| [CTP 2.4](#CTP24)         | 15.0.1400.75  | 2019-3-27    |
| [CTP 2.3](#CTP23)         | 15.0.1300.359 | 2019-3-01    |
| [CTP 2.2](#CTP22)         | 15.0.1200.24  | 2018-12-11   |
| [CTP 2.1](#CTP21)         | 15.0.1100.94  | 2018-11-06   |
| [CTP 2.0](#CTP20)         | 15.0.1000.34  | 2018-09-24   |

## <a id="cuinstall"></a>更新プログラムのインストール方法

プレビュー リポジトリ (**mssql-server-preview**) を構成済みの場合は、新規インストールを実行すると、最新の SQL Server CTP パッケージを取得します。 Docker コンテナー イメージが必要な場合は、[Docker エンジン用の Microsoft SQL Server on Linux](https://hub.docker.com/r/microsoft/mssql-server/) の公式イメージを参照してください。 リポジトリ構成の詳細については、[SQL Server on Linux 用のリポジトリの構成](sql-server-linux-change-repo.md)に関するページを参照してください。

既存の SQL Server パッケージを更新する場合は、パッケージごとに適切な更新コマンドを実行して、最新の CU を取得します。 各パッケージの特定の更新手順については、次のインストール ガイドを参照してください。

- [SQL Server パッケージのインストール](sql-server-linux-setup.md#upgrade)
- [フルテキスト検索パッケージのインストール](sql-server-linux-setup-full-text-search.md)
- [SQL Server Integration Services のインストール](sql-server-linux-setup-ssis.md)
- [Linux への SQL Server 2019 プレビュー Machine Learning Services R および Python のサポートのインストール](sql-server-linux-setup-machine-learning.md)
- [PolyBase パッケージのインストール](../relational-databases/polybase/polybase-linux-setup.md)
- [SQL Server エージェントの有効化](sql-server-linux-setup-sql-agent.md)

## <a id="rc"></a> リリース候補 (2019 年 8 月)

次のセクションでは、リリース候補のパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1900.25-1 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1900.25-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1900.25-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1900.25-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1900.25-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1900.25-1.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-polybase-15.0.1900.25-1.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1900.25-1 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1900.25-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1900.25-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1900.25-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1900.25-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1900.25-1.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-polybase-15.0.1900.25-1.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1900.25-1 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1900.25-1_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1900.25-1_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1900.25-1_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1900.25-1_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1900.25-1_amd64.deb)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.1900.25-1_amd64.deb)|

## <a id="CTP32"></a> CTP 3.2 (2019 年 7 月)

次のセクションでは、CTP 3.2 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1800.32-1 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1800.32-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1800.32-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1800.32-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1800.32-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1800.32-1.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-polybase-15.0.1800.32-1.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1800.32-1 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1800.32-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1800.32-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1800.32-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1800.32-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1800.32-1.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-polybase-15.0.1800.32-1.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1800.32-1 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1800.32-1_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1800.32-1_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1800.32-1_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1800.32-1_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1800.32-1_amd64.deb)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.1800.32-1_amd64.deb)|

## <a id="CTP31"></a> CTP 3.1 (2019 年 6 月)

次のセクションでは、CTP 3.1 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1700.37-2 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1700.37-2.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1700.37-2.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1700.37-2.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1700.37-2.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1700.37-2.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-polybase-15.0.1700.37-2.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1700.37-2 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1700.37-2.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1700.37-2.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1700.37-2.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1700.37-2.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1700.37-2.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-polybase-15.0.1700.37-2.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1700.37-2 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1700.37-2_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1700.37-2_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1700.37-2_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1700.37-2_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1700.37-2_amd64.deb)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.1700.37-2_amd64.deb)|

## <a id="CTP30"></a> CTP 3.0 (2019 年 5 月)

次のセクションでは、CTP 3.0 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1600.8-1 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1600.8-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1600.8-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1600.8-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1600.8-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1600.8-1.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-polybase-15.0.1600.8-1.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1600.8-1 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1600.8-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1600.8-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1600.8-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1600.8-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1600.8-1.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-polybase-15.0.1600.8-1.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1600.8-1 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1600.8-1_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1600.8-1_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1600.8-1_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1600.8-1_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1600.8-1_amd64.deb)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.1600.8-1_amd64.deb)|

### <a name="known-issues"></a>既知の問題

#### <a id="msdtc"></a> Microsoft 分散トランザクション コーディネーター

現時点では、MSDTC ではトランザクションが認証されていない必要があります。 たとえば、Windows 上の SQL Server から SQL Server on Linux へのリンク サーバーを使用している場合や、Windows クライアント アプリケーションを使用して SQL Server on Linux に対して分散トランザクションを開始する場合は、Windows サーバー/クライアントの MSDTC ではオプション "認証を必要としない" を使用する必要があります。

## <a id="CTP25"></a> CTP 2.5 (2019 年 4 月)

次のセクションでは、CTP 2.5 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1500.28-1 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1500.28-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1500.28-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1500.28-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1500.28-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1500.28-1.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-polybase-15.0.1500.28-1.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1500.28-1 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1500.28-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1500.28-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1500.28-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1500.28-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1500.28-1.x86_64.rpm)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-polybase-15.0.1500.28-1.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1500.28-1 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1500.28-1_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1500.28-1_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1500.28-1_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1500.28-1_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1500.28-1_amd64.deb)</br>[PolyBase RPM パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-polybase/mssql-server-polybase_15.0.1500.28-1_amd64.deb)|

### <a name="known-issues"></a>既知の問題

#### <a id="msdtc"></a> Microsoft 分散トランザクション コーディネーター

現時点では、MSDTC ではトランザクションが認証されていない必要があります。 たとえば、Windows 上の SQL Server から SQL Server on Linux へのリンク サーバーを使用している場合や、Windows クライアント アプリケーションを使用して SQL Server on Linux に対して分散トランザクションを開始する場合は、Windows サーバー/クライアントの MSDTC ではオプション "認証を必要としない" を使用する必要があります。

## <a id="CTP24"></a> CTP 2.4 (2019 年 3 月)

次のセクションでは、CTP 2.4 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1400.75-2 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1400.75-2.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1400.75-2.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1400.75-2.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1400.75-2.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1400.75-2.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1400.75-2 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1400.75-2.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1400.75-2.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1400.75-2.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1400.75-2.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1400.75-2.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1400.75-2 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1400.75-2_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1400.75-2_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1400.75-2_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1400.75-2_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1400.75-2_amd64.deb)|

### <a name="known-issues"></a>既知の問題

#### <a id="msdtc"></a> Microsoft 分散トランザクション コーディネーター

現時点では、MSDTC ではトランザクションが認証されていない必要があります。 たとえば、Windows 上の SQL Server から SQL Server on Linux へのリンク サーバーを使用している場合や、Windows クライアント アプリケーションを使用して SQL Server on Linux に対して分散トランザクションを開始する場合は、Windows サーバー/クライアントの MSDTC ではオプション "認証を必要としない" を使用する必要があります。

## <a id="CTP23"></a> CTP 2.3 (2019 年 2 月)

次のセクションでは、CTP 2.3 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1300.359-1 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1300.359-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1300.359-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1300.359-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1300.359-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1300.359-1.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1300.359-1 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1300.359-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1300.359-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1300.359-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1300.359-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1300.359-1.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1300.359-1 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1300.359-1_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1300.359-1_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1300.359-1_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1300.359-1_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1300.359-1_amd64.deb)|

### <a name="known-issues"></a>既知の問題

#### <a id="msdtc"></a> Microsoft 分散トランザクション コーディネーター

現時点では、MSDTC ではトランザクションが認証されていない必要があります。 たとえば、Windows 上の SQL Server から SQL Server on Linux へのリンク サーバーを使用している場合や、Windows クライアント アプリケーションを使用して SQL Server on Linux に対して分散トランザクションを開始する場合は、Windows サーバー/クライアントの MSDTC ではオプション "認証を必要としない" を使用する必要があります。

## <a id="CTP22"></a> CTP 2.2 (2018 年 12 月)

次のセクションでは、CTP 2.2 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1200.24-2 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1200.24-2.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1200.24-2.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1200.24-2.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1200.24-2.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1200.24-2.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1200.24-2 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1200.24-2.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1200.24-2.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1200.24-2.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1200.24-2.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1200.24-2.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1200.24-2 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1200.24-2_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1200.24-2_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1200.24-2_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1200.24-2_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1200.24-2_amd64.deb)|

### <a name="known-issues"></a>既知の問題

#### <a id="msdtc"></a> Microsoft 分散トランザクション コーディネーター

現時点では、MSDTC ではトランザクションが認証されていない必要があります。 たとえば、Windows 上の SQL Server から SQL Server on Linux へのリンク サーバーを使用している場合や、Windows クライアント アプリケーションを使用して SQL Server on Linux に対して分散トランザクションを開始する場合は、Windows サーバー/クライアントの MSDTC ではオプション "認証を必要としない" を使用する必要があります。

## <a id="CTP21"></a> CTP 2.1 (2018 年 11 月)

次のセクションでは、CTP 2.1 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1100.94-1 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1100.94-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1100.94-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1100.94-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1100.94-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1100.94-1.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1100.94-1 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1100.94-1.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1100.94-1.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1100.94-1.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1100.94-1.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1100.94-1.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1100.94-1 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1100.94-1_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1100.94-1_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1100.94-1_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1100.94-1_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1100.94-1_amd64.deb)|

### <a name="known-issues"></a>既知の問題

#### <a name="microsoft-distributed-transaction-coordinator"></a>Microsoft 分散トランザクション コーディネーター

現時点では、MSDTC ではトランザクションが認証されていない必要があります。 たとえば、Windows 上の SQL Server から SQL Server on Linux へのリンク サーバーを使用している場合や、Windows クライアント アプリケーションを使用して SQL Server on Linux に対して分散トランザクションを開始する場合は、Windows サーバー/クライアントの MSDTC ではオプション "認証を必要としない" を使用する必要があります。

## <a id="CTP20"></a> CTP 2.0 (2018 年 9 月)

次のセクションでは、CTP 2.0 リリースのパッケージの場所と既知の問題について説明します。 SQL Server 2019 の Linux の新機能の詳細については、[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)に関するページを参照してください。

### <a name="package-details"></a>パッケージの詳細

手動またはオフラインのパッケージ インストールの場合は、次の表の情報を使用して RPM と Debian のパッケージをダウンロードすることができます。

| [パッケージ] | パッケージ バージョン | ダウンロード |
|-----|-----|-----|
| Red Hat RPM パッケージ | 15.0.1000.34-2 | [エンジン RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-15.0.1000.34-2.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-ha-15.0.1000.34-2.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-fts-15.0.1000.34-2.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-15.0.1000.34-2.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/rhel/7/mssql-server-preview/mssql-server-extensibility-java-15.0.1000.34-2.x86_64.rpm)|
| SLES RPM パッケージ | 15.0.1000.34-2 | [mssql-server エンジン RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-15.0.1000.34-2.x86_64.rpm)</br>[高可用性 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-ha-15.0.1000.34-2.x86_64.rpm)</br>[フルテキスト検索 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-fts-15.0.1000.34-2.x86_64.rpm)</br>[拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-15.0.1000.34-2.x86_64.rpm)</br>[Java 拡張機能 RPM パッケージ](https://packages.microsoft.com/sles/12/mssql-server-preview/mssql-server-extensibility-java-15.0.1000.34-2.x86_64.rpm)|
| Ubuntu 16.04 Debian パッケージ | 15.0.1000.34-2 | [エンジン Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server/mssql-server_15.0.1000.34-2_amd64.deb)</br>[高可用性 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-ha/mssql-server-ha_15.0.1000.34-2_amd64.deb)</br>[フルテキスト検索 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-fts/mssql-server-fts_15.0.1000.34-2_amd64.deb)</br>[拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility/mssql-server-extensibility_15.0.1000.34-2_amd64.deb)</br>[Java 拡張機能 Debian パッケージ](https://packages.microsoft.com/ubuntu/16.04/mssql-server-preview/pool/main/m/mssql-server-extensibility-java/mssql-server-extensibility-java_15.0.1000.34-2_amd64.deb)|

### <a name="known-issues"></a>既知の問題

#### <a name="microsoft-distributed-transaction-coordinator"></a>Microsoft 分散トランザクション コーディネーター

現時点では、MSDTC ではトランザクションが認証されていない必要があります。 たとえば、Windows 上の SQL Server から SQL Server on Linux へのリンク サーバーを使用している場合や、Windows クライアント アプリケーションを使用して SQL Server on Linux に対して分散トランザクションを開始する場合は、Windows サーバー/クライアントの MSDTC ではオプション "認証を必要としない" を使用する必要があります。

## <a name="next-steps"></a>次の手順

作業を開始するには、次のクイック スタートを参照してください。

- [Red Hat Enterprise Linux へのインストール](quickstart-install-connect-red-hat.md)
- [SUSE Linux Enterprise Server へのインストール](quickstart-install-connect-suse.md)
- [Ubuntu へのインストール](quickstart-install-connect-ubuntu.md)
- [Docker 上での実行](quickstart-install-connect-ubuntu.md)
- [Azure での SQL VM プロビジョニング](/azure/virtual-machines/linux/sql/provision-sql-server-linux-virtual-machine?toc=/sql/toc/toc.json)
- [実行と接続 - クラウド](quickstart-install-connect-clouds.md)

よく寄せられる質問に対する回答については、[SQL Server on Linux に関してよく寄せられる質問](sql-server-linux-faq.md)に関するページを参照してください。
