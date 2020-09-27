---
title: Active Directory モードでの展開
titleSuffix: SQL Server Big Data Cluster
description: Active Directory ドメインで SQL Server ビッグ データ クラスターをアップグレードする方法について説明します。
author: mihaelablendea
ms.author: mihaelab
ms.reviewer: mikeray
ms.date: 09/15/2020
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 92c170e16a05d67f21931479f82f5edb1856b12f
ms.sourcegitcommit: ac9feb0b10847b369b77f3c03f8200c86ee4f4e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90687788"
---
# <a name="deploy-big-data-clusters-2019-in-active-directory-mode"></a>Active Directory モードで [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] を展開する

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

このドキュメントでは、SQL Server ビッグ データ クラスター (BDC) を Active Directory 認証モードでデプロイする方法について説明します。 クラスターでは、認証に既存の AD ドメインを使用します。

>[!Note]
>SQL Server 2019 CU5 リリースの前は、ビッグ データ クラスターに制限があるため、Active Directory ドメインに対してデプロイできるクラスターは 1 つだけでした。 この制限は、CU5 リリースで削除されています。新しい機能の詳細については、[概念: Active Directory モードで](active-directory-deployment-background.md) [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] をデプロイする方法に関するページを参照してください。 この記事の例は、デプロイの両方のユース ケースに対応するように調整されています。
>

## <a name="background"></a>バックグラウンド

Active Directory (AD) 認証を有効にするために、BDC では、クラスター内のさまざまなサービスで必要となるユーザー、グループ、コンピューター アカウント、サービス プリンシパル名 (SPN) が自動的に作成されます。 このようなアカウントを一部含め、スコーピングを許可するために、クラスター デプロイの前に組織単位 (OU) を作成することをお勧めします。 すべての BDC 関連の AD オブジェクトは、デプロイ中に作成されます。 

## <a name="pre-requisites"></a>前提条件

### <a name="organizational-unit-ou"></a>組織単位 (OU)
組織単位 (OU) は、ユーザー、グループ、およびその他の組織単位を配置する、Active Directory 内の区分です。 全体像としては、組織単位を使用して組織の機能やビジネスの構造を反映することができます。 この記事では、例として `bdc` という名前の OU を作成します。 

>[!NOTE]
>組織単位 (OU) は管理上の境界を表し、お客様がデータ管理者の権限の範囲を制御できるようになります。 


[OU の設計原則](/windows-server/identity/ad-ds/plan/reviewing-ou-design-concepts)に従って、組織内で OU を使用する際の最適な構造を決定することができます。 

### <a name="ad-account-for-bdc-domain-service-account"></a>BDC ドメイン サービス アカウントの AD アカウント

BDC には、Active Directory で必要なすべてのオブジェクトを自動的に作成できるようにするために、指定した組織単位 (OU) 内にユーザー、グループ、およびコンピューター アカウントを作成するための特定のアクセス許可を持つ AD アカウントが必要です。 この記事では、この AD アカウントのアクセス許可を構成する方法について説明します。 この記事では、例として `bdcDSA` という AD アカウントを使用します。

### <a name="auto-generated-active-directory-objects"></a>自動的に生成される Active Directory のオブジェクト
BDC をデプロイすると、アカウントとグループ名が自動的に生成されます。 各アカウントは BDC 内の 1 つのサービスを表し、BDC クラスターが使用されている全有効期間にわたり BDC によって管理されます。 これらのアカウントにより、各サービスに必要なサービス プリンシパル名 (SPN) が所有されます。  AD の自動生成されるアカウント、グループ、およびそれらによって管理されるサービスの完全な一覧については、「[自動的に生成される Active Directory のオブジェクト](active-directory-objects.md)」をご覧ください。



>[!IMPORTANT]
>ドメイン コントローラーで設定されているパスワードの有効期限ポリシーによっては、これらのアカウントのパスワードの有効期限が切れることがあります。 既定の有効期限ポリシーは 42 日です。 BDC 内のすべてのアカウントの資格情報をローテーションするメカニズムはないため、有効期限が切れるとクラスターは動作しなくなります。 この問題を回避するには、ドメイン コントローラーで、BDC サービス アカウントの有効期限ポリシーを "パスワードを無期限にする" に更新します。 この操作は、有効期限の前または後に行うことができます。 後者の場合、Active Directory によって、期限切れのパスワードが再アクティブ化されます。
>
>次の図は、[Active Directory ユーザーとコンピューター] でこのプロパティを設定する場所を示しています。
>
>:::image type="content" source="media/deploy-active-directory/image25.png" alt-text="パスワードの有効期限のポリシーの設定":::


次の手順では、Active Directory ドメイン コントローラーを既に用意していることを前提としています。 ドメイン コントローラーがない場合、[こちら](https://social.technet.microsoft.com/wiki/contents/articles/37528.create-and-configure-active-directory-domain-controller-in-azure-windows-server.aspx)のガイドに含まれる手順が参考になります。


## <a name="create-ad-objects"></a>AD オブジェクトの作成

AD 統合で BDC を展開する前に次の操作を行います。

1. すべての BDC 関連の AD オブジェクトを格納する組織単位 (OU) を作成します。 または、デプロイ時に、既存の OU を選択することもできます。
1. BDC 用の AD アカウントを作成するか、既存のアカウントを使用し、この BDC の AD アカウントに指定した組織単位 (OU) の適切なアクセス許可を付与します。

### <a name="create-a-user-in-ad-for-bdc-domain-service-account"></a>BDC ドメイン サービス アカウントの AD でユーザーを作成する

ビッグ データ クラスターには、特定のアクセス許可を持つアカウントが必要です。 続行する前に、既存の AD アカウントを持っていることを確認するか、新しいアカウントを作成します。このアカウントは、ビッグ データ クラスターで必要なオブジェクトを設定するために使用できます。

AD で新しいユーザーを作成するには、ドメインまたは OU を右クリックし、 **[新規]** 、 **[ユーザー]** の順に選択します。

![image12](./media/deploy-active-directory/image12.png)

このユーザーは、この記事で *BDC ドメイン サービス アカウント*と呼びます。

### <a name="create-an-ou"></a>OU を作成する

ドメイン コントローラーで、 **[Active Directory ユーザーとコンピューター]** を開きます。 左側のパネルで、OU を作成するディレクトリを右クリックし、 **[新規]** \> **[組織単位]** の順に選択します。次に、ウィザードの指示に従って OU を作成します。 または、PowerShell で OU を作成できます。

```powershell
New-ADOrganizationalUnit -Name "<name>" -Path "<Distinguished name of the directory you wish to create the OU in>"
```

この記事の例では、OU 名に `bdc` を使用しています。

![image13](./media/deploy-active-directory/image13.png)

![image14](./media/deploy-active-directory/image14.png)

### <a name="set-permissions-for-an-ad-account"></a>AD アカウントのアクセス許可を設定する 

新しい AD ユーザーを作成したか、既存の AD ユーザーを使用しているかに関係なく、ユーザーには特定のアクセス許可を与える必要があります。 このアカウントは、クラスターを AD に参加させるとき、BDC コントローラーによって使用されるユーザー アカウントです。

BDC ドメイン サービス アカウント (DSA) は、OU でユーザー、グループ、コンピューター アカウントを作成できる必要があります。 次の手順では、BDC ドメイン サービス アカウントに `bdcDSA` という名前を付けています。 このアカウントには任意の名前を選択できます。

1. ドメイン コントローラーで、 **[Active Directory ユーザーとコンピューター]** を開きます。

1. 左側のパネルでドメインに移動し、`bdc` で使用される OU に移動します。

1. OU を右クリックし、 **[プロパティ]** を選択します。

1. [セキュリティ] タブに移動します (OU を右クリックし、 **[表示]** を選択し、 **[高度な機能]** が選択されていることを確認してください)。

    ![image15](./media/deploy-active-directory/image15.png)

1. **[追加]** をクリックし、**bdcDSA** ユーザーを追加します。

    ![image16](./media/deploy-active-directory/image16.png)

    ![image17](./media/deploy-active-directory/image17.png)

1. **bdcDSA** ユーザーを選択し、アクセス許可をすべて消去し、 **[詳細]** をクリックします。

1. **[追加]** をクリックします。

    ![image18](./media/deploy-active-directory/image18.png)

    - **[プリンシパルの選択]** をクリックし、**bdcDSA** を挿入し、[OK] をクリックします。

    - **[種類]** を **[許可]** に設定します。

    - **[適用先]** を **[このオブジェクトとすべての子オブジェクト]** に設定します。

        ![image19](./media/deploy-active-directory/image19.png)

    - 一番下までスクロールし、 **[すべて消去]** をクリックします。

    - スクロールで一番上まで戻り、次を選択します。
       - **すべてのプロパティの読み取り**
       - **すべてのプロパティの書き込み**
       - **コンピューター オブジェクトの作成**
       - **コンピューター オブジェクトの削除**
       - **グループ オブジェクトの作成**
       - **グループ オブジェクトの削除**
       - **ユーザー オブジェクトの作成**
       - **ユーザー オブジェクトの削除**

    - **[OK]**

- **[追加]** をクリックします。

    - **[プリンシパルの選択]** をクリックし、**bdcDSA** を挿入し、[OK] をクリックします。

    - **[種類]** を **[許可]** に設定します。

    - **[適用先]** を **[Descendant Computer objects]\(子コンピューター オブジェクト\)** に設定します。

    - 一番下までスクロールし、 **[すべて消去]** をクリックします。

    - スクロールで一番上まで戻り、 **[パスワードのリセット]** を選択します。

    - **[OK]**

- **[追加]** をクリックします。

    - **[プリンシパルの選択]** をクリックし、**bdcDSA** を挿入し、[OK] をクリックします。

    - **[種類]** を **[許可]** に設定します。

    - **[適用先]** を **[Descendant User objects]\(子ユーザー オブジェクト\)** に設定します。

    - 一番下までスクロールし、 **[すべて消去]** をクリックします。

    - スクロールで一番上まで戻り、 **[パスワードのリセット]** を選択します。

    - **[OK]**

- **[OK]** をさらに 2 回クリックしてダイアログ ボックスを開きます。

## <a name="prepare-deployment"></a>展開の準備

AD 統合で BDC を展開するためには、AD で BDC 関連のオブジェクトを作成するために必要な情報をいくつか追加します。

`kubeadm-prod` プロファイル (または CU5 リリース以降の `openshift-prod`) を利用することで、AD 統合に必要なセキュリティ関連の情報とエンドポイント関連の情報のプレースホルダーが自動的に作成されます。

さらに、AD で必要なオブジェクトを作成するために [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)] で使用される資格情報を提供する必要があります。 これらの資格情報は環境変数として提供されます。

## <a name="set-security-environment-variables"></a>セキュリティ環境変数の設定

次の環境変数からは、BDC ドメイン サービス アカウントの資格情報が与えられます。この情報が AD 統合の設定に使用されます。 このアカウントはまた、この先、BDC 関連の AD オブジェクトを維持する目的で BDC によって使用されます。

```bash
export DOMAIN_SERVICE_ACCOUNT_USERNAME=<AD principal account name>
export DOMAIN_SERVICE_ACCOUNT_PASSWORD=<AD principal password>
```

## <a name="provide-security-and-endpoint-parameters"></a>セキュリティとエンドポイントのパラメーターを指定する

資格情報の環境変数に加えて、AD 統合が機能するためのセキュリティとエンドポイントの情報も提供する必要があります。 必要なパラメーターは、自動的に `kubeadm-prod`/`openshift-prod` [デプロイ プロファイル](deployment-guidance.md#configfile)に含まれます。

AD 統合には次のパラメーターが必要です: この記事の後半に出てくる `config replace` コマンドを使用し、`control.json` ファイルと `bdc.json` ファイルにこれらのパラメーターを追加します。 下の例ではすべて、サンプル ドメイン `contoso.local` が使用されています。

- `security.activeDirectory.ouDistinguishedName`: クラスターにより作成されたあらゆる AD アカウントが追加される組織単位 (OU) の識別名。 ドメインの名前が `contoso.local` の場合、OU の識別名は `OU=BDC,DC=contoso,DC=local` です。

- `security.activeDirectory.dnsIpAddresses`: ドメインの DNS サーバーの IP アドレスの一覧が含まれます。 

- `security.activeDirectory.domainControllerFullyQualifiedDns`:ドメイン コントローラーの FQDN の一覧。 FQDN には、ドメイン コントローラーのコンピューター名またはホスト名が含まれています。 複数のドメイン コントローラーがある場合、ここで一覧を指定できます。 例: `HOSTNAME.CONTOSO.LOCAL`.

  > [!IMPORTANT]
  > 複数のドメイン コントローラーがドメインにサービスを提供している場合は、セキュリティ構成の `domainControllerFullyQualifiedDns` 一覧の最初のエントリとして、プライマリ ドメインコントローラー (PDC) を使用します。PDC 名を取得するには、コマンド プロンプトで「`netdom query fsmo`」と入力し、**Enter** キーを押します。

- `security.activeDirectory.realm` **省略可能なパラメーター**: ほとんどの場合、領域はドメイン名と同じです。 同じでない場合、このパラメーターを使用し、領域の名前を定義します (例: `CONTOSO.LOCAL`)。 このパラメーターに指定する値は、完全修飾されている必要があります。

  > [!IMPORTANT]
  > 現時点では、BDC では、Active Directory ドメイン名が Active Directory ドメインの **NETBIOS** 名と異なる構成はサポートされていません。

- `security.activeDirectory.domainDnsName`:クラスターに使用される DNS ドメインの名前 (例: `contoso.local`)。

- `security.activeDirectory.clusterAdmins`:このパラメーターは、1 つの AD グループを受け取ります。 AD グループのスコープは、ユニバーサルまたはグローバルである必要があります。 このグループのメンバーには、クラスターの管理者アクセス許可を付与する *bdcAdmin* クラスター ロールが割り当てられます。 これは、[SQL Server で `sysadmin` アクセス許可](../relational-databases/security/authentication-access/server-level-roles.md#fixed-server-level-roles)、[HDFS で `superuser` アクセス許可](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#The_Super-User)、コントローラー エンドポイントに接続したときに管理者アクセス許可が与えられることを意味します。

  >[!IMPORTANT]
  >展開を開始する前に、このグループを AD 内に作成します。 この AD グループのスコープがドメイン ローカルの場合、展開は失敗します。

- `security.activeDirectory.clusterUsers`:ビッグ データ クラスター内で通常のユーザー (管理者権限なし) となる AD グループの一覧。 この一覧には、ユニバーサル グループまたはグローバル グループのいずれかのスコープを持つ AD グループを含めることができます。 ドメイン ローカル グループを含めることはできません。

この一覧の AD グループは、*bdcUser* ビッグデータ クラスター ロールにマップされており、SQL Server ([SQL Server アクセス許可](../relational-databases/security/permissions-hierarchy-database-engine.md)を参照) または HDFS ([HDFS アクセス許可ガイド](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#:~:text=Permission%20Checks%20%20%20%20Operation%20%20,%20%20N%2FA%20%2029%20more%20rows%20)を参照) へのアクセスが許可される必要があります。 コントローラー エンドポイントに接続されている場合、これらのユーザーは *azdata bdc endpoint list* コマンドを使用して、クラスターで使用可能なエンドポイントのみを一覧表示できます。

この設定の AD グループを更新する方法の詳細については、「[Active Directory モードでビッグ データ クラスター アクセスを管理する](manage-user-access.md)」を参照してください。

  >[!TIP]
  >HDFS に接続するために必要な Knox ゲートウェイ エンドポイントを取得するために、Azure Data Studio では *sys.dm_cluster_endpoints* DMV を使用しています。そのため、Azure Data Studio で SQL Server マスターに接続したときの HDFS ブラウジング エクスペリエンスを有効にするには、bdcUser ロールを持つユーザーに VIEW SERVER STATE 権限を付与する必要があります。

  >[!IMPORTANT]
  >展開を開始する前に、これらのグループを AD 内に作成します。 これらの AD グループのいずれかのスコープがドメイン ローカルの場合、展開は失敗します。

  >[!IMPORTANT]
  >ドメイン ユーザーが多数のグループ メンバーシップを所有している場合は、カスタム *bdc.json* デプロイ構成ファイルを使用して、ゲートウェイ設定 *httpserver.requestHeaderBuffer* (既定値は *8192*) と HDFS 設定 *hadoop.security.group.mapping.ldap.search.group.hierarchy.levels* (既定値は *10*) の値を調整する必要があります。 これは、ゲートウェイへの接続タイムアウトや 431 (*Request Header Fields Too Large*) 状態コードの HTTP 応答を回避するためのベスト プラクティスです。 以下は、これらの設定の値を定義する方法と、グループ メンバーシップの数が多い場合に推奨される値を示す構成ファイルのセクションです。 

```json
{
    ...
    "spec": {
        "resources": {
            ...
            "gateway": {
                "spec": {
                    "replicas": 1,
                    "endpoints": [{...}],
                    "settings": {
                        "gateway-site.gateway.httpserver.requestHeaderBuffer": "65536"
                    }
                }
            },
            ...
        },
        "services": {
            ...
            "hdfs": {
                "resources": [...],
                "settings": {
                  "core-site.hadoop.security.group.mapping.ldap.search.group.hierarchy.levels": "4"
                }
            },
            ...
        }
    }
}
```

  >[!IMPORTANT]
  >デプロイを開始する前に、AD で下記の設定に提供されるグループを作成します。 これらの AD グループのいずれかのスコープがドメイン ローカルの場合、展開は失敗します。

- `security.activeDirectory.appOwners` **省略可能なパラメーター**: あらゆるアプリケーションを作成、削除、実行する権限が与えられる AD グループの一覧。 この一覧には、ユニバーサル グループまたはグローバル グループのいずれかのスコープを持つ AD グループを含めることができます。 ドメイン ローカル グループを含めることはできません。

- `security.activeDirectory.appReaders` **省略可能なパラメーター**: あらゆるアプリケーションを実行する権限を持つ AD グループの一覧。 この一覧には、ユニバーサル グループまたはグローバル グループのいずれかのスコープを持つ AD グループを含めることができます。 ドメイン ローカル グループを含めることはできません。

次の表に、アプリケーション管理用の承認モデルを示します。

|   承認済みロール   |   azdata コマンド   |
|----------------------|--------------------|
|   appOwner           | azdata app create  |
|   appOwner           | azdata app update  |
|   appOwner、appReader| azdata app list    |
|   appOwner、appReader| azdata app describe|
|   appOwner           | azdata app delete  |
|   appOwner、appReader| azdata app run     |

- `security.activeDirectory.subdomain`:**省略可能なパラメーター** このパラメーターは、同じドメインに対する複数のビッグ データ クラスターのデプロイをサポートするために SQL Server 2019 CU5 リリースで導入されました。 この設定を使用すると、デプロイされたビッグ データ クラスターごとに異なる DNS 名を指定できます。 このパラメーターの値が `control.json` ファイルの Active Directory セクションに指定されていない場合、既定で、ビッグ データ クラスター名 (Kubernetes 名前空間名と同じ) がサブドメイン設定の値を計算するために使用されます。 

  >[!NOTE]
  >サブドメイン設定を介して渡される値は、新しい AD ドメインではなく、BDC クラスターによって内部で使用される DNS ドメインだけです。

  >[!IMPORTANT]
  >これらの新機能を利用し、同じドメイン内に複数のビッグ データ クラスターをデプロイするには、SQL Server 2019 CU5 リリース以降の **azdata CLI** の最新バージョンをインストールまたはアップグレードする必要があります。

  同じ Active Directory ドメインに複数のビッグ データクラスターをデプロイすることに関する詳細については、[概念: Active Directory モードで [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] をデプロイする](active-directory-deployment-background.md)方法に関するページを参照してください。

- `security.activeDirectory.accountPrefix`:**省略可能なパラメーター** このパラメーターは、同じドメインに対する複数のビッグ データ クラスターのデプロイをサポートするために SQL Server 2019 CU5 リリースで導入されました。 この設定により、さまざまなビッグ データ クラスター サービスに対してアカウント名の一意性が保証されます。それは 2 つのクラスター間で異なる必要があります。 アカウント プレフィックス名のカスタマイズは省略可能で、既定では、サブドメイン名がアカウント プレフィックスとして使用されます。 サブドメイン名が 12 文字より長い場合、サブドメイン名の最初の 12 文字がアカウント プレフィックスとして使用されます。  

  >[!NOTE]
  >Active Directory では、アカウント名を 20 文字までに制限する必要があります。 ポッドと StatefulSet を区別するために、BDC クラスターでは 8 文字を使用する必要があります。 これにより、アカウント プレフィックスの制限として 12 文字が残されます

[AD グループのスコープを確認](/powershell/module/activedirectory/get-adgroup?view=winserver2012-ps&viewFallbackFrom=winserver2012r2-ps)し、DomainLocal であるかどうかを確認します。

展開構成ファイルをまだ初期化していない場合、このコマンドを実行して構成のコピーを取得できます。 次の例では、`kubeadm-prod` プロファイルを使用します。これは `openshift-prod` にも当てはまります。

```bash
azdata bdc config init --source kubeadm-prod  --target custom-prod-kubeadm
```

`control.json` ファイルで上記のパラメーターを設定するには、次の `azdata` コマンドを使用します。 これらのコマンドによって展開前に構成が置換され、独自の値が提供されます。

> [!IMPORTANT]
> SQL Server 2019 CU2 リリースでは、展開プロファイルのセキュリティ構成セクションの構造が多少変更され、Active Directory 関連の設定はすべて、*control.json* ファイル内の *security* の下にある json ツリー内の新しい *activeDirectory* 内にあります。

>[!NOTE]
> このセクションで説明されているように、サブドメインに異なる値を指定するだけでなく、同じ Kubernetes クラスターに複数の BDC をデプロイする場合は、BDC エンドポイントに異なるポート番号を使用する必要もあります。 これらのポート番号は、デプロイ時に [デプロイ構成](deployment-custom-configuration.md)プロファイルを使用して構成できます。

次の例は、SQL Server 2019 CU2 の使用に基づいています。 この例は、展開構成内の AD 関連のパラメーター値を置き換える方法を示しています。下のドメインの詳細はサンプルの値です。

```bash
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.ouDistinguishedName=OU\=bdc\,DC\=contoso\,DC\=local"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.dnsIpAddresses=[\"10.100.10.100\"]"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.domainControllerFullyQualifiedDns=[\"HOSTNAME.CONTOSO.LOCAL\"]"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.domainDnsName=contoso.local"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.clusterAdmins=[\"bdcadminsgroup\"]"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.clusterUsers=[\"bdcusersgroup\"]"
#Example for providing multiple clusterUser groups: [\"bdcusergroup1\",\"bdcusergroup2\"]
```

必要に応じて SQL Server 2019 CU5 リリースを起動するだけで、`subdomain` と `accountPrefix` の設定の既定値をオーバーライドできます。

```bash
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.subdomain=[\"bdctest\"]"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.accountPrefix=[\"bdctest\"]"
```

同様に、SQL Server 2019 CU2 以前のリリースでは、次のことを実行できます。

```bash
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.ouDistinguishedName=OU\=bdc\,DC\=contoso\,DC\=local"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.dnsIpAddresses=[\"10.100.10.100\"]"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.domainControllerFullyQualifiedDns=[\"HOSTNAME.CONTOSO.LOCAL\"]"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.domainDnsName=contoso.local"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.clusterAdmins=[\"bdcadminsgroup\"]"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.clusterUsers=[\"bdcusersgroup\"]"
#Example for providing multiple clusterUser groups: [\"bdcusergroup1\",\"bdcusergroup2\"]
```

上記の情報に加え、さまざまなクラスター エンドポイントの DNS 名を指定する必要もあります。 指定した DNS 名を使用する DNS エントリは、展開時に DNS サーバーで自動的に作成されます。 これらの名前は、さまざまなクラスター エンドポイントに接続するときに使用します。 たとえば、SQL マスター インスタンスの DNS 名が `mastersql` で、サブドメインで *control.json* にクラスター名の既定値を使用することを考えている場合は、`mastersql.contoso.local,31433` または `mastersql.mssql-cluster.contoso.local,31433` (デプロイ構成ファイルでエンドポイント DNS 名に対して指定した値によって異なります) を使用して、ツールからマスター インスタンスに接続します。 

```bash
# DNS names for BDC services
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.spec.endpoints[0].dnsName=<controller DNS name>.contoso.local"
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.spec.endpoints[1].dnsName=<monitoring services DNS name>.<Domain name. e.g. contoso.local>"
azdata bdc config replace -c custom-prod-kubeadm/bdc.json -j "$.spec.resources.master.spec.endpoints[0].dnsName=<SQL Master Primary DNS name>.<Domain name. e.g. contoso.local>"
azdata bdc config replace -c custom-prod-kubeadm/bdc.json -j "$.spec.resources.master.spec.endpoints[1].dnsName=<SQL Master Secondary DNS name>.<Domain name. e.g. contoso.local>"
azdata bdc config replace -c custom-prod-kubeadm/bdc.json -j "$.spec.resources.gateway.spec.endpoints[0].dnsName=<Gateway (Knox) DNS name>.<Domain name. e.g. contoso.local>"
azdata bdc config replace -c custom-prod-kubeadm/bdc.json -j "$.spec.resources.appproxy.spec.endpoints[0].dnsName=<app proxy DNS name>.<Domain name. e.g. contoso.local>"
```

> [!IMPORTANT]
> 任意のエンドポイント DNS 名を使用できます。ただし、これらが完全に修飾されており、同じドメインにデプロイされた任意の 2 つのビッグ データ クラスター間で競合しない場合に限ります。 必要に応じて、`subdomain` パラメーター値を使用して、クラスター間で確実に DNS 名が異なるようにすることができます。 次に例を示します。

```bash
# DNS names for BDC services
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.spec.endpoints[0].dnsName=<controller DNS name>.<subdomain e.g. mssql-cluster>.contoso.local"
```

AD 統合を利用し、単一ノード Kubernetes クラスター (kubeadm) で SQL Server ビッグ データ クラスターを展開するサンプル スクリプトは[こちら](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/sql-big-data-cluster/deployment/kubeadm/ubuntu-single-node-vm-ad)にあります。

> [!Note]
> 新しく導入された `subdomain` パラメーターに対応できないシナリオもあります。 たとえば、CU5 より前のリリースをデプロイする必要があり、**azdata CLI** が既にアップグレード済みである場合です。 これは非常にまれですが、CU5 より前の動作に戻す必要がある場合は、`control.json` の Active Directory セクションで `useSubdomain` パラメーターを `false` に設定できます。  これを実行するコマンドを次に示します。

```bash
azdata bdc config replace -c custom-prod-kubeadm/control.json -j "$.security.activeDirectory.useSubdomain=false"
```

これで、Active Directory 統合を使用し、BDC の展開に必要なパラメーターをすべて設定できたはずです。

`azdata` コマンドと kubeadm-prod デプロイ プロファイルを使用して、Active Directory と統合された BDC クラスターをデプロイできるようになりました。 [!INCLUDE[big-data-clusters](../includes/ssbigdataclusters-nover.md)] をデプロイする方法の完全なドキュメントについては、「[Kubernetes 上に SQL Server ビッグ データ クラスターを展開する方法](deployment-guidance.md)」を参照してください。

## <a name="verify-reverse-dns-entry-for-domain-controller"></a>ドメイン コントローラーの逆引き DNS エントリを確認する

ドメイン コントローラー自体の逆引き DNS エントリ (PTR レコード) が DNS サーバーに登録されていることを確認します。 これは、ドメイン コントローラーでドメイン名の `nslookup` を実行し、ドメイン名がドメイン コントローラー IP アドレスで解決されるかを調べることで確認できます。

## <a name="connect-to-cluster-endpoints-in-ad-mode"></a>AD モードでクラスター エンドポイントに接続する

AD Auth を使用して SQL Server マスター インスタンスにログインします。

SQL Server インスタンスへの AD 接続を確認するには、`sqlcmd` を使用して SQL マスター インスタンスに接続します。 展開時、指定されたグループに対してログインが自動的に作成されます (`clusterUsers` と `clusterAdmins`)。

Linux を使用している場合、最初に AD ユーザーとして `kinit` を実行し、次に `sqlcmd` を実行します。 Windows を使用している場合、**ドメインに参加しているクライアント コンピューター**から目的のユーザーとしてログインします。

### <a name="connect-to-master-instance-from-linuxmac"></a>Linux/Mac からマスター インスタンスに接続する

```bash
kinit <username>@<domain name>
sqlcmd -S <DNS name for master instance>,31433 -E
```

### <a name="connect-to-master-instance-from-windows"></a>Windows からマスター インスタンスに接続する

```cmd
sqlcmd -S <DNS name for master instance>,31433 -E
```

### <a name="log-in-to-sql-server-master-instance-using-azure-data-studio-or-ssms"></a>Azure Data Studio または SSMS を使用し、SQL Server マスター インスタンスにログインする

ドメインに参加しているクライアントから、SSMS または Azure Data Studio を開き、マスター インスタンスに接続できます。 これは、AD 認証を使用して SQL Server インスタンスに接続する場合と同じです。

SSMS から:

![image23](./media/deploy-active-directory/image23.png)

Azure Data Studio から:

![image24](./media/deploy-active-directory/image24.png)}

### <a name="log-in-to-controller-with-ad-authentication"></a>AD 認証を使用してコントローラーにログインする

#### <a name="connect-to-controller-with-ad-authentication-from-linuxmac"></a>Linux/Mac から AD 認証を使用してコントローラーに接続する

`azdata` と AD 認証を使用し、コントローラー エンドポイントに接続するために、2 つのオプションがあります。 *--endpoint/-e* パラメーターを使用できます。

```bash
kinit <username>@<domain name>
azdata login -e https://<controller DNS name>:30080 --auth ad
```

または、 *--namespace/-n* パラメーター (ビッグ データ クラスター名) を使用して接続することもできます。

```bash
kinit <username>@<domain name>
azdata login -n <clusterName> --auth ad
```

#### <a name="connect-to-controller-with-ad-authentication-from-windows"></a>Windows から AD 認証を使用してコントローラーに接続する

```cmd
azdata login -e https://<controller DNS name>:30080 --auth ad
```

### <a name="use-ad-authentication-to-knox-gateway-webhdfs"></a>Knox ゲートウェイ (webHDFS) に AD 認証を使用する

Knox ゲートウェイ エンドポイント経由で curl を使用して HDFS コマンドを発行することもできます。 これには Knox の AD 認証が必要です。 次の curl コマンドでは、Knox ゲートウェイを介して webHDFS REST 呼び出しが発行され、`products` という名前のディレクトリが作成されます。

```bash
curl -k -v --negotiate -u : https://<Gateway DNS name>:30443/gateway/default/webhdfs/v1/products?op=MKDIRS -X PUT
```

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

**SQL Server 2019 CU5 で注意すべき制限事項**

- 現在のところ、ログ検索ダッシュボードとメトリクス ダッシュボードは AD 認証に対応していません。 展開時に設定される基本ユーザー名とパスワードは、これらのダッシュボードに対する認証に使用することができます。 他のすべてのクラスター エンドポイントは、AD 認証に対応しています。

- セキュア AD モードは現在のところ、`kubeadm` および `openshift` デプロイ環境でのみ動作し、AKS または ARO では動作しません。 `kubeadm-prod` および `openshift-prod` デプロイ プロファイルには既定で、セキュリティ セクションが含まれています。

- SQL Server 2019 CU5 リリースより前は、ドメイン (Active Directory) につき BDC が 1 つしか許可されません。 CU5 リリース以降では、ドメインごとに複数の BDC を有効にすることができます。

- セキュリティ構成で指定されているどの AD グループも、DomainLocal にスコープ指定できません。 AD グループのスコープは、[この手順](/powershell/module/activedirectory/get-adgroup?view=winserver2012-ps&viewFallbackFrom=winserver2012r2-ps)に従って確認できます。

- BDC へのログインに使用できる AD アカウントは、BDC 用に構成されたのと同じドメインから許可されます。 その他の信頼される側のドメインからのログインを有効にすることはサポートされていません。

## <a name="next-steps"></a>次のステップ

[SQL Server ビッグ データ クラスターの Active Directory 統合のトラブルシューティング](troubleshoot-active-directory.md)

[概念: Active Directory モードで [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] をデプロイする](active-directory-deployment-background.md)
