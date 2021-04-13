---
title: '[Common Criteria Compliance Enabled] の構成'
description: SQL Server の [情報セキュリティ国際評価基準 (Common Criteria) への準拠] オプションによってどの基準が有効になるかについて説明します。 情報セキュリティ国際評価基準の評価保証レベルに準拠する方法を参照してください。 EUCC 証明書承認の場合。 規制されている業界や機関にわたる世界規模のコンプライアンスの義務。
ms.prod: sql
ms.prod_service: high-availability
ms.technology: configuration
ms.topic: conceptual
f1_keywords:
- common criteria compliance
helpviewer_keywords:
- CC (common criteria) [Database Engine]
- common criteria compliance [Database Engine]
- Risidual Information Protection [Database Engine]
- RIP (Residual Information Protection)
author: markingmyname
ms.author: maghan
ms.reviewer: wopeter
ms.custom: ''
ms.date: 04/07/2021
ms.openlocfilehash: 8d601d227ebfc63007cb0af9cd859316d5c93357
ms.sourcegitcommit: 09122d02fc3d86c6028366653337c083da8a3f4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107072424"
---
# <a name="common-criteria-compliance-enabled-server-configuration"></a>Common Criteria Compliance Enabled サーバー構成

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

[情報セキュリティ国際評価基準 (Common Criteria) への準拠] オプションを使用すると、[情報技術セキュリティ評価のためのコモンクライテリア](https://www.commoncriteriaportal.org)で必要とされる次の要素を有効にできます。 規制されている業界や機関にわたる世界規模のコンプライアンスの義務のための要件。

| 条件 | 説明 |
|----------|-------------|
| 残存情報保護 (RIP) | RIP の要件とは、新しいリソースにメモリを再度割り当てる前に、メモリ割り当てを既知のビット パターンで上書きすることです。 RIP 標準を満たすとセキュリティの向上が図れますが、メモリ割り当てを上書きすることによってパフォーマンスが低下する場合があります。 [情報セキュリティ国際評価基準 (Common Criteria) への準拠を有効にする] を有効にすると、この上書きが行われます。 |
|ログインの統計を表示する機能 | [情報セキュリティ国際評価基準 (Common Criteria) への準拠] オプションを有効にすると、ログイン監査が有効になります。 </br></br></br> ユーザーが SQL Server に正常にログインするたびに、セッションごとに使用可能になるログイン時間。 </br> - 最後に成功したログイン時間に関する情報 </br> - 最後に失敗したログイン時間 </br> - 最後に成功したログインから現在のログインまでの試行回数。 </br></br></br> このログインに関する統計情報は、 [sys.dm_exec_sessions](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md) 動的管理ビューにクエリを実行すると表示できます。 |
|その列の `GRANT` がテーブルの `DENY` をオーバーライドしないこと | [common criteria compliance enabled] オプションを有効にすると、テーブル レベルの `DENY` が列レベルの `GRANT` より優先されます。 このオプションが有効でない場合は、列レベルの `GRANT` がテーブル レベルの `DENY` より優先されます。 |

[Common Criteria Compliance Enabled] オプションは拡張オプションです。 情報セキュリティ国際評価基準は、Enterprise Edition と Datacenter Edition についてのみ評価され、認定されています。 情報セキュリティ国際評価基準の最新の状況については、[Microsoft SQL Server の情報セキュリティ国際評価基準](https://go.microsoft.com/fwlink/?LinkId=616319)のサイトを参照してください。

> [!IMPORTANT]
> 情報セキュリティ国際評価基準の評価保証レベル 4+ (EAL4+) に準拠するには、[Common Criteria Compliance Enabled] オプションを有効にすることの他に、SQL Server の構成を完了するスクリプトをダウンロードして実行する必要があります。 このスクリプトは、[Microsoft SQL Server 情報セキュリティ国際評価基準](https://go.microsoft.com/fwlink/?LinkId=616319)のサイトからダウンロードできます。

`sp_configure` システム ストアド プロシージャを使用してこの設定を変更する場合、[Common Criteria Compliance Enabled] を変更できるのは、show advanced options が 1 に設定されているときだけです。 この設定は、サーバーを再起動した後に有効になります。 有効値は、0 および 1 です。

- 0 は [情報セキュリティ国際評価基準 (Common Criteria) への準拠] が有効でない (既定値) ことを表します。

- 1 は [情報セキュリティ国際評価基準 (Common Criteria) への準拠を有効にする] が有効であることを表します。

## <a name="examples"></a>例

次の例では、情報セキュリティ国際評価基準への準拠を有効にしています。

```sql
sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
sp_configure 'common criteria compliance enabled', 1;
GO
RECONFIGURE WITH OVERRIDE;
GO
```

SQL Server を再起動してください。

## <a name="next-steps"></a>次のステップ

- [サーバー構成オプション &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)
