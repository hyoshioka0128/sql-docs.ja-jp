---
title: OLEDB Call イベント クラス | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
helpviewer_keywords:
- OLEDB Call event class
ms.assetid: e1be1e90-98cc-47a3-addd-59d4aeca6547
author: stevestein
ms.author: sstein
manager: craigg
monikerRange: =azuresqldb-current||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 817f77f3f9ca47df17af0e6a9a71ba790d768863
ms.sourcegitcommit: 61381ef939415fe019285def9450d7583df1fed0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47640754"
---
# <a name="oledb-call-event-class"></a>OLEDB Call イベント クラス
[!INCLUDE[appliesto-ss-asdb-xxxx-xxx-md](../../includes/appliesto-ss-asdb-xxxx-xxx-md.md)]
  **OLEDB Call** イベント クラスは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] から分散クエリやリモート ストアド プロシージャの OLE DB プロバイダーが呼び出されるときに発生します。  
  
 データを要求しない呼び出し、または **QueryInterface** メソッドに対して行われていない呼び出しだけを監視するには、トレースに **OLEDB Call** イベント クラスを含めます。 **OLEDB Call** イベント クラスをトレースに含めた場合、発生するオーバーヘッドの量は、トレース中にデータベースに対して OLE DB 呼び出しが発生する頻度によって異なります。 呼び出しが頻繁に行われると、トレースによってパフォーマンスが大きく低下することがあります。  
  
## <a name="oledb-call-event-class-data-columns"></a>OLEDB Call イベント クラスのデータ列  
  
|データ列名|データ型|[説明]|列 ID|フィルターの適用|  
|----------------------|---------------|-----------------|---------------|----------------|  
|ApplicationName|**nvarchar**|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスへの接続を作成したクライアント アプリケーションの名前。 この列には、プログラムの表示名ではなく、アプリケーションによって渡された値が格納されます。|10|[ユーザー アカウント制御]|  
|ClientProcessID|**Int**|クライアント アプリケーションが実行されているプロセスに対し、ホスト コンピューターが割り当てた ID。 クライアントによりクライアント プロセス ID が指定されると、このデータ列に値が格納されます。|9|[ユーザー アカウント制御]|  
|DatabaseID|**Int**|USE *database* ステートメントで指定されたデータベースの ID、または特定のインスタンスについて USE *database* ステートメントが実行されていない場合は既定のデータベースの ID となります。 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] では、 **ServerName** データ列がトレースにキャプチャされ、そのサーバーが利用可能な場合、データベースの名前が表示されます。 データベースに対応する値は、DB_ID 関数を使用して特定します。|3|[ユーザー アカウント制御]|  
|DatabaseName|**nvarchar**|ユーザーのステートメントが実行されているデータベースの名前。|35|[ユーザー アカウント制御]|  
|Duration|**Bigint**|OLE DB Call イベントを完了する時間長。|13|いいえ|  
|EndTime|**DateTime**|イベントが終了する時刻。|15|[ユーザー アカウント制御]|  
|[エラー]|**int**|特定のイベントのエラー番号。 多くの場合、sys.messages カタログ ビューに保存されているエラー番号です。|31|[ユーザー アカウント制御]|  
|EventClass|**Int**|イベントの種類 = 119。|27|いいえ|  
|EventSequence|**Int**|バッチ内の OLE DB イベント クラスのシーケンス。|51|いいえ|  
|EventSubClass|**Int**|0 = 開始<br /><br /> 1 = 完了|21|いいえ|  
|GroupID|**int**|SQL トレース イベントが発生したワークロード グループの ID。|66|[ユーザー アカウント制御]|  
|HostName|**nvarchar**|クライアントが実行されているコンピューターの名前。 このデータ列にはクライアントからホスト名が提供されている場合に値が格納されます。 ホスト名を指定するには、HOST_NAME 関数を使用します。|8|[ユーザー アカウント制御]|  
|IsSystem|**int**|イベントがシステム プロセスとユーザー プロセスのどちらで発生したか。 1 はシステム、0 はユーザーです。|60|[ユーザー アカウント制御]|  
|LinkedServerName|**nvarchar**|リンク サーバーの名前|45|[ユーザー アカウント制御]|  
|LoginName|**nvarchar**|ユーザーのログイン名 ( [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] セキュリティ ログインまたは DOMAIN\username という形式の [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows ログイン資格情報)。|11|[ユーザー アカウント制御]|  
|LoginSid|**[イメージ]**|ログインしたユーザーのセキュリティ識別子 (SID)。 この情報は、sys.server_principals カタログ ビューで参照できます。 各 SID はサーバーのログインごとに一意です。|41|可|  
|MethodName|**nvarchar**|OLE DB メソッドの名前。|47|[ユーザー アカウント制御]|  
|NTDomainName|**nvarchar**|ユーザーが所属する Windows ドメイン。|7|[ユーザー アカウント制御]|  
|NTUserName|**nvarchar**|Windows のユーザー名。|6|[ユーザー アカウント制御]|  
|ProviderName|**nvarchar**|OLE DB プロバイダーの名前です。|46|[ユーザー アカウント制御]|  
|RequestID|**Int**|ステートメントが含まれている要求の ID。|49|[ユーザー アカウント制御]|  
|SessionLoginName|**nvarchar**|セッションを開始したユーザーのログイン名。 たとえば、Login1 を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] に接続し、Login2 でステートメントを実行すると、 **SessionLoginName** には Login1 が表示され、 **LoginName** には Login2 が表示されます。 この列には、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインと Windows ログインの両方が表示されます。|64|[ユーザー アカウント制御]|  
|SPID|**Int**|イベントが発生したセッションの ID。|12|[ユーザー アカウント制御]|  
|StartTime|**datetime**|イベントの開始時刻 (取得できた場合)。|14|[ユーザー アカウント制御]|  
|TextData|**nvarchar**|OLE DB 呼び出しで送受信されるパラメーター。|1|いいえ|  
|TransactionID|**bigint**|システムによって割り当てられたトランザクション ID。|4|[ユーザー アカウント制御]|  
  
## <a name="see-also"></a>参照  
 [拡張イベント](../../relational-databases/extended-events/extended-events.md)   
 [sp_trace_setevent &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-trace-setevent-transact-sql.md)   
 [Transact-SQL での OLE オートメーション オブジェクト](../../relational-databases/stored-procedures/ole-automation-objects-in-transact-sql.md)  
  
  
