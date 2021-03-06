---
title: '手順 1: レッスン 3 のパッケージのコピー | Microsoft Docs'
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- integration-services
ms.topic: conceptual
ms.assetid: 0d053786-5203-43f3-a613-27a8dd2bc44a
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: f530061bcc4956391a79b78e656233812a877d1e
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48229412"
---
# <a name="step-1-copying-the-lesson-3-package"></a>手順 1: レッスン 3 のパッケージのコピー
  ここでは、レッスン 3 で作成した Lesson 3.dtsx パッケージのコピーを作成します。 レッスン 3 を終了していない場合は、チュートリアルに含まれている、レッスン 3 を完了した状態のパッケージをプロジェクトに追加し、作業用のコピーを作成することもできます。 レッスン 4 の実習では、このパッケージの新しいコピーを使用します。  
  
### <a name="to-create-the-lesson-4-package"></a>レッスン 4 のパッケージを作成するには  
  
1.  [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Data Tools がまだ開いていない場合は、 **[スタート]** ボタンをクリックし、 **[すべてのプログラム]**、 **[Microsoft SQL Server]** の順にポイントして、 **[SQL Server Data Tools]** をクリックします。  
  
2.  **[ファイル]** メニューの **[開く]** をクリックし、 **[プロジェクト/ソリューション]** をクリックします。次に、 **[SSIS Tutorial]** フォルダーをクリックして **[開く]** をクリックした後、 **SSIS Tutorial.sln**をダブルクリックします。  
  
3.  ソリューション エクスプローラーで、 **Lesson 3.dtsx**を右クリックし、 **[コピー]** をクリックします。  
  
4.  ソリューション エクスプローラーで **[SSIS パッケージ]** を右クリックし、 **[貼り付け]** をクリックします。  
  
     コピーしたパッケージの既定の名前は、Lesson 4.dtsx です。  
  
5.  ソリューション エクスプローラーで **Lesson 4.dtsx** をダブルクリックし、パッケージを開きます。  
  
6.  **[制御フロー]** タブの背景上で任意の場所を右クリックし、 **[プロパティ]** をクリックします。  
  
7.  [プロパティ] ウィンドウでは、更新、`Name`プロパティを`Lesson 4`します。  
  
8.  ボックスをクリックして、 **ID**プロパティ、一覧でクリック **\<Generate New ID >**。  
  
### <a name="to-add-the-completed-lesson-3-package"></a>レッスン 3 を完了した状態のパッケージを追加するには  
  
1.  [!INCLUDE[ssBIDevStudioFull](../includes/ssbidevstudiofull-md.md)] を開いて、SSIS Tutorial プロジェクトを開きます。  
  
2.  ソリューション エクスプローラーで **[SSIS パッケージ]** を右クリックし、 **[既存のパッケージを追加]** をクリックします。  
  
3.  **[既存のパッケージのコピーを追加]** ダイアログ ボックスの **[パッケージの場所]** で、**[ファイル システム]** をクリックします。  
  
4.  参照ボタン ( **[...]** ) をクリックし、コンピューター上の Lesson 3.dtsx に移動して、 **[開く]** をクリックします。  
  
     このチュートリアルのレッスン パッケージをすべてダウンロードするには、次の手順を実行します。  
  
    1.  「[Integration Services 製品サンプル](http://go.microsoft.com/fwlink/?LinkId=275027)」に移動します。  
  
    2.  **[ダウンロード]** タブをクリックします。  
  
    3.  SQL2012.Integration_Services.Create_Simple_ETL_Tutorial.Sample.zip ファイルをクリックします。  
  
5.  前の手順の手順 3 ～ 8 の説明に従って、レッスン 3 のパッケージをコピーして貼り付けます。  
  
## <a name="next-task-in-lesson"></a>このレッスンの次の作業  
 [手順 2: 破損ファイルの作成](lesson-4-2-creating-a-corrupted-file.md)  
  
  
