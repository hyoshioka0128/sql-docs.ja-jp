---
ms.openlocfilehash: ffd608faf64818a7acd9e38d9c502f575be6716a
ms.sourcegitcommit: 5e838bdf705136f34d4d8b622740b0e643cb8d96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69653052"
---
## <a name="enabled-deployment-scenarios"></a>有効なデプロイ シナリオ

SQL Server 2019 リリース候補 (RC) では、次のシナリオが可能になります。

- サイド バイ サイド インストール。 SQL Server 2019 RC のインスタンスを、SQL Server 2012 から SQL Server 2017 までのインスタンス、または SQL Server 2019 CTP 3.0 以降のインスタンスと一緒にインストールします。
   >[!NOTE]
   >サイド バイ サイドは、SQL Server 2008 および 2008 R2 ではブロックされていませんが、これらと SQL Server 2019 の間で、一般的にサポートされている Windows オペレーティングシステムのバージョンはありません。
- インプレース アップグレード。 SQL Server 2019 RC のインスタンスを、SQL Server 2012 から SQL Server 2017 までのインスタンスおよび SQL Server CTP 3.0 からアップグレードします。 3\.0 より下の SQL Server 2019 CTP からのアップグレードはサポートされていないため、新しいインストールを実行する必要があります。
   >[!NOTE]
   >SQL Server 2008 と 2008 R2 からのインプレース アップグレードはブロックされませんが、これらと SQL Server 2019 の間で、一般的にサポートされている Windows オペレーティングシステムのバージョンはありません。

## <a name="support"></a>サポート

SQL Server 2019 RC はプレビュー ソフトウェアです。 この操作は、公式にはサポートされていません。 [SQL 早期導入プログラム](http://aka.ms/sqleap)を使用しているお客様は、Microsoft と相談して特別な契約を結び、SQL Server 2019 RC の実行がサポートされている場合があります。

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