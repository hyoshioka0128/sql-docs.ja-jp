---
title: SQL Server Python および R チュートリアル用の航空便デモデータセット
Description: R と Python の航空データセットを含むデータベースを作成します。 このデータセットは、R 言語または Python コードを SQL Server ストアドプロシージャにラップする方法を示す演習で使用されます。
ms.prod: sql
ms.technology: machine-learning
ms.date: 10/22/2018
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: b7f28dd4b3e7e6990e037dbd9afe164d8d0e4bec
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68714813"
---
#  <a name="airline-flight-arrival-demo-data-for-sql-server-python-and-r-tutorials"></a>SQL Server の Python と R のチュートリアルの航空便到着デモデータ
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この演習では、R または Python の組み込みのエアラインデモデータセットからインポートしたデータを格納する SQL Server データベースを作成します。 R と Python のディストリビューションには同等のデータが用意されており、Management Studio を使用して SQL Server データベースにインポートできます。

この演習を完了するには、 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)または t-sql クエリを実行できる別のツールが必要です。

このデータセットを使用したチュートリアルとクイックスタートには、次のものがあります。

+  [Revoscalepy を使用して Python モデルを作成する](use-python-revoscalepy-to-create-model.md)

## <a name="create-the-database"></a>データベースの作成

1. SQL Server Management Studio を開始するには、R または Python 統合のあるデータベースエンジンインスタンスに接続します。  

2. オブジェクトエクスプローラーで、 **[データベース]** を右クリックし、 **[フライトデータ]** という名前の新しいデータベースを作成します。

3. **[フライトデータ]** を右クリックし、 **[タスク]** をクリックして、 **[フラットファイルのインポート]** をクリックします。

4. インストールした言語に応じて、R または Python ディストリビューションに用意されている放映 Linedemodata .csv ファイルを開きます。

   R の場合は、C:\Program ・ SQL Server\MSSQL14. で、**放映 Linedemosmall .csv を探します。** MSSQLSERVER\R_SERVICES\library\RevoScaleR\SampleData
   
   Python の場合は、C:\Program ・ SQL Server\MSSQL14. で、**放映 Linedemosmall .csv を探します。** MSSQLSERVER\PYTHON_SERVICES\Lib\site-packages\revoscalepy\data\sample_data
  
ファイルを選択すると、テーブル名とスキーマに既定値が設定されます。

  ![エアラインのデモの既定値を示すフラットファイルのインポートウィザード](media/import-airlinedemosmall.png)

残りのページをクリックして、既定値をそのまま使用してデータをインポートします。


## <a name="query-the-data"></a>データのクエリ

検証手順として、クエリを実行してデータがアップロードされたことを確認します。

1. オブジェクトエクスプローラーの データベース で、**フライトデータ** データベースを右クリックし、新しいクエリを開始します。

2. いくつかの単純なクエリを実行します。

    ```sql
    SELECT TOP(10) * FROM AirlineDemoSmall;
    SELECT COUNT(*) FROM AirlineDemoSmall;
    ```

## <a name="next-steps"></a>次の手順

次のレッスンでは、このデータに基づいて線形回帰モデルを作成します。

+ [Revoscalepy を使用して Python モデルを作成する](use-python-revoscalepy-to-create-model.md)
