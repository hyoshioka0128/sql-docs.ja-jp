---
title: SQL Server Launchpad サービスアカウントの構成
description: SQL Server で外部スクリプトを実行するために使用される SQL Server Launchpad サービスアカウントを変更する方法。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/17/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: a94ae0f82b407f8f254a31b33d4d9dcd97f24e77
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714940"
---
# <a name="sql-server-launchpad-service-configuration"></a>サービス構成の SQL Server Launchpad
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

[!INCLUDE[rsql_launchpad_md](../../includes/rsql-launchpad-md.md)]は、フルテキストクエリを処理するために、フルテキストインデックス作成とクエリサービスが個別のホストを起動するのと同様に、外部スクリプトを管理および実行するサービスです。

詳細については、 [SQL Server Machine Learning Services の機能拡張フレームワークの](../../advanced-analytics/concepts/security.md#launchpad) [SQL Server Machine Learning Services](../../advanced-analytics/concepts/extensibility-framework.md#launchpad)とセキュリティの概要に関するセクションを参照してください。

## <a name="account-permissions"></a>アカウントのアクセス許可

既定では、SQL Server Launchpad は**NT Service\MSSQLLaunchpad**で実行するように構成されています。これは、外部スクリプトを実行するために必要なすべてのアクセス許可を使用してプロビジョニングされます。 このアカウントからアクセス許可を削除すると、スタートパッドが起動に失敗するか、外部スクリプトを実行する SQL Server インスタンスにアクセスできなくなる可能性があります。

サービスアカウントを変更する場合は、必ず [[ローカルセキュリティポリシー] コンソール](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/how-to-configure-security-policy-settings)を使用してください。

このアカウントに必要なアクセス許可を次の表に示します。

| グループポリシー設定 | 定数名 |
|----------------------|---------------|
| [プロセスのメモリクォータを調整する](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/adjust-memory-quotas-for-a-process) | SeIncreaseQuotaPrivilege | 
| [走査チェックのバイパス](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/bypass-traverse-checking) | SeChangeNotifyPrivilege | 
| [サービスとしてログオン](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/log-on-as-a-service) | SeServiceLogonRight | 
| [プロセスレベルトークンの置換](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/replace-a-process-level-token) | SeAssignPrimaryTokenPrivilege | 

SQL Server サービスの実行に必要なアクセス許可の詳細については、「[Configure Windows Service Accounts and Permissions (Windows サービス アカウントとアクセス許可の構成)](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)」をご覧ください。

<a name="bkmk_ChangingConfig"></a> 

## <a name="configuration-properties"></a>構成プロパティ

通常、サービス構成を変更する理由はありません。 変更できるプロパティには、サービスアカウント、外部プロセスの数 (既定では 20)、またはワーカーアカウントのパスワードリセットポリシーなどがあります。

1. [[SQL Server 構成マネージャー](../../relational-databases/sql-server-configuration-manager.md)] を開きます。

2. SQL Server Services で SQL Server Launchpad を右クリックし、**プロパティ** を選択します。
  + サービスアカウントを変更するには、 **[ログオン]** タブをクリックします。
  + ユーザー数を増やすには、 **[詳細設定]** タブをクリックし、**セキュリティコンテキスト数**を変更します。

> [!Note]
> 以前のバージョンの SQL Server 2016 R Services では、 [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]構成ファイルを編集することで、サービスの一部のプロパティを変更することができました。 このファイルは、構成の変更には使用されなくなりました。 SQL Server 構成マネージャーは、サービスアカウントやユーザーの数など、サービス構成を変更するための適切な方法です。

## <a name="debug-settings"></a>デバッグ設定

いくつかのプロパティは、スタートパッドの構成ファイルを使用することによってのみ変更できます。これは、デバッグなどの限られたケースで役立つ場合があります。 構成ファイルは[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]セットアップ中に作成され、既定ではプレーンテキストファイルとして`<instance path>\binn\rlauncher.config`に保存されます。

このファイルを変更するには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を実行しているコンピューターの管理者になる必要があります。 ファイルを編集する場合は、変更を保存する前に、バックアップ コピーを作成することをお勧めします。

次の表に、の[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]詳細設定と、許容される値を示します。

|**設定名**|**型**|**[説明]**|
|----|----|----|
|終了\_時\_の\_ジョブのクリーンアップ|整数型 |これは内部設定のみです。この値は変更しないでください。 </br></br>セッションの完了後に、外部ランタイムセッションごとに作成された一時作業フォルダーをクリーンアップするかどうかを指定します。 この設定はデバッグに便利です。 </br></br>サポートされている値は、 **0** (無効) または**1** (有効) です。 </br></br>既定値は1です。これは、終了時にログファイルが削除されることを意味します。|
|トレース\_レベル|Integer |デバッグのために MSSQLLAUNCHPAD のトレース詳細レベルを構成します。 これは、LOG_DIRECTORY 設定で指定されたパスのトレースファイルに影響します。 </br></br>サポートされている値は次のとおりです。**1** (エラー)、 **2** (パフォーマンス)、 **3** (警告)、 **4** (情報)。 </br></br>既定値は1で、出力エラーのみを意味します。|

すべての設定が、キーと値のペアの形をとり、各設定は個別の行に表示されます。 たとえば、トレースレベルを変更するには、行`Default: TRACE_LEVEL=4`を追加します。

<a name="bkmk_EnforcePolicy"></a>

## <a name="enforcing-password-policy"></a>パスワードポリシーの適用

パスワードを定期的に変更するポリシーが組織で実施されている場合は、ユーザー アカウントに対してスタート パッドが保持している暗号化パスワードを、強制的に再生成しなければならない場合があります。

この設定を有効にし、パスワードの更新を強制するには、SQL Server 構成マネージャーでスタート パッド サービスの **[プロパティ]** ペインを開き、 **[詳細設定]** をクリックして、 **[外部ユーザーのパスワードのリセット]** を **[はい]** に変更します。 この変更を適用すると、すべてのユーザー アカウントのパスワードが直ちに再生成されます。 この変更後に外部スクリプトを実行するには、スタートパッドサービスを再起動する必要があります。このとき、新しく生成されたパスワードが読み取られます。

パスワードを定期的にリセットするには、このフラグを手動で設定するか、スクリプトを使用します。

## <a name="next-steps"></a>次の手順

+ [機能拡張フレームワーク](../concepts/extensibility-framework.md)
+ [セキュリティの概要](../concepts/security.md)
