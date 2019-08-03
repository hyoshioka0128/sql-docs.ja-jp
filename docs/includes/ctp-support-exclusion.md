---
ms.openlocfilehash: 522e06e85343a19f80490f07290a1435cebd2d77
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68220726"
---
### <a name="enabled-deployment-scenarios"></a>有効なデプロイ シナリオ

CTP 3.1 により、次のシナリオが有効です。

- サイド バイ サイド インストール。 SQL Server 2019 CTP 3.1 のインスタンスを、SQL Server 2012 から SQL Server 2017 までのインスタンス、または SQL Server 2019 CTP 3.0 以降のインスタンスと一緒にインストールします。
   >[!NOTE]
   >サイド バイ サイドは、SQL Server 2008 および 2008 R2 ではブロックされていませんが、これらと SQL Server 2019 の間で、一般的にサポートされている Windows オペレーティングシステムのバージョンはありません。
- インプレース アップグレード。 SQL Server 2019 CTP 3.1 のインスタンスを、SQL Server 2012 から SQL Server 2017 までのインスタンスおよび SQL Server CTP 3.0 からアップグレードします。 3\.0 より下の SQL Server 2019 CTP からのアップグレードはサポートされていないため、新しいインストールを実行する必要があります。
   >[!NOTE]
   >SQL Server 2008 と 2008 R2 からのインプレース アップグレードはブロックされませんが、これらと SQL Server 2019 の間で、一般的にサポートされている Windows オペレーティングシステムのバージョンはありません。

### <a name="support"></a>サポート

SQL Server 2019 CTP 3.1 はプレビュー ソフトウェアです。 この操作は、公式にはサポートされていません。 [SQL 早期導入プログラム](http://aka.ms/sqleap)を使用しているお客様は、Microsoft と相談して特別な契約を結び、SQL Server 2019 CTP 3.1 の実行がサポートされている場合があります。

早期導入プログラムを使用されていないお客様の制限付きサポートについては、次のいずれかで見つかる可能性があります。

- フォーラム
  - [SQL Server の概要](https://social.msdn.microsoft.com/Forums/sqlserver/en-US/home?forum=sqlgetstarted)
  - [Transact-SQL](https://social.msdn.microsoft.com/Forums/sqlserver/en-US/home?forum=transactsql)
  - [SQL Server のドキュメント](https://social.msdn.microsoft.com/Forums/sqlserver/en-US/home?forum=sqldocumentation)

- または、[#sqlhelp](https://twitter.com/search?q=%23sqlhelp) を付けて [@SQLServer](https://twitter.com/SQLServer) にツイートしてください

**[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] をお試しください**

- [![Evaluation Center からダウンロードする](../includes/media/download2.png)](https://go.microsoft.com/fwlink/?LinkID=862101) [[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] をダウンロードして Windows にインストールする](https://go.microsoft.com/fwlink/?LinkID=862101)。
- [Red Hat Enterprise Server](../linux/quickstart-install-connect-red-hat.md)、[SUSE Linux Enterprise Server](../linux/quickstart-install-connect-suse.md)、および [Ubuntu](../linux/quickstart-install-connect-ubuntu.md) の Linux にインストールする。
- [Docker 上で [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)]を実行する](../linux/quickstart-install-connect-docker.md)。