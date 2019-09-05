---
title: フェールオーバー クラスター インスタンスの操作 - SQL Server on Linux
description: この記事では、Linux で SQL Server フェールオーバー クラスター インスタンス (FCI) を操作する方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: vanto
ms.date: 08/28/2017
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: ''
ms.openlocfilehash: 0da3a3225e3ef47bd4a38d1ccbcc2d074d543a55
ms.sourcegitcommit: 5e45cc444cfa0345901ca00ab2262c71ba3fd7c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70154576"
---
# <a name="operate-failover-cluster-instance---sql-server-on-linux"></a>フェールオーバー クラスター インスタンスの操作 - SQL Server on Linux

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-linuxonly](../includes/appliesto-ss-xxxx-xxxx-xxx-md-linuxonly.md)]

この記事では、Linux で SQL Server フェールオーバー クラスター インスタンス (FCI) を操作する方法について説明します。 Linux に SQL Server FCI を作成していない場合は、「[フェールオーバー クラスター インスタンスの構成 - SQL Server on Linux](sql-server-linux-shared-disk-cluster-configure.md)」をご覧ください。 

## <a name="failover"></a>[フェールオーバー]

FCI のフェールオーバーは、Windows Server フェールオーバー クラスター (WSFC) に似ています。 FCI がホストされているクラスター ノードで何らかの障害が発生した場合、FCI を自動的に別のノードにフェールオーバーする必要があります。 WSFC とは異なり、優先所有者を設定する方法がないため、Pacemaker によって FCI の新しいホストとなるノードが選択されます。

FCI を別のノードに手動でフェールオーバーすることが必要になる場合があります。 そのプロセスは、WSFC 上の FCI と同じではありません。 WSFC では、ロール レベルでリソースをフェールオーバーします。 Pacemaker では、ユーザーが移動するリソースを選択し、すべての制約が正しいと仮定すると、他のすべてのものも同様に移動されます。 

フェールオーバーの方法は、Linux のディストリビューションによって異なります。 お使いの Linux ディストリビューションの指示に従ってください。

- [RHEL または Ubuntu](#manual-failover-rhel-or-ubuntu)
- [SLES](#manual-failover-sles)

## <a name="manual-failover-rhel-or-ubuntu"></a>手動フェールオーバー (RHEL または Ubuntu)

手動フェールオーバーを実行するには、Red Hat Enterprise Linux (RHEL) サーバーまたは Ubuntu サーバーで、次の手順を実行します。
1.  次のコマンドを実行します。 

   ```bash
   sudo pcs resource move <FCIResourceName> <NewHostNode> 
   ```

   \<FCIResourceName> は、SQL Server FCI の Pacemaker リソース名です。

   \<NewHostNode> は、FCI をホストするクラスター ノードの名前です。 

   確認は表示されません。

2.  手動フェールオーバーの間に、Pacemaker によって、手動で移動するように選択したリソースに対して場所の制約が作成されます。 この制約を見るには、`sudo pcs constraint` を実行します。

3.  フェールオーバーが完了した後、`sudo pcs resource clear <FCIResourceName>` を発行して制約を削除します。 

\<FCIResourceName> は、FCI の Pacemaker リソース名です。 

## <a name="manual-failover-sles"></a>手動フェールオーバー (SLES)


Suse Linux Enterprise Server (SLES) では、SQL Server FCI を手動でフェールオーバーするには、`migrate` コマンドを使います。 例:

```bash
crm resource migrate <FCIResourceName> <NewHostNode>
```

\<FCIResourceName> は、フェールオーバー クラスター インスタンスのリソース名です。 

\<NewHostNode> は、新しいフェールオーバー先ホストの名前です。 


<!---

|Distribution |Topic 
|----- |-----
|**Red Hat Enterprise Linux with HA add-on** |[Configure](sql-server-linux-shared-disk-cluster-red-hat-7-configure.md)<br/>[Operate](sql-server-linux-shared-disk-cluster-red-hat-7-operate.md)
|**SUSE Linux Enterprise Server with HA add-on** |[Configure](sql-server-linux-shared-disk-cluster-sles-configure.md)

--->

## <a name="next-steps"></a>Next Steps

- [フェールオーバー クラスター インスタンスの構成 - SQL Server on Linux](sql-server-linux-shared-disk-cluster-configure.md)

<!--Image references-->
