---
description: SQL Server Python と R のチュートリアルの航空会社フライト到着デモ データ
title: チュートリアルの航空会社のフライト デモ データ
Description: R と Python の航空会社データセットを含むデータベースを作成します。 このデータセットは、SQL Server Machine Learning Services 用の R および Python チュートリアルで使用されます。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 10/22/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15'
ms.openlocfilehash: 3b19e905d68d53764a496ab5ee7745e3529484cf
ms.sourcegitcommit: 3bd188e652102f3703812af53ba877cce94b44a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97489902"
---
#  <a name="airline-flight-arrival-demo-data-for-sql-server-python-and-r-tutorials"></a>SQL Server Python と R のチュートリアルの航空会社フライト到着デモ データ
[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)]

この演習では、R または Python の組み込みの航空会社デモ データ セットからインポートしたデータを格納する SQL Server データベースを作成します。 R と Python のディストリビューションには同等のデータが用意されており、Management Studio を使用してそれを SQL Server データベースにインポートできます。

この演習を完了するには、[SQL Server Management Studio](../../ssms/download-sql-server-management-studio-ssms.md) または T-SQL クエリを実行できる別のツールが必要です。

このデータセットを使用したチュートリアルとクイックスタートには、次のものがあります。

+  [revoscalepy を使用した Python モデルの作成](use-python-revoscalepy-to-create-model.md)

## <a name="create-the-database"></a>データベースの作成

1. SQL Server Management Studio を開始し、R または Python 統合のあるデータベース エンジン インスタンスに接続します。  

2. オブジェクト エクスプローラーで、**[データベース]** を右クリックし、**flightdata** という名前の新しいデータベースを作成します。

3. **flightdata** を右クリックし、**[タスク]** をクリックして、**[フラットファイルのインポート]** をクリックします。

4. インストールした言語に応じて、R または Python ディストリビューションに用意されている AirlineDemoData.csv ファイルを開きます。

   R の場合は、C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES\library\RevoScaleR\SampleData で **AirlineDemoSmall.csv** を探します。
   
   Python の場合は、C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\PYTHON_SERVICES\Lib\site-packages\revoscalepy\data\sample_data で **AirlineDemoSmall.csv** を探します。
  
ファイルを選択すると、テーブル名とスキーマに既定値が設定されます。

  ![航空会社のデモの既定値を表示したフラット ファイルのインポート ウィザード](media/import-airlinedemosmall.png)

残りのページをクリックし、既定値をそのまま使用してデータをインポートします。


## <a name="query-the-data"></a>データにクエリを実行する

検証手順として、クエリを実行してデータがアップロードされたことを確認します。

1. オブジェクト エクスプローラーの [データベース] で、**flightdata** データベースを右クリックし、新しいクエリを開始します。

2. いくつかの単純なクエリを実行します。

    ```sql
    SELECT TOP(10) * FROM AirlineDemoSmall;
    SELECT COUNT(*) FROM AirlineDemoSmall;
    ```

## <a name="next-steps"></a>次のステップ

次のレッスンでは、このデータに基づいて線形回帰モデルを作成します。

+ [revoscalepy を使用した Python モデルの作成](use-python-revoscalepy-to-create-model.md)
