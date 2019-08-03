---
title: R および Python 拡張機能のセキュリティの概要
description: SQL Server Machine Learning Services での機能拡張フレームワークのセキュリティの概要について説明します。 ログインとユーザーアカウント、SQL Server Launchpad サービス、ワーカーアカウント、複数のスクリプトの実行、およびファイルのアクセス許可のセキュリティ。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/17/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: f5b0af74633fb13b9cfd0b187bc2b180d7fd87b4
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715851"
---
# <a name="security-overview-for-the-extensibility-framework-in-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services の機能拡張フレームワークのセキュリティの概要

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、SQL Server データベースエンジンとそれに関連するコンポーネントを拡張機能フレームワークと統合するために使用される全体的なセキュリティアーキテクチャについて説明します。 Securables、サービス、プロセス id、アクセス許可を調べます。 SQL Server の機能拡張の主要な概念とコンポーネントの詳細については、「 [SQL Server Machine Learning Services] の拡張アーキテクチャ](extensibility-framework.md)」を参照してください。

## <a name="securables-for-external-script"></a>外部スクリプトの Securables

R または Python で記述された外部スクリプトは、この目的のために作成された[システムストアドプロシージャ](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)に入力パラメーターとして送信されるか、または定義されたストアドプロシージャでラップされます。 また、モデルを事前にトレーニングし、データベーステーブルにバイナリ形式で格納し、T-sql [PREDICT](../../t-sql/queries/predict-transact-sql.md)関数で呼び出すこともできます。

スクリプトは、既存のデータベーススキーマオブジェクト、ストアドプロシージャ、およびテーブルによって提供されるため、SQL Server Machine Learning Services の新しい[securables](../../relational-databases/security/securables.md)はありません。

スクリプトの使用方法や、スクリプトがどのように構成されているかにかかわらず、データベースオブジェクトが作成され、保存される可能性がありますが、スクリプトを格納するための新しいオブジェクト型は導入されません。 その結果、データベースオブジェクトの使用、作成、および保存の機能は、ユーザーに対して既に定義されているデータベースのアクセス許可に大きく依存します。

<a name="permissions"></a>

## <a name="permissions"></a>アクセス許可

SQL Server のデータベースログインとロールのデータセキュリティモデルは、R および Python スクリプトにまで拡張されています。 SQL Server データを使用する外部スクリプトを実行する場合、または計算コンテキストとして SQL Server で実行する外部スクリプトを実行する場合は、SQL Server ログインまたは Windows ユーザーアカウントが必要です。 アドホッククエリを実行する権限を持つデータベースユーザーは、R または Python スクリプトから同じデータにアクセスできます。

ログインまたはユーザーアカウントは、外部スクリプトの要件に応じて、複数のレベルのアクセスを必要とする可能性のある*セキュリティプリンシパル*を識別します。

+ 外部スクリプトが有効になっているデータベースにアクセスする権限。
+ テーブルなどのセキュリティで保護されたオブジェクトからデータを読み取る権限。
+ モデルやスコア付けの結果など、新しいデータをテーブルに書き込む機能。
+ テーブル、外部スクリプトを使用するストアドプロシージャ、R または Python ジョブを使用するカスタム関数など、新しいオブジェクトを作成する機能。
+ SQL Server コンピューターに新しいパッケージをインストールする権限、またはユーザーのグループに提供されるパッケージを使用する権限。

実行コンテキストとして SQL Server を使用して外部スクリプトを実行する各ユーザーは、データベース内のユーザーにマップする必要があります。 個別にユーザーのアクセス許可を設定するのではなく、データベースユーザーのアクセス許可を個別に設定するのではなく、アクセス許可のセットを管理するロールを作成し、それらのロールにユーザーを割り当てることができます。

詳細については、「 [Machine Learning Services を SQL Server するためのアクセス許可をユーザーに付与する](../../advanced-analytics/security/user-permission.md)」を参照してください。

## <a name="permissions-when-using-an-external-client-tool"></a>外部クライアントツールを使用する場合のアクセス許可

外部クライアントツールで R または Python を使用するユーザーは、データベース内で外部スクリプトを実行したり、データベースオブジェクトやデータにアクセスしたりする必要がある場合は、データベース内のユーザーにログインまたはアカウントをマップする必要があります。 外部スクリプトをリモートデータサイエンスクライアントから送信するか、T-sql ストアドプロシージャを使用して実行するかにかかわらず、同じ権限が必要です。

たとえば、ローカルコンピューター上で実行され、そのスクリプトを SQL Server で実行する外部スクリプトを作成したとします。 次の条件が満たされていることを確認する必要があります。

+ データベースがリモート接続を許可します。
+ データベースアクセスに使用した SQL ログインまたは Windows アカウントが、インスタンスレベルで SQL Server に追加されています。
+ SQL ログインまたは Windows ユーザーは、外部スクリプトを実行する権限を持っている必要があります。 一般に、このアクセス許可を追加できるのはデータベース管理者のみです。
+ SQL ログインまたはウィンドウユーザーは、外部スクリプトが次のいずれかの操作を実行する各データベースに、適切な権限を持つユーザーとして追加する必要があります。
  + データを取得しています。
  + データの書き込みまたは更新。
  + テーブルやストアドプロシージャなどの新しいオブジェクトを作成します。

ログインまたは Windows ユーザーアカウントがプロビジョニングされ、必要なアクセス許可が付与されたら、SQL Server で外部スクリプトを実行できます。そのためには、R または Python の**revoscalepy**ライブラリのデータソースオブジェクトを使用するか、ストアドプロシージャを呼び出します。外部スクリプトが含まれています。

外部スクリプトが SQL Server から起動されるたびに、データベースエンジンのセキュリティは、ジョブを開始したユーザーのセキュリティコンテキストを取得し、ユーザーまたはログインのセキュリティ保護可能なオブジェクトへのマッピングを管理します。

そのため、リモートクライアントから開始されるすべての外部スクリプトでは、接続文字列の一部として、ログイン情報またはユーザー情報を指定する必要があります。

<a name="launchpad"></a>

## <a name="services-used-in-external-processing-launchpad"></a>外部処理で使用されるサービス (スタートパッド)

拡張フレームワークは、SQL Server インストールの[サービスの一覧](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md#Service_Details)に新しい NT サービスを1つ追加します。[**SQL Server Launchpad (MSSSQLSERVER)** ](extensibility-framework.md#launchpad)。

データベースエンジンは、SQL Server Launchpad サービスを使用して、R または Python セッションを別個のプロセスとしてインスタンス化します。 プロセスは低い特権のアカウントで実行されます。SQL Server、スタートパッド自体、およびストアドプロシージャまたはホストクエリの実行に使用されたユーザー id とは異なります。 低い特権のアカウントでスクリプトを別のプロセスで実行することは、SQL Server での R および Python のセキュリティと分離モデルの基礎となります。

外部プロセスを起動するだけでなく、スタートパッドは、呼び出し元ユーザーの id を追跡し、その id をプロセスの開始に使用される低い特権のワーカーアカウントにマッピングする役割も担います。 場合によっては、スクリプトまたはコードがデータと操作のために SQL Server にコールバックする場合、スタートパッドは通常、id 転送をシームレスに管理できます。 SELECT ステートメントまたは呼び出し元の関数やその他のプログラミングオブジェクトを含むスクリプトは、通常、呼び出し元のユーザーが十分な権限を持っている場合に成功します。

> [!NOTE]
> 既定では[!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)] 、は**NT Service\MSSQLLaunchpad**で実行するように構成されています。これは、外部スクリプトを実行するために必要なすべてのアクセス許可を使用してプロビジョニングされます。 構成可能なオプションの詳細については、「 [SQL Server Launchpad サービスの構成](../security/sql-server-launchpad-service-account.md)」を参照してください。

<a name="sqlrusergroup"></a>

## <a name="identities-used-in-processing-sqlrusergroup"></a>処理で使用される id (SQLRUserGroup)

**SQLRUserGroup**(SQL 制限ユーザーグループ) は SQL Server セットアップによって作成され、低い特権のローカル Windows ユーザーアカウントのプールを含みます。 外部プロセスが必要な場合、スタートパッドは使用可能なワーカーアカウントを取得し、それを使用してプロセスを実行します。 具体的には、スタートパッドは使用可能なワーカーアカウントをアクティブ化し、呼び出し元ユーザーの id にマップして、ワーカーアカウントでスクリプトを実行します。

+ **SQLRUserGroup**は特定のインスタンスにリンクされています。 Machine learning が有効になっているインスタンスごとに、ワーカーアカウントのプールが個別に必要です。 インスタンス間でアカウントを共有することはできません。

+ ユーザーアカウントプールのサイズは静的で、既定値は20で、20個の同時セッションをサポートします。 同時に起動できる外部ランタイムセッションの数は、このユーザーアカウントプールのサイズによって制限されます。 

+ プール内のワーカーアカウント名の形式は、SQLInstanceName*nn*です。 たとえば、既定のインスタンスでは、 **SQLRUserGroup**に MSSQLSERVER01、MSSQLSERVER02 という名前のアカウントが、最大 MSSQLSERVER20 に含まれています。

並列化されたタスクは、追加のアカウントを使用しません。 たとえば、ユーザーが並列処理を使用するスコアリングタスクを実行する場合、同じワーカーアカウントがすべてのスレッドで再利用されます。 機械学習を頻繁に使用する場合は、外部スクリプトの実行に使用するアカウントの数を増やすことができます。 詳細については、「 [machine learning のユーザーアカウントプールを変更](../../advanced-analytics/administration/modify-user-account-pool.md)する」を参照してください。

::: moniker range=">=sql-server-ver15||=sqlallproducts-allversions"
### <a name="appcontainer-isolation-in-sql-server-2019"></a>SQL Server 2019 での AppContainer 分離

SQL Server 2019 では、セットアップで**SQLRUserGroup**のワーカーアカウントが作成されなくなりました。 代わりに、 [Appcontainers](https://docs.microsoft.com/windows/desktop/secauthz/appcontainer-isolation)によって分離が実現されます。 実行時に、ストアドプロシージャまたはクエリで外部スクリプトが検出されると、SQL Server は、拡張機能固有のランチャーの要求を使用してスタートパッドを呼び出します。 スタートパッドは、id の下にあるプロセスで適切なランタイム環境を呼び出し、それを含むように AppContainer をインスタンス化します。 この変更は、ローカルアカウントとパスワードの管理が不要になったため、有益です。 また、ローカルユーザーアカウントが禁止されているインストールでは、ローカルユーザーアカウントの依存関係を削除することで、この機能を使用できるようになります。

SQL Server によって実装された AppContainers は内部メカニズムです。 プロセスモニターに AppContainers の物理的な証拠が表示されることはありませんが、プロセスがネットワーク呼び出しを行わないように、セットアップによって作成された送信ファイアウォールルールで見つけることができます。 詳細については、「 [SQL Server Machine Learning Services のファイアウォールの構成](../../advanced-analytics/security/firewall-configuration.md)」を参照してください。

> [!Note]
> SQL Server 2019 では、 **SQLRUserGroup**には1つのメンバーがあります。これは、複数のワーカーアカウントではなく、1つの SQL Server Launchpad サービスアカウントになります。
::: moniker-end

### <a name="permissions-granted-to-sqlrusergroup"></a>SQLRUserGroup に付与されたアクセス許可

既定では、 **SQLRUserGroup**のメンバーは、R の実行可能ファイル、ライブラリ、および組み込みデータセットへのアクセス権を持つ、SQL Server **Binn**、 **R_SERVICES**、および**PYTHON_SERVICES**ディレクトリ内のファイルに対する読み取りおよび実行アクセス許可を持っています。および SQL Server と共にインストールされる Python ディストリビューション。 

SQL Server の重要なリソースを保護するために、必要に応じて、 **SQLRUserGroup**へのアクセスを拒否するアクセス制御リスト (ACL) を定義できます。 逆に、ホストコンピューター上に存在するローカルデータリソースにアクセス許可を付与することもできます。これは SQL Server 自体とは別のものです。 

仕様として、 **SQLRUserGroup**には、データベースログインまたはデータへのアクセス許可がありません。 特定の状況では、特に信頼された Windows id が呼び出し元のユーザーである場合に、ループバック接続を許可するログインを作成することができます。 この機能は[*暗黙の認証*](#implied-authentication)と呼ばれます。 詳細については、「[データベースユーザーとしての SQLRUserGroup の追加](../../advanced-analytics/security/create-a-login-for-sqlrusergroup.md)」を参照してください。

## <a name="identity-mapping"></a>Id マッピング

セッションが開始されると、スタートパッドは、呼び出し元のユーザーの id をワーカーアカウントにマップします。 外部の Windows ユーザーまたは有効な SQL ログインのワーカーアカウントへのマッピングは、外部スクリプトを実行する SQL ストアドプロシージャの有効期間中のみ有効です。 同じログインからの並列クエリは、同じユーザー ワーカー アカウントにマップされます。

実行中、スタートパッドはセッションデータを格納する一時フォルダーを作成し、セッションの終了時にそれらを削除します。 ディレクトリはアクセスが制限されています。 R の場合、RLauncher はこのタスクを実行します。 Python の場合、python ランチャーはこのタスクを実行します。 個々のワーカーアカウントはそれぞれ固有のフォルダーに制限され、フォルダー内のファイルには、独自のレベルを超えてアクセスすることはできません。 ただし、ワーカーアカウントは、作成されたセッション作業フォルダーの下にある子の読み取り、書き込み、または削除を行うことができます。 コンピューターの管理者であれば、各プロセスによって作成されたディレクトリを表示できます。 各ディレクトリはセッション GUID によって識別されます。

<a name="implied-authentication"></a>

## <a name="implied-authentication-loop-back-requests"></a>暗黙の認証 (ループバック要求)

*暗黙の認証*は、低い特権のワーカーアカウントとして実行されている外部プロセスを、データまたは操作のループバック要求に SQL Server するための信頼されたユーザー id として提示される接続要求の動作について説明します。 概念として、暗黙的な認証は Windows 認証に固有であり、SQL Server 接続文字列では、R や Python スクリプトなどの外部プロセスからの要求に対して、信頼関係接続を指定します。 これは、*ループバック*と呼ばれることもあります。

信頼関係接続は R および Python スクリプトから使用できますが、追加の構成でのみ使用できます。 拡張性アーキテクチャでは、R および Python プロセスは、親**SQLRUserGroup**からアクセス許可を継承するワーカーアカウントで実行されます。 接続文字列でを指定`Trusted_Connection=True`すると、ワーカーアカウントの id が接続要求に提示されます。これは、既定では SQL Server には不明です。

信頼関係接続を正常に確立するには、 **SQLRUserGroup**のデータベースログインを作成する必要があります。 この操作を実行すると、 **SQLRUserGroup**のメンバーからのすべての信頼関係接続に、SQL Server へのログイン権限が与えられます。 詳細な手順については、「 [Add SQLRUserGroup to a database login](../../advanced-analytics/security/create-a-login-for-sqlrusergroup.md)」を参照してください。

信頼関係接続は、最も広く使用されている接続要求の定式化ではありません。 R または Python スクリプトが接続を指定する場合は、ODBC データソースへの接続の場合、SQL ログインを使用するか、完全に指定されたユーザー名とパスワードを使用するのが一般的です。

### <a name="how-implied-authentication-works-for-r-and-python-sessions"></a>R および Python セッションでの暗黙の認証のしくみ

次の図は、SQL Server コンポーネントと R ランタイムの相互作用、および R の暗黙の認証を行う方法を示しています。

![R の暗黙の認証](../security/media/implied-auth-rsql.png)

次の図は、SQL Server コンポーネントと Python ランタイムの相互作用、および Python に対して暗黙的な認証を行う方法を示しています。

![Python の暗黙の認証](../security/media/implied-auth-python2.png)

## <a name="no-support-for-transparent-data-encryption-at-rest"></a>Rest での Transparent Data Encryption はサポートしていません

外部スクリプトランタイムとの間で送受信されるデータに対しては、 [Transparent Data Encryption (TDE)](../../relational-databases/security/encryption/transparent-data-encryption.md)はサポートされていません。 これは、外部プロセス (R または Python) が SQL Server プロセスの外部で実行されるためです。 そのため、外部ランタイムによって使用されるデータは、データベースエンジンの暗号化機能によって保護されません。 この動作は、SQL Server コンピューター上で実行されている他のクライアントとは異なり、データベースからデータを読み取り、コピーを行います。

その結果、TDE は、R または Python スクリプトで使用するデータや、ディスクに保存されているデータ、または保存された中間結果には適用され**ません**。 ただし、Windows BitLocker 暗号化や、ファイルまたはフォルダーレベルで適用されたサードパーティ暗号化など、他の種類の暗号化は引き続き適用されます。

[Always Encrypted](../../relational-databases/security/encryption/overview-of-key-management-for-always-encrypted.md)の場合、外部ランタイムは暗号化キーにアクセスできません。 そのため、スクリプトにデータを送信することはできません。

## <a name="next-steps"></a>次の手順

この記事では、[機能拡張フレームワーク](../../advanced-analytics/concepts/extensibility-framework.md)に組み込まれているセキュリティアーキテクチャのコンポーネントと相互作用モデルについて学習しました。 この記事で取り上げている主なポイントは、スタートパッド、SQLRUserGroup、worker アカウントの目的、R と Python のプロセス分離、およびユーザー id をワーカーアカウントにマップする方法です。 

次の手順として、[アクセス許可を付与](../../advanced-analytics/security/user-permission.md)するための手順を確認します。 Windows 認証を使用するサーバーの場合は、追加の構成が必要になるタイミングについては、「 [Add SQLRUserGroup to a database login](../../advanced-analytics/security/create-a-login-for-sqlrusergroup.md) 」も参照してください。