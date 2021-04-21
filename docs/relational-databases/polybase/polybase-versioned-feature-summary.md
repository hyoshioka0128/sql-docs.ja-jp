---
description: PolyBase の機能と制限事項
title: PolyBase の機能と制限事項
descriptions: This article summarizes PolyBase features available for SQL Server products and services. It lists T-SQL operators supported for pushdown and known limitations.
ms.date: 04/19/2021
ms.prod: sql
ms.technology: polybase
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: ''
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 7ad508f4d745468a194ca95769439845887f2d1f
ms.sourcegitcommit: 708b3131db2e542b1d461d17dec30d673fd5f0fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107729126"
---
# <a name="polybase-features-and-limitations"></a>PolyBase の機能と制限事項

[!INCLUDE[appliesto-ss2016-asdb-asdw-pdw-md](../../includes/tsql-appliesto-ss2016-all-md.md)]

この記事は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の製品およびサービスで利用可能な PolyBase 機能の概要です。  
  
## <a name="feature-summary-for-product-releases"></a>製品リリースの機能の概要

PolyBase の主な機能と、これらの機能を利用できる製品を一覧表示した表を次に示します。  

|**機能** |**[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]** (2016 以降) |**Azure SQL Database** |**[!INCLUDE[ssazuresynapse_md](../../includes/ssazuresynapse_md.md)]** |**Parallel Data Warehouse** |
|---------|---------|---------|---------|---------|
|で Hadoop データのクエリを実行する [!INCLUDE[tsql](../../includes/tsql-md.md)]|はい|いいえ|いいえ|はい|
|Hadoop からデータをインポートする|はい|いいえ|いいえ|はい|
|データを Hadoop にエクスポートする  |はい|いいえ|いいえ| はい|
|Azure HDInsight のクエリ、インポート、エクスポート |いいえ|いいえ|いいえ|いいえ
|クエリの計算を Hadoop にプッシュダウンする|はい|いいえ|いいえ|はい|  
|Azure Blob Storage からデータをインポートする|はい|はい<sup>*</sup>|はい|はい|
|Azure Blob Storage にデータをエクスポートする|はい|いいえ|はい|はい|  
|Azure Data Lake Store からデータをインポートする|いいえ|いいえ|はい|いいえ|
|Azure Data Lake Store にデータをエクスポートする|いいえ|いいえ|はい|いいえ|
|Microsoft BI ツールから PolyBase クエリを実行する|はい|いいえ|はい|Yes|

<sup>*</sup> [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)] で導入されています。「[Azure BLOB ストレージのデータに一括アクセスする例](../import-export/examples-of-bulk-access-to-data-in-azure-blob-storage.md)」をご覧ください。



## <a name="known-limitations"></a>既知の制限事項

PolyBase には次の制限事項があります。

- PolyBase を使用するには、データベースでの sysadmin または CONTROL SERVER レベルのアクセス許可が必要です。

- [!INCLUDE[sssql19-md](../../includes/sssql19-md.md)] より前では、可変長列の全長を含む最大行サイズは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で 32 KB 以下、または [!INCLUDE[ssazuresynapse_md](../../includes/ssazuresynapse_md.md)] で 1 MB 以下にする必要があります。 [!INCLUDE[sssql19-md](../../includes/sssql19-md.md)] 以降では、この制限は解除されています。 Hadoop データ ソースの場合、制限は 1 MB のままですが、他のデータ ソースについては、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の上限によってのみ制限されます。

- [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] または [!INCLUDE[ssazuresynapse_md](../../includes/ssazuresynapse_md.md)] からデータが ORC ファイル形式にエクスポートされるときに、テキストの量が多い列が制限される可能性があります。 Java のメモリ不足エラー メッセージにより、わずか 50 列に制限される場合があります。 この問題を回避するには、列のサブセットだけをエクスポートします。

- Knox が有効になっている場合、PolyBase を Hadoop インスタンスに接続することはできません。

- transactional = true の Hive テーブルを使用している場合、PolyBase は Hive テーブルのディレクトリにあるデータにアクセスできません。

- PolyBase サービスが正常に機能するには、SQL Server サービスで TCP/IP ネットワーク プロトコルを有効にする必要があります。 また、TCP/IP プロトコルの構成設定 **[すべて受信待ち]** が **[いいえ]** に設定されている場合は、TCP/IP プロパティの **[IPAll]** の **[TCP 動的ポート]** または **[TCP ポート]** のどちらかに正しいリスナー ポートのエントリが残っている必要があります。 これが必要なのは、PolyBase サービスによる SQL Server エンジンのリスナー ポートの解決方法によるものです。

<!--SQL Server 2016-->
::: moniker range="= sql-server-2016 "

- [[!INCLUDE[sssql16-md](../../includes/sssql16-md.md)] のフェールオーバー クラスターにノードを追加すると、PolyBase の機能をインストールできません](https://support.microsoft.com/help/3173087/fix-polybase-feature-doesn-t-install-when-you-add-a-node-to-a-sql-server-2016-failover-cluster)。

::: moniker-end

## <a name="next-steps"></a>次のステップ

PolyBase の詳細については、「[PolyBase によるデータ仮想化の概要](polybase-guide.md)」を参照してください。
