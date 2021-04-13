---
title: 'Spark ジョブを送信する: コマンド ライン'
titleSuffix: SQL Server big data clusters
description: コマンド ライン ツールを使用して、SQL Server ビッグ データ クラスター上の Spark ジョブを送信します。
author: dacoelho
ms.author: dacoelho
ms.reviewer: MikeRayMSFT
ms.date: 04/01/2021
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 3983f6354c84d11809b37d784d64c2f8c5b28f10
ms.sourcegitcommit: 14b97028da137f872a0a35cfe9d5a639a2d116a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107221768"
---
# <a name="submit-spark-jobs-using-command-line-tools"></a>コマンドライン ツールを使用して Spark ジョブを送信する

[!INCLUDE[SQL Server 2019](../includes/applies-to-version/sqlserver2019.md)]

この記事では、コマンド ライン ツールを使用して SQL Server ビッグ データ クラスターで Spark ジョブを実行する方法についてのガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

* [SQL Server 2019 ビッグ データ ツール](deploy-big-data-tools.md)が構成されており、クラスターにログインしている。
  * **azdata** 
  * Livy への REST API 呼び出しを実行するための **curl** アプリケーション

## <a name="spark-jobs-using-__azdata__-or-livy"></a>__azdata__ または Livy を使用する Spark ジョブ

この記事では、Spark アプリケーションを SQL Server ビッグ データ クラスターに送信するためのコマンド ライン パターンの使用例を示します。

Azure データ CLI [ `azdata bdc spark` コマンド](../azdata/reference/reference-azdata-bdc-spark.md)では、コマンド ラインで SQL Server ビッグ データ クラスター Spark のすべての機能が提供されます。 このガイドでは、ジョブの送信に焦点を当てますが、`azdata bdc spark` では、`azdata bdc spark session` コマンドを使用して、Python、Scala、SQL、および R の対話モードもサポートされます。

REST API に直接統合する必要がある場合は、標準の Livy 呼び出しを使用してジョブを送信します。 ここでは、Livy の例の `curl` コマンド ライン ツールを使用して、REST API 呼び出しを実行します。 Python コードを使用して Spark Livy エンドポイントとやりとりする方法の詳細な例については、GitHub の「[Livy エンド ポイントからの Spark の使用](https://github.com/microsoft/sql-server-samples/blob/master/samples/features/sql-big-data-cluster/spark/restful-api-access/accessing_spark_via_livy.ipynb)」を参照してください。

## <a name="simple-etl-using-sql-server-bdc-spark"></a>SQL Server BDC Spark を使用した単純な ETL

このアプリケーションでは、一般的な Data Engineering パターンを例示し、HDFS ランディング ゾーン パスから表形式データを読み込んでから、表形式を使用して HDFS で処理されたゾーン パスに書き込みます。 このサンプル アプリケーションで使用されるデータセットは、[こちら](https://ailab.criteo.com/download-criteo-1tb-click-logs-dataset/)からダウンロードできます。 PySpark、Spark Scala、または Spark SQL を使用して、PySpark アプリケーションを作成できます。次の例では、各ソリューションのサンプルの演習を示します。 下のタブを使用して任意のプラットフォームを選択し、後で、`azdata` または `curl` を使ってアプリケーションを実行します。

### <a name="pyspark"></a>[PySpark](#tab/pyspark)

この例では、ローカル コンピューターで ```parquet_etl_sample.py``` という名前の python ファイルとして保存された次の PySpark アプリケーションを使用します。

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.getOrCreate()

# Read clickstream_data from Storage Pool HDFS into a Spark data frame. Applies column renames.
df = spark.read.option("inferSchema", "true").csv('/securelake/landing/criteo/test.txt', sep='\t', 
    header=False).toDF("feat1","feat2","feat3","feat4","feat5","feat6","feat7","feat8",
    "feat9","feat10","feat11","feat12","feat13","catfeat1","catfeat2","catfeat3","catfeat4",
    "catfeat5","catfeat6","catfeat7","catfeat8","catfeat9","catfeat10","catfeat11","catfeat12",
    "catfeat13","catfeat14","catfeat15","catfeat16","catfeat17","catfeat18","catfeat19",
    "catfeat20","catfeat21","catfeat22","catfeat23","catfeat24","catfeat25","catfeat26")

# Prints the data frame inferred schema:
df.printSchema()

tot_rows = df.count()
print("Number of rows:", tot_rows)

# Drop the managed table
spark.sql("DROP TABLE dl_clickstream")

# Write data frame to HDFS managed table using optimized Delta Lake table format
df.write.format("parquet").mode("overwrite").saveAsTable("dl_clickstream")

print("Sample ETL pipeline completed")
```

#### <a name="copy-the-pyspark-application-to-hdfs"></a>PySpark アプリケーションを HDFS にコピーする

アプリケーションを HDFS に格納し、クラスターでそこにアクセスして実行できるようにする必要があります。 管理を効率化するために、クラスター内のアプリケーションの場所を標準化および管理することをお勧めします。 この例のユース ケースでは、すべての ETL パイプライン アプリケーションが `hdfs:/apps/ETL-Pipelines` パスに格納されます。 サンプル アプリケーションは `hdfs:/apps/ETL-Pipelines/parquet_etl_sample.py` に格納されます。

次のコマンドを実行して、ローカルの開発またはステージング コンピューターから HDFS クラスターに __`parquet_etl_sample.py`__ をアップロードします。 

```bash
azdata bdc hdfs cp --from-path parquet_etl_sample.py  --to-path "hdfs:/apps/ETL-Pipelines/parquet_etl_sample.py"
```

### <a name="spark-scala"></a>[Spark Scala](#tab/scala)

この例では、Scala Spark で記述された次の Spark アプリケーションを使用します。

```scala
import org.apache.spark.sql.SparkSession

object ParquetETLSample {
    def main(args: Array[String]) {
        val spark = SparkSession.builder.getOrCreate()
        
        val df = spark.read.
            option("inferSchema", "true").
            option("header", "false").
            option("delimiter", "\t").
            csv("/securelake/landing/criteo/test.txt").
            toDF("feat1","feat2","feat3","feat4","feat5","feat6","feat7","feat8","feat9","feat10","feat11","feat12","feat13","catfeat1","catfeat2","catfeat3","catfeat4","catfeat5","catfeat6","catfeat7","catfeat8","catfeat9","catfeat10","catfeat11","catfeat12","catfeat13","catfeat14","catfeat15","catfeat16","catfeat17","catfeat18","catfeat19","catfeat20","catfeat21","catfeat22","catfeat23","catfeat24","catfeat25","catfeat26")
        
        val tot_rows = df.count()
        println(s"Number of rows: $tot_rows")

        spark.sql("DROP TABLE dl_clickstream")

        df.write.format("parquet").mode("overwrite").saveAsTable("dl_clickstream")

        println("Sample ETL pipeline completed")
        
        spark.stop()
    }
}
```

#### <a name="bundle-and-copy-the-spark-application-to-hdfs"></a>Spark アプリケーションをバンドルして HDFS にコピーする

Spark のドキュメントでは、アプリケーションとそのすべての依存関係を含む __アセンブリ JAR__ (またはバンドル) を作成することが推奨されています。 これは、アプリケーション バンドルをクラスターに送信して実行するための必須の手順です。 完全な Scala Spark 開発環境の設定については、このガイドでは扱いません。詳細については、公式の [Spark の自己完結型アプリケーションの作成に関するドキュメント](https://spark.apache.org/docs/latest/quick-start.html#self-contained-applications)を参照してください。

この例では、`parquet-etl-sample.jar` という名前のアプリケーション jar バンドルがコンパイルされ、使用可能であることを前提とします。 次のコマンドを実行し、ローカルの開発またはステージング コンピューターから HDFS クラスターにバンドルをアップロードします。

```bash
azdata bdc hdfs cp --from-path parquet-etl-sample.jar  --to-path "hdfs:/apps/ETL-Pipelines/parquet-etl-sample.jar"
```

### <a name="spark-sql"></a>[Spark SQL](#tab/sql)

この例では Spark SQL を使って、テーブルとビューを使用したインジェスト ロジックを実行し、SQL 中心の ETL アプローチを提供します。

```sql
DROP VIEW IF EXISTS etl_clickstream;

CREATE TEMPORARY VIEW etl_clickstream
USING CSV
OPTIONS (path "/securelake/landing/criteo/test.txt", header "false", delimiter "\t", mode "FAILFAST");

DROP TABLE IF EXISTS dl_clickstream;

CREATE TABLE dl_clickstream (
    feat1 integer,
    feat2 integer,
    feat3 integer,
    feat4 integer,
    feat5 integer,
    feat6 integer,
    feat7 integer,
    feat8 integer,
    feat9 integer,
    feat10 integer,
    feat11 integer,
    feat12 integer,
    feat13 integer,
    catfeat1 string,
    catfeat2 string,
    catfeat3 string,
    catfeat4 string,
    catfeat5 string,
    catfeat6 string,
    catfeat7 string,
    catfeat8 string,
    catfeat9 string,
    catfeat10 string,
    catfeat11 string,
    catfeat12 string,
    catfeat13 string,
    catfeat14 string,
    catfeat15 string,
    catfeat16 string,
    catfeat17 string,
    catfeat18 string,
    catfeat19 string,
    catfeat20 string,
    catfeat21 string,
    catfeat22 string,
    catfeat23 string,
    catfeat24 string,
    catfeat25 string,
    catfeat26 string
) 
USING PARQUET
AS SELECT * FROM etl_clickstream;
```

#### <a name="copy-the-spark-sql-application-to-hdfs"></a>Spark SQL アプリケーションを HDFS にコピーする

次のコマンドを実行して、ローカルの開発またはステージング コンピューターから HDFS クラスターに __```parquet-etl-sample.sql```__ をアップロードします。

```bash
azdata bdc hdfs cp --from-path parquet-etl-sample.sql --to-path "hdfs:/apps/ETL-Pipelines/parquet-etl-sample.sql"
```

---

#### <a name="execute-the-spark-application"></a>Spark アプリケーションを実行する

次のコマンドを使用して、アプリケーションを SQL Server BDC Spark に送信して実行します。


# <a name="pyspark-and-azdata"></a>[PySpark と azdata](#tab/azdata/pyspark)

これは、一般的に指定されたパラメーターを使用してこのアプリケーションを実行する __`azdata`__ コマンドです。 `azdata bdc spark batch create` の完全なパラメーター オプションについては、「[`azdata bdc spark`](../azdata/reference/reference-azdata-bdc-spark.md)」を参照してください。

このアプリケーションには Spark 構成パラメーター `spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation` が必要であるため、コマンドでは `--config` オプションを使用しています。 これは、構成を Spark セッションに渡す方法を例示するための意図的なものです。 `--config` オプションを使用して、複数の構成パラメーターを指定することもできます。 これは、アプリケーション セッション内で、SparkSession オブジェクトの構成を設定することによって実現することもできます。

```bash
azdata bdc spark batch create -f hdfs:/apps/ETL-Pipelines/parquet_etl_sample.py \
--config '{"spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation":"true"}' \
-n MyETLPipelinePySpark --executor-count 2 --executor-cores 2 --executor-memory 1664m
```

# <a name="pyspark-and-curl-using-livy"></a>[Livy を使用した PySpark と curl](#tab/curl/pyspark)

これは、Livy を使用してこのアプリケーションを実行する __`curl`__ コマンドです。 USER、PASSWORD、および LIVY_ENDPOINT は必ずご利用の環境に合わせて置き換えてください。

```bash
curl -k -u <USER>:<PASSWORD> -X POST <LIVY_ENDPOINT>/batches \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF
{
    "file": "/apps/ETL-Pipelines/parquet_etl_sample.py",
    "name": "MyETLPipelinePySpark",
    "numExecutors": 2,
    "executorCores": 2,
    "executorMemory": "1664m",
    "conf": {
        "spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation":"true"
    }
}
EOF
```

# <a name="scala-and-azdata"></a>[Scala と azdata](#tab/azdata/scala)

これは、一般的に指定されたパラメーターを使用してこのアプリケーションを実行する __`azdata`__ コマンドです。 `azdata bdc spark batch create` の完全なパラメーター オプションについては、「[`azdata bdc spark`](../azdata/reference/reference-azdata-bdc-spark.md)」を参照してください。

このアプリケーションには Spark 構成パラメーター `spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation` が必要であるため、コマンドでは `--config` オプションを使用しています。 これは、構成を Spark セッションに渡す方法を例示するための意図的なものです。 `--config` オプションを使用して、複数の構成パラメーターを指定することもできます。 これは、アプリケーション セッション内で、SparkSession オブジェクトの構成を設定することによって実現することもできます。

```bash
azdata bdc spark batch create -f hdfs:/apps/ETL-Pipelines/parquet-etl-sample.jar \
--class "ParquetETLSample" \
--config '{"spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation":"true"}' \
-n MyETLPipeline --executor-count 2 --executor-cores 2 --executor-memory 1664m
```

# <a name="scala-and-curl-using-livy"></a>[Livy を使用した Scala と curl](#tab/curl/scala)

これは、Livy を使用してこのアプリケーションを実行する __`curl`__ コマンドです。 USER、PASSWORD、および LIVY_ENDPOINT は必ずご利用の環境に合わせて置き換えてください。

```bash
curl -k -u <USER>:<PASSWORD> -X POST <LIVY_ENDPOINT>/batches \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF
{
    "file": "/apps/ETL-Pipelines/parquet-etl-sample.jar",
    "class": "ParquetETLSample",
    "name": "MyETLPipeline",
    "numExecutors": 2,
    "executorCores": 2,
    "executorMemory": "1664m",
    "conf": {
        "spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation":"true"
    }
}
EOF
```

# <a name="sql-and-azdata"></a>[SQL と azdata](#tab/azdata/sql)

これは、一般的に指定されたパラメーターを使用してこのアプリケーションを実行する __`azdata`__ コマンドです。 `azdata bdc spark batch create` の完全なパラメーター オプションについては、「[`azdata bdc spark`](../azdata/reference/reference-azdata-bdc-spark.md)」を参照してください。

PySpark の例と同様に、このアプリケーションでも Spark 構成パラメーター `spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation` が必要であるため、コマンドでは `--config` オプションを使用しています。 これは、構成を Spark セッションに渡す方法を例示するための意図的なものです。 `--config` オプションを使用して、複数の構成パラメーターを指定することもできます。 これは、アプリケーション セッション内で、SparkSession オブジェクトの構成を設定することによって実現することもできます。

```bash
azdata bdc spark batch create -f hdfs:/apps/ETL-Pipelines/parquet_etl_sample.sql \
--config '{"spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation":"true"}' \
-n MyETLPipelineSQL --executor-count 2 --executor-cores 2 --executor-memory 1664m
```

# <a name="sql-and-curl-using-livy"></a>[Livy を使用した SQL および curl](#tab/curl/sql)

これは、Livy を使用してこのアプリケーションを実行する __`curl`__ コマンドです。 USER、PASSWORD、および LIVY_ENDPOINT は必ずご利用の環境に合わせて置き換えてください。

```bash
curl -k -u <USER>:<PASSWORD> -X POST <LIVY_ENDPOINT>/batches \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF
{
    "file": "/apps/ETL-Pipelines/parquet_etl_sample.sql",
    "name": "MyETLPipelineSQL",
    "numExecutors": 2,
    "executorCores": 2,
    "executorMemory": "1664m",
    "conf": {
        "spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation":"true"
    }
}
EOF
```

---

## <a name="monitoring-spark-jobs"></a>Spark ジョブの監視

[`azdata bdc spark batch` コマンド](../azdata/reference/reference-azdata-bdc-spark.md)には、Spark バッチ ジョブの管理アクションが含まれています。

__実行中のすべてのジョブを一覧表示する__ には、次のコマンドを実行します。

これは `azdata` コマンドです。

```bash
azdata bdc spark batch list -o table
```

これは、Livy を使用する `curl` コマンドです。

```bash
curl -k -u <USER>:<PASSWORD> -X POST <LIVY_ENDPOINT>/batches
```

指定された ID の Spark バッチの __情報を取得する__ には、次のコマンドを実行します。 `batch id` は 'spark batch create' から返されます。

これは `azdata` コマンドです。

```bash
azdata bdc spark batch info --batch-id 0
```

これは、Livy を使用する `curl` コマンドです。

```bash
curl -k -u <USER>:<PASSWORD> -X POST <LIVY_ENDPOINT>/batches/<BATCH_ID>
```

指定された ID の Spark バッチの __状態情報を取得する__ には、次のコマンドを実行します。

これは `azdata` コマンドです。

```bash
azdata bdc spark batch state --batch-id 0
```

これは、Livy を使用する `curl` コマンドです。

```bash
curl -k -u <USER>:<PASSWORD> -X POST <LIVY_ENDPOINT>/batches/<BATCH_ID>/state
```

指定された ID の Spark バッチの __ログを取得する__ には、次のコマンドを実行します。

これは `azdata` コマンドです。

```bash
azdata bdc spark batch log --batch-id 0
```

これは、Livy を使用する `curl` コマンドです。

```bash
curl -k -u <USER>:<PASSWORD> -X POST <LIVY_ENDPOINT>/batches/<BATCH_ID>/log
```

## <a name="next-steps"></a>次のステップ

Spark コードのトラブルシューティングの詳細については、「[pyspark ノートブックのトラブルシューティング](troubleshoot-pyspark-notebook.md)」を参照してください。

Spark サンプル コードの包括的なセットは、GitHub の [SQL Server ビッグ データ クラスター Spark のサンプル](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/sql-big-data-cluster/spark)に関するページで入手できます。

SQL Server ビッグ データ クラスターと関連するシナリオの詳細については、「[[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]](big-data-cluster-overview.md)」を参照してください。
