---
title: データの取得と変更
description: .NET では、Microsoft SqlClient Data Provider for SQL Server は、データを読み取って更新するための、アプリケーションとデータ ソース間のブリッジとして機能します。
ms.date: 03/24/2021
ms.assetid: 722e7f87-3691-46c6-87e8-7d159722d675
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: cfe047c94c39c9c07fb1f8cf32013ef4c62a5959
ms.sourcegitcommit: d8cbbeffa3faa110e02056ff97dc7102b400ffb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107003778"
---
# <a name="retrieving-and-modifying-data-in-adonet"></a>ADO.NET でのデータの取得と変更

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

データベース アプリケーションの主な機能は、データ ソースとの接続およびデータベースに格納されているデータの取得です。 SqlClient データ プロバイダーは、アプリケーションとデータ ソースの間のブリッジとして機能し、**DataReader** または **DataAdapter** を使用することで、コマンドを実行したり、データを取得したりできます。 データベースに格納されているデータを更新する機能は、データベース アプリケーションの重要な機能の 1 つです。 Microsoft SqlClient Data Provider for SQL Server でデータを更新するには、**DataAdapter** と <xref:System.Data.DataSet>、および **Command** オブジェクトを使用する必要があります。また、トランザクションを使用することが必要になる場合もあります。

## <a name="in-this-section"></a>このセクションの内容

[データ ソースへの接続](connecting-to-data-source.md)  
データ ソースへの接続を確立する方法、および接続イベントを使用する方法について説明します。

[接続文字列](connection-strings.md)  
接続文字列のキーワード、セキュリティ情報、セキュリティ情報の格納や取得など、接続文字列を使用するうえでのさまざまな側面について説明します。

[接続プール](connection-pooling.md)  
Microsoft SqlClient Data Provider for SQL Server の接続プールについて説明します。

[コマンドおよびパラメーター](commands-parameters.md)  
コマンドおよびコマンド ビルダーを作成する方法、パラメーターを構成する方法、およびコマンドを実行してデータを取得および変更する方法について説明します。

[DataAdapter と DataReader](dataadapters-datareaders.md)  
DataReaders、DataAdapters、パラメーター、DataAdapter イベントの処理、およびバッチ操作の実行について説明します。

[トランザクションとコンカレンシー](transactions-and-concurrency.md)  
ローカル トランザクションや分散トランザクションの実行方法、およびオプティミスティック コンカレンシーの使用方法について説明します。

[データベース スキーマ情報の取得](retrieving-database-schema-information.md)  
データベースまたはカタログ、データベース内のテーブルおよびビュー、テーブルに対して存在する制約、およびその他のスキーマ情報をデータ ソースから取得する方法について説明します。

[DbProviderFactories](dbproviderfactories.md)  
プロバイダー ファクトリ モデルについて説明し、`System.Data.Common` 名前空間の基本クラスの使用方法を示します。

[SqlClient の構成可能な再試行ロジック](configurable-retry-logic.md)  
接続を確立するとき、またはコマンドを実行するときに **構成可能な再試行ロジック** 機能を使用する方法について説明します。

[ID 値または autonumber 値の取得](retrieve-identity-or-autonumber-values.md)  
SQL Server テーブル内の **identity** 列用に生成された値を、テーブルの挿入行の列に割り当てる例を示します。 `DataTable` での ID 値の結合について説明します。

[バイナリ データの取得](retrieve-binary-data.md)  
`CommandBehavior`.`SequentialAccess` を使用してバイナリ データまたは大きなデータ構造を取得し、 `DataReader` の既定の動作を変更する方法について説明します。

[ストアド プロシージャによるデータの変更](modify-data-with-stored-procedures.md)  
ストアド プロシージャの入力パラメーターおよび出力パラメーターを使用してデータベースに行を挿入し、新しい ID 値を返す方法について説明します。

[SqlClient でのデータ トレース](data-tracing.md)  
Microsoft SqlClient Data Provider for SQL Server によって組み込みのデータ トレース機能が提供される方法について説明します。
  
[SqlClient のパフォーマンス カウンター](performance-counters.md)  
Microsoft SqlClient Data Provider for SQL Server で利用できるパフォーマンス カウンターについて説明します。
  
[非同期プログラミング](asynchronous-programming.md)  
Microsoft SqlClient Data Provider for SQL Server の非同期プログラミングのサポートについて説明します。
  
[SqlClient ストリーミング サポート](sqlclient-streaming-support.md)  
完全にメモリに読み込むことなく SQL Server からデータをストリーミングするアプリケーションの作成方法について説明します。

## <a name="see-also"></a>関連項目

- [ADO.NET のデータ型のマッピング](data-type-mappings-ado-net.md)
- [SQL Server と ADO.NET](./sql/index.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
