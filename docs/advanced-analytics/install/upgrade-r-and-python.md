---
title: R および Python コンポーネントのアップグレード
description: SQL Server Machine Learning Services で R と Python をアップグレードするか、sqlbindr .exe を使用して Machine Learning Server にバインドし SQL Server R Services。
ms.prod: sql
ms.technology: machine-learning
ms.date: 07/30/2019
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||=sqlallproducts-allversions'
ms.openlocfilehash: 948ce20bf32aaa2051c4a805a3ca2f131a7c0c8f
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715210"
---
# <a name="upgrade-machine-learning-r-and-python-components-in-sql-server-instances"></a>SQL Server インスタンス内の machine learning (R および Python) コンポーネントをアップグレードする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

SQL Server での R および Python の統合には、オープンソースのパッケージと Microsoft 独自のパッケージが含まれます。 Standard SQL Server サービスでは、パッケージは SQL Server リリースサイクルに従って更新され、現在のバージョンの既存のパッケージに対するバグ修正がありますが、メジャーバージョンのアップグレードは行われません。 

しかし、多くのデータ科学者は、使用可能になった新しいパッケージを使用することに慣れています。 SQL Server Machine Learning Services (データベース内) と SQL Server R Services (データベース内) の両方について、 **Microsoft Machine Learning Server**に*バインド*することにより、[新しいバージョンの R および Python](#version-map)を取得できます。 

## <a name="what-is-binding"></a>バインドとは

バインドは、R_SERVICES フォルダーと PYTHON_SERVICES フォルダーの内容を、 [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index)の新しい実行可能ファイル、ライブラリ、およびツールを使用して交換するインストールプロセスです。

更新されたコンポーネントと共に、サービスモデルのスイッチが用意されています。 [SQL Server 累積更新プログラム](https://support.microsoft.com/help/4047329/sql-server-2017-build-versions)を使用して、 [SQL Server 製品のライフサイクル](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202017)の代わりに、サービスの更新が[最新のライフサイクル](https://support.microsoft.com/help/30881/modern-lifecycle-policy)の[Microsoft R Server & Machine Learning Server のサポートタイムライン](https://docs.microsoft.com/machine-learning-server/resources-servicing-support)に準拠するようになりました。

コンポーネントのバージョンとサービスの更新を除き、バインドによってインストールの基本が変更されることはありません。R と Python の統合はデータベースエンジンのインスタンスの一部であるため、ライセンスは変更されません (バインドに関連する追加コストはありません)。また、データベースエンジンの SQL Server サポートポリシーも保持されます。 この記事の残りの部分では、バインドメカニズムと SQL Server の各バージョンでの動作について説明します。

> [!NOTE]
> バインドは、SQL Server インスタンスにバインドされている (データベース内の) インスタンスにのみ適用されます。 バインドは、(スタンドアロン) のインストールには関係ありません。

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
**SQL Server 2017 のバインドに関する考慮事項**

SQL Server Machine Learning Services については、Microsoft Machine Learning Server が既にインストールされているパッケージまたは新しいバージョンを提供し始める場合にのみ、バインドを検討してください。
::: moniker-end

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
**SQL Server 2016 のバインドに関する考慮事項**

SQL Server 2016 R Services のお客様の場合、バインドでは、更新された R パッケージ、元のインストール ([Microsoft ml](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/microsoftml-package)) に含まれていない新しいパッケージ、および[事前トレーニング](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models)済みのモデルが提供されます。これらはすべて、の新しいメジャーリリースとマイナーリリースでさらに更新できます。[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index)。 バインディングでは、Python サポートは提供されません。これは SQL Server 2017 機能です。
::: moniker-end

<a name="version-map"></a>

## <a name="version-map"></a>バージョンマップ

次の表は、バージョンマップで、リリース車両間でパッケージバージョンを示しています。これにより、Python サポートを開始する前に、Microsoft Machine Learning Server (以前の R Server) にバインドするときに、可能性のあるアップグレードパスを確認できるようになります。MLS 9.2.1)。 

バインドでは、R または Anaconda の最新バージョンが保証されないことに注意してください。 Microsoft Machine Learning Server (MLS) にバインドすると、セットアップによってインストールされた R または Python のバージョンが取得されますが、これは web で使用可能な最新バージョンではない可能性があります。

::: moniker range="=sql-server-2016||=sqlallproducts-allversions"
[**SQL Server 2016 R Services**](../install/sql-r-services-windows-install.md)

コンポーネント |最初のリリース | [R Server 9.0.1](https://docs.microsoft.com/machine-learning-server/install/r-server-install-windows) | [R Server 9.1](https://docs.microsoft.com/machine-learning-server/install/r-server-install-windows) | [MLS 9.2.1](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-install) | [MLS 9.3](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-install) |
----------|----------------|----------------|--------------|---------|-------|
Microsoft R Open (MRO) over R | R 3.2.2     | R 3.3.2   |R 3.3.3   | R 3.4.1  | R 3.4.3 |
[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) | 8.0.3  | 9.0.1 |  9.1 |  9.2.1 |  9.3 |
[MicrosoftML](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/microsoftml-package)| n.a. | 9.0.1 |  9.1 |  9.2.1 |  9.3 |
[事前トレーニング済みのモデル](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models)| n.a. | 9.0.1 |  9.1 |  9.2.1 |  9.3 |
[sqlrutils](https://docs.microsoft.com/machine-learning-server/r-reference/sqlrutils/sqlrutils)| n.a. | 1.0 |  1.0 |  1.0 |  1.0 |
[olapR](https://docs.microsoft.com/machine-learning-server/r-reference/olapr/olapr) | n.a. | 1.0 |  1.0 |  1.0 |  1.0 |
::: moniker-end

::: moniker range=">=sql-server-2017||=sqlallproducts-allversions"
[**SQL Server Machine Learning Services**](../install/sql-machine-learning-services-windows-install.md)

コンポーネント |最初のリリース | MLS 9.3 | | | |
----------|----------------|---------|-|-|-|-|
Microsoft R Open (MRO) over R | R 3.3.3 | R 3.4.3 | | | |
[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) |   9.2 |  9.3 | | | |
[MicrosoftML](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/microsoftml-package) | 9.2  | 9.3| | | |
[sqlrutils](https://docs.microsoft.com/machine-learning-server/r-reference/sqlrutils/sqlrutils)| 1.0 |  1.0 | | | |
[olapR](https://docs.microsoft.com/machine-learning-server/r-reference/olapr/olapr) | 1.0 |  1.0 | | | |
Anaconda 4.2 over Python 3.5  | 4.2/3.5.2 | 4.2/3.5.2 | | | |
[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) | 9.2  | 9.3| | | |
[microsoftml](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) | 9.2  | 9.3| | | |
[事前トレーニング済みのモデル](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | 9.2 | 9.3| | | |
::: moniker-end

## <a name="how-component-upgrade-works"></a>コンポーネントのアップグレードのしくみ 

R と Python のライブラリおよび実行可能ファイルは、R と Python の既存のインストールを Machine Learning Server にバインドするときにアップグレードされます。 バインドは、R または Python の統合を持つ既存の SQL Server データベースエンジンインスタンスでセットアップを実行するときに、 [Microsoft Machine Learning Server インストーラー](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-install)によって実行されます。 セットアップによって既存の機能が検出され、Machine Learning Server に再バインドするように求めるメッセージが表示されます。 

バインド`C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\R_SERVICES`時に、と`\PYTHON_SERVICES`の内容は、および`\PYTHON_SERVER`の`C:\Program Files\Microsoft\ML Server\R_SERVER`新しい実行可能ファイルおよびライブラリで上書きされます。

同時に、サービスモデルも SQL Server の更新メカニズムから、Microsoft Machine Learning Server のより頻繁なメジャーおよびマイナーリリースサイクルにフリップされます。 サポートポリシーの切り替えは、ソリューションに新しい世代の R および Python モジュールを必要とするデータサイエンスチームにとって魅力的な選択肢です。 

+ バインドを使用しない場合、SQL Server Service Pack または累積的な更新プログラム (CU) をインストールすると、R および Python のパッケージにバグ修正のための修正プログラムが適用されます。 
+ バインドを使用すると、[最新のライフサイクルポリシー](https://support.microsoft.com/help/30881/modern-lifecycle-policy)と Microsoft Machine Learning Server のリリースで、CU リリーススケジュールとは独立して、新しいバージョンのパッケージをインスタンスに適用できます。 最新のライフサイクルサポートポリシーでは、より短い1年間の有効期間でより多くの更新が提供されます。 事後バインドでは、R と Python の今後の更新に MLS インストーラーを引き続き使用して、Microsoft ダウンロードサイトから入手できるようになります。

バインドは R および Python の機能にのみ適用されます。 つまり、R および Python 機能のオープンソースパッケージ (Microsoft R Open、Anaconda)、独自のパッケージ RevoScaleR、revoscalepy などがあります。 バインドでは、データベースエンジンインスタンスのサポートモデルは変更されず、SQL Server のバージョンは変更されません。

バインドは元に戻すことができます。 [インスタンスをバインド](#bkmk_Unbind)解除し、SQL Server データベースエンジンインスタンスを reparing することによって、SQL Server サービスに戻すことができます。

合計でのバインドの手順は次のとおりです。

+ SQL Server R Services または SQL Server Machine Learning Services の既存の構成済みインストールを使用して開始します。
+ アップグレードされたコンポーネントを使用する Microsoft Machine Learning Server のバージョンを確認します。
+ そのバージョンのセットアップをダウンロードして実行します。 セットアップでは、既存のインスタンスが検出され、バインドオプションが追加され、互換性のあるインスタンスの一覧が返されます。
+ バインドするインスタンスを選択し、セットアップを完了してバインドを実行します。

ユーザーエクスペリエンスの観点から、テクノロジとその使用方法は変更されていません。 唯一の違いは、新しいバージョンのパッケージが存在し、場合によっては SQL Server によって最初に使用できない追加のパッケージが存在することです。

## <a name="bkmk_BindWizard"></a>セットアップを使用して MLS にバインドする

Microsoft Machine Learning セットアップでは、既存の機能と SQL Server バージョンが検出され、SqlBindR .exe というユーティリティを呼び出してバインドが変更されます。 内部的には、SqlBindR はセットアップに連結され、間接的に使用されます。 後で、SqlBindR をコマンドラインから直接呼び出して、特定のオプションを実行することができます。

1. SSMS でを実行`SELECT @@version`して、サーバーが最小ビルド要件を満たしていることを確認します。 

   SQL Server 2016 R Services の場合、最小値は[Service Pack 1](https://www.microsoft.com/download/details.aspx?id=54276)と[CU3](https://support.microsoft.com/help/4019916/cumulative-update-3-for-sql-server-2016-sp1)です。

1. R base パッケージと RevoScaleR パッケージのバージョンを確認して、既存のバージョンが、それを置き換える予定よりも古いことを確認します。 

    ```sql
    EXECUTE sp_execute_external_script
    @language=N'R'
    ,@script = N'str(OutputDataSet);
    packagematrix <- installed.packages();
    Name <- packagematrix[,1];
    Version <- packagematrix[,3];
    OutputDataSet <- data.frame(Name, Version);'
    , @input_data_1 = N''
    WITH RESULT SETS ((PackageName nvarchar(250), PackageVersion nvarchar(max) ))
    ```

1. SSMS と、開いている接続を持つその他のツールを閉じて SQL Server します。 バインドは、プログラムファイルを上書きします。 SQL Server が開いているセッションがある場合、バインドエラーコード6でバインドが失敗します。

1. アップグレードするインスタンスがあるコンピューターに Microsoft Machine Learning Server をダウンロードします。 [最新バージョン](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-install#download-machine-learning-server-installer)をお勧めします。

1. フォルダーを解凍し、MLSWIN93 の下にある ServerSetup を起動します。

   ![Microsoft Machine Learning Server セットアップウィザード](media/mls-921-installer-start.PNG)

1. **[インストールの構成]** で、アップグレードするコンポーネントを確認し、互換性のあるインスタンスの一覧を確認します。 

   この手順は非常に重要です。

   左側で、保持またはアップグレードするすべての機能を選択します。 一部の機能をアップグレードすることはできません。 空のチェックボックスは、現在インストールされていることを前提として、その機能を削除します。 スクリーンショットでは、SQL Server 2016 R Services (MSSQL13.MSSQLSERVER) のインスタンスがある場合、R と、事前トレーニング済みのモデルの R バージョンが選択されています。 この構成は、SQL Server 2016 では R がサポートされますが Python はサポートされないため、有効です。

   SQL Server 2016 R Services のコンポーネントをアップグレードする場合は、Python 機能を選択しないでください。 SQL Server 2016 R Services に Python を追加することはできません。

   右側で、インスタンス名の横にあるチェックボックスをオンにします。 インスタンスが一覧に表示されない場合は、互換性のない組み合わせになっています。 インスタンスを選択しない場合は、Machine Learning Server の新しいスタンドアロンインストールが作成され、SQL Server ライブラリは変更されません。 インスタンスを選択できない場合は、 [SP1 CU3](https://support.microsoft.com/help/4019916/cumulative-update-3-for-sql-server-2016-sp1)にない可能性があります。 

    ![インストール手順の構成](media/mls-931-installer-mssql13.png)

1. **[使用許諾契約書]** ページで、[Machine Learning Server のライセンス条項に**同意する]** を選択します。 

1. 連続したページで、Microsoft R Open や Python Anaconda distribution など、選択したオープンソースコンポーネントの追加のライセンス条件に同意します。

1. **ほとんど**のページで、インストールフォルダーをメモしておきます。 既定のフォルダーは、Files\Microsoft\ML Server です。

    インストールフォルダーを変更する場合は、 **[詳細設定]** をクリックしてウィザードの最初のページに戻ります。 ただし、前の選択をすべて繰り返す必要があります。

インストールプロセス中に、SQL Server によって使用されている R または Python ライブラリはすべて置き換えられ、スタートパッドは新しいコンポーネントを使用するように更新されます。 このため、以前にインスタンスが既定の R_SERVICES フォルダー内でライブラリを使用していた場合、アップグレード後、これらのライブラリが削除され、スタートパッドサービスのプロパティが新しい場所にあるライブラリを使用するように変更されます。

バインドは、次のフォルダーの内容に影響します。C:\Program Server\MSSQL13. SQLMSSQLSERVER\R_SERVICES\library は、C:\Program Files\Microsoft\ML Server\r serverの内容に置き換えられます。 2番目のフォルダーとその内容は Microsoft Machine Learning Server セットアップによって作成されます。 

アップグレードに失敗した場合は、 [Sqlbindr のエラーコード](#sqlbindr-error-codes)で詳細を確認してください。

## <a name="confirm-binding"></a>バインドの確認

R と RevoScaleR のバージョンを再確認して、新しいバージョンがあることを確認します。 データベースエンジンインスタンスで R パッケージと共に配布された R コンソールを使用して、パッケージ情報を取得します。

```sql
EXECUTE sp_execute_external_script
@language=N'R'
,@script = N'str(OutputDataSet);
packagematrix <- installed.packages();
Name <- packagematrix[,1];
Version <- packagematrix[,3];
OutputDataSet <- data.frame(Name, Version);'
, @input_data_1 = N''
WITH RESULT SETS ((PackageName nvarchar(250), PackageVersion nvarchar(max) ))
```

Machine Learning Server 9.3 にバインドされている SQL Server 2016 R Services の場合、R Base package は3.4.3、RevoScaleR は9.3 である必要があります。また、Microsoft Ml 9.3 も必要です。 

事前トレーニング済みのモデルを追加した場合、モデルは Microsoft Ml ライブラリに埋め込まれ、Microsoft Ml の関数を使用して呼び出すことができます。 詳細については、「 [microsoft Azure ml の R サンプル](https://docs.microsoft.com/machine-learning-server/r/sample-microsoftml)」を参照してください。

## <a name="offline-binding-no-internet-access"></a>オフラインバインド (インターネットアクセスなし)

インターネットに接続されていないシステムでは、インストーラーと .cab ファイルをインターネットに接続されたコンピューターにダウンロードして、分離されたサーバーにファイルを転送できます。 

インストーラー (ServerSetup) には、Microsoft のパッケージ (RevoScaleR、Microsoft Ml、olapR、sqlRUtils) が含まれています。 .Cab ファイルには、他のコアコンポーネントが用意されています。 たとえば、"SRO" cab は R Open (Microsoft のオープンソース R の配布) を提供します。

次の手順では、オフラインインストール用のファイルを配置する方法について説明します。

1. MLS インストーラーをダウンロードします。 1つの zip 形式のファイルとしてダウンロードします。 [最新バージョン](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-install#download-machine-learning-server-installer)を使用することをお勧めしますが、[以前のバージョン](https://docs.microsoft.com/machine-learning-server/install/r-server-install-windows-offline#download-required-components)をインストールすることもできます。

1. .Cab ファイルをダウンロードします。 9\.3 リリースのリンクは次のとおりです。 以前のバージョンが必要な場合は、 [R Server 9.1](https://docs.microsoft.com/machine-learning-server/install/r-server-install-windows-offline#download-required-components)に追加のリンクがあります。 Python/Anaconda は SQL Server Machine Learning Services インスタンスにのみ追加できることを思い出してください。 事前トレーニング済みのモデルは、R と Python の両方に存在します.cab は、使用している言語でモデルを提供します。

    | 機能 | ダウンロード |
    |---------|----------|
    | R       | [SRO_3.4.3.0_1033.cab](https://go.microsoft.com/fwlink/?LinkId=867186&clcid=1033) |
    | Python  | [SPO_ 9.3.0.0 (_s)](https://go.microsoft.com/fwlink/?LinkId=859054) | 
    | トレーニング済みモデル | [MLM_9.3.0.0_1033.cab](https://go.microsoft.com/fwlink/?LinkId=859053) |

1. .Zip ファイルと .cab ファイルを対象サーバーに転送します。

1. サーバーで、Run コマンド`%temp%`を入力して、temp ディレクトリの物理的な場所を取得します。 物理パスはコンピューターによって異なりますが、 `C:\Users\<your-user-name>\AppData\Local\Temp`通常はです。

1. .Cab ファイルを% temp% フォルダーに配置します。

1. インストーラーを解凍します。

1. ServerSetup を実行し、画面の指示に従ってインストールを完了します。

## <a name="bkmk_BindCmd"></a>コマンドライン操作

Microsoft Machine Learning Server を実行すると、SqlBindR .exe というコマンドラインユーティリティが使用できるようになり、さらにバインド操作に使用できるようになります。 たとえば、バインドを逆にする場合は、セットアップを再実行するか、コマンドラインユーティリティを使用します。 また、このツールを使用して、インスタンスの互換性と可用性を確認することもできます。

> [!TIP]
> SqlBindR が見つかりませんか? セットアップを実行していない可能性があります。 SqlBindR は Machine Learning Server セットアップを実行した後にのみ使用できます。

1. 管理者としてコマンド プロンプトを開き、sqlbindr.exe を含むフォルダーに移動します。 既定の場所は C:\Program Files\Microsoft\MLServer\Setup です。

2. 次のコマンドを入力して、使用可能なインスタンスの一覧を表示します。 `SqlBindR.exe /list`
  
   一覧表示されている完全なインスタンス名をメモしておきます。 たとえば、インスタンス名は MSSQL14. になることがあります。既定のインスタンスの場合は MSSQLSERVER、SERVERNAME のようになります。MYNAMEDINSTANCE.

3. */Bind*引数を指定して**sqlbindr .exe**コマンドを実行し、前の手順で返されたインスタンス名を使用して、アップグレードするインスタンスの名前を指定します。

   たとえば、既定のインスタンスをアップグレードするには、次のように入力します。`SqlBindR.exe /bind MSSQL14.MSSQLSERVER`

4. アップグレードが完了したら、変更されたインスタンスに関連付けられているスタートパッドサービスを再起動します。

## <a name="bkmk_Unbind"></a>インスタンスを元に戻すまたはバインド解除する

バインドされたインスタンスを、SQL Server セットアップによって確立された R および Python コンポーネントの初期インストールに復元できます。 SQL Server サービスに戻すには、3つの部分があります。

+ [ステップ 1: Microsoft Machine Learning Server からのバインド解除](#step-1-unbind)
+ [手順 2:インスタンスを元の状態に復元する](#step-2-restore)
+ [ステップ 3:インストールに追加したパッケージを再インストールする](#step-3-reinstall-packages)

<a name="step-1-unbind"></a> 

### <a name="step-1-unbind"></a>手順 1:[バインドの解除]

バインドをロールバックするには、2つのオプションがあります。セットアップを再実行するか、SqlBindR コマンドラインユーティリティを使用します。

#### <a name="bkmk_wizunbind"></a>セットアップを使用してバインドを解除する

1. Machine Learning Server のインストーラーを見つけます。 インストーラーを削除した場合は、再度ダウンロードしたり、別のコンピューターからコピーしたりすることが必要になる場合があります。
2. バインドを解除するインスタンスがあるコンピューターでインストーラーを実行してください。
2. インストーラーによって、バインド解除の候補となるローカルインスタンスが識別されます。
3. 元の構成に戻すインスタンスの横にあるチェックボックスをオフにします。
4. ライセンス契約に同意します。 をインストールする場合でも、ライセンス条項への同意を示す必要があります。
5. **[完了]** をクリックします。 処理には時間がかかります。

#### <a name="bkmk_cmdunbind"></a>コマンドラインを使用したバインド解除

1. コマンド プロンプトを開き、前のセクションで説明したように **sqlbindr.exe** が含まれているフォルダーに移動します。

2. **SqlBindR.exe** コマンドを実行し、" */unbind*" 引数でインスタンスを指定します。

   たとえば、次のコマンドは、既定のインスタンスを元に戻します。
   
    `SqlBindR.exe /unbind MSSQL14.MSSQLSERVER`

<a name="step-2-restore"></a> 

###  <a name="step-2-repair-the-sql-server-instance"></a>手順 2:SQL Server インスタンスを修復する

SQL Server セットアップを実行して、R および Python の機能を備えたデータベースエンジンインスタンスを修復します。 既存の更新プログラムは保持されますが、R および Python パッケージへの SQL Server サービス更新プログラムを実行しなかった場合は、この手順によって修正プログラムが適用されます。

また、これはより多くの作業ですが、データベースエンジンインスタンスを完全にアンインストールして再インストールしてから、すべてのサービス更新プログラムを適用することもできます。

<a name="step-3-reinstall-packages"></a> 

### <a name="step-3-add-any-third-party-packages"></a>手順 3:サードパーティ製のパッケージを追加する

パッケージライブラリに、他のオープンソースまたはサードパーティのパッケージを追加した可能性があります。 バインドを逆にすると、既定のパッケージライブラリの場所が切り替わります。そのため、R および Python が現在使用しているライブラリにパッケージを再インストールする必要があります。 詳細については、「[既定のパッケージ](../package-management/default-packages.md)」、「[新しい R パッケージのインストール](../r/install-additional-r-packages-on-sql-server.md)」、および「[新しい Python パッケージのインストール](../python/install-additional-python-packages-on-sql-server.md)」を参照してください。

## <a name="sqlbindrexe-command-syntax"></a>SqlBindR .exe コマンドの構文

### <a name="usage"></a>使用方法

`sqlbindr [/list] [/bind <SQL_instance_ID>] [/unbind <SQL_instance_ID>]`

### <a name="parameters"></a>パラメーター

|名前|説明|
|------|------|
|*list*| 現在のコンピューター上にあるすべての SQL データベース インスタンスの ID を一覧表示します|
|*bind*| 指定された SQL データベース インスタンスを R Server の最新バージョンにアップグレードし、インスタンスが R Server の今後のアップグレードを自動的に取得するようにします|
|*unbind*|R Server の最新バージョンを指定した SQL データベース インスタンスからアンインストールし、R Server の今後のアップグレードがインスタンスに影響を与えないようにします|

<a name="sqlbindr-error-codes"><a/>

## <a name="binding-errors"></a>バインドエラー

MLS Installer と SqlBindR の両方で、次のエラーコードとメッセージが返されます。

|エラー コード  | メッセージ           | 詳細               |
|------------|-------------------|-----------------------|
|バインドエラー0 | Ok (成功) | バインドがエラーなしで渡されました。 |
|バインドエラー1 | 無効な引数 | 構文エラーです。 |
|バインドエラー2 | 無効なアクション | 構文エラーです。 |
|バインドエラー3 | 無効なインスタンス | インスタンスは存在しますが、バインドには無効です。 |
|バインドエラー4 | バインド不可能 | |
|バインドエラー5 | 既にバインド済み | *bind* コマンドを実行しましたが、指定したインスタンスは既にバインドされています。 |
|バインドエラー6 | バインドに失敗しました | インスタンスのバインド解除中にエラーが発生しました。 このエラーは、機能を選択せずに MLS インストーラーを実行した場合に発生する可能性があります。 バインドで SQL Server は、インスタンスが2017であると仮定して、MSSQL インスタンスと R および Python の両方を選択する必要があります。 このエラーは、SqlBindR が Program Files フォルダーに書き込むことができなかった場合にも発生します。 SQL Server するセッションまたはハンドルを開くと、このエラーが発生します。 このエラーが発生した場合は、新しいセッションを開始する前に、コンピューターを再起動し、バインドの手順をやり直してください。|
|バインドエラー7 | 非バインド | データベースエンジンのインスタンスには、R Services または SQL Server Machine Learning Services があります。 インスタンスが Microsoft Machine Learning Server にバインドされていません。 |
|バインドエラー8 | バインド解除失敗 | インスタンスのバインド解除中にエラーが発生しました。 |
|バインドエラー9 | No instances found (インスタンスが見つかりませんでした) | このコンピューターにはデータベースエンジンインスタンスが見つかりませんでした。 |

## <a name="known-issues"></a>既知の問題

ここでは、SqlBindR ユーティリティの使用に固有の問題、または SQL Server インスタンスに影響する可能性がある Machine Learning Server のアップグレードについて説明します。

### <a name="restoring-packages-that-were-previously-installed"></a>以前にインストールされたパッケージの復元

Microsoft R Server 9.0.1 にアップグレードした場合、そのバージョンの SqlBindR .exe のバージョンでは、元のパッケージまたは R コンポーネントを完全に復元できませんでした。そのためには、ユーザーがインスタンスで SQL Server 修復を実行し、すべてのサービスリリースを適用してから再起動する必要があります。インスタンス。

新しいバージョンの SqlBindR では、元の R 機能が自動的に復元されるため、R コンポーネントを再インストールしたり、サーバーを再適用したりする必要がなくなります。 ただし、初期インストール後に追加された可能性のある R パッケージの更新プログラムをインストールする必要があります。

パッケージ管理ロールを使用してパッケージをインストールして共有する場合、このタスクははるかに簡単です。 R コマンドを使用すると、データベース内のレコードを使用して、インストールされているパッケージをファイルシステムに同期できます。また、その逆も可能です。 詳細については、「 [R package management for SQL Server](../r/install-additional-r-packages-on-sql-server.md)」を参照してください。

### <a name="problems-with-multiple-upgrades-from-sql-server"></a>SQL Server からの複数のアップグレードに関する問題

以前に SQL Server 2016 R Services のインスタンスを9.0.1 にアップグレードした場合、Microsoft R Server 9.1.0 の新しいインストーラーを実行すると、すべての有効なインスタンスの一覧が表示され、既定では、以前にバインドされたインスタンスが選択されます。 続行すると、バインド済みのインスタンスはバインド解除されます。 その結果、関連するパッケージも含めて、以前の9.0.1 インストールが削除されますが、新しいバージョンの Microsoft R Server (9.1.0) はインストールされません。

この回避策として、既存の R Server のインストールを次のように変更できます。
1. コントロールパネルで、 **[プログラムの追加と削除]** を開きます。
2. Microsoft R Server を見つけて、 **[変更/変更]** をクリックします。
3. インストーラーが開始されたら、9.1.0 にバインドするインスタンスを選択します。

Microsoft Machine Learning Server 9.2.1 と9.3 には、この問題はありません。

### <a name="binding-or-unbinding-leaves-multiple-temporary-folders"></a>バインドまたはバインド解除によって複数の一時フォルダーが残される

場合によっては、バインドとバインド解除の操作によって一時フォルダーのクリーンアップに失敗することがあります。
次のような名前のフォルダーが見つかった場合は、インストールの完了後に削除できます。R_SERVICES_<guid>

> [!NOTE]
> インストールが完了するまでお待ちください。 1つのバージョンに関連付けられている R ライブラリを削除してから、新しい R ライブラリを追加するには、長い時間がかかることがあります。 操作が完了すると、一時フォルダーが削除されます。

## <a name="see-also"></a>関連項目

+ [Windows 用 Machine Learning Server のインストール (インターネット接続)](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-install)
+ [Windows 用 Machine Learning Server のインストール (オフライン)](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-windows-offline)
+ [Machine Learning Server の既知の問題](https://docs.microsoft.com/machine-learning-server/resources-known-issues)
+ [以前のリリースの R Server からの機能に関するお知らせ](https://docs.microsoft.com/r-server/whats-new-in-r-server)
+ [非推奨、廃止、または変更された機能](https://docs.microsoft.com/machine-learning-server/resources-deprecated-features)
