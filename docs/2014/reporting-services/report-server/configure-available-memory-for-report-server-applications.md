---
title: レポート サーバー アプリケーションで利用可能なメモリの構成 | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology:
- reporting-services-native
ms.topic: conceptual
helpviewer_keywords:
- memory [Reporting Services]
- memory thresholds [Reporting Services]
ms.assetid: ac7ab037-300c-499d-89d4-756f8d8e99f6
author: markingmyname
ms.author: maghan
manager: craigg
ms.openlocfilehash: 9006ff1f237c42be123c3937270a4bdf5e915c6d
ms.sourcegitcommit: 3da2edf82763852cff6772a1a282ace3034b4936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48172812"
---
# <a name="configure-available-memory-for-report-server-applications"></a>レポート サーバー アプリケーションで利用可能なメモリの構成
  [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] は、利用可能なすべてのメモリを使用できますが、[!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] サーバー アプリケーションに割り当てることのできる合計メモリ リソース量に上限を設けることによって、既定の動作をオーバーライドすることができます。 また、しきい値を設定することにより、メモリの圧迫度 (低、中、高) に応じて、要求の優先度付けや処理の方法を変えることもできます。 メモリ圧迫の度合いが低い場合、レポート サーバーは、対話型のレポート処理またはオンデマンドのレポート処理に若干高い優先度を割り当てます。 メモリ圧迫の度合いが高い場合、レポート サーバーは、さまざまな手法を用いながら、利用できる限られたリソースで稼働状態を保ちます。  
  
 このトピックでは、指定できる構成設定について取り上げ、さらに、要求の処理に影響するメモリ不足が発生した場合に、サーバーがどのように対処するかについて説明します。  
  
## <a name="memory-management-policies"></a>メモリ管理ポリシー  
 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] は、特定のアプリケーションや処理要求の種類に対して割り当て可能なメモリ量を調整することによってシステム リソースの制約に対処します。 レポート サーバー サービスとして実行され、メモリ管理の対象となるアプリケーションには、次のものがあります。  
  
-   レポート マネージャー (レポート サーバーの Web フロントエンド アプリケーション)  
  
-   レポート サーバー Web サービス (対話型のレポート処理およびオンデマンドの要求用)  
  
-   バックグラウンド処理アプリケーション (定期的なレポート処理、サブスクリプション配信、およびデータベース メンテナンス用)  
  
 メモリ管理ポリシーは、プロセス内の個々のアプリケーションに対してではなく、レポート サーバー サービス全体に対して適用されます。  
  
 システムでメモリ不足が生じていなければ、各サーバー アプリケーションは、要求を受け取る前に、あらかじめ、ある程度のメモリを起動時に確保するため、実際に要求を受け取ったときには最大限のパフォーマンスで動作します。 メモリが不足してくると、レポート サーバーは、次の表に示すように、そのプロセス モデルを調整します。  
  
|メモリ不足|サーバーの対処|  
|---------------------|---------------------|  
|Low|引き続き現在の要求を処理します。 ほとんどの場合、新しい要求が受け付けられます。 バックグラウンド処理アプリケーションに送られる要求には、レポート サーバー Web サービスに送られる要求よりも低い優先度が割り当てられます。|  
|Medium|引き続き現在の要求を処理します。 通常、新しい要求が受け付けられます。 バックグラウンド処理アプリケーションに送られる要求には、レポート サーバー Web サービスに送られる要求よりも低い優先度が割り当てられます。 3 つのサーバー アプリケーションに対して割り当てられるメモリはすべて減らされます。また、Web サービス要求用にできるだけ多くのメモリを確保するため、バックグラウンド処理アプリケーションに割り当てられるメモリは、他のアプリケーションよりも大きく削減されます。|  
|高|メモリ割り当てがさらに削減されます。 多くのメモリを要求するサーバー アプリケーションは拒否されます。 現在の要求はスローダウンされ、完了までにより多くの時間がかかるようになります。 新しい要求は受け付けられません。 レポート サーバーは、メモリ内のデータ ファイルをディスクにスワップします。<br /><br /> メモリ制約が深刻化し、新しい要求を処理できるだけのメモリが存在しない場合、現在の要求が完了するまでの間、レポート サーバーは HTTP 503 (サーバー利用不可) エラーを返します。 場合によっては、メモリ不足を直ちに解消するために、アプリケーション ドメインがリサイクルされることもあります。|  
  
 メモリ不足に対するレポート サーバーの対処をシナリオごとにカスタマイズすることはできませんが、メモリの圧迫度 (高、中、低) に応じた対処を構成設定で指定することはできます。  
  
## <a name="when-to-customize-memory-management-settings"></a>メモリ管理設定をカスタマイズするタイミング  
 メモリ圧迫度を低、中、高という 3 つの範囲に分けたとき、既定の設定では、その範囲が均等にはなっていません。 既定では、低メモリ圧迫度ゾーンの方が、中と高のメモリ圧迫度ゾーンよりも大きくとられています。 これは、処理負荷が均等に分布する場合や一定の割合で増減する場合に最適な構成です。 このシナリオでは、ゾーンからゾーンへの遷移が段階的であるため、レポート サーバーはその対処を余裕を持って調整できます。  
  
 負荷が急激に増加するような場合は、既定の設定を変更する必要があります。 処理負荷の急激な増加が発生した場合、メモリ不足がまったく生じていない状態から、メモリ割り当てエラーの状態へと、きわめて短時間の間に遷移することが考えられます。 たとえば、メモリを激しく消費するレポートの複数のインスタンスが同時に開始されて実行された場合などが該当します。 この種の処理負荷に対処するためには、メモリ消費が中レベルまたは高レベルに達したことをレポート サーバーに認識させ、できるだけ早期に処理をスローダウンさせる必要があります。 こうすることで、より多くの要求を完了させることができます。 そのためには、`MemorySafetyMargin` の値を低く設定して、低メモリ圧迫度ゾーンを他のゾーンよりも小さくする必要があります。 これにより、中～高レベルのメモリ圧迫が発生した場合の対応を早めることができます。  
  
## <a name="configuration-settings-for-memory-management"></a>メモリ管理の構成設定  
 レポート サーバーのメモリ割り当てを制御する構成設定を含める`WorkingSetMaximum`、 `WorkingSetMinimum`、 `MemorySafetyMargin`、および`MemoryThreshold`します。  
  
-   `WorkingSetMaximum` `WorkingSetMinimum`使用可能なメモリの範囲を定義します。 これらを構成することで、レポート サーバー アプリケーションに使用可能なメモリの範囲を設定できます。 たとえば、同じコンピューターで複数のアプリケーションをホストしているとき、レポート サーバーに対し、同じコンピューター上の他のアプリケーションとは異なる割合で、システム リソースを使用させることもできます。  
  
-   `MemorySafetyMargin` `MemoryThreshold`低、中、高レベルのメモリ不足の境界を設定します。 各状態の[!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)]コンピューターで使用可能なメモリの量に応じて、レポート処理やその他の要求を確実に必要な是正アクションが適切に処理されます。 低、中、高の各圧迫度を評価する際の基準を構成設定で指定できます。  
  
     構成設定を変更することはできますが、変更したからといってレポート処理のパフォーマンスが向上するわけではありません。 構成設定の変更によってパフォーマンスが向上するのは、要求が完了前に破棄された場合だけです。 サーバーのパフォーマンスを向上させる最適な方法は、レポート サーバーまたは個々のレポート サーバー アプリケーションを専用のコンピューターに配置することです。  
  
 次の図は、メモリの圧迫度を低、中、高に分ける際に使用される設定の組み合わせを示しています。  
  
 ![メモリの状態の構成設定](../media/rs-memoryconfigurationzones.gif "メモリの状態の構成設定")  
  
 次の表`WorkingSetMaximum`、 `WorkingSetMinimum`、 `MemorySafetyMargin`、および`MemoryThreshold`設定します。 構成設定で指定されます、 [RSReportServer.config ファイル](rsreportserver-config-configuration-file.md)します。  
  
|要素|説明|  
|-------------|-----------------|  
|`WorkingSetMaximum`|メモリのしきい値を指定します。この値を超えた場合、レポート サーバー アプリケーションに対する、新しいメモリ割り当て要求は許可されません。<br /><br /> 既定では、コンピューターで使用可能なメモリ量が `WorkingSetMaximum` として設定されます。 この値は、サービスの開始時に検出されます。<br /><br /> この設定は、手動で追加しない限り、RSReportServer.config ファイルには存在しません。 レポート サーバーによって使用されるメモリ量を少なくしたい場合は、RSReportServer.config ファイルにこの要素と値を追加します。 有効な値は、0 から整数型の最大値までです。 この値はキロバイト単位で指定します。<br /><br /> `WorkingSetMaximum` の値に達すると、レポート サーバーは新しい要求を受け付けなくなります。 現在実行中の要求は最後まで実行されます。 メモリ使用量がで指定された値を下回った場合にのみ、新しい要求が受け付けられます`WorkingSetMaximum`します。<br /><br /> 既存の要求も後に追加のメモリを消費する場合、`WorkingSetMaximum`値に達している、すべてのレポート サーバー アプリケーション ドメインがリサイクルされます。 詳細については、「 [Application Domains for Report Server Applications](application-domains-for-report-server-applications.md)」を参照してください。|  
|`WorkingSetMinimum`|リソース消費の下限を指定します。全体的なメモリ使用量がこの値を下回っている場合、レポート サーバーはメモリを解放しません。<br /><br /> 既定では、この値がサービスの開始時に計算されます。 初回メモリ割り当て要求時に `WorkingSetMaximum` の 60% として計算されます。<br /><br /> この設定は、手動で追加しない限り、RSReportServer.config ファイルには存在しません。 この値をカスタマイズするかどうか、追加する必要あります、`WorkingSetMinimum`要素を RSReportServer.config ファイルにします。 有効な値は、0 から整数型の最大値までです。 この値はキロバイト単位で指定します。|  
|`MemoryThreshold`|割合を指定します`WorkingSetMaximum`圧力が高、中規模のシナリオとの間の境界を定義します。 レポート サーバーは、メモリの使用状況がこの値に達した場合、要求の処理速度を落とし、別のサーバー アプリケーションに対して割り当てられるメモリ量を変更します。 既定値は 90 です。 この値が設定された値を超える必要がある`MemorySafetyMargin`します。|  
|`MemorySafetyMargin`|`WorkingSetMaximum` を上限として、メモリ圧迫の度合いを中レベルと低レベルに分けたとき、その境界を定義するパーセンテージを指定します。 メモリには、システム用に予約され、レポート サーバーの処理には使用できない領域も存在します。この値は、使用可能なメモリに対するパーセンテージを示します。 既定値は 80 です。|  
  
> [!NOTE]  
>  `MemoryLimit` および `MaximumMemoryLimit` の設定は、[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 以降のバージョンでは廃止されています。 既存のインストールをアップグレードした場合、または、該当する設定を含んだ RSReportServer.config ファイルを使用している場合、これらの値は読み取られません。  
  
#### <a name="example-of-memory-configuration-settings"></a>メモリの構成設定の例  
 次の例は、カスタム メモリ構成値を使用したレポート サーバー コンピューターの構成設定を示しています。 追加する場合`WorkingSetMaximum`または`WorkingSetMinimum`、RSReportServer.config ファイルで、要素と値を入力する必要があります。 どちらの値も、サーバー アプリケーションに割り当てる RAM のサイズ (キロバイト単位) を整数で指定します。 次の例では、レポート サーバー アプリケーションに対するメモリの合計割当量が 4 GB を超えないように指定しています。 `WorkingSetMinimum` の既定値 (`WorkingSetMaximum` の 60%) を使用する場合は、そちらを省略し、RSReportServer.config ファイルには `WorkingSetMaximum` だけを指定するということでもかまいません。 この例では、実際にどのように追加すればよいかを示すために、`WorkingSetMinimum` を省略せずに記載しています。  
  
```  
      <MemorySafetyMargin>80</MemorySafetyMargin>  
      <MemoryThreshold>90</MemoryThreshold>  
      <WorkingSetMaximum>4000000</WorkingSetMaximum>  
      <WorkingSetMinimum>2400000</WorkingSetMinimum>  
```  
  
#### <a name="about-aspnet-memory-configuration-settings"></a>ASP.NET のメモリの構成設定について  
 レポート サーバー Web サービスとレポート マネージャーは[!INCLUDE[vstecasp](../../../includes/vstecasp-md.md)]メモリ構成設定で指定したアプリケーションでは、どちらのアプリケーション応答、`processModel`用の machine.config のセクション[!INCLUDE[vstecasp](../../../includes/vstecasp-md.md)]アプリケーションをIIS 5.0 互換モードで実行します。 [!INCLUDE[ssRSnoversion](../../../includes/ssrsnoversion-md.md)] RSReportServer.config ファイルのみからのメモリ構成設定を読み取ります。  
  
## <a name="see-also"></a>参照  
 [RSReportServer 構成ファイル](rsreportserver-config-configuration-file.md)   
 [RSReportServer 構成ファイル](rsreportserver-config-configuration-file.md)   
 [Reporting Services の構成ファイル &#40;RSreportserver.config&#41; の変更](modify-a-reporting-services-configuration-file-rsreportserver-config.md)   
 [レポート サーバー アプリケーションのアプリケーション ドメイン](application-domains-for-report-server-applications.md)  
  
  
