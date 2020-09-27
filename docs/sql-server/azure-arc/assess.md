---
title: Azure Arc 対応 SQL Server の SQL 評価を構成する
titleSuffix: ''
description: SQL Server の Azure Arc 対応インスタンスのオンデマンド評価を構成する
author: anosov1960
ms.author: sashan
ms.reviewer: mikeray
ms.date: 09/10/2020
ms.topic: conceptual
ms.prod: sql
ms.openlocfilehash: 59a6ff65e25878aefe08cfd87bf1f9e36da7366b
ms.sourcegitcommit: d56f1eca807c55cf606a6316f3872585f014fec1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943046"
---
# <a name="configure-on-demand-sql-assessment-for-azure-arc-enabled-sql-server-instance"></a>Azure Arc 対応 SQL Server インスタンスのオンデマンド SQL 評価を構成する

次の手順に従って、SQL Server インスタンスの SQL 評価を有効にすることができます。

## <a name="prerequisites"></a>前提条件

* SQL Server インスタンスが Azure Arc に接続されていること。次の手順に従って、[SQL Server インスタンスを Arc 対応の SQL Server にオンボード](connect.md)します。

* MMA の拡張機能がマシンにインストールされ、構成されていること。 次の手順に従って [Microsoft Monitoring Agent (MMA) をインストールします](configure-advanced-data-security.md#install-microsoft-monitoring-agent-mma)。 詳細については、[Log Analytics エージェント](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)に関するページを参照してください。

* SQL Server で [TCP/IP プロトコルが有効になっていること](../../database-engine/configure-windows/enable-or-disable-a-server-network-protocol.md)。

* SQL Server の名前付きインスタンスを操作する場合は、[SQL Server ブラウザー](../../tools/configuration-manager/sql-server-browser-service.md)を実行していること。

* 「[サービス ハブのオンデマンド評価の前提条件](https://docs.microsoft.com/services-hub/health/assessment-prereq-docs#on-demand-assessment-prerequisite-documents)」の SQL Server ドキュメントを確認済みであること。

## <a name="enable-on-demand-sql-assessment"></a>オンデマンド SQL Assessment を有効にする

1. SQL Server - Azure Arc リソースを開き、左側のメニューで __[Environment Health]\(環境の正常性\)__ を選択します。

   ![SQL Assessment の選択](media/assess/sql-assessment-heading-sql-server-arc.png)

1. データ コレクション マシンで作業ディレクトリを指定します。 収集および分析時、データは一時的にそのフォルダーに格納されます。 フォルダーが存在しない場合は、自動的に作成されます。

1. __[構成スクリプトのダウンロード]__ をクリックし、ダウンロードしたスクリプトをターゲット マシンにコピーします。

1. __powershell.exe__ の管理者インスタンスを起動し、次のいずれかを実行します。 
   * ドメイン アカウントを使用している場合は、次のコマンドを実行します。 ユーザー アカウントとパスワードの入力を求められます。 

      ```powershell
      Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass -Force
      & '.\AddSqlAssessment.ps1'
      ```

    * MSA アカウントを使用している場合は、次のコマンドを実行します。

      ```powershell
      Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass -Force
      & '.\AddSqlAssessment.ps1' -ManagedServiceAccountName <MSA account name>
      ```

   > [!NOTE]
   > このスクリプトでは、前のスクリプトを実行してから 1 時間以内に実行し、その後は 7 日ごとに実行する *SQLAssessment* という名前のタスクをスケジュールします。 このタスクは、別の日時に実行するように変更することや、タスク スケジューラ ライブラリ > [Microsoft] > [Operations Management Suite] > [AOI***] > [Assessments] > [SQLAssessment] から即時に実行するように強制することもできます。 このタスクによって、データ収集がトリガーされます。

## <a name="view-the-assessment-results"></a>評価結果を確認する

結果が Log Analytics で準備ができるまで、 _[Environment Health]\(環境の正常性\)_ ブレードのボタン __[View SQL assessment result]\(SQL 評価の結果を表示する\)__ は無効です。 ボタンがアクティブになったら、クリックして結果を表示できます。 ターゲット マシンでデータ ファイルが処理された後、Log Analytics で結果が表示されるまでに最大 2 時間かかる場合があります。

![SQ: 評価の結果](media/assess/sql-assessment-results.png)

作業フォルダー内のファイルを確認することで、収集マシンでのデータ処理の状態を確認できます。 スケジュールされたタスクが完了すると、_new._ プレフィックスが付いたいくつかのファイルが作業ディレクトリに表示されます。

![データ ファイルの準備完了](media/assess/sql-assessment-data-files-ready.png)

Microsoft Monitoring Agent を使用すると、15 分ごとに作業フォルダーをスキャンして _new.*_ ファイルを探し、データを Log Analytics ワークスペースに送信することができます。 ファイルがアップロードされると、プレフィックスは _new._ から _processed._ に変わります。

![処理されたデータ ファイル](media/assess/sql-assessment-data-files-processed.png)

## <a name="next-steps"></a>次のステップ

詳細については、「[サービス ハブのオンデマンド評価の前提条件](https://docs.microsoft.com/services-hub/health/assessment-prereq-docs#on-demand-assessment-prerequisite-documents)」の SQL Server ドキュメントを参照してください。
