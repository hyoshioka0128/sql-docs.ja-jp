---
title: ファイアウォールの構成
description: SQL Server Machine Learning Services からの発信接続用にファイアウォールを構成する方法について説明します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/17/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: c55f68a1134fee6477c52182ad66b8705e363296
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715593"
---
# <a name="firewall-configuration-for-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services のファイアウォール構成
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、machine learning サービスを使用する場合に管理者またはアーキテクトが念頭に置く必要があるファイアウォールの構成に関する考慮事項を示します。

## <a name="default-firewall-rules"></a>既定のファイアウォール規則

既定では、SQL Server セットアップは、ファイアウォール規則を作成することによって送信接続を無効にします。

SQL Server 2016 および2017では、これらの規則はローカルユーザーアカウントに基づいています。セットアップでは、メンバーへのネットワークアクセスを拒否する**SQLRUserGroup**の送信規則が1つ作成されています (各ワーカーアカウントは、規則に従ってローカルの原則として一覧表示されていました。 SQLRUserGroup の詳細については、 [SQL Server Machine Learning Services の「機能拡張フレームワークのセキュリティの概要](../../advanced-analytics/concepts/security.md#sqlrusergroup)」を参照してください。

SQL Server 2019 では、AppContainers への移行の一環として、AppContainer Sid に基づく新しいファイアウォールルールがあります。これには、SQL Server セットアップで作成された20の AppContainers それぞれに1つずつあります。 ファイアウォール規則名の名前付け規則は、 **SQL Server インスタンス MSSQLSERVER の appcontainer-00 のネットワークアクセスをブロック**します。ここで、00は appcontainer の番号 (既定では 00-20)、MSSQLSERVER は SQL Server インスタンスの名前です。

> [!Note]
> ネットワーク呼び出しが必要な場合は、Windows ファイアウォールで送信ルールを無効にすることができます。

## <a name="restrict-network-access"></a>ネットワークアクセスを制限する

既定のインストールでは、外部ランタイムプロセスからのすべての送信ネットワークアクセスをブロックするために Windows ファイアウォール規則が使用されます。 外部ランタイムプロセスがパッケージをダウンロードしたり、悪意のある可能性があるその他のネットワーク呼び出しを行ったりするのを防ぐために、ファイアウォール規則を作成する必要があります。

別のファイアウォールプログラムを使用している場合は、ローカルユーザーアカウントまたはユーザーアカウントプールによって表されるグループのルールを設定することによって、外部ランタイムの送信ネットワーク接続をブロックするルールを作成することもできます。

R または Python ランタイムによる無制限のネットワークアクセスを防ぐために、Windows ファイアウォール (または任意の別のファイアウォール) を有効にすることを強くお勧めします。

## <a name="next-steps"></a>次の手順

[バインドされた接続用に Windows ファイアウォールを構成する](../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)