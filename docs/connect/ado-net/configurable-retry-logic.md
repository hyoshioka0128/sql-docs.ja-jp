---
title: SqlClient の構成可能な再試行ロジック
description: 接続を確立したり、コマンドを実行したりするときに、Microsoft.Data.SqlClient の構成可能な再試行ロジック機能を使用する方法について説明します。
ms.date: 03/22/2021
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-deshtehari
ms.openlocfilehash: aa0b6a5d984b2891f2fcf868b15a495b4984b8ba
ms.sourcegitcommit: d8cbbeffa3faa110e02056ff97dc7102b400ffb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107003954"
---
# <a name="configurable-retry-logic-in-sqlclient"></a>SqlClient の構成可能な再試行ロジック

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

クラウドで実行されている要素と通信するアプリケーションは、この環境で発生する一時的な障害に敏感である必要があります。 通常、これらの障害は自己修正されます。 障害のトリガーとなったアクションを適切な待ち時間の経過後に再試行すると、高い確率で正常に実行されます。

> [!NOTE]
> この機能は、Microsoft.Data.SqlClient バージョン 3.0.0 プレビュー 1 以降で使用できます。

## <a name="retry-pattern"></a>再試行パターン

例外をスローしてユーザーに次のアクションを決定させるのではなく、一時的なエラーにもかかわらず操作を完了しようとすることは、再試行パターンと呼ばれるインテリジェントな決定です。 詳細については、「[再試行パターン](/azure/architecture/patterns/retry)」を参照してください。

## <a name="transient-faults"></a>一時的な障害

堅牢なインフラストラクチャを構築し、最新のテクノロジで実装された既知のアプリケーションを使用して、サービスのダウンタイムを短縮できます。 しかし、障害をゼロにすることは不可能です。 一時的なエラーとは、既知の理由で発生することがあり、しばらくすると消える障害です。 たとえば、サーバー側で負荷分散の変更が進行しているときに、要求されたサービスが一時的に失敗したり、タイムアウトしたりする可能性があります。詳細については、[一時的な障害](/azure/azure-sql/database/troubleshoot-common-connectivity-issues#transient-errors-transient-faults)に関する記事を参照してください。

## <a name="do-and-dont"></a>やるべきことと、やってはならないこと

再試行パターンを使用するとアプリケーションの回復性は大幅に向上しますが、間違った状況で使用すると、アプリケーションに悪影響を与える可能性があります。 一時的な障害のリストに例外を追加する前に、しばらく時間をおいて、"すぐに解決するか" を自問自答してください。 急がないでください。 質問に対する適切な答えが見つからない場合は、理由を調べてみましょう。 詳細については、「[Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング](/azure/azure-sql/database/troubleshoot-common-errors-issues)」を参照してください。

## <a name="in-this-section"></a>このセクションの内容

[SqlClient の構成可能な再試行ロジックの概要](configurable-retry-logic-sqlclient-introduction.md)  
構成可能な再試行ロジックのさまざまなセクションについて説明します。

[SqlClient の内部再試行ロジック プロバイダー](internal-retry-logic-providers-sqlclient.md)  
事前定義済みの再試行プロバイダーを使用して、再試行ロジックをデータベースに適用する方法を示します。

[SqlClient の構成可能な再試行ロジックのコア API](configurable-retry-logic-core-apis-sqlclient.md)  
コア API を使用してカスタム再試行ロジックを実装する方法を示します。

[SqlClient での構成可能な再試行ロジック構成ファイル](configurable-retry-logic-config-file-sqlclient.md)  
構成ファイルを介して既定の再試行ロジック プロバイダーを指定する方法を示します。

## <a name="see-also"></a>関連項目

- [再試行パターン](/azure/architecture/patterns/retry)
- [一時的な障害](/azure/azure-sql/database/troubleshoot-common-connectivity-issues#transient-errors-transient-faults)
- [Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング](/azure/azure-sql/database/troubleshoot-common-errors-issues)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
