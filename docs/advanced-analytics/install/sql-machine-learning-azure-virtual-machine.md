---
title: Azure の仮想マシンに R 言語と Python の機能をインストールする
description: Azure クラウドの SQL Server 仮想マシンで、R と Python のデータサイエンスソリューションと機械学習ソリューションを実行できます。
ms.prod: sql
ms.technology: machine-learning
ms.date: 11/09/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2017||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: b7aa37c3ec72390d76ecf9e939916f9641187956
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715882"
---
# <a name="install-sql-server-machine-learning-services-with-r-and-python-on-an-azure-virtual-machine"></a>Azure 仮想マシンに R と Python を使用して SQL Server Machine Learning Services をインストールする
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

Azure の SQL Server 仮想マシンに Machine Learning Services との R および Python 統合をインストールすると、インストールと構成のタスクが不要になります。 仮想マシンがデプロイされると、機能を使用する準備が整います。
 
詳細な手順については、「 [Azure portal で Windows SQL Server 仮想マシンをプロビジョニングする方法](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)」を参照してください。

[ [SQL server 設定の構成](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#3-configure-sql-server-settings)] 手順では、インスタンスに machine learning を追加します。

<a name="firewall"></a>

## <a name="unblock-the-firewall"></a>ファイアウォールのブロックを解除する

既定では、Azure 仮想マシンのファイアウォールには、ローカルユーザーアカウントのネットワークアクセスをブロックする規則が含まれています。

リモートデータサイエンスクライアントから[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]インスタンスにアクセスできるようにするには、このルールを無効にする必要があります。  そうしないと、仮想マシンのワークスペースを使用するコンピューティングコンテキストで machine learning コードを実行できなくなります。

リモートデータサイエンスクライアントからのアクセスを有効にするには:

1. 仮想マシン で、[セキュリティが強化された Windows ファイアウォール] を開きます。
2. **[送信の規則]** を選択します
3. 次の規則を無効にします。
  
     `Block network access for R local user accounts in SQL Server instance MSSQLSERVER`
  
## <a name="enable-odbc-callbacks-for-remote-clients"></a>リモート クライアントの ODBC コールバックを有効にする

サーバーを呼び出すクライアントが machine learning ソリューションの一部として ODBC クエリを発行する必要があると予想される場合は、スタートパッドがリモートクライアントの代わりに ODBC 呼び出しを行うことができるようにする必要があります。 

これを行うには、スタート パッドによって使用される SQL ワーカー アカウントがインスタンスにログインするできるようにする必要があります。 詳細については、「[データベースユーザーとしての SQLRUserGroup の追加](../security/create-a-login-for-sqlrusergroup.md)」を参照してください。

<a name="network"></a>

## <a name="add-network-protocols"></a>ネットワークプロトコルの追加

+ 名前付きパイプを有効にする
  
  [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)] では、クライアントとサーバー コンピューター間の接続 (および一部の内部接続) に、名前付きパイプ プロトコルが使用されます。 名前付きパイプが有効になっていない場合は、Azure 仮想マシンと、サーバーに接続するデータ サイエンス クライアントの両方に名前付きパイプをインストールし、有効化する必要があります。
  
+ TCP/IP を有効にする

  ループバック接続には TCP/IP が必要です。 エラー "DBNETLIB;SQL Server が存在しないか、アクセスが拒否されました "という、インスタンスをサポートする仮想マシンで TCP/IP を有効にします。