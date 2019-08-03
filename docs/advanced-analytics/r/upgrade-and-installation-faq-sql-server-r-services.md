---
title: アップグレードとインストールに関してよく寄せられる質問 (FAQ)
ms.custom: sqlseattle
ms.prod: sql
ms.technology: machine-learning
ms.date: 06/13/2019
ms.topic: conceptual
ms.author: davidph
author: dphansen
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: fe196a82badcab9ebe05004ee05cd67131942dd1
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715612"
---
# <a name="upgrade-and-installation-faq-for-sql-server-machine-learning-or-r-server"></a>SQL Server Machine Learning または R Server のアップグレードとインストールに関する FAQ
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

このトピックでは、SQL Server での機械学習機能のインストールに関してよく寄せられる質問への回答を示します。 また、アップグレードに関してよく寄せられる質問についても説明します。

+ 一部の問題は、プレリリース版からのアップグレードでのみ発生します。 そのため、これらのメモを読む前に、まずバージョンとエディションを識別することをお勧めします。 バージョン情報を取得するに`@@VERSION`は、SQL Server Management Studio からクエリを実行します。
+ 最新のリリースまたはサービスリリースにできるだけ早くアップグレードし、最近のリリースで修正された問題を解決してください。

**適用対象:** SQL Server 2016 R Services、SQL Server Machine Learning Services (データベース内)

## <a name="requirements-and-restrictions-on-older-versions-of-sql-server-2016"></a>以前のバージョンの SQL Server 2016 の要件と制限 

インストールする SQL Server のビルドによっては、次の制限事項が適用される場合があります。

- 以前のバージョンの SQL Server 2016 R Services では、作業ディレクトリを含むドライブに8dot3 表記が必要でした。 プレリリース版をインストールした場合は、SQL Server 2016 Service Pack 1 にアップグレードすると、この問題を解決する必要があります。 この要件は、SP1 以降のリリースには適用されません。

- 現時点では、を[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]フェールオーバークラスターにインストールすることはできません。 ただし、テスト環境でこの機能を評価する場合は、SQL Server 2019 preview によってフェールオーバーがサポートされます。 詳細については、「[新機能](../what-s-new-in-sql-server-machine-learning-services.md)」を参照してください。

- Azure VM では、追加の構成が必要になる場合があります。 たとえば、リモートアクセスをサポートするために、ファイアウォールの例外を作成する必要がある場合があります。

- 別のバージョンの R とのサイドバイサイドインストール、または回転分析からの他のリリースはサポートされていません。

- セットアップを開始する前に、ウイルスのスキャンを無効にします。 セットアップが完了したら、によって[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]使用されるフォルダーのウイルススキャンを中断することをお勧めします。 可能であれば、ツリー全体[!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)]のスキャンを中断します。

 - Windows Core にインストールされている SQL Server のインスタンスに Microsoft R Server をインストールする。 SQL Server 2016 の RTM バージョンでは、Windows Server Core エディションのインスタンスに Microsoft R Server を追加するときに既知の問題がありました。 この問題は修正されています。 この問題が発生した場合は、「 [KB3164398](https://support.microsoft.com/kb/3164398) 」に記載されている修正プログラムを適用して、Windows Server Core 上の既存のインスタンスに R 機能を追加することができます。 詳細については、 [「Can't install Microsoft R Server Standalone on a Windows Server Core operating system」](https://support.microsoft.com/kb/3168691)(Windows Server Core オペレーティング システムに Microsoft R Server (スタンドアロン) をインストールすることはできません) を参照してください。


## <a name="offline-installation-of-machine-learning-components-for-a-localized-version-of-sql-server-2016"></a>SQL Server 2016 のローカライズ版用の machine learning コンポーネントのオフラインインストール

SQL Server 2016 の初期リリースバージョンでは、インターネットに接続せずにオフラインセットアップ中にロケール固有の .cab ファイルをインストールできませんでした。 この問題は、今後のリリースで修正されましたが、正しい言語をインストールできないというメッセージがインストーラーから返された場合は、ファイル名を編集してセットアップを続行することができます。

+ インストーラーファイルを手動で編集して、正しい言語がインストールされていることを確認します。 たとえば、日本語版の SQL Server をインストールするには、ファイル名を SRS_ 8.0.3.0 _**1033**から srs_ 8.0.3.0 _**1041**に変更します。
+ Machine learning コンポーネントに使用する言語識別子は SQL Server セットアップ言語と同じである必要があります。または、セットアップを完了できません。

## <a name="pre-release-versions-support-policies-upgrade-and-known-issues"></a>プレリリース版: サポートポリシー、アップグレード、および既知の問題

の[!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)]プレリリース版の新規インストールはサポートされなくなりました。 プレリリース版を使用している場合は、できるだけ早くアップグレードしてください。

ここでは、特定のアップグレードシナリオの詳細な手順について説明します。

### <a name="how-to-upgrade-sql-server"></a>SQL Server をアップグレードする方法

セットアップウィザードを再実行して、SQL Server のバージョンをアップグレードできます。

+ [SQL Server のアップグレード](../../database-engine/install-windows/upgrade-sql-server.md)
+ [インストールウィザードを使用して SQL Server をアップグレードする](../../database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup.md)

バインドと呼ばれるプロセスを使用して、machine learning コンポーネントだけをアップグレードすることができます。 
+ [SqlBindR を使用して machine learning コンポーネントをアップグレードする](../install/upgrade-r-and-python.md)

### <a name="end-of-support-for-in-place-upgrades-from-prerelease-versions"></a>プレリリース版からのインプレースアップグレードのサポート終了

SQL Server 2016 のプレリリース版からのアップグレードはサポートされなくなりました。 これには SQL Server 2016 CTP3、CTP 3.1、CTP 3.2、RC0、または RC1 が含まれます。

SQL Server 2016 のプレリリース版では、次のバージョンがインストールされています。

| バージョン | ビルド         |
|---------|---------------|
| CTP 3.0 | 13.0.xxx      |
| CTP 3.1 | 13.0.801.12   |
| CTP 3.2 | 13.0.900.73   |
| CTP 3.3 | 13.0.1000.281 |
| RC1     | 13.0.1200.242 |
| RC2     | 13.0.1300.275 |
| RC3     | 13.0.1400.361 |

使用しているバージョンが不明な場合は、SQL Server Management Studio から`@@VERSION`クエリを実行します。

一般に、アップグレードのプロセスは次のとおりです。

1. スクリプトとデータをバックアップします。
2. プレリリース版をアンインストールします。
3. リリースバージョンをインストールします。

プレリリース版の SQL Server machine learning コンポーネントをアンインストールすることは複雑で、特殊なスクリプトを実行する必要がある場合があります。 ご購入元に問い合わせてください。

###  <a name="bkmk_Uninstall"></a>以前のバージョンの Microsoft R Server からアップグレードする前にアンインストールする

Microsoft R Server のプレリリース版がインストールされている場合は、それをアンインストールしてから新しいバージョンにアップグレードする必要があります。

1.  **[コントロール パネル]** を開き、 **[プログラムの追加と削除]** をクリックし、 `Microsoft SQL Server 2016 <version number>`を選択します。

2.  コンポーネントに対する **[追加]** 、 **[修復]** 、または **[削除]** オプションを含むダイアログ ボックスが表示されたら、 **[削除]** を選択します。
  
3.  **[機能の選択]** ページの **[共有機能]** で **[R Server (スタンドアロン)]** を選択します。 **[次へ]** をクリックし、 **[完了]** をクリックして、選択したコンポーネントのみをアンインストールします。

## <a name="r-services-and-r-server-standalone-side-by-side-errors"></a>R Services と R Server (スタンドアロン) のサイドバイサイドエラー 

以前のバージョンの SQL Server 2016 では、R Server (スタンドアロン) と R Services (データベース内) の両方を同時にインストールすると、セットアップが失敗し、"アクセスが拒否されました" というメッセージが表示されることがありました。 この問題は、SQL Server 2016 の Service Pack 1 で修正されました。

このエラーが発生した場合に、これらの機能をアップグレードする必要がある場合は、SQL Server 2016 SP1 のスリップストリームインストールを実行してください。 この問題を解決するには2つの方法があり、どちらもアンインストールと再インストールが必要です。

1. R Services (データベース内) をアンインストールし、SQLRUserGroup のユーザーアカウントが削除されていることを確認します。

2. サーバーを再起動し、R Server (スタンドアロン) を再インストールします。

3. セットアップをもう一度 SQL Server 実行し、今度は **[既存の SQL Server に機能を追加]** を選択します。

4. インスタンスを選択し、追加する**R Services (データベース内)** オプションを選択します。

この手順で問題を解決できない場合は、次の回避策を試してください。

1. R Services (データベース内) と R Server (スタンドアロン) を同時にアンインストールします。

2. ローカルユーザーアカウント (SQLRUserGroup) を削除します。

3. サーバーを再起動します。

4. SQL Server セットアップを実行し、R Services (データベース内) 機能のみを追加します。 **[R Server (スタンドアロン)]** を選択しないでください。

一般に、R Services (データベース内) と R Server (スタンドアロン) の両方を同じコンピューターにインストールしないことをお勧めします。 ただし、サーバーに十分な容量があることを前提として、R Server のスタンドアロンが開発ツールとして役に立つ場合があります。 もう1つのシナリオとしては、R Server の運用化機能を使用する必要がありますが、データを移動せずに SQL Server のデータにアクセスすることもできます。

## <a name="incompatible-version-of-r-client-and-r-server"></a>R Client および R Server の互換性のないバージョン

Microsoft R Client をインストールし、それを使用してリモートの SQL Server 計算コンテキストで R を実行すると、次のようなエラーが表示されることがあります。

*コンピューターでバージョン9.0.0 の Microsoft R client を実行していますが、これは Microsoft R Server バージョン8.0.3 と互換性がありません。互換性のあるバージョンをダウンロードしてインストールしてください。*

SQL Server 2016 では、で実行されていた R のバージョン SQL Server R Services、Microsoft R Client のライブラリとまったく同じである必要がありました。 この要件は、以降のバージョンでは削除されています。 ただし、常に最新バージョンの machine learning コンポーネントを取得し、すべてのサービスパックをインストールすることをお勧めします。 

以前のバージョンの Microsoft R Server があり、Microsoft R Client 9.0.0 との互換性を確保する必要がある場合は、この[サポート記事](https://support.microsoft.com/kb/3210262)で説明されている更新プログラムをインストールしてください。


## <a name="installation-fails-with-error-only-one-revolution-enterprise-product-can-be-installed-at-a-time"></a>インストールに失敗し "Revolution Enterprise 製品は一度に 1 つしかインストールできません" というエラーが表示される

このエラーは、以前の Revolution Analytics 製品またはプレリリース版の SQL Server R Services がインストールされている場合に発生する可能性があります。 以前のバージョンが存在する場合は、そのアンインストールを行ってから、Microsoft R Server の新しいバージョンをインストールしてください。 他のバージョンの Revolution Enterprise ツールとのサイド バイ サイド インストールはサポートされていません。

ただし、サイド バイ サイド インストールは、R Server (スタンドアロン) を [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] または SQL Server 2016 と共に使用する場合はサポートされます。

## <a name="registry-cleanup-to-uninstall-older-components"></a>古いコンポーネントをアンインストールするレジストリのクリーンアップ

以前のバージョンの削除時に問題が発生する場合は、レジストリを編集して関連するキーを削除することが必要な場合があります。

> [!IMPORTANT]
> この問題は、プレリリース版の Microsoft R Server または CTP バージョンの SQL Server 2016 R Services がインストールされている場合にのみ発生します。
  
1. Windows レジストリを開き、 `HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall`キーを見つけます。
2. 次のエントリのいずれかが存在し、キーに値 `sEstimatedSize2`のみが含まれる場合は、該当するエントリを削除します。
  
    -   E0B2C29E-B8FC-490B-A043-2CAE75634972        (8.0.2 の場合)
  
    -   46695879-954E-4072-9D32-1CC84D4158F4        (8.0.1 の場合)
  
    -   2DF16DF8-A2DB-4EC6-808B-CB5A302DA91B        (8.0.0 の場合)
  
    -   5A2A1571-B8CD-4AAF-9303-8DF463DABE5A        (7.5.0 の場合)

## <a name="see-also"></a>関連項目

 [SQL Server Machine Learning Services (データベース内)](../r/sql-server-r-services.md)

 [SQL Server Machine Learning Server (スタンドアロン)](../r/r-server-standalone.md)
