---
description: タスク ホスト コンテナー
title: タスク ホスト コンテナー | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.dts.designer.taskhostcontainer.f1
helpviewer_keywords:
- containers [Integration Services], Task Host
- Task Host container
ms.assetid: 7394a2c2-1b07-427d-b94a-9792e7783d35
author: chugugrace
ms.author: chugu
ms.openlocfilehash: fd2ab8fe157992077618f426f68931c8c87d8ed7
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88496000"
---
# <a name="task-host-container"></a>タスク ホスト コンテナー

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  タスク ホスト コンテナーは、1 つのタスクをカプセル化します。 [!INCLUDE[ssIS](../../includes/ssis-md.md)] デザイナーでは、タスク ホストは個別には構成されず、カプセル化するタスクのプロパティを設定する際に構成されます。 タスク ホスト コンテナーがカプセル化するタスクの詳細については、「 [Integration Services タスク](../../integration-services/control-flow/integration-services-tasks.md)」を参照してください。  
  
 タスク ホスト コンテナーは、変数とイベント ハンドラーの使用を、タスク レベルに拡張します。 詳細については、「[Integration Services (SSIS) のイベント ハンドラー](../../integration-services/integration-services-ssis-event-handlers.md)」および「[Integration Services (SSIS) の変数](../../integration-services/integration-services-ssis-variables.md)」を参照してください。  
  
## <a name="configuration-of-the-task-host"></a>タスク ホストの構成  
 プロパティを設定するには、 **の** [プロパティ] [!INCLUDE[ssBIDevStudioFull](../../includes/ssbidevstudiofull-md.md)] ウィンドウで行うか、またはプログラムによって設定します。  
  
 [!INCLUDE[ssBIDevStudioFull](../../includes/ssbidevstudiofull-md.md)]でのこれらのプロパティの設定については、「 [タスクまたはコンテナーのプロパティを設定する](https://msdn.microsoft.com/library/52d47ca4-fb8c-493d-8b2b-48bb269f859b)」を参照してください。  
  
 プログラムによってこれらのプロパティを設定する方法については、 <xref:Microsoft.SqlServer.Dts.Runtime.TaskHost>」を参照してください。  
  
## <a name="related-tasks"></a>関連タスク  
  
-   [タスクまたはコンテナーのプロパティを設定する](https://msdn.microsoft.com/library/52d47ca4-fb8c-493d-8b2b-48bb269f859b)  
  
## <a name="see-also"></a>参照  
 [Integration Services コンテナー](../../integration-services/control-flow/integration-services-containers.md)  
  
  
