---
title: 'レッスン 6: プロジェクト配置モデルを持つパラメーターを使用して |Microsoft Docs'
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- integration-services
ms.topic: conceptual
ms.assetid: 9216f18c-1762-4f2d-8c22-bd0ab7107555
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 37a7cc27f7c8773a3cee801d73d8ade5caaba2f2
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48068202"
---
# <a name="lesson-6-using-parameters-with-the-project-deployment-model"></a>レッスン 6: プロジェクト配置モデルを持つパラメーターを使用する
  SQL Server 2012 では、Integration Services サーバーにプロジェクトを配置できる新しい配置モデルが導入されています。 Integration Services サーバーを使用すると、パッケージの管理、パッケージの実行、およびパッケージに合わせたランタイム値の構成を行うことができます。  
  
 このレッスンで作成したパッケージを変更します[レッスン 5: パッケージ配置モデルのパッケージ構成の追加](lesson-5-add-ssis-package-configurations-for-the-package-deployment-model.md)プロジェクト配置モデルを使用します。 構成値をパラメーターに置換して、サンプル データの場所を指定します。 または、チュートリアルに含まれている、レッスン 5 を完了した状態のパッケージをコピーすることもできます。  
  
 Integration Services プロジェクト構成ウィザードを使用して、プロジェクトをプロジェクト配置モデルに変換し、構成値ではなくパラメーターを使用してディレクトリ プロパティを設定します。 このレッスンでは、新規プロジェクト配置モデルに既存の SSIS パッケージを変換するための手順について部分的に説明します。  
  
 パッケージを再度実行すると、Integration Services サービスはパラメーターを使用して変数の値を生成します。さらに、この変数は Directory プロパティを更新します。 結果として、パッケージ構成ファイルで設定されたフォルダーではなく、パラメーター値によって指定された新しいデータ フォルダーのファイルに対してパッケージが繰り返し実行されます。  
  
> [!IMPORTANT]  
>  このチュートリアルには、 **AdventureWorksDW2012** サンプル データベースが必要です。 **AdventureWorksDW2012** をインストールおよび配置する方法の詳細については、「[SQL Server のサンプルとサンプル データベースのインストールに関する注意点](http://technet.microsoft.com/library/ms161556%28v=sql.105%29)」を参照してください。  
  
## <a name="lesson-tasks"></a>このレッスンの作業  
 このレッスンの内容は次のとおりです。  
  
1.  [手順 1: レッスン 5 のパッケージのコピー](lesson-6-1-copying-the-lesson-5-package.md)  
  
2.  [手順 2: プロジェクトをプロジェクト配置モデルに変換する](lesson-6-2-converting-the-project-to-the-project-deployment-model.md)  
  
3.  [手順 3: レッスン 6 のパッケージのテスト](lesson-6-3-testing-the-lesson-6-package.md)  
  
4.  [手順 4: レッスン 6 のパッケージの展開](lesson-6-4-deploying-the-lesson-6-package.md)  
  
## <a name="start-the-lesson"></a>レッスンの開始  
 [手順 1: レッスン 5 のパッケージのコピー](lesson-6-1-copying-the-lesson-5-package.md)  
  
  
