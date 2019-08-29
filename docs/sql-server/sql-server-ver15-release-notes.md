---
title: SQL Server 2019 リリース ノート | Microsoft Docs
ms.date: 08/21/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: release-landing
ms.topic: article
ms.assetid: 13942af8-5a40-4cef-80f5-918386767a47
author: MikeRayMSFT
ms.author: mikeray
monikerRange: = sql-server-ver15 || = sqlallproducts-allversions
ms.openlocfilehash: d9d6f1f0bdf1a0e38bf26fdc18bc91c5825ca412
ms.sourcegitcommit: 5e838bdf705136f34d4d8b622740b0e643cb8d96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69653045"
---
# <a name="sql-server-2019-preview-release-notes"></a>SQL Server 2019 プレビュー リリース ノート
[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

この記事では、[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] の制限事項と既知の問題について説明します。 関連情報については、次を参照してください。

>[SQL Server 2019 の新機能](../sql-server/what-s-new-in-sql-server-ver15.md)

>[!NOTE]
>内容は [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] リリース候補に関するものです。 リリース候補はプレリリース版ソフトウェアです。 情報は変更される可能性があります。 サポートのシナリオについては、「[サポート](#support)」を参照してください。

## <a name="includesql-server-2019includessssqlv15-mdmd-release-candidate-rc"></a>[!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] リリース候補 (RC)

[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] RC は、[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] の最新のパブリック リリースです。

[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] RC は、評価版としてのみ使用可能です。 他のエディションは提供されません。

リリース候補ソフトウェアのサポートとライセンスに関するすべての詳細情報については、インストール メディアの `license_Eval.rtf` に記載されています。

[!INCLUDE[ctp-support-exclusion](../includes/ctp-support-exclusion.md)]

## <a name="documentation"></a>ドキュメント

- **問題およびユーザーへの影響**:SQL Server 2019 (15.x) のドキュメントは制限されており、コンテンツは [!INCLUDE[ssSQL17](../includes/sssql17-md.md)] ドキュメント セットに付属しています。 記事の中で SQL Server 2019 (15.x) に固有の内容は、**適用対象**で示されています。

- **問題およびユーザーへの影響**: [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]ドキュメントはバージョンによってフィルター処理できます。 各ドキュメント ページの左上にあるコントロールを使用し、要件に応じてフィルター処理してください。

- **問題およびユーザーへの影響**:SQL Server 2019 (15.x) には、オフライン コンテンツがありません。

## <a name="hardware-and-software-requirements"></a>ハードウェアとソフトウェアの要件

- **問題およびユーザーへの影響**:ハードウェア要件とソフトウェア要件は現在レビュー段階であり、製品リリース用の最終版ではありません。

  - **ハードウェア**
    - [Windows - プロセッサ、メモリ、およびオペレーティング システムの要件](../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md#pmosr)
    - [Linux - システム要件](../linux/sql-server-linux-setup.md#system)
  - **ソフトウェア**
    - Windows Server 2016 以降。 その他の要件については、[SQL Server のインストール要件](../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)に関する記事をご覧ください
    - Microsoft .NET Framework 4.6.2。 [ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53344)から入手できます。
    - Linux については、[サポートされている Linux プラットフォーム](../linux/sql-server-linux-setup.md#supportedplatforms)に関する記事をご覧ください

## <a name = "release-notes"></a>サポートから除外された機能

- **問題とお客様への影響**: [!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] では、次のコンポーネント、機能、シナリオのサポートが除外されています。
  - SQL Server Analysis Services (SQL Server Analysis Services)
  - SQL Server Reporting Services (SQL Server Reporting Services)
  - Kubernetes での Always On 可用性グループ

- **回避策**:[なし] : 除外は、SQL 早期導入者プログラムの参加者を含む、すべてのお客様に適用されます。

- **適用対象**:リリース候補

## <a name="updated-compiler"></a>更新されたコンパイラ

- **問題およびユーザーへの影響**: [!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] は更新されたコンパイラで構築されています。 コンパイラの更新の結果、CTP 2.1 には浮動小数点数の結果と他の変換シナリオで、前のバージョンとは異なる値が返される場合があるという既知の問題がありました。 CTP 2.2 には、影響のあるシナリオで [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] の前のバージョンと同じ結果が返されるようにする作業が含まれています。 リリース候補リリースでは、もう問題は解決されたと考えています。 [!INCLUDE[ss2017](../includes/sssqlv14-md.md)] と比較した結果に異常がある場合、[[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] チーム](https://aka.ms/sqlfeedback)にすぐに報告してください。

- **回避策**:なし

- **適用対象**:リリース候補

## <a name="installation-wizard-may-wait-between-eula-pages"></a>インストール ウィザードが使用許諾契約ページの間で待機状態になることがある

- **問題およびユーザーへの影響**:インストール ウィザードでのインストールの間に、R Services の使用許諾契約 (EULA) と Python の EULA の間で、プロセスが長時間待機状態になることがあります。

- **回避策**:インストール ウィザードが動くまで待ちます。 待つ時間は 30 分を超える可能性があります。

- **適用対象**:[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] CTP 3.0

## <a name="utf-8-collations"></a>UTF-8 対応の照合順序

- **問題およびユーザーへの影響**:UTF-8 対応の照合順序は、他の [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 機能とは併用できない場合があります。 UTF-8 は、次の [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 機能が使用されている場合にはサポートされません。

  - インメモリ OLTP
  - PolyBase 用の外部テーブル ([!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] RC 1)
  - Always Encrypted ([!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] RC 1 以前)
  - リンク サーバー ([!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] CTP 3.2 以前)

  > [!Note]
  > Azure Data Studio および SQL Server Data Tools (SSDT) では、UTF-8 対応の照合順序を選択するための UI は現在のところサポートされていません。 最新の [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] (SSMS) バージョン 18 では、UTF-8 対応の照合順序を UI で選択することができます。
 
- **回避策**:[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] CTP には回避策はありません。


- **適用対象**:すべての CTP リリース。

## <a name="always-encrypted-with-secure-enclaves"></a>セキュア エンクレーブを使用する Always Encrypted

- **問題およびユーザーへの影響**:高度な計算がパフォーマンス最適化とエラー処理拡張の妨げとなっており、現在は既定で無効になっています。

- **回避策**:高度な計算を有効にするには、`DBCC traceon(127,-1)` を実行します。 詳細については、[高度な計算の有効化](../relational-databases/security/encryption/configure-always-encrypted-enclaves.md#configure-a-secure-enclave)に関する記事をご覧ください。

- **適用対象**:[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] CTP 3.2、CTP 3.1

## <a name="sql-server-configuration-manager-may-not-start"></a>SQL Server 構成マネージャーが起動しない場合がある

- **問題およびユーザーへの影響**:SQL Server 構成マネージャー (SSCM) は、VCRuntime 140 がないマシンでは起動しません。 SSCM を開始するときに、次のダイアログ ボックスが表示される場合があります。 


  `MMC could not create the snap-in. The snap-in might not have been installed correctly.`

- **回避策**:最新の VC ランタイム 2013 (x86) をインストールします。

  - [詳細](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
  - [直接](https://support.microsoft.com/en-us/help/4032938/update-for-visual-c-2013-redistributable-package)

- **適用対象**:[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] CTP 3.1、CTP 3.0、CTP 2.5。

## <a name="always-on-availability-group-kubernetes-operator-not-supported"></a>Always On 可用性グループの Kubernetes 演算子はサポートされていない

- **問題およびユーザーへの影響**:Always On 可用性グループの Kubernetes 演算子は、このリリース候補ではサポートされておらず、RTM では使用できません。 

- **回避策**:なし

- **適用対象**:[!INCLUDE[SQL Server 2019](../includes/sssqlv15-md.md)] リリース候補

[!INCLUDE[get-help-options-msft-only](../includes/paragraph-content/get-help-options.md)]

![MS_Logo_X-Small](../sql-server/media/ms-logo-x-small.png)
