---
title: SQL Server ビッグ データ クラスター Spark ストリーミングのガイド
titleSuffix: SQL Server Big Data Clusters
description: このガイドでは、ストリーミングのユース ケースと、SQL Server ビッグ データ クラスター機能を使用してそれを実装する方法について説明します。
author: dacoelho
ms.author: dacoelho
ms.reviewer: mikeray
ms.metadata: seo-lt-2019
ms.date: 04/06/2021
ms.topic: guide
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 05e35cf861a46a667ce25c88e9d6d2ea17b1183b
ms.sourcegitcommit: 14b97028da137f872a0a35cfe9d5a639a2d116a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107221775"
---
# <a name="sql-server-big-data-clusters-spark-streaming-guide"></a>SQL Server ビッグ データ クラスター Spark ストリーミングのガイド

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

このガイドでは、ストリーミングのユース ケースと、SQL Server ビッグ データ クラスター Spark を使用してそれを実装する方法について説明します。

このガイドでは、以下の方法について説明します。

> [!div class="checklist"]
> * ストリーミング ライブラリを読み込んで PySpark および Scala Spark を使用する。
> * SQL Server ビッグ データ クラスターを使用して、3 つの一般的なストリーミング パターンを実装する。

## <a name="prerequisites"></a>前提条件

* SQL Server ビッグ データ クラスターのデプロイ
* これらの 2 つのオプションのいずれか:
    * Apache Kafka クラスター 2.0 以上
    * Azure Event Hubs - 名前空間とイベント ハブ

このガイドでは、ストリーミング テクノロジの概念とアーキテクチャについて十分に理解していることを前提とします。
次の記事では、優れた概念ベースラインについて説明します。

* [データ アーキテクチャ ガイド - リアルタイム処理](/azure/architecture/data-guide/big-data/real-time-processing)
* [Apache Kafka アプリケーションから Azure Event Hubs を使用する](/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)
* [データ アーキテクチャ ガイド - Azure でのリアルタイム メッセージ取り込みテクノロジの選択](/azure/architecture/data-guide/technology-choices/real-time-ingestion)

### <a name="apache-kafka-and-azure-event-hub-conceptual-mapping"></a>Apache Kafka と Azure Event Hub の概念のマッピング

|Apache Kafka の概念|Event Hubs での概念|
|--------------------|------------------|
|クラスター|名前空間|
|トピック|イベント ハブ|
|Partition|Partition|
|コンシューマー グループ|コンシューマー グループ|
|Offset|Offset|

### <a name="reproducibility"></a>再現性

このガイドでは、「[クイック スタート: Kafka プロトコルを使用した Event Hubs によるデータ ストリーミング](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)」で提供されるプロデューサー アプリケーションを活用します。 さらに、[GitHub の Apache Kafka 用の Azure Event Hubs](https://github.com/Azure/azure-event-hubs-for-kafka) に関するページには、ストリーミング シナリオをすぐに開始するのに役立つ多くのプログラミング言語のサンプル アプリケーションがあります。

以下は、Kafka 互換クライアントを使用して、シミュレートされたセンサーの JSON データをストリーミング エンジンにストリーミングする変更済みの `producer.py` です。 Azure Event Hubs は Kafka プロトコルと互換性があることに注目してください。 [GitHub リポジトリ](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python)のセットアップ手順に従って、ご自分に合ったサンプルを入手してください。 `conf` ディクショナリは、すべての接続情報が配置される場所であり、有用性はご利用の環境によって異なる場合があります。 以下のコード サンプルでは最適な構成が示されていますが、少なくとも `bootstrap.servers` と `sasl.password` は必ず置き換えてください。

```python
#!/usr/bin/env python
#
# Copyright (c) Microsoft Corporation. All rights reserved.
# Copyright 2016 Confluent Inc.
# Licensed under the MIT License.
# Licensed under the Apache License, Version 2.0
#
# Original Confluent sample modified for use with Azure Event Hubs for Apache Kafka Ecosystems

from confluent_kafka import Producer
import sys
import random
import time
import json

sensors = ["Sensor 1", "Sensor 2", "Sensor 3"]

if __name__ == '__main__':
    if len(sys.argv) != 2:
        sys.stderr.write('Usage: %s <topic>\n' % sys.argv[0])
        sys.exit(1)
    topic = sys.argv[1]

    # Producer configuration
    # See https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md
    # See https://github.com/edenhill/librdkafka/wiki/Using-SSL-with-librdkafka#prerequisites for SSL issues
    conf = {
        'bootstrap.servers': '',                     #replace!
        'security.protocol': 'SASL_SSL',
        'ssl.ca.location': '/usr/lib/ssl/certs/ca-certificates.crt',
        'sasl.mechanism': 'PLAIN',
        'sasl.username': '$ConnectionString',
        'sasl.password': '',                         #replace!
        'client.id': 'python-sample-producer'
    }

    # Create Producer instance
    p = Producer(**conf)

    def delivery_callback(err, msg):
        if err:
            sys.stderr.write('%% Message failed delivery: %s\n' % err)
        else:
            sys.stderr.write('%% Message delivered to %s [%d] @ %o\n' % (msg.topic(), msg.partition(), msg.offset()))

    # Simulate stream
    for i in range(0, 10000):
        try:
            payload = {
                'sensor': random.choice(sensors),
                'measure1': random.gauss(37, 7),
                'measure2': random.random(),
            }
            p.produce(topic, json.dumps(payload).encode('utf-8'), callback=delivery_callback)
            #p.produce(topic, str(i), callback=delivery_callback)
        except BufferError as e:
            sys.stderr.write('%% Local producer queue is full (%d messages awaiting delivery): try again\n' % len(p))
        p.poll(0)
        time.sleep(2)

    # Wait until all messages have been delivered
    sys.stderr.write('%% Waiting for %d deliveries\n' % len(p))
    p.flush()

```

次のコマンドを使用してサンプル プロデューサー アプリケーションを実行します。`<my-sample-topic>` はご利用の環境の情報に置き換えてください。

```bash
python producer.py <my-sample-topic>
```

## <a name="streaming-scenarios"></a>ストリーミング シナリオ

|ストリーミング パターン             |シナリオの説明と実装         |
|------------------------------|------------------------------------------------|
|Kafka または Event Hubs からプルする |ストリーミング エンジンから継続的にデータをプルする Spark Streaming ジョブを作成し、省略可能な変換および分析ロジックを実行します。|
|ストリーミング データを HDFS にシンクする |一般に、これは前のパターンと関連しています。ストリーミング プルおよび変換ロジックの後、必要なデータ永続化要件を実現するために、データが多数のデータの場所に書き込まれることがあります。|
|Spark から Kafka または Event Hubs にプッシュする |Spark によって処理された後、データが外部のストリーミング エンジンにプッシュバックされる可能性があります。 これが必要になるシナリオは多数あります。たとえば、リアルタイムの製品推奨、マイクロ バッチの不正行為および異常検出などです。|

## <a name="sample-spark-streaming-application"></a>Spark Streaming アプリケーションのサンプル

このサンプル アプリケーションでは、前のセクションで説明した 3 つのストリーミング パターンをすべて実装します。 このアプリケーションでは次のことを行います。

1. ストリーミング サービスに接続するための構成変数を設定する
1. データをプルする Spark Streaming データ フレームを作成する
1. HDFS にローカルで集計されたデータを書き込む
1. 集計データをストリーミング サービスの別のトピックに書き込む

以下は完全な `sample-spark-streaming-python.py` コードです。
```python
from pyspark import SparkContext, SparkConf
from pyspark.streaming import StreamingContext
from pyspark.sql import SparkSession
from pyspark.sql.types import *
from pyspark.sql.functions import *

# Sets up batch size to 15 seconds
duration_ms = 15000
# Changes Spark Session into a Structured Streaming context
sc = SparkContext.getOrCreate()
ssc = StreamingContext(sc, duration_ms)
spark = SparkSession(sc)

# Connection Information
bootstrap_servers = "" # Replace!
sasl = "" # Replace!
# Topic we will consume from
topic = "sample-topic"
# Topic we will write toa
topic_to = "sample-topic-processed"

# Define the schema to speed up processing
jsonSchema = StructType([StructField("sensor", StringType(), True), StructField("measure1", DoubleType(), True), StructField("measure2", DoubleType(), True)])

streaming_input_df = (
    spark.readStream \
    .format("kafka") \
    .option("subscribe", topic) \
    .option("kafka.bootstrap.servers", bootstrap_servers) \
    .option("kafka.sasl.mechanism", "PLAIN") \
    .option("kafka.security.protocol", "SASL_SSL") \
    .option("kafka.sasl.jaas.config", sasl) \
    .option("kafka.request.timeout.ms", "60000") \
    .option("kafka.session.timeout.ms", "30000") \
    .option("failOnDataLoss", "true") \
    .option("startingOffsets", "latest") \
    .load()
)

def foreach_batch_function(df, epoch_id):
    # Transform and write batchDF
    if df.count() <= 0:
        None
    else:
        # Create a data frame to be written to HDFS
        sensor_df = df.selectExpr('CAST(value AS STRING)').select(from_json("value", jsonSchema).alias("value")).select("value.*")
        # root
        #  |-- sensor: string (nullable = true)
        #  |-- measure1: double (nullable = true)
        #  |-- measure2: double (nullable = true)
        sensor_df.persist()
        # Write to HDFS:
        sensor_df.write.format('parquet').mode('append').saveAsTable('sensor_data')
        # Create a summarization dataframe
        sensor_stats_df = (sensor_df.groupBy('sensor').agg({'measure1':'avg', 'measure2':'avg', 'sensor':'count'}).withColumn('ts', current_timestamp()).withColumnRenamed('avg(measure1)', 'measure1_avg').withColumnRenamed('avg(measure2)', 'measure2_avg').withColumnRenamed('avg(measure1)', 'measure1_avg').withColumnRenamed('count(sensor)', 'count_sensor'))
        # root
        # |-- sensor: string (nullable = true)
        # |-- measure2_avg: double (nullable = true)
        # |-- measure1_avg: double (nullable = true)
        # |-- count_sensor: long (nullable = false)
        # |-- ts: timestamp (nullable = false)
        sensor_stats_df.write.format('parquet').mode('append').saveAsTable('sensor_data_stats')
        # Group by and send metrics to an output kafka topic:
        sensor_stats_df.writeStream
            .format("kafka")
            .option("topic", topic_to)
            .option("kafka.bootstrap.servers", bootstrap_servers)
            .option("kafka.sasl.mechanism", "PLAIN")
            .option("kafka.security.protocol", "SASL_SSL")
            .option("kafka.sasl.jaas.config", sasl)
            .save()
        # For example, you could write to SQL Server
        # df.write.format('com.microsoft.sqlserver.jdbc.spark').mode('append').option('url', url).option('dbtable', datapool_table).save()
        sensor_df.unpersist()


writer = streaming_input_df.writeStream.foreachBatch(foreach_batch_function).start().awaitTermination()

```

以下は、__Spark SQL__ を使用して作成する必要があるテーブルです。
```sql
CREATE TABLE IF NOT EXISTS sensor_data (sensor string, measure1 double, measure2 double)
USING PARQUET;

CREATE TABLE IF NOT EXISTS sensor_data_stats (sensor string, measure2_avg double, measure1_avg double, count_sensor long, ts timestamp)
USING PARQUET;
```


### <a name="copy-the-application-to-hdfs"></a>アプリケーションを HDFS にコピーする

```bash
azdata bdc hdfs cp --from-path sample-spark-streaming-python.py --to-path "hdfs:/apps/ETL-Pipelines/sample-spark-streaming-python.py"
```

### <a name="configuring-kafka-libraries"></a>Kafka ライブラリの構成

最も重要な手順は、送信前にアプリケーションに Kafka クライアント ライブラリを設定することです。

次の __2 つのライブラリが必要__ です。

* [kafka-clients](https://mvnrepository.com/artifact/org.apache.kafka/kafka-clients) - コア ライブラリ。Kafka プロトコルのサポートと接続を可能にします。
* [spark-sql-kafka](https://mvnrepository.com/artifact/org.apache.spark/spark-sql-kafka-0-10) - Kafka ストリームで Spark SQL データ フレーム機能を有効にします。

どちらのライブラリも、次の要件を満たしている必要があります。

* ターゲット Target Scala 2.11 および Spark 2.4.7。 これは、CU9 以降に従った SQL Server BDC の要件です。
* ストリーミング サーバーと互換性があること。

> [!CAUTION]
   > 原則として、最新の互換性のあるライブラリを使用します。 このガイドで提供されているコードは、Azure Event Hubs 用の Apache Kafka を使用してテストされたものであり、現状のまま提供されます。サポート可能であることを示すものでありません。 Apache Kafka では、設計により双方向のクライアントの互換性が提供されますが、ライブラリの実装はプログラミング言語によって異なります。 互換性を正しくマッピングするために、常に Kafka プラットフォームのドキュメントを参照してください。

#### <a name="shared-library-locations-for-jobs-on-hdfs"></a>HDFS 上のジョブの共有ライブラリの場所

複数のアプリケーションが同じ Kafka クラスターに接続されている場合、または組織に 1 つのバージョン管理された Kafka クラスターがある場合は、適切なライブラリ jar ファイルを HDFS 上の共有場所にコピーします。 その後、すべてのジョブで同じライブラリ ファイルが参照されるはずです。

ライブラリを共通の場所にコピーします。

```bash
azdata bdc hdfs cp --from-path kafka-clients-2.7.0.jar --to-path "hdfs:/apps/jars/kafka-clients-2.7.0.jar"
azdata bdc hdfs cp --from-path spark-sql-kafka-0-10_2.11-2.4.7.jar --to-path "hdfs:/apps/jars/spark-sql-kafka-0-10_2.11-2.4.7.jar"
```

#### <a name="dynamically-install-the-libraries"></a>ライブラリを動的にインストールする

SQL Server ビッグ データ クラスターの[パッケージ管理機能](spark-install-packages.md)を使用して、ジョブの送信時にパッケージを動的にインストールすることができます。 ジョブが送信されるたびにライブラリ ファイルが定期的にダウンロードされるため、ジョブの起動時間が長くなります。

### <a name="submit-the-spark-streaming-job-using-azdata"></a>`azdata` を使用して Spark Streaming ジョブを送信する

最初の例では、HDFS で共有ライブラリ jar ファイルを使用します。

```bash
azdata bdc spark batch create -f hdfs:/apps/ETL-Pipelines/sample-spark-streaming-python.py \
-j '["/apps/jars/kafka-clients-2.7.0.jar","/apps/jars/spark-sql-kafka-0-10_2.11-2.4.7.jar"]' \
--config '{"spark.streaming.concurrentJobs":"3","spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation":"true"}' \
-n MyStreamingETLPipelinePySpark --executor-count 2 --executor-cores 2 --executor-memory 1664m
```

この例では、動的パッケージ管理を使用して依存関係をインストールします。

```bash
azdata bdc spark batch create -f hdfs:/apps/ETL-Pipelines/sample-spark-streaming-python.py \
--config '{"spark.jars.packages": "org.apache.kafka:kafka-clients:2.7.0,org.apache.spark:spark-sql-kafka-0-10_2.11:2.4.7","spark.streaming.concurrentJobs":"3","spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation":"true"}' \
-n MyStreamingETLPipelinePySpark --executor-count 2 --executor-cores 2 --executor-memory 1664m
```

## <a name="next-steps"></a>次の手順

azdata または Livy エンドポイントを使用して SQL Server ビッグ データ クラスターに Spark ジョブを送信する方法について詳しくは、「[コマンドライン ツールを使用して Spark ジョブを送信する](spark-submit-job-command-line.md)」を参照してください。

SQL Server ビッグ データ クラスターと関連するシナリオの詳細については、「[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](big-data-cluster-overview.md)」を参照してください。
