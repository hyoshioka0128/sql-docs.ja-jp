---
title: Visual Studio .NET C#で Visual SMO プロジェクトを作成する |Microsoft Docs
ms.custom: ''
ms.date: 08/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- Visual C# [SMO]
ms.assetid: 1e7abb16-23a0-4a18-91ad-253261e6bf84
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 11fb5e8aec7f61c83ec2b3edecdb3aa027cf2693
ms.sourcegitcommit: f3f83ef95399d1570851cd1360dc2f072736bef6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "70148641"
---
# <a name="how-to-create-a-visual-c-smo-project-in-visual-studio-net"></a>Visual Studio .NET で Visual C# SMO プロジェクトを作成する方法
[!INCLUDE[appliesto-ss-asdb-asdw-xxx-md](../../includes/appliesto-ss-asdb-asdw-xxx-md.md)]

  このセクションでは、簡単な SMO コンソール アプリケーションを構築する方法について説明します。  
  
 この例では、プログラムが SMO の型を参照できるように、名前空間をインポートします。 **エージェント**の名前空間のインポートは任意です。 エージェントを使用[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]するプログラムを作成するときに使用します。 **共通**の名前空間は、のインスタンスへの[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]セキュリティで保護された接続を確立するために必要です。 **SqlClient**名前空間は、SQL 例外エラーを処理するために使用されます。  
  
### <a name="creating-a-visual-c-smo-project-in-visual-studionet"></a>Visual Studio.NET でC#の visual SMO プロジェクトの作成  
  
1. Visual Studio の起動
  
2. **[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。  **[新しいプロジェクト]** ダイアログ ボックスが表示されます。   
  
3. \\ \\**C#** [インストール済み] ウィンドウで、[テンプレート] [ビジュアルウィンドウ] に移動し、[コンソールアプリケーション] を選択します。 [!INCLUDE[vsprvs](../../includes/vsprvs-md.md)]  
  
4. Optional **[名前]** テキストボックスに、新しいアプリケーションの名前を入力します。  

5. **[OK]** をクリックして、コンソールアプリケーションテンプレートを読み込みます。  

6. [SMO のインストール](installing-smo.md)に関する手順に従って、参照するプロジェクトのパッケージをインストールします。
  
7. **[表示]** メニューの **[コード]** をクリックします。
    
8. コードの namespace ステートメントの前に、次の using ステートメントを入力し**て**、SMO 名前空間の型を修飾します。
  
    ```  
    using Microsoft.SqlServer.Management.Smo;  
    using Microsoft.SqlServer.Management.Common;  
    ```  
  
15. SMO では、Microsoft.SqlServer.Management.Smo の下に、Microsoft.SqlServer.Management.Smo.Agent などのさまざまな名前空間があります。 これらの名前空間は、必要に応じて追加します。  
  
16. SMO コードを追加できます。  

[!INCLUDE[freshInclude](../../includes/paragraph-content/fresh-note-steps-feedback.md)]

