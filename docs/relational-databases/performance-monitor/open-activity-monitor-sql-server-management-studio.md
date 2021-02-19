---
title: 利用状況モニターを開く (SSMS)
description: SQL Server Management Studio で利用状況モニターを開く方法について説明します。 利用状況モニターでは、表示する情報を取得するため、監視対象のインスタンスにクエリが実行されます。
ms.custom: seo-dt-2019
ms.date: 08/05/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- Activity Monitor [SQL Server], setting the refresh interval
- refresh interval for Activity Monitor
- Activity Monitor [SQL Server], opening
- opening Activity Monitor
ms.assetid: 0a6eeb16-f02b-479d-9a60-543e40ebf46b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 5569286a31b9897b8fcca0eafea6830593d05183
ms.sourcegitcommit: 0e0cd9347c029e0c7c9f3fe6d39985a6d3af967d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96506010"
---
# <a name="open-activity-monitor-in-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) で利用状況モニターを開く
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
   
 利用状況モニターでは、監視対象となるインスタンスでクエリを実行し、[利用状況モニター] 表示ペインに表示する情報を取得します。 更新間隔を 10 秒未満に設定すると、これらのクエリを実行する時間がサーバーのパフォーマンスに影響を与える可能性があります。  
  
  
##  <a name="check-your-permissions"></a><a name="Permissions"></a> 権限を確認してください。  
 実際のアクティビティを表示するには、VIEW SERVER STATE 権限が必要です。 利用状況モニターの [データ ファイル I/O] セクションを表示するには、VIEW SERVER STATE に加えて、CREATE DATABASE、ALTER ANY DATABASE、VIEW ANY DEFINITION のいずれかの権限が必要です。  
  
 プロセスを強制終了するには、sysadmin 固定サーバー ロールまたは processadmin 固定サーバー ロールのメンバーである必要があります。  
  
  
## <a name="open-activity-monitor"></a>利用状況モニターを開きます。  

### <a name="keyboard-shortcut"></a>キーボード ショートカット  
 - **Ctrl + Alt + A** キーを押すと、いつでも利用状況モニターを開くことができます。

 >**ヒント!** SSMS のアイコンにマウスを移動すると、説明と、実行するキーボード ショートカットが表示されます。

### <a name="toolbar"></a>ツール バー

[標準] ツール バーの **[利用状況モニター]** アイコンをクリックします。 中央の [元に戻す]/[やり直し] ボタンの右にあります。
![Activity_Monitor_icon](../../relational-databases/performance-monitor/media/activity-monitor-icon.png)  
  
監視する SQL Server のインスタンスにまだ接続していない場合は、 **[サーバーに接続]** ダイアログ ボックスに入力します。
  
## <a name="launch-activity-monitor-and-object-explorer-on-startup"></a>スタートアップ時に利用状況モニターとオブジェクト エクスプローラーを起動する
  
1.  **[ツール]** メニューの **[オプション]** をクリックします。  
  
2.  **[オプション]** ダイアログ ボックスで **[環境]** を展開し、 **[スタートアップ]** をクリックします。  
  
3.  **[スタートアップ時]** ドロップダウン リストで **[オブジェクト エクスプローラーと利用状況モニターを開く]** をクリックします。  

4.  **[OK]** をクリックします。

![open_object_explorer](../../relational-databases/performance-monitor/media/open-object-explorer.png)
  
  
## <a name="set-the-activity-monitor-refresh-interval"></a>利用状況モニターの更新間隔の設定  
  
1.   利用状況モニターを開きます。  
  
2.   **[概要]** を右クリックして **[更新間隔]** をクリックし、利用状況モニターで新しいインスタンス情報を取得する間隔を選択します。  
  
  
