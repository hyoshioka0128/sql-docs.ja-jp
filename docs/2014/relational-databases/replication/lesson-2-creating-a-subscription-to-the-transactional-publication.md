---
title: 'レッスン 2 : トランザクション パブリケーションへのサブスクリプションの作成 | Microsoft Docs'
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- replication
ms.topic: conceptual
helpviewer_keywords:
- replication [SQL Server], tutorials
ms.assetid: 5995b7d2-7c06-46f5-b96c-2bee879bcda2
author: craigg-msft
ms.author: craigg
manager: craigg
ms.openlocfilehash: ace792a76e1bcdbcc7aa6b372d96de1f92c570f5
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48072252"
---
# <a name="lesson-2-creating-a-subscription-to-the-transactional-publication"></a>レッスン 2 : トランザクション パブリケーションへのサブスクリプションの作成
  このレッスンでは、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]を使用してサブスクリプションを作成します。 このレッスンを薦めるには、前のレッスンである「 [レッスン 1: トランザクション レプリケーションを使用したデータのパブリッシュ](lesson-1-publishing-data-using-transactional-replication.md)」を完了している必要があります。  
  
### <a name="to-create-the-subscription"></a>サブスクリプションを作成するには  
  
1.  [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]でパブリッシャーに接続して、サーバー ノードを展開し、 **[レプリケーション]** フォルダーを展開します。  
  
2.  **[ローカル パブリケーション]** フォルダーを展開し、 **[AdvWorksProductTrans]** パブリケーションを右クリックして、 **[新しいサブスクリプション]** をクリックします。  
  
     サブスクリプションの新規作成ウィザードが起動します。  
  
3.  [パブリケーション] ページで **[AdvWorksProductTrans]** を選択し、 **[次へ]** をクリックします。  
  
4.  [ディストリビューション エージェントの場所] ページで、 **[ディストリビューターですべてのエージェントを実行する]** を選択し、 **[次へ]** をクリックします。  
  
5.  [サブスクライバー] ページにサブスクライバー インスタンスの名前が表示されない場合は、 **[サブスクライバーの追加]**、 **[SQL Server サブスクライバーの追加]** の順にクリックし、 **[サーバーの接続]** ダイアログ ボックスにサブスクライバー インスタンス名を入力して、 **[接続]** をクリックします。  
  
6.  サブスクライバー ページで、サブスクライバー サーバーのインスタンス名を選択および選択**\<新しいデータベース >** **サブスクリプション データベース**します。  
  
7.  **[新しいデータベース]** ダイアログ ボックスで、 **[データベース名]** ボックスに「 **ProductReplica** 」と入力し、 **[OK]** をクリックして **[次へ]** をクリックします。  
  
8.  **[ディストリビューション エージェント セキュリティ]** ダイアログ ボックスで、参照ボタン (**[…]**) をクリックし、**[プロセス アカウント]** ボックスに「\<*コンピューター名>***\repl_distribution**」と入力します。次に、このアカウントのパスワードを入力して **[OK]** をクリックし、**[次へ]** をクリックします。  
  
9. 以降のページでは既定値をそのまま採用し、 **[完了]** をクリックしてウィザードを終了します。  
  
### <a name="setting-database-permissions-at-the-subscriber"></a>サブスクライバー側のデータベース権限を設定するには  
  
1.  [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]でサブスクライバーに接続します。次に、 **[データベース]**、 **[ProductReplica]**、 **[セキュリティ]** の順に展開し、 **[ユーザー]** を右クリックして、 **[新しいユーザー]** をクリックします。  
  
2.  **[全般]** ページの **[ユーザーの種類]** ボックスの一覧の **[Windows ユーザー]** をクリックします。  
  
3.  **[ユーザー名]** ボックスを選択し、**[選択するオブジェクト名を入力してください]** ボックスに「<Machine_Name>**\repl_distribution**」と入力し、**[名前の確認]** をクリックして、**[OK]** をクリックします。  
  
4.  **[メンバーシップ]** ページの **[データベース ロールのメンバーシップ]** 領域で、**[db_owner]** を選択し、**[OK]** をクリックしてユーザーを作成します。  
  
### <a name="to-view-the-synchronization-status-of-the-subscription"></a>サブスクリプションの同期状態を表示するには  
  
1.  [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]でパブリッシャーに接続して、サーバー ノードを展開し、 **[レプリケーション]** フォルダーを展開します。  
  
2.  **[ローカル パブリケーション]** フォルダーで、 **AdvWorksProductTrans** パブリケーションを展開し、 **ProductReplica** データベースのサブスクリプションを右クリックして、 **[同期の状態の表示]** をクリックします。  
  
     サブスクリプションの現在の同期状態が表示されます。  
  
3.  **[AdvWorksProductTrans]** の下にサブスクリプションが表示されない場合は、F5 キーを押して一覧を更新します。  
  
## <a name="next-steps"></a>次の手順  
 ここでは、トランザクション パブリケーションへのサブスクリプションを作成しました。 このサブスクリプションのディストリビューション エージェントは常時動作しているので、サブスクリプションの作成時に初期化も行われます。 次は、トレーサー トークンを使って、変更内容がサブスクライバーにレプリケートされているかどうかを確認し、待機時間を決定します。 「 [レッスン 3: サブスクリプションの検証と待機時間の計測](lesson-3-validating-the-subscription-and-measuring-latency.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [Initialize a Subscription with a Snapshot (スナップショットを使用したサブスクリプションの初期化)](initialize-a-subscription-with-a-snapshot.md)   
 [Create a Push Subscription](create-a-push-subscription.md)   
 [パブリケーションのサブスクライブ](subscribe-to-publications.md)  
  
  
