---
title: Management Studio でカスタムレポートを使用して R Services を監視する
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: a7ab7a4ccd4956bd1752be398b25a6ff9fd92ce5
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715080"
---
# <a name="monitor-machine-learning-services-using-custom-reports-in-management-studio"></a>Management Studio でカスタム レポートを使用して Machine Learning Services を監視する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

機械学習に使用されるインスタンスの管理を容易にするために、製品チームには SQL Server Management Studio に追加できるサンプルカスタムレポートが多数用意されています。 これらのレポートでは、次のような詳細情報を表示できます。

- アクティブな R または Python セッション
- インスタンスの構成設定
- Machine learning ジョブの実行統計
- R Services の拡張イベント
- 現在のインスタンスにインストールされている R または Python パッケージ

この記事では、machine learning 専用のカスタムレポートをインストールして使用する方法について説明します。 

Management Studio でのレポートの概要については、 [Management Studio の「カスタムレポート](../../ssms/object/custom-reports-in-management-studio.md)」を参照してください。

## <a name="how-to-install-the-reports"></a>レポートのインストール方法

レポートは SQL Server Reporting Services を使用するように設計されていますが、SQL Server Management Studio から直接使用することができます。インスタンスに Reporting Services がインストールされていない場合でも同じです。 

これらのレポートを使用する場合は、次のようにします。

* SQL Server 製品サンプルの GitHub リポジトリから RDL ファイルをダウンロードします。
* SQL Server Management Studio で使用されるカスタム レポート フォルダーにファイルを追加します。
* SQL Server Management Studio でレポートを開きます。


### <a name="step-1-download-the-reports"></a>手順 1. サンプルのダウンロード

1. [SQL Server 製品サンプル](https://github.com/Microsoft/sql-server-samples)が含まれている GitHub リポジトリを開き、サンプルレポートをダウンロードします。 

    + [SSMS カスタムレポート](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/machine-learning-services/ssms-custom-reports)

    > [!NOTE]
    > レポートは、SQL Server 2017 Machiine Learning Services、SQL Server 2016 R Services のいずれかで使用できます。

2. サンプルをダウンロードする場合、GitHub にログインし、サンプルのローカル分岐を作成することもできます。 

### <a name="step-2-copy-the-reports-to-management-studio"></a>手順 2. Management Studio へのレポートのコピー

3. SQL Server Management Studio で使用されるカスタム レポート フォルダーを見つけます。 既定では、カスタム レポートは次のフォルダーに格納されます。
    
   `C:\Users\user_name\Documents\SQL Server Management Studio\Custom Reports`

   ただし、別のフォルダーを指定したり、サブフォルダーを作成したりすることもできます。

4. *.RDL ファイルをカスタム レポート フォルダーにコピーします。


### <a name="step-3-run-the-reports"></a>手順 3. レポートの実行

5. Management Studio で、レポートを実行するインスタンスの **[データベース]** ノードを右クリックします。
6. **[レポート]** 、 **[カスタム レポート]** の順にクリックします。
7. **[ファイルを開く]** ダイアログ ボックスで、カスタム レポート フォルダーを見つけます。
8. ダウンロードした RDL ファイルを選択し、 **[開く]** をクリックします。

> [!IMPORTANT]
> 高 DPI または 1080 p を超える解像度のディスプレイ デバイスを持つ一部のコンピューターや、一部のリモート デスクトップ セッションでは、これらのレポートは使用できません。 SSMS のレポート ビューアー コントロールには、レポートのクラッシュの原因となるバグがあります。

## <a name="report-list"></a>レポートの一覧

GitHub の product samples リポジトリには、現在、次のレポートが含まれています。

+ **R Services - Active Sessions**

  このレポートを使用して、現在 SQL Server インスタンスに接続していて、machine learning ジョブを実行しているユーザーを表示します。 
  
+ **R Services - Configuration**

  このレポートを使用して、外部スクリプトランタイムおよび関連するサービスの構成を表示します。 レポートでは、再起動が必要かどうかが示され、必要なネットワーク プロトコルが確認されます。 
  
  コンピューティングコンテキストとして SQL Server で実行される機械学習タスクには、暗黙的な認証が必要です。 暗黙の認証が構成されていることを確認するために、レポートでは、グループ SQLRUserGroup のデータベースログインが存在するかどうかを確認します。

 + **R Services - Configure Instance** 

   このレポートは、machine learning の構成を支援することを目的としています。 また、このレポートを実行して、前のレポートで検出された構成エラーを修正することもできます。
 
+ **R Services - Execution Statistics**

  このレポートを使用して、machine learning ジョブの実行の統計情報を表示します。 たとえば、実行された R スクリプトの合計数、並列実行の数、および最も頻繁に使用される RevoScaleR 関数を確認できます。 **[Sql スクリプトの表示]** をクリックして、このレポートの背後にある完全な t-sql コードを取得します。

  現在、レポートで監視されるのは、RevoScaleR パッケージ関数の統計のみです。

+ **R Services - Extended Events**

  このレポートを使用して、外部スクリプトランタイムに関連する監視タスクで使用できる拡張イベントの一覧を表示します。 **[Sql スクリプトの表示]** をクリックして、このレポートの背後にある完全な t-sql コードを取得します。

+ **R Services - Packages**

  このレポートを使用して、SQL Server インスタンスにインストールされている R または Python パッケージの一覧を表示します。

+ **R Services - Resource Usage**

  このレポートを使用して、外部スクリプトの実行による CPU、メモリ、および i/o リソースの消費量を表示します。 外部のリソース プールのメモリ設定を表示することもできます。

## <a name="see-also"></a>関連項目

[サービスの監視](managing-and-monitoring-r-solutions.md)

[R Services の拡張イベント](extended-events-for-sql-server-r-services.md)
