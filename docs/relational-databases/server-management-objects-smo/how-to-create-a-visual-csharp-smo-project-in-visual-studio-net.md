---
description: Visual Studio .NET で Visual C# SMO プロジェクトを作成する方法
title: Visual Studio .NET での Visual C# SMO プロジェクトの作成
ms.custom: seo-dt-2019
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
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 943cfd787cccff42973862b12f0f52feae24410c
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97467193"
---
# <a name="how-to-create-a-visual-c-smo-project-in-visual-studio-net"></a>Visual Studio .NET で Visual C# SMO プロジェクトを作成する方法
[!INCLUDE [SQL Server ASDB, ASDBMI, ASDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa.md)]

  このセクションでは、簡単な SMO コンソール アプリケーションを構築する方法について説明します。  
  
 この例では、プログラムが SMO の型を参照できるように、名前空間をインポートします。 **エージェント** の名前空間のインポートは任意です。 エージェントを使用するプログラムを作成するときに使用し [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ます。 **共通** の名前空間は、のインスタンスへのセキュリティで保護された接続を確立するために必要です [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 **SqlClient** 名前空間は、SQL 例外エラーを処理するために使用されます。  
  
### <a name="creating-a-visual-c-smo-project-in-visual-studionet"></a>Visual C# SMO プロジェクトを Visual Studio.NET で作成する  
  
1. Visual Studio を起動する
  
2. [ **ファイル** ] メニューの [ **新規作成** ] をポイントし、[ **プロジェクト**] をクリックします。  **[新しいプロジェクト]** ダイアログ ボックスが表示されます。   
  
3. [ [!INCLUDE[vsprvs](../../includes/vsprvs-md.md)] **インストール済み**] ウィンドウで、[**テンプレート**] [Visual C#] [Windows] の順に移動し、 \\  \\  [**コンソールアプリケーション**] を選択します。  
  
4. Optional[ **名前** ] テキストボックスに、新しいアプリケーションの名前を入力します。  

5. [ **OK** ] をクリックして、コンソールアプリケーションテンプレートを読み込みます。  

6. [SMO のインストール](installing-smo.md)に関する手順に従って、参照するプロジェクトのパッケージをインストールします。
  
7. **[表示]** メニューの **[コード]** をクリックします。
    
8. コードの namespace ステートメントの前に、次の using ステートメントを入力し **て** 、SMO 名前空間の型を修飾します。
  
    ```  
    using Microsoft.SqlServer.Management.Smo;  
    using Microsoft.SqlServer.Management.Common;  
    ```  
  
15. SMO では、Microsoft.SqlServer.Management.Smo の下に、Microsoft.SqlServer.Management.Smo.Agent などのさまざまな名前空間があります。 これらの名前空間は、必要に応じて追加します。  
  
16. SMO コードを追加できます。  

