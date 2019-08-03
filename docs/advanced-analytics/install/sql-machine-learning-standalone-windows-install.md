---
title: SQL Server セットアップを使用して R Server または Machine Learning Server (スタンドアロン) をインストールする
description: RevoScaleR、revoscalepy、Microsoft Ml、およびその他のパッケージを使用して、R および Python 開発用のインスタンス対応ではないスタンドアロン machine learning サーバーをセットアップします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 08/28/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 94ca7b3646b9005e11b3ee4968cbfaaa65d42264
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715845"
---
# <a name="install-machine-learning-server-standalone-or-r-server-standalone-using-sql-server-setup"></a>SQL Server セットアップを使用して Machine Learning Server (スタンドアロン) または R Server (スタンドアロン) をインストールする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
SQL Server セットアップには、SQL Server の外部で実行される、インスタンス対応ではないスタンドアロン機械学習サーバーをインストールするための**共有機能**オプションが用意されています。 これは**Machine Learning Server (スタンドアロン)** と呼ばれ、R と Python を含みます。 
::: moniker-end
::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
SQL Server セットアップには、SQL Server の外部で実行される、インスタンス対応ではないスタンドアロン機械学習サーバーをインストールするための**共有機能**オプションが用意されています。 SQL Server 2016 では、この機能は**R Server (スタンドアロン)** と呼ばれます。  
::: moniker-end

SQL Server セットアップによってインストールされるスタンドアロンサーバーは、機能的には、次のような同じユースケースとシナリオをサポートする、SQL 以外の[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)のバージョンと同等です。

+ リモート実行、同じコンソールでのローカルセッションとリモートセッションの切り替え
+ Web ノードとコンピューティングノードを使用した運用化
+ Web サービスのデプロイ: R および Python スクリプトを web サービスにパッケージ化する機能
+ R および Python 関数ライブラリの完全なコレクション

SQL Server から切り離された独立したサーバーとして、R と Python の環境は、SQL Server ではなく、スタンドアロンサーバーに用意されている基盤のオペレーティングシステムとツールを使用して構成、セキュリティ保護、およびアクセスします。

SQL Server の補完として、スタンドアロンサーバーは、サポートされているすべてのデータプラットフォームにリモートコンピューティングコンテキストを使用できる高パフォーマンスの機械学習ソリューションを開発する必要がある場合に役立ちます。 ローカルサーバーから、Spark クラスターまたは別の SQL Server インスタンスのリモート Machine Learning Server に実行をシフトできます。

<a name="bkmk_prereqs"> </a>

## <a name="pre-install-checklist"></a>インストール前のチェックリスト

SQL Server 2016 R Server (スタンドアロン) や Microsoft R Server などの以前のバージョンをインストールした場合は、続行する前に既存のインストールをアンインストールしてください。

一般的なルールとして、スタンドアロンサーバーとデータベースエンジンのインスタンス対応インストールは、リソースの競合を避けるために相互に排他的に扱うことをお勧めしますが、十分なリソースがある場合は、両方にインストールする禁止はありません。同じ物理コンピューター。

コンピューターには、スタンドアロンサーバーを1つだけ含めることができます。 SQL Server Machine Learning Server (スタンドアロン) または SQL Server R Server (スタンドアロン) のいずれかです。 新しいバージョンを追加する前に、必ず1つのバージョンをアンインストールしてください。

::: moniker range="=sql-server-2016"
<a name="bkmk_ga_instalpatch"></a> 

 ###  <a name="install-patch-requirement"></a>パッチのインストール要件 

SQL Server 2016 のみ:SQL Server の前提条件としてインストールされる特定のバージョンの Microsoft VC++ 2013 ランタイム バイナリに問題が見つかりました。 VC ランタイム バイナリに対するこの更新プログラムをインストールしないと、特定のシナリオにおいて、SQL Server で安定性の問題が発生する可能性があります。 SQL Server をインストールする前に、「[SQL Server リリース ノート](../../sql-server/sql-server-2016-release-notes.md#bkmk_ga_instalpatch)」にある手順に従って、ご使用のコンピューターに VC ランタイム バイナリのパッチが必要かどうかを確認してください。  
::: moniker-end

## <a name="get-the-installation-media"></a>インストール メディアを入手する

[!INCLUDE[GetInstallationMedia](../../includes/getssmedia.md)]

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
## <a name="run-setup"></a>セットアップの実行

ローカル インストールの場合は、セットアップを管理者として実行する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をリモート共有からインストールする場合は、そのリモート共有に対する読み取り権限と実行権限を持つドメイン アカウントを使用する必要があります。

1. インストールウィザードを開始します。

2. **[インストール]** タブをクリックし、[**新しい Machine Learning Server (スタンドアロン)] インストール**を選択します。
    
     ![スタンドアロン Machine Learning Server のインストール](media/2017setup-installation-page-mlsvr.png "スタンドアロン Machine Learning Server のインストールを開始")する

3. ルールの確認が完了したら SQL Server ライセンス条項に同意し、新しいインストールを選択します。

4. **[機能の選択]** ページでは、次のオプションが既に選択されています。

    - Microsoft Machine Learning Server (スタンドアロン)

    - 既定では、R と Python の両方が選択されています。 どちらの言語も選択解除できますが、サポートされている言語の少なくとも1つをインストールすることをお勧めします。

     ![R または Python の機能の選択](media/2017setup-features-page-mlsvr-rpy.png "スタンドアロン Machine Learning Server のインストールを開始")する
    
    その他のオプションはすべて無視します。 
    
    > [!NOTE]
    > SQL Server データベース内分析用にコンピューター Machine Learning Services が既にインストールされている場合は、**共有機能**をインストールしないようにしてください。 これにより、重複するライブラリが作成されます。
    > 
    > また、SQL Server で実行されている R または Python スクリプトは SQL Server によって管理されるので、他のデータベースエンジンサービスで使用されているメモリと競合しないように、スタンドアロンの machine learning Server にはそのような制約がないため、他のデータベース操作に干渉する可能性があります. 最後に、RDP セッション経由のリモートアクセス (運用化によく使用される) は、通常、データベース管理者によってブロックされます。
    > 
    > このような理由から、通常は SQL Server Machine Learning Services とは別のコンピューターに Machine Learning Server (スタンドアロン) をインストールすることをお勧めします。

5.  基本言語の配布をダウンロードおよびインストールするためのライセンス条項に同意します。 **[同意する]** ボタンが使用できない状態になっている場合は、 **[次へ]** をクリックします。 

     ![Python 使用許諾契約書](media/2017setup-python-license.png "Python 使用許諾契約書")

6.  **[インストールの準備完了]** ページで、選択内容を確認し、 **[インストール]** をクリックします。

インストールが完了したら、「 [SQL Server R Services のカスタムレポート](../r/monitor-r-services-using-custom-reports-in-management-studio.md)」で、エラーまたは警告の詳細については、「[アップグレードとインストールに関する FAQ-Machine Learning Services](../r/upgrade-and-installation-faq-sql-server-r-services.md)」を参照してください。
::: moniker-end

::: moniker range="=sql-server-2016"
## <a name="run-setup"></a>セットアップの実行

ローカル インストールの場合は、セットアップを管理者として実行する必要があります。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] をリモート共有からインストールする場合は、そのリモート共有に対する読み取り権限と実行権限を持つドメイン アカウントを使用する必要があります。

1. インストールウィザードを開始します。

2. **[インストール]** タブで、 **[R Server (スタンドアロン) の新規インストール]** をクリックします。
    
     ![R Server スタンドアロンのセットアップを開始]する(media/2016-setup-installation-rsvr.png "R Server スタンドアロンのセットアップを開始")する

3. ルールの確認が完了したら SQL Server ライセンス条項に同意し、新しいインストールを選択します。

4.  **[機能の選択]** ページで、次のオプションが既に選択されています。
    
    **R Server (スタンドアロン)**  
    
    ![R Server スタンドアロンの機能の選択](media/2016setup-rserver-features.png "R Server スタンドアロンの機能の選択")
    
    その他のすべてのオプションは無視できます。 
    
    > [!NOTE]
    > データベース内分析 SQL Server 用に R Services が既にインストールされているコンピューターでセットアップを実行している場合は、**共有機能**をインストールしないようにしてください。 これにより、重複するライブラリが作成されます。
    > 
    > SQL Server で実行されている R スクリプトは SQL Server によって管理されるので、他のデータベースエンジンサービスで使用されているメモリと競合しないように、スタンドアロンの R Server にはそのような制約はなく、他のデータベース操作に干渉する可能性があります。
    > 
    > 通常、R Server (スタンドアロン) は、SQL Server R Services (データベース内) とは別のコンピューターにインストールすることをお勧めします。

5.  基本言語の配布をダウンロードおよびインストールするためのライセンス条項に同意します。 **[同意する]** ボタンが使用できない状態になっている場合は、 **[次へ]** をクリックします。 

6.  **[インストールの準備完了]** ページで、選択内容を確認し、 **[インストール]** をクリックします。

インストールが完了したら、「 [SQL Server R Services のカスタムレポート](../r/monitor-r-services-using-custom-reports-in-management-studio.md)」で、エラーまたは警告の詳細については、「[アップグレードとインストールに関する FAQ-Machine Learning Services](../r/upgrade-and-installation-faq-sql-server-r-services.md)」を参照してください。
::: moniker-end

## <a name="set-environment-variables"></a>環境変数の設定

R 機能の統合のみの場合、 **MKL_CBWR**環境変数を設定し[て](https://software.intel.com/articles/introduction-to-the-conditional-numerical-reproducibility-cnr)、Intel MATH Kernel Library (MKL) 計算からの一貫した出力を確保する必要があります。

1. コントロールパネルで、[**システムとセキュリティ** >  > ] [システム] [システム**設定** > ] **[環境変数]** をクリックします。

2. 新しいユーザー変数またはシステム変数を作成します。 

  + 変数名をに設定します`MKL_CBWR`
  + 変数の値をに設定します。`AUTO`

3. サーバーを再起動します。

<a name="install-path"></a>

### <a name="default-installation-folders"></a>既定のインストールフォルダー

R と Python の開発では、同じコンピューターに複数のバージョンがあることが一般的です。 SQL Server セットアップによってインストールされたように、基本配布はセットアップに使用した SQL Server バージョンに関連付けられているフォルダーにインストールされます。

次の表は、Microsoft インストーラーによって作成された R および Python ディストリビューションのパスを示しています。 完全を期すために、テーブルには SQL Server セットアップによって生成されるパスと、Microsoft Machine Learning Server 用のスタンドアロンインストーラーが含まれています。

|バージョン| インストール方法 | 既定のフォルダー|
|----|----|----|
|SQL Server 2017 Machine Learning Server (スタンドアロン) |  SQL Server 2017 セットアップウィザード |`C:\Program Files\Microsoft SQL Server\140\R_SERVER` <br/>`C:\Program Files\Microsoft SQL Server\140\PYTHON_SERVER`|
|Microsoft Machine Learning Server (スタンドアロン) |  Windows スタンドアロンインストーラー |`C:\Program Files\Microsoft\ML Server\R_SERVER`<br/>`C:\Program Files\Microsoft\ML Server\PYTHON_SERVER`|
|SQL Server Machine Learning Services (データベース内) |SQL Server 2017 セットアップウィザード、R 言語オプション|`C:\Program Files\Microsoft SQL Server\MSSQL14.<instance_name>\R_SERVICES`  <br/>`C:\Program Files\Microsoft SQL Server\MSSQL14.<instance_name>\PYTHON_SERVICES` |
|SQL Server 2016 R Server (スタンドアロン) |  SQL Server 2016 セットアップウィザード |`C:\Program Files\Microsoft SQL Server\130\R_SERVER`|
|SQL Server 2016 R Services (データベース内) |SQL Server 2016 セットアップウィザード|`C:\Program Files\Microsoft SQL Server\MSSQL13.<instance_name>\R_SERVICES`|

<a name="apply-cu"></a>

## <a name="apply-updates"></a>更新プログラムの適用

最新の累積的な更新プログラムをデータベースエンジンと機械学習コンポーネントの両方に適用することをお勧めします。 累積的な更新プログラムは、セットアッププログラムを使用してインストールされます。 

インターネットに接続されたデバイスでは、自己解凍形式の実行可能ファイルをダウンロードできます。 データベースエンジンの更新プログラムを適用すると、既存の R および Python の機能の累積的な更新プログラムが自動的に取得されます。 

切断されたサーバーでは、追加の手順が必要です。 データベースエンジンの累積的な更新プログラムと、機械学習機能用の CAB ファイルを取得する必要があります。 すべてのファイルを分離サーバーに転送し、手動で適用する必要があります。

1. ベースラインインスタンスを使用して開始します。 累積的な更新プログラムは、既存のインストールにのみ適用できます。

  + SQL Server 2017 最初のリリースからの Machine Learning Server (スタンドアロン)
  + SQL Server 2016 の初期リリース、SQL Server 2016 SP 1、または SQL Server 2016 SP 2 の R Server (スタンドアロン)

2. 開いている R または Python セッションを終了し、システムでまだ実行中のプロセスを停止します。

3. Web サービスのデプロイのために web ノードとコンピューティングノードとして実行するための運用化を有効にした場合は、予防策として**AppSettings**ファイルをバックアップします。 SQL Server 2017 CU13 以降を適用すると、このファイルが改訂されるため、元のバージョンを保持するためにバックアップコピーが必要になる場合があります。

4. インターネットに接続されているデバイスで、使用しているバージョンの SQL Server の [累積更新プログラム] リンクをクリックします。

  + [SQL Server 2017 の更新プログラム](https://sqlserverupdates.com/sql-server-2017-updates/)
  + [SQL Server 2016 の更新プログラム](https://sqlserverupdates.com/sql-server-2016-updates/)

5. 最新の累積的な更新プログラムをダウンロードします。 これは実行可能ファイルです。

6. インターネットに接続されているデバイスで、.exe をダブルクリックしてセットアップを実行し、ウィザードをステップ実行してライセンス条項に同意し、影響を受けた機能を確認して、完了するまで進行状況を監視します。

7. インターネットに接続されていないサーバーの場合:

   + R および Python 用の対応する CAB ファイルを取得します。 ダウンロードリンクについては、 [SQL Server データベース内分析インスタンスの累積的な更新プログラムの CAB ダウンロード](sql-ml-cab-downloads.md)に関するページを参照してください。

   + すべてのファイル (メインの実行可能ファイルと CAB ファイル) をオフラインコンピューター上のフォルダーに転送します。

   + .Exe ファイルをダブルクリックして、セットアップを実行します。 インターネットに接続されていないサーバーに累積 update をインストールする場合は、R と Python の .cab ファイルの場所を選択するように求められます。

8. インストール後、web ノードとコンピューティングノードでの運用化を有効にしたサーバーで、 **AppSettings**を編集し、"MMLResourcePath" エントリを "Mml path" の下に直接追加します。

    ```json
    "ScorerParameters": {
        "MMLNativePath": "C:\Program Files\Microsoft SQL Server\140\R_SERVER\library\MicrosoftML\mxLibs\x64\",
        "MMLResourcePath": "C:\Program Files\Microsoft SQL Server\140\R_SERVER\library\MicrosoftML\mxLibs\x64\"
    }
    ```

9. [管理 CLI ユーティリティを実行](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch)して、web ノードとコンピューティングノードを再起動します。 手順と構文については、「 [web ノードとコンピューティングノードの監視、開始、停止](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-stop-start)」を参照してください。

## <a name="development-tools"></a>開発ツール

開発用 IDE は、セットアップの一部としてインストールされません。 開発環境の構成の詳細については、「 [R ツールのセットアップ](../r/set-up-a-data-science-client.md)」および「 [Python ツールの設定](../python/setup-python-client-tools-sql.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

R 開発者は、いくつかの簡単な例を使って開始し、SQL Server での R の動作の基本を学習できます。 次の手順については、次のリンクを参照してください。

+ [チュートリアル: T-sql で R を実行する](../tutorials/rtsql-using-r-code-in-transact-sql-quickstart.md)
+ [チュートリアル: R 開発者向けのデータベース内分析](../tutorials/sqldev-in-database-r-for-sql-developers.md)

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
Python 開発者は、次のチュートリアルに従って、SQL Server で Python を使用する方法を学習できます。

+ [チュートリアル: T-sql で Python を実行する](../tutorials/run-python-using-t-sql.md)
+ [チュートリアル: Python 開発者向けのデータベース内分析](../tutorials/sqldev-in-database-python-for-sql-developers.md)
::: moniker-end

実際のシナリオに基づく機械学習の例については、「 [machine learning のチュートリアル](../tutorials/machine-learning-services-tutorials.md)」を参照してください。
