---
title: 外部スクリプトの同時実行のスケーリング
description: ユーザーアカウントプールで並行または同時 R と Python スクリプトの実行を構成して、SQL Server Machine Learning Services をスケーリングします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/17/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: a5a0cd5ab05f7675ce011fe5205cbba3432725f4
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715862"
---
# <a name="scale-concurrent-execution-of-external-scripts-in-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services での外部スクリプトの同時実行のスケーリング
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)] サービスによるタスクの実行をサポートするために、[!INCLUDE[rsql-productnamenew-md](../../includes/rsql-productnamenew-md.md)] のインストール プロセスの一部として、新しい Windows *"ユーザー アカウント プール"* が作成されました。 これらのワーカーアカウントの目的は、さまざまな SQL ユーザーによって外部スクリプトの同時実行を分離することです。

この記事では、ワーカーアカウントの既定の構成と容量について説明し、SQL Server Machine Learning Services での外部スクリプトの同時実行数をスケールするように既定の構成を変更する方法について説明します。

## <a name="worker-account-group"></a>ワーカーアカウントグループ

Windows アカウントグループは、machine learning [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]がインストールされて有効になっている各インスタンスについて、セットアップによって作成されます。

- 既定のインスタンスでは、グループ名は **SQLRUserGroup** です。 名前は、R と Python のどちらを使用している場合も同じです。
- 名前付きインスタンスでは、既定のグループ名にインスタンス名が付加されます: たとえば、 **SQLRUserGroupMyInstanceName**。

既定では、ユーザー アカウント プールには 20 個のユーザー アカウントが含まれています。 多くの場合、20は機械学習のタスクをサポートするのに十分ではありませんが、アカウント数を変更することができます。 アカウントの最大数は100です。

- 既定のインスタンスでは、個々のアカウントは **MSSQLSERVER01** ～ **MSSQLSERVER20** と命名されます。
- 名前付きインスタンスの場合、個々のアカウントはインスタンス名に基づいて命名されます。たとえば、 **MyInstanceName01** ～ **MyInstanceName20**。

複数のインスタンスが machine learning を使用している場合、コンピューターには複数のユーザーグループが存在します。 グループをインスタンス間で共有することはできません。

> [!Note]
> SQL Server 2019 では、 **SQLRUserGroup**には1つのメンバーがあります。これは、複数のワーカーアカウントではなく、1つの SQL Server Launchpad サービスアカウントになります。

<a name = "HowToChangeGroup"> </a>

## <a name="number-of-worker-accounts"></a>ワーカーアカウントの数

アカウント プール内のユーザー数を変更するには、以下に示すように、[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)] サービスのプロパティを編集する必要があります。

各ユーザー アカウントに関連付けられるパスワードはランダムに生成されますが、アカウントの作成後に変更することもできます。

1. SQL Server 構成マネージャーを開き、 **[SQL Server のサービス]** をクリックします。
2. SQL Server スタート パッド サービスをダブルクリックし、サービスが実行されている場合は、サービスを停止します。
3.  **[サービス]** タブで、[開始モード] が [自動] に設定されていることを確認します。 スタートパッドが実行されていない場合、外部スクリプトを開始することはできません。
4.  **[詳細設定]** タブをクリックし、必要であれば、 **[外部ユーザーの数]** の値を編集します。 この設定では、さまざまな SQL ユーザーが外部スクリプトセッションを同時に実行できる回数を制御します。 既定値は20アカウントです。 ユーザーの最大数は100です。
5. パスワードを定期的に変更するポリシーが組織で実施されている場合は、オプションで、 **[外部ユーザーのパスワードのリセット]** を _[はい]_ に設定できます。 これを行うと、ユーザー アカウントに対してスタート パッドが保持している暗号化パスワードが再生成されます。 詳しくは、「[パスワード ポリシーの実施](../security/sql-server-launchpad-service-account.md#bkmk_EnforcePolicy)」をご覧ください。
6.  スタートパッドサービスを再起動します。

## <a name="managing-workloads"></a>ワークロードの管理

このプール内のアカウントの数によって、同時にアクティブにできる外部スクリプトセッションの数が決まります。  既定では、20個のアカウントが作成されます。つまり、20人のユーザーが同時にアクティブな R または Python セッションを持つことができます。 同時に20個を超えるスクリプトを実行することが予想される場合は、ワーカーアカウントの数を増やすことができます。

同じユーザーが複数の外部スクリプトを同時に実行すると、そのユーザーによって実行されるすべてのセッションで同じワーカーアカウントが使用されます。 たとえば、リソースが許可されている限り、1人のユーザーが同時に100の異なる R スクリプトを実行する場合がありますが、すべてのスクリプトが1つのワーカーアカウントを使用して実行されます。

サポートできるワーカーアカウントの数と、1人のユーザーが実行できる同時セッションの数は、サーバーリソースによってのみ制限されます。 通常、R ランタイムの使用時に遭遇する最初のボトルネックは、メモリです。

Python または R スクリプトで使用できるリソースは、SQL Server によって管理されます。 SQL Server の DMV を使用してリソースの使用状況を監視するか、または関連する Windows ジョブ オブジェクトのパフォーマンス カウンターを見て、サーバー メモリの使用量を必要に応じて調整することをお勧めします。 SQL Server Enterprise エディションを使用している場合は、外部[リソースプール](how-to-create-a-resource-pool.md)を構成することによって、外部スクリプトの実行に使用するリソースを割り当てることができます。

## <a name="see-also"></a>関連項目

容量の構成の詳細については、次の記事を参照してください。

- [R Services の SQL Server 構成](../../advanced-analytics/r/sql-server-configuration-r-services.md)
- [R Services のパフォーマンスケーススタディ](../../advanced-analytics/r/performance-case-study-r-services.md)