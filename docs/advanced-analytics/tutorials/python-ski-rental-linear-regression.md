---
title: Python のチュートリアル:スキーレンタル (線形回帰)
description: このチュートリアルでは、SQL Server Machine Learning Services で Python と線形回帰を使用して、ski のレンタル数を予測します。
ms.prod: sql
ms.technology: machine-learning
ms.date: 09/03/2019
ms.topic: tutorial
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 718487ba55b2b8db8f16b59df5785cf78ff501f1
ms.sourcegitcommit: ecb19d0be87c38a283014dbc330adc2f1819a697
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242510"
---
# <a name="python-tutorial-predict-ski-rental-with-linear-regression-in-sql-server-machine-learning-services"></a>Python のチュートリアル:SQL Server Machine Learning Services での線形回帰を使用した ski レンタルの予測
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この4部構成のチュートリアルシリーズでは、 [SQL Server Machine Learning Services](../what-is-sql-server-machine-learning.md)で Python と線形回帰を使用して、ski のレンタル数を予測します。 このチュートリアルでは[Azure Data Studio で python notebook](../../azure-data-studio/sql-notebooks.md)を使用しますが、独自の python 統合開発環境 (IDE) を使用することもできます。

たとえば、ski レンタル事業を所有していて、将来の日付に対するレンタルの数を予測する場合を考えてみましょう。 この情報は、在庫、スタッフ、設備を準備するのに役立ちます。

このシリーズの第1部では、前提条件を設定します。 パート2と3では、Jupyter notebook でいくつかの Python スクリプトを開発して、データを準備し、機械学習モデルをトレーニングします。 次に、パート3では、T-sql ストアドプロシージャを使用して SQL Server 内でこれらの Python スクリプトを実行します。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * サンプルデータベースを SQL Server にインポートする 

[パート 2](python-ski-rental-linear-regression-prepare-data.md)では、SQL Server から python データフレームにデータを読み込み、python でデータを準備する方法を学習します。

[パート 3](python-ski-rental-linear-regression-train-model.md)では、Python で線形回帰モデルをトレーニングする方法について学習します。

[パート 4](python-ski-rental-linear-regression-deploy-model.md)では、モデルを SQL Server に格納する方法について学習した後、パート2と3で開発した Python スクリプトからストアドプロシージャを作成します。 ストアドプロシージャは、新しいデータに基づいて予測を行うために SQL Server で実行されます。

## <a name="prerequisites"></a>前提条件

* SQL Server Machine Learning Services-Machine Learning Services をインストールする方法については、「 [Windows インストールガイド](../install/sql-machine-learning-services-windows-install.md)」または[Linux インストールガイド](../../linux/sql-server-linux-setup-machine-learning.md?toc=%2Fsql%2Fadvanced-analytics%2Ftoc.json)を参照してください。

* Python IDE-このチュートリアルでは、 [Azure Data Studio](../../azure-data-studio/what-is.md)の python notebook を使用します。 詳細については、「 [Azure Data Studio で notebook を使用する方法](../../azure-data-studio/sql-notebooks.md)」を参照してください。 

    Jupyter notebook や、 [python 拡張](https://marketplace.visualstudio.com/items?itemName=ms-python.python)機能と[mssql extension](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)を使用した[Visual Studio Code](https://code.visualstudio.com/docs)など、独自の python IDE を使用することもできます。 

* [revoscalepy](../python/ref-py-revoscalepy.md) package- **revoscalepy**パッケージは SQL Server Machine Learning Services に含まれています。 クライアントコンピューターでパッケージを使用するには、「 [Python 開発用のデータサイエンスクライアントを設定](../python/setup-python-client-tools-sql.md)する」を参照して、このパッケージをローカルにインストールするオプションを選択してください。

    Azure Data Studio で Python notebook を使用している場合は、次の追加の手順に従って**revoscalepy**を使用します。

    1. Azure Data Studio を開く
    1. **[ファイル]** メニューの **[基本設定]** を選択し、 **[設定]** をクリックします。
    1. **拡張機能**を展開し、 **Notebook 構成**を選択します
    1. **[Python パス]** で、ライブラリをインストールしたパスを入力します`C:\path-to-python-for-mls`(たとえば、)。
    1. 既存の**Python を使用する**ことを確認する
    1. 再起動 Azure Data Studio

    別の Python IDE を使用している場合は、IDE の同様の手順に従います。

* SQL クエリツール-このチュートリアルでは、 [Azure Data Studio](../../azure-data-studio/what-is.md)を使用していることを前提としています。 [SQL Server Management Studio](../../ssms/sql-server-management-studio-ssms.md) (SSMS) を使用することもできます。

* 追加の Python パッケージ-このチュートリアルシリーズの例では、インストールされている可能性のある Python パッケージを使用します。 必要に応じて、次の**pip**コマンドを使用してこれらのパッケージをインストールします。

    ```console
    pip install pandas
    pip install sklearn
    pip install pickle
    ```

## <a name="restore-the-sample-database"></a>サンプルデータベースを復元する

このチュートリアルで使用されているサンプルデータセットは、ダウンロードして使用するための **.bak**データベースバックアップファイルに保存されています。

1. [TutorialDB](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bak)ファイルをダウンロードします。

1. 次の詳細を使用して、「Azure Data Studio で[バックアップファイルからデータベースを復元](../../azure-data-studio/tutorial-backup-restore-sql-server.md#restore-a-database-from-a-backup-file)する」の手順に従います。

   * ダウンロードした TutorialDB ファイルからインポートし**ます。**
   * ターゲットデータベースに "TutorialDB" という名前を指定します。

1. データベースを復元した後、 **rental_data**テーブルに対してクエリを実行することで、データセットが存在することを確認できます。

    ```sql
    USE TutorialDB;
    SELECT * FROM [dbo].[rental_data];
    ```

## <a name="next-steps"></a>次の手順

このチュートリアルシリーズの第1部では、次の手順を完了しました。

* 必須コンポーネントのインストール
* サンプルデータベースを SQL Server にインポートする

TutorialDB データベースからデータを準備するには、このチュートリアルシリーズの第2部の手順を実行します。

> [!div class="nextstepaction"]
> [Python のチュートリアル:線形回帰モデルをトレーニングするためのデータを準備する](python-ski-rental-linear-regression-prepare-data.md)