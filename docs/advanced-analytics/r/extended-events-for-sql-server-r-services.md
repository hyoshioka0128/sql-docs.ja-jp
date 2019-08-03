---
title: R および Python プロセスを監視するための拡張イベント
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 8dc99a6f5ac1ff660f34f2248c844e5386bea5f0
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715133"
---
# <a name="extended-events-for-sql-server-machine-learning-services"></a>SQL Server Machine Learning Services の拡張イベント
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

SQL Server には、に関連[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)]する操作のトラブルシューティングに使用する一連の拡張イベントと、に[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]送信される Python または R のジョブが用意されています。

**適用対象:** SQL Server 2016 R Services、SQL Server Machine Learning Services

## <a name="sql-server-events-for-machine-learning"></a>Machine learning の SQL Server イベント

SQL Server に関するイベントの一覧を表示するには、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]から次のクエリを実行します。

```sql
SELECT o.name AS event_name, o.description
FROM sys.dm_xe_objects o
JOIN sys.dm_xe_packages p
ON o.package_guid = p.guid
WHERE o.object_type = 'event'
AND p.name = 'SQLSatellite';
```

拡張イベントの使用に関する一般的な情報については、「[拡張イベントツール](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events-tools)」を参照してください。

> [!TIP]
> SQL Server によって生成された拡張イベントの場合は、新しい[SSMS XEvent profiler](https://docs.microsoft.com/sql/relational-databases/extended-events/use-the-ssms-xe-profiler)を試してください。 Management Studio のこの新機能は、拡張イベントのライブビューアーを表示します。これは、同様のプロファイラートレースよりも SQL Server には影響しません。

## <a name="additional-events-specific-to-machine-learning-components"></a>Machine learning コンポーネントに固有のその他のイベント

追加の拡張イベントは、R ランタイムを起動するサテライトプロセスである[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)]、bxlserver などの SQL Server Machine Learning Services に関連付けられているコンポーネントで使用できます。 これらの追加の拡張イベントは外部プロセスから起動されるため、外部のユーティリティを使用してキャプチャする必要があります。

これを行う方法の詳細については、「[外部プロセスからのイベントの収集](#bkmk_externalevents)」を参照してください。

##  <a name="bkmk_xeventtable"></a>拡張イベントの表

|event|説明|メモ|  
|-----------|-----------------|---------|  
|connection_accept|新しい接続が受け入れられたときに発生します。 このイベントは、すべての接続試行をログに記録するために役立ちます。||  
|failed_launching|起動に失敗しました。|エラーを示します。|  
|satellite_abort_connection|接続の中止レコード||  
|satellite_abort_received|中止メッセージがサテライト接続経由で受信したときに発生します。||  
|satellite_abort_sent|中止メッセージがサテライト接続経由で送信されたときに発生します。||  
|satellite_authentication_completion|TCP または名前付きパイプ経由の接続の認証が完了したときに発生します。||  
|satellite_authorization_completion|TCP または名前付きパイプ経由の接続の承認が完了したときに発生します。||  
|satellite_cleanup|サテライトがクリーンアップを呼び出したときに発生します。|外部プロセスからのみ起動されます。 外部プロセスからイベントを収集する手順を参照してください。|  
|satellite_data_chunk_sent|サテライト接続が 1 つのデータ チャンクの送信を完了したときに発生します。|イベントにより、送信された行数と列数、使用された SNI パケット数、チャンクの送信にかかった時間 (ミリ秒) が報告されます。 この情報は、さまざまな型のデータを渡すためにかかった時間と、使用されたパケット数を理解するのに役立ちます。|  
|satellite_data_receive_completion|サテライト接続経由でクエリに必要なすべてのデータが受信されたときに発生します。|外部プロセスからのみ起動されます。 外部プロセスからイベントを収集する手順を参照してください。|  
|satellite_data_send_completion|サテライト接続経由でセッションに必要なすべてのデータが送信されたときに発生します。||  
|satellite_data_send_start|データ転送の開始時に発生します。| データ転送は、最初のデータチャンクが送信される直前に開始されます。|  
|satellite_error|Sql サテライト エラーのトレースに使われます||  
|satellite_invalid_sized_message|メッセージのサイズが正しくありません。||  
|satellite_message_coalesced|ネットワーク レイヤーでのメッセージ結合のトレースに使われます||  
|satellite_message_ring_buffer_record|メッセージ リング バッファー レコード||  
|satellite_message_summary|メッセージングに関する概要情報||  
|satellite_message_version_mismatch|メッセージのバージョン フィールドが一致しません||  
|satellite_messaging|メッセージング イベント (バインド、バインド解除など) のトレースに使われます||  
|satellite_partial_message|ネットワーク レイヤーでの部分的なメッセージのトレースに使われます||  
|satellite_schema_received|スキーマ メッセージが受信され、SQL によって読み取られたときに発生します。||  
|satellite_schema_sent|スキーマ メッセージが、サテライトによって送信されたときに発生します。|外部プロセスからのみ起動されます。 外部プロセスからイベントを収集する手順を参照してください。|  
|satellite_service_start_posted|サービス開始メッセージがスタートパッドに投稿されたときに発生します。|これにより、スタートパッドに外部プロセスを起動するように指示し、新しいセッションの ID が含まれます。|  
|satellite_unexpected_message_received|予期しないメッセージを受信したときに発生します。|エラーを示します。|  
|stack_trace|プロセスのメモリ ダンプが要求されたときに発生します。|エラーを示します。|  
|trace_event|トレース目的で使用されます|これらのイベントには、SQL Server、スタートパッド、および外部プロセスのトレース メッセージを含めることができます。 これには、R からの stdout と stderr への出力が含まれます。|  
|launchpad_launch_start|スタートパッドがサテライトの起動を開始したときに発生します。|スタートパッドからのみ起動されます。 launchpad.exe からイベントを収集する手順を参照してください。|  
|launchpad_resume_sent|スタートパッドがサテライトを起動し、再開メッセージを SQL Server に送信したときに発生します。|スタートパッドからのみ起動されます。 launchpad.exe からイベントを収集する手順を参照してください。|  
|satellite_data_chunk_sent|サテライト接続が 1 つのデータ チャンクの送信を完了したときに発生します。|列数、行数、パケット数、チャンクの送信にかかった時間に関する情報を格納します。|  
|satellite_sessionId_mismatch|メッセージのセッション ID が予期されたものではありません||  
  
###  <a name="bkmk_externalevents"></a>外部プロセスからのイベントの収集

SQL Server Machine Learning Services は、SQL Server プロセスの外部で実行される一部のサービスを開始します。 これらの外部プロセスに関連するイベントをキャプチャするには、イベントトレース構成ファイルを作成し、そのファイルをプロセスの実行可能ファイルと同じディレクトリに配置する必要があります。  
  
+ **[!INCLUDE[rsql_launchpad](../../includes/rsql-launchpad-md.md)]**   
  
    スタートパッドに関連するイベントをキャプチャするには、Binn ディレクトリに SQL Server インスタンスの *.config* ファイルを配置します。  既定のインストールでは、次のようになります。

    `C:\Program Files\Microsoft SQL Server\MSSQL_version_number.MSSQLSERVER\MSSQL\Binn` 。  
  
+ **Bxlserver**は、R や Python などの外部スクリプト言語を使用した SQL 拡張機能をサポートするサテライトプロセスです。 外部言語のインスタンスごとに、BxlServer の個別のインスタンスが起動されます。
  
    BXLServer に関連するイベントをキャプチャするには、R または Python のインストールディレクトリに *.config*ファイルを配置します。  既定のインストールでは、次のようになります。
     
    **R:** `C:\Program Files\Microsoft SQL Server\MSSQL_version_number.MSSQLSERVER\R_SERVICES\library\RevoScaleR\rxLibs\x64`.  

    **Python:** `C:\Program Files\Microsoft SQL Server\MSSQL_version_number.MSSQLSERVER\PYTHON_SERVICES\library\RevoScaleR\rxLibs\x64`.

構成ファイルは、"[name] xevent" という形式を使用して、実行可能ファイルと同じ名前にする必要があります。 つまり、次のようにファイルに名前を付ける必要があります。

+ `Launchpad.xevents.xml`
+ `bxlserver.xevents.xml`

構成ファイル自体は、次の形式になります。

```xml
\<?xml version="1.0" encoding="utf-8"?>  
<event_sessions>  
<event_session name="[session name]" maxMemory="1" dispatchLatency="1" MaxDispatchLatency="2 SECONDS">  
    <description owner="you">Xevent for launchpad or bxl server.</description>  
    <event package="SQLSatellite" name="[XEvent Name 1]" />  
    <event package="SQLSatellite" name="[XEvent Name 2]" />  
    <target package="package0" name="event_file">  
      <parameter name="filename" value="[SessionName].xel" />  
      <parameter name="max_file_size" value="10" />  
      <parameter name="max_rollover_files" value="10" />  
    </target>  
  </event_session>  
</event_sessions>  
```

+ トレースを構成するには、[*セッション名*] プレースホルダー、ファイル名のプレースホルダー`[SessionName].xel`()、キャプチャ`[XEvent Name 1]`するイベントの名前 (、など`[XEvent Name 1]`) を編集します。  
+ 任意の数のイベントパッケージタグが表示され、name 属性が正しい限り収集されます。

### <a name="example-capturing-launchpad-events"></a>例:スタートパッドイベントのキャプチャ

次の例は、スタートパッドサービスのイベントトレースの定義を示しています。

```xml
\<?xml version="1.0" encoding="utf-8"?>  
<event_sessions>  
<event_session name="sqlsatelliteut" maxMemory="1" dispatchLatency="1" MaxDispatchLatency="2 SECONDS">  
    <description owner="hay">Xevent for sql tdd runner.</description>  
    <event package="SQLSatellite" name="launchpad_launch_start" />  
    <event package="SQLSatellite" name="launchpad_resume_sent" />  
    <target package="package0" name="event_file">  
      <parameter name="filename" value="launchpad_session.xel" />  
      <parameter name="max_file_size" value="10" />  
      <parameter name="max_rollover_files" value="10" />  
    </target>  
  </event_session>  
</event_sessions>  
```

+ Binn ディレクトリに SQL Server インスタンスの *.config* ファイルを配置します。
+ このファイルの名前`Launchpad.xevents.xml`はにする必要があります。

### <a name="example-capturing-bxlserver-events"></a>例:BXLServer イベントのキャプチャ  

次の例に、BXLServer 実行可能ファイルのイベント トレースの定義を示します。
  
```xml
\<?xml version="1.0" encoding="utf-8"?>  
<event_sessions>  
 <event_session name="sqlsatelliteut" maxMemory="1" dispatchLatency="1" MaxDispatchLatency="2 SECONDS">  
    <description owner="hay">Xevent for sql tdd runner.</description>  
    <event package="SQLSatellite" name="satellite_abort_received" />  
    <event package="SQLSatellite" name="satellite_authentication_completion" />  
    <event package="SQLSatellite" name="satellite_cleanup" />  
    <event package="SQLSatellite" name="satellite_data_receive_completion" />  
    <event package="SQLSatellite" name="satellite_data_send_completion" />  
    <event package="SQLSatellite" name="satellite_data_send_start" />  
    <event package="SQLSatellite" name="satellite_schema_sent" />   
    <event package="SQLSatellite" name="satellite_unexpected_message_received" />    
    <event package="SQLSatellite" name="satellite_data_chunk_sent" />   
    <target package="package0" name="event_file">  
      <parameter name="filename" value="satellite_session.xel" />  
      <parameter name="max_file_size" value="10" />  
      <parameter name="max_rollover_files" value="10" />  
    </target>  
  </event_session>  
</event_sessions>  
```

+ BXLServer 実行可能ファイルと同じディレクトリに、 *.config* ファイルを配置します。
+ このファイルの名前`bxlserver.xevents.xml`はにする必要があります。

## <a name="see-also"></a>関連項目

[Machine Learning Services のカスタム Management Studio レポート](../../advanced-analytics/r/monitor-r-services-using-custom-reports-in-management-studio.md)
