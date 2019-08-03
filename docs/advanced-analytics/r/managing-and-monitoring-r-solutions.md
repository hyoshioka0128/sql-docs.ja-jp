---
title: Machine learning ワークロードの管理と統合
description: SQL Server DBA として、データベースエンジンインスタンスに machine learning R と Python サブシステムをデプロイするための管理タスクを確認します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/10/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 2677b48daf253a85f2b74078bdad7de65e37d572
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715057"
---
# <a name="manage-and-integrate-machine-learning-workloads-on-sql-server"></a>SQL Server での machine learning ワークロードの管理と統合
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事は、複数のワークロードをサポートするサーバー資産への効率的なデータサイエンスインフラストラクチャのデプロイを担当する SQL Server データベース管理者を対象としています。 ここでは、R の管理と、SQL Server での Python コードの実行に関連する管理上の問題領域をフレームにします。 

## <a name="what-is-feature-integration"></a>機能の統合とは

R および Python machine learning は、データベースエンジンインスタンスの拡張機能として[SQL Server Machine Learning Services](../what-is-sql-server-machine-learning.md)によって提供されます。 統合は、主にセキュリティ層とデータ定義言語を通じて行われ、次のようにまとめられています。

+ ストアドプロシージャは、入力パラメーターとして R および Python コードを受け入れる機能を備えています。 開発者やデータ科学者は、[システムストアドプロシージャ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql?view=sql-server-2017)を使用したり、コードをラップするカスタムプロシージャを作成したりできます。
+ 以前にトレーニングされたデータモデルを使用できる t-sql 関数 (つまり、 [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql))。 この機能は、T-sql を通じて使用できます。 そのため、特に machine learning 拡張機能がインストールされていないシステムで呼び出すことができます。
+ 既存のデータベースログインおよびロールベースの権限は、その同じデータを使用するユーザーが呼び出したスクリプトに対応します。 一般的な規則として、ユーザーがクエリを使用してデータにアクセスできない場合は、スクリプトを使用してアクセスすることはできません。

## <a name="feature-availability"></a>機能の可用性

R と Python の統合は、一連の手順を通じて利用可能になります。 最初の設定は、データベースエンジンインスタンスに[ **Machine Learning Services**機能を含めたり、追加したり](../install/sql-machine-learning-services-windows-install.md)するときに設定します。 その後の手順として、データベースエンジンインスタンスで外部スクリプトを有効にする必要があります (既定ではオフになっています)。

この時点で、外部スクリプトの作成と実行、パッケージの追加または削除、およびストアドプロシージャやその他のオブジェクトの作成を行うための完全な権限は、データベース管理者のみに与えられています。

他のすべてのユーザーには、EXECUTE ANY EXTERNAL SCRIPT 権限が許可されている必要があります。 追加の[標準データベース権限](../security/user-permission.md)により、ユーザーがオブジェクトを作成したり、スクリプトを実行したり、シリアル化およびトレーニング済みのモデルを使用したりできるかどうかなどが決まります。 

## <a name="resource-allocation"></a>リソース割り当て

外部処理を呼び出すストアドプロシージャと T-sql クエリでは、既定のリソースプールで使用できるリソースが使用されます。 既定の構成の一部として、R や Python セッションなどの外部プロセスでは、ホストシステムの合計メモリの 20% までを使用できます。 

リソースを再調整する場合は、既定のプールを変更し、そのシステムで実行されている machine learning ワークロードに対応する効果を与えることができます。

または、特定の期間に発生した特定のプログラム、ホスト、またはアクティビティからのセッションをキャプチャするカスタム外部リソースプールを作成する方法もあります。 詳細については、「 [R および Python の実行に関するリソースレベルを変更するためのリソースガバナンス](../administration/resource-governance.md)」と「[リソースプールを作成する方法](../administration/how-to-create-a-resource-pool.md)」を参照してください。

## <a name="isolation-and-containment"></a>分離とコンテインメント

処理アーキテクチャは、コアエンジン処理から外部スクリプトを分離するように設計されています。 R スクリプトと Python スクリプトは、ローカルワーカーアカウントで個別のプロセスとして実行されます。 タスクマネージャーでは、SQL Server サービスアカウントとは別に、低い特権のローカルユーザーアカウントで実行されている R および Python プロセスを監視することができます。 

個々の低い特権のアカウントで R および Python プロセスを実行すると、次のような利点があります。

+ R と Python セッションからコアエンジンプロセスを分離することで、コアデータベース操作に影響を与えることなく R または Python プロセスを終了できます。 

+ ホストコンピューター上の外部スクリプトのランタイムプロセスの特権を減らします。

最小特権のアカウントは、セットアップ時に作成され、 **SQLRUserGroup**という Windows*ユーザーアカウントプール*に配置されます。 既定では、このグループには、SQL Server の R および Python の program フォルダーにある実行可能ファイル、ライブラリ、および組み込みのデータセットを使用するためのアクセス許可があります。 

DBA は、SQL Server データセキュリティを使用して、スクリプトを実行するアクセス許可を持つユーザーを指定できます。また、ジョブで使用されるデータは、T-sql クエリを使用してアクセスを制御するのと同じセキュリティロールで管理されます。 システム管理者は、Acl を作成することによって、ローカルサーバー上の機微なデータへの**SQLRUserGroup**アクセスを明示的に拒否できます。

>[!NOTE]
> 既定では、 **SQLRUserGroup**には SQL Server 自体にログインまたはアクセス許可がありません。 ワーカーアカウントにデータアクセスのためのログインが必要な場合は、自分で作成する必要があります。 ログインの作成を明示的に呼び出すシナリオでは、ユーザー id が Windows ユーザーであり、接続文字列に信頼されたユーザーが指定されている場合に、データベースエンジンインスタンスでのデータまたは操作に対して実行中のスクリプトからの要求をサポートします。 詳細については、「[データベースユーザーとしての SQLRUserGroup の追加](../../advanced-analytics/security/create-a-login-for-sqlrusergroup.md)」を参照してください。

## <a name="disable-script-execution"></a>スクリプトの実行を無効にする

ランナウェイスクリプトが発生した場合は、すべてのスクリプトの実行を無効にして、前の手順を逆にして外部スクリプトを実行できるようにすることができます。

1. SQL Server Management Studio または別のクエリツールで、次のコマンド`external scripts enabled`を実行して0または FALSE に設定します。

    ```sql
    EXEC sp_configure  'external scripts enabled', 0
    RECONFIGURE WITH OVERRIDE
    ```
2. データベースエンジンサービスを再起動します。

問題を解決したら、データベースエンジンインスタンスで R および Python スクリプトのサポートを再開する場合は、インスタンスでスクリプトの実行を再度有効にしてください。 詳細については、「[スクリプトの実行の有効化](../install/sql-machine-learning-services-windows-install.md#enable-script-execution)」を参照してください。

## <a name="extend-functionality"></a>機能の拡張

データサイエンスでは、多くの場合、パッケージの配置と管理に関する新しい要件が導入されています。 データ科学者にとっては、オープンソースとサードパーティのパッケージをカスタムソリューションで利用するのが一般的です。 これらのパッケージの中には、他のパッケージに依存しているものもあります。その場合は、複数のパッケージを評価してインストールし、目標を達成する必要があります。

サーバー資産を担当する DBA は、任意の R および Python パッケージを実稼働サーバーに配置することは、未知の課題を表します。 パッケージを追加する前に、外部パッケージによって提供される機能が本当に必要かどうかを評価し、SQL Server セットアップによってインストールされた組み込みの[R 言語](r-libraries-and-data-types.md)および[Python ライブラリ](../python/python-libraries-and-data-types.md)に相当するものはありません。 

サーバーパッケージのインストールの代わりに、データ科学者は、[外部ワークステーションでソリューションを構築して実行](../r/set-up-a-data-science-client.md)し、SQL Server からデータを取得することができますが、すべての分析をサーバーではなくワークステーションでローカルに実行することもできます。自分自身. 

その後、外部ライブラリ関数が必要であり、サーバーの操作やデータ全体に対するリスクがないと判断した場合は、複数の方法論を選択してパッケージを追加できます。 ほとんどの場合、SQL Server にパッケージを追加するには、管理者権限が必要です。 詳細については、「 [SQL Server での Python パッケージのインストール](../python/install-additional-python-packages-on-sql-server.md)」と「 [SQL Server での R パッケージのインストール](install-additional-r-packages-on-sql-server.md)」を参照してください。

> [!NOTE]
> R パッケージの場合、別の方法を使用すると、パッケージのインストールにサーバー管理者権限は特に必要ありません。 詳細については、「 [SQL Server での R パッケージのインストール](install-additional-r-packages-on-sql-server.md)」を参照してください。

## <a name="monitoring-script-execution"></a>スクリプトの実行の監視

で[!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)]実行される R および Python スクリプトは、 [!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)]インターフェイスによって開始されます。 ただし、スタートパッドは、リソースを適切に管理するために Microsoft によって提供されるセキュリティで保護されたサービスであるため、リソースを個別に管理または監視することはありません。

スタートパッドサービスで実行される外部スクリプトは、 [Windows ジョブオブジェクト](/windows/desktop/ProcThread/job-objects)を使用して管理されます。 ジョブ オブジェクトによって、プロセス グループをユニットとして管理できます。 各ジョブ オブジェクトは階層的であり、それに関連付けられているすべてのプロセスの属性を制御します。 ジョブ オブジェクトに対して実行される操作は、そのジョブ オブジェクトに関連付けられているすべてのプロセスに影響します。

したがって、オブジェクトに関連付けられている 1 つのジョブを終了する必要がある場合は、関連するすべてのプロセスも終了されることに注意してください。 Windows ジョブ オブジェクトに割り当てられている R スクリプトを実行しているときに、終了する必要がある関連する ODBC ジョブをそのスクリプトが実行している場合は、親の R スクリプトのプロセスも終了します。

並列処理を使用する外部スクリプトを開始すると、1つの Windows ジョブオブジェクトがすべての並列子プロセスを管理します。

プロセスがジョブで実行されているかどうかを調べるには、`IsProcessInJob` 関数を使用します。

## <a name="next-steps"></a>次のステップ

+ 詳細については、[機能拡張アーキテクチャ](../concepts/extensibility-framework.md)と[セキュリティ](../concepts/security.md)の概念とコンポーネントを確認してください。

+ 機能のインストールの一環として、既にエンドユーザーのデータアクセス制御について理解しているかもしれませんが、詳細については、「 [machine learning を SQL Server するためのアクセス許可をユーザーに付与する](../security/user-permission.md)」を参照してください。 

+ 計算集中型の機械学習ワークロードのためにシステムリソースを調整する方法について説明します。 詳細については、「[リソースプールを作成する方法](../administration/how-to-create-a-resource-pool.md)」を参照してください。
