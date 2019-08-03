---
title: Sqlrutils を使用してストアドプロシージャを作成する方法
description: SQL Server の sqlrutils R パッケージを使用して、R 言語コードを、引数としてストアドプロシージャに渡すことができる単一の関数にバンドルします。
ms.prod: sql
ms.technology: machine-learning
ms.date: 04/15/2018
ms.topic: conceptual
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=sqlallproducts-allversions'
ms.openlocfilehash: 22faeb2ea9f3e2104c2c1921b91a26ec5068079e
ms.sourcegitcommit: 321497065ecd7ecde9bff378464db8da426e9e14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68715702"
---
# <a name="create-a-stored-procedure-using-sqlrutils"></a>sqlrutils を使用してストアド プロシージャを作成する
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]

この記事では、T-sql ストアドプロシージャとして実行するように R コードを変換する手順について説明します。 考えられる最良の結果を得るために、コードを少し変更し、すべての入力をパラメーター化できるようにする必要がある場合があります。

## <a name="bkmk_rewrite"></a>手順 1. R スクリプトの書き直し

最良の結果を得るために、R コードを書き直して1つの関数としてカプセル化する必要があります。

関数によって使用されるすべての変数は、関数内で定義するか、入力パラメーターとして定義する必要があります。 この記事の[サンプルコード](#samples)を参照してください。

また、R 関数の入力パラメーターは、SQL ストアドプロシージャの入力パラメーターになるため、入力と出力が次の型要件に準拠していることを確認する必要があります。

### <a name="inputs"></a>入力

入力パラメーターの中には、最大で1つのデータフレームを使用できます。

データ フレーム内のオブジェクトと、関数の他のすべての入力パラメーターは、次の R データ型でなければなりません。
- POSIXct
- NUMERIC
- character
- 整数 (integer)
- logical
- raw

入力の種類が上記の種類でない場合は、シリアル化し、 *raw*として関数に渡す必要があります。 その場合、関数に入力を逆シリアル化するコードを含める必要もあります。

### <a name="outputs"></a>出力

関数は次のいずれかを出力できます。

- サポートされているデータ型を含むデータ フレーム。 データ フレームのすべてのオブジェクトでは、サポートされているデータ型のいずれかを使用する必要があります。
- 最大で 1 つのデータ フレームを含む名前付きリスト。 リストのすべてのメンバーでは、サポートされるデータ型のいずれかを使用する必要があります。
- NULL (関数が結果を返さない場合)

## <a name="step-2-generate-required-objects"></a>手順 2. 必要なオブジェクトの生成

R コードをクリーンアップし、1つの関数として呼び出すことができるようになったら、 **sqlrutils**パッケージの関数を使用して、実際にストアドプロシージャをビルドするコンストラクターに渡すことができる形式で入力と出力を準備します。

**sqlrutils**には、入力データスキーマと型を定義し、出力データスキーマと型を定義する関数が用意されています。 また、R オブジェクトを必要な出力の種類に変換できる関数も含まれています。 コードで使用するデータ型に応じて、複数の関数を呼び出して必要なオブジェクトを作成することもできます。

### <a name="inputs"></a>入力

関数が入力を受け取る場合は、各入力に対して次の関数を呼び出します。

- `setInputData`入力がデータフレームである場合
- `setInputParameter`その他すべての入力型

各関数を呼び出すと、R オブジェクトが作成されます。このオブジェクトは、後で`StoredProcedure`に引数として渡すことで、完全なストアドプロシージャを作成します。

### <a name="outputs"></a>出力

**sqlrutils**には、リストなどの R オブジェクトを SQL Server で必要なデータフレームに変換するための複数の関数が用意されています。
関数でデータ フレームが直接出力された場合は、最初にリストにラップせずに、この手順を省略できます。
関数から NULL が返された場合は、このステップの変換をスキップすることもできます。

リストを変換する場合、またはリストから特定の項目を取得する場合は、次の関数から選択します。

- `setOutputData`リストから取得する変数がデータフレームである場合
- `setOutputParameter`リストの他のすべてのメンバーについて

各関数を呼び出すと、R オブジェクトが作成されます。このオブジェクトは、後で`StoredProcedure`に引数として渡すことで、完全なストアドプロシージャを作成します。

## <a name="step-3-generate-the-stored-procedure"></a>手順 3. ストアドプロシージャの生成

すべての入力パラメーターと出力パラメーターの準備ができたら、 `StoredProcedure`コンストラクターを呼び出します。

**使用方法**

`StoredProcedure (func, spName, ..., filePath = NULL ,dbName = NULL, connectionString = NULL, batchSeparator = "GO")`

例として、次のパラメーターを使用して、 **sp_rsample**という名前のストアドプロシージャを作成するとします。

- 既存の関数**foosql**を使用します。 関数は、R 関数**foo**内の既存のコードに基づいていましたが、[このセクション](#bkmk_rewrite)で説明する要件に準拠し、更新された関数を**foosql**として指定することで、関数を書き直します。
- データフレームの**queryinput**を入力として使用します。
- 出力として R 変数名**sqloutput**を持つデータフレームを生成します
- T-sql コードを`C:\Temp`フォルダー内のファイルとして作成し、後で SQL Server Management Studio を使用して実行できるようにする場合

```R
StoredProcedure (foosql, sp_rsample, queryinput, sqloutput, filePath = "C:\\Temp")
```

> [!NOTE]
> ファイルをファイルシステムに書き込んでいるため、データベース接続を定義する引数を省略できます。

関数の出力は、SQL Server 2016 (R Services が必要) または SQL Server 2017 (R との Machine Learning Services が必要) のインスタンスで実行できる T-sql ストアドプロシージャです。 

その他の例については、R 環境`help(StoredProcedure)`からを呼び出して、パッケージのヘルプを参照してください。

## <a name="step-4-register-and-run-the-stored-procedure"></a>手順 4. ストアドプロシージャを登録して実行する

ストアドプロシージャを実行するには、次の2つの方法があります。

- SQL Server 2016 または SQL Server 2017 インスタンスへの接続をサポートする任意のクライアントからの T-sql の使用
- R 環境から

どちらの方法でも、ストアドプロシージャを使用するデータベースにストアドプロシージャを登録する必要があります。

### <a name="register-the-stored-procedure"></a>ストアドプロシージャを登録する

ストアドプロシージャは、R を使用して登録することも、T-sql で CREATE PROCEDURE ステートメントを実行して登録することもできます。

- T-sql を使用します。  T-sql に慣れている場合は、SQL Server Management Studio (または SQL DDL コマンドを実行できるその他のクライアント) を開き、 `StoredProcedure`関数によって準備されたコードを使用して CREATE PROCEDURE ステートメントを実行します。
- R を使用します。まだ R 環境では、 `registerStoredProcedure` **sqlrutils**の関数を使用して、ストアドプロシージャをデータベースに登録できます。

  たとえば、次の R 呼び出しを行うことで、 *Sqlconnstr*で定義されているインスタンスとデータベースにストアドプロシージャ**sp_rsample**を登録できます。

  ```R
  registerStoredProcedure(sp_rsample, sqlConnStr)
  ```


> [!IMPORTANT]
> R と SQL のどちらを使用するかにかかわらず、新しいデータベースオブジェクトを作成する権限を持つアカウントを使用してステートメントを実行する必要があります。

### <a name="run-using-sql"></a>SQL を使用して実行する

ストアドプロシージャを作成した後、T-sql をサポートする任意のクライアントを使用して SQL database への接続を開き、ストアドプロシージャで必要なパラメーターの値を渡します。

### <a name="run-using-r"></a>R を使用して実行する

SQL Server からではなく、R コードからストアドプロシージャを実行する場合は、追加の準備が必要になります。 たとえば、ストアドプロシージャに入力値が必要な場合は、関数を実行する前にそれらの入力パラメーターを設定し、これらのオブジェクトを R コード内のストアドプロシージャに渡す必要があります。

準備された SQL ストアドプロシージャを呼び出すプロセス全体は、次のとおりです。

1. 入力パラメーター オブジェクトのリストを取得するには、 `getInputParameters` を呼び出します。
2. `$query` を定義するか、入力パラメーターごとに `$value` を設定します。
3. `executeStoredProcedure` を使用して、設定した入力パラメーター オブジェクトのリストを渡し、R 開発環境からストアド プロシージャを実行します。

## <a name = "samples"></a>よう

この例では、SQL Server データベースからデータを取得し、データに対して何らかの変換を実行し、別のデータベースに保存する R スクリプトの before バージョンと after バージョンを示します。

この単純な例は、ストアドプロシージャへの変換を容易にするために R コードを再配置する方法を示すためにのみ使用されます。

### <a name="before-code-preparation"></a>コードの準備前


```R
sqlConnFrom <- "Driver={ODBC Driver 13 for SQL Server};Server=MyServer01;Database=AirlineSrc;Trusted_Connection=Yes;"
  
sqlConnTo <- "Driver={ODBC Driver 13 for SQL Server};Server=MyServer01;Database=AirlineTest;Trusted_Connection=Yes;"
  
sqlQueryAirline <- "SELECT TOP 10000 ArrDelay, CRSDepTime, DayOfWeek FROM [AirlineDemoSmall]"
  
dsSqlFrom <- RxSqlServerData(sqlQuery = sqlQueryAirline, connectionString = sqlConnFrom)
  
dsSqlTo <- RxSqlServerData(table = "cleanData", connectionString = sqlConnTo)
  
xFunc <- function(data) {
    data$CRSDepHour <- as.integer(trunc(data$CRSDepTime))
    return(data)
    }
  
xVars <- c("CRSDepTime")
  
sqlCompute <- RxInSqlServer(numTasks = 4, connectionString = sqlConnTo)
  
rxOpen(dsSqlFrom)
rxOpen(dsSqlTo)
  
if (rxSqlServerTableExists("cleanData", connectionString = sqlConnTo))   {
    rxSqlServerDropTable("cleanData")}
  
rxDataStep(inData = dsSqlFrom, 
     outFile = dsSqlTo,
     transformFunc = xFunc,
     transformVars = xVars,
     overwrite = TRUE)
```

> [!NOTE]
> 
> *RxSqlServerData*関数を呼び出すのではなく、ODBC 接続を使用する場合は、データベースに対して操作を実行する前に、 *rxOpen*を使用して接続を開く必要があります。


### <a name="after-code-preparation"></a>コードの準備後

更新後のバージョンでは、最初の行によって関数名が定義されます。 元の R ソリューションのその他すべてのコードは、その機能の一部になります。

```R
myetl1function <- function() { 
   sqlConnFrom <- "Driver={ODBC Driver 13 for SQL Server};Server=MyServer01;Database=Airline01;Trusted_Connection=Yes;"
   sqlConnTo <- "Driver={ODBC Driver 13 for SQL Server};Server=MyServer02;Database=Airline02;Trusted_Connection=Yes;"
    
   sqlQueryAirline <- "SELECT TOP 10000 ArrDelay, CRSDepTime, DayOfWeek FROM [AirlineDemoSmall]"

   dsSqlFrom <- RxSqlServerData(sqlQuery = sqlQueryAirline, connectionString = sqlConnFrom)
  
   dsSqlTo <- RxSqlServerData(table = "cleanData", connectionString = sqlConnTo)
  
   xFunc <- function(data) {
     data$CRSDepHour <- as.integer(trunc(data$CRSDepTime))
     return(data)}
  
   xVars <- c("CRSDepTime")
  
   sqlCompute <- RxInSqlServer(numTasks = 4, connectionString = sqlConnTo)
  
   if (rxSqlServerTableExists("cleanData", connectionString = sqlConnTo)) {rxSqlServerDropTable("cleanData")}
  
   rxDataStep(inData = dsSqlFrom, 
        outFile = dsSqlTo,
        transformFunc = xFunc,
        transformVars = xVars,
        overwrite = TRUE)
   return(NULL)
}
```

> [!NOTE]
> 
> コードの一部として明示的に ODBC 接続を開く必要はありませんが、 **sqlrutils**を使用するには引き続き ODBC 接続が必要になります。

## <a name="see-also"></a>参照

[sqlrutils (SQL)](ref-r-sqlrutils.md)


