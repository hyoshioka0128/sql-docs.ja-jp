---
title: SQLRUserGroup のログインｎ作成
description: 暗黙の認証を使用したループバック接続の場合は、SQLRUserGroup に対して SQL Server でログインを作成し、ワーカーアカウントがサーバーにログインできるようにします。これにより、呼び出し元のユーザーへの id 変換が返されます。
ms.prod: sql
ms.technology: machine-learning
ms.date: 01/25/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: a5194f251b7ea47e0d9485446b8957e96037ded0
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714960"
---
# <a name="create-a-login-for-sqlrusergroup"></a>SQLRUserGroup のログインｎ作成
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

スクリプト内の[ループバック接続](../../advanced-analytics/concepts/security.md#implied-authentication)が*信頼関係接続*を指定し、オブジェクトを実行するために使用される id に Windows ユーザーアカウントが含まれている場合は、 [SQLRUserGroup](../concepts/security.md#sqlrusergroup)の[ログインを SQL Server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login)に作成します。

信頼関係接続は、 `Trusted_Connection=True`接続文字列に含まれています。 SQL Server は、信頼関係接続を指定する要求を受信すると、現在の Windows ユーザーの id にログインがあるかどうかを確認します。 ワーカーアカウント ( **SQLRUserGroup**から MSSQLSERVER01 など) として実行されている外部プロセスの場合、既定では、これらのアカウントにはログインがないため、要求は失敗します。

接続エラーを回避するには、 **SQLServerRUserGroup**のログインを作成します。 Id と外部プロセスの詳細については、「[機能拡張フレームワークのセキュリティの概要](../concepts/security.md)」を参照してください。

> [!Note]
> **SQLRUserGroup**に "ローカルログオンを許可する" アクセス許可があることを確認します。 既定では、この権限はすべての新しいローカルユーザーに与えられますが、グループポリシーを厳密に設定すると、この権限が無効になる場合があります。

## <a name="create-a-login"></a>ログインを作成します

1. [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]のオブジェクト エクスプローラーで、 **[セキュリティ]** を展開し、 **[ログイン]** を右クリックして、 **[新しいログイン]** を選択します。

2. **[ログイン-新規作成]** ダイアログボックスで、 **[検索]** を選択します。 (まだボックスに何も入力しないでください)。
    
     ![[検索] をクリックして machine learning の新しいログインを追加し]ます(media/implied-auth-login1.png "[検索] をクリックして machine learning の新しいログインを追加し")ます

3. **[ユーザーまたはグループの選択]** ボックスで、 **[オブジェクトの種類]** ボタンをクリックします。

     ![オブジェクトの種類を検索して machine learning の新しいログインを追加する](media/implied-auth-login2.png "オブジェクトの種類を検索して machine learning の新しいログインを追加する")

4. **[オブジェクトの種類]** ダイアログボックスで、 **[グループ]** を選択します。 他のすべてのチェックボックスをオフにします。

     ![[オブジェクトの種類] ダイアログボックスの [グループの選択]](media/implied-auth-login3.png "[オブジェクトの種類] ダイアログボックスの [グループの選択]")

4. **[詳細設定]** をクリックして、検索する場所が現在のコンピューターであることを確認し、 **[検索開始]** をクリックします。

     ![[検索開始] をクリックして、グループの一覧を取得します]。(media/implied-auth-login4.png "[検索開始] をクリックして、グループの一覧を取得します")。

5. サーバー上のグループアカウントの一覧をスクロールして、で`SQLRUserGroup`始まるものがあることを確認します。
    
    + _既定のインスタンス_のスタートパッドサービスに関連付けられているグループの名前は、R と Python のどちらをインストールしたかに関係なく、常に**SQLRUserGroup**になります。 既定のインスタンスに対してのみ、このアカウントを選択します。
    + _名前付きインスタンス_を使用している場合は、既定のワーカーグループ名 () `SQLRUserGroup`の名前にインスタンス名が付加されます。 たとえば、インスタンスの名前が "MLTEST" の場合、このインスタンスの既定のユーザーグループ名は**Sqlrusergroupmltest**になります。
 
    ![サーバー上のグループの例](media/implied-auth-login5.png "サーバー上のグループの例")
   
5. **[OK]** をクリックして、[高度な検索] ダイアログボックスを閉じます。

    > [!IMPORTANT]
    > インスタンスに適切なアカウントを選択していることを確認してください。 各インスタンスは、独自のスタートパッドサービスと、そのサービス用に作成されたグループのみを使用できます。 インスタンスは、スタートパッドサービスまたはワーカーアカウントを共有できません。

6. もう一度 **[OK]** をクリックして、 **[ユーザーまたはグループの選択]** ダイアログボックスを閉じます。

7. **[ログイン-新規作成]** ダイアログボックスで、 **[OK]** をクリックします。 既定では、ログインは **Public** ロールに割り当てられ、データベース エンジンに接続するためのアクセス許可を与えられます。

## <a name="next-steps"></a>次の手順

+ [セキュリティの概要](../concepts/security.md)
+ [機能拡張フレームワーク](../concepts/extensibility-framework.md)
