---
title: 'チュートリアル: HDInsight の Hive を使用して抽出、変換、読み込み (ETL) を実行する - Azure'
description: 生の CSV データセットからデータを抽出し、HDInsight の Hive を使用してデータを変換した後、Sqoop を使用することで変換済みデータを Azure SQL データベースに読み込む方法について説明します。
services: storage
author: jamesbak
tags: azure-portal
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: jamesbak
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: 376ac623c37183cdfbd6e27c54b0c726928237e2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526006"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>チュートリアル: Azure HDInsight の Apache Hive を使用したデータの抽出、変換、読み込み

このチュートリアルでは、生の CSV データ ファイルを取得して HDInsight クラスターにインポートした後、Azure HDInsight の Apache Hive を使用してデータを変換します。 データを変換したら、Apache Sqoop を使用して Azure SQL データベースにデータを読み込みます。 この記事では、一般に公開されているフライト データを使用します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * サンプルのフライト データをダウンロードする
> * HDInsight クラスターにデータをアップロードする
> * Hive を使用してデータを変換する
> * Azure SQL データベースにテーブルを作成する
> * Sqoop を使用して Azure SQL データベースにデータをエクスポートする


次の図に、一般的な ETL アプリケーション フローを示します。

![Azure HDInsight の Apache Hive を使用した ETL 操作](./media/tutorial-extract-transform-load-hive/hdinsight-etl-architecture.png "Azure HDInsight の Apache Hive を使用した ETL 操作")

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* **HDInsight での Linux ベースの Hadoop クラスター**。 Linux ベースの新しい HDInsight クラスターを作成する手順については、[Hadoop、Spark、Kafka などを使用した HDInsight でのクラスターの設定](./quickstart-create-connect-hdi-cluster.md)に関するページをご覧ください。

* **Azure SQL データベース**。 保存先データ ストアとして Azure SQL Database を使用します。 SQL データベースがない場合は、「[Azure Portal で Azure SQL データベースを作成する](../../sql-database/sql-database-get-started.md)」を参照してください。

* **Azure CLI 2.0**。 Azure CLI をインストールしていない場合、詳しい手順については [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関する記事をご覧ください。

* **SSH クライアント**。 詳細については、「[SSH を使用して HDInsight (Hadoop) に接続する](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、Azure HDInsight バージョン 3.4 以降で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="download-the-flight-data"></a>フライト データのダウンロード

1. [米国運輸省研究・革新技術庁/運輸統計局][rita-website]のページに移動します。

2. このページで、次の値を選択します。

   | Name | 値 |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | フィールド |Year、FlightDate、UniqueCarrier、Carrier、FlightNum、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |
   その他のフィールドはすべてオフにします

3. **[Download]** を選択します。 選択したデータ フィールドを含む .zip ファイルがダウンロードされます。

## <a name="upload-data-to-an-hdinsight-cluster"></a>HDInsight クラスターにデータをアップロードする

HDInsight クラスターに関連付けられたストレージにデータをアップロードする方法はたくさんあります。 このセクションでは、`scp` を使用してデータをアップロードします。 他のデータ アップロード方法については、「[Use Distcp to copy data between Azure Storage Blobs and Data Lake Storage Gen2 Preview (Distcp を使用して Azure Storage Blob と Data Lake Store Gen2 プレビューの間でデータをコピーする)](use-distcp.md)」を参照してください。

1. コマンド プロンプトを開き、次のコマンドを使用して HDInsight クラスターのヘッド ノードに .zip ファイルをアップロードします。

    ```bash
    scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
    ```

    *FILE_NAME* を .zip ファイルの名前に置き換えます。 *SSH_USER_NAME* を HDInsight クラスターの SSH ログインに置き換えます。 *CLUSTER_NAME* を HDInsight クラスターの名前に置き換えます。

   > [!NOTE]
   > パスワードを使用して SSH ログインを認証する場合は、パスワードを入力するよう求められます。 公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーへのパスを指定することが必要な場合があります。 たとえば、「`scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`」のように入力します。

2. アップロードが完了したら、SSH を使用してクラスターに接続します。 コマンド プロンプトで次のコマンドを入力します。

    ```bash
    ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
    ```

3. 次のコマンドを使用して .zip ファイルを解凍します。

    ```bash
    unzip <FILE_NAME>.zip
    ```

    このコマンドで、約 60 MB の *.csv* ファイルが抽出されます。

4. 次のコマンドを使用してディレクトリを作成し、*.csv* ファイルをそのディレクトリにコピーします。

    ```bash
    hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
    hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
    ```

5. Data Lake Storage Gen2 ファイル システムを作成します。

    ```bash
    hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
    ```

## <a name="transform-data-using-a-hive-query"></a>Hive クエリを使用したデータの変換

HDInsight クラスター上で Hive ジョブを実行する方法はたくさんあります。 このセクションでは、Beeline を使用して Hive ジョブを実行します。 Hive ジョブを実行するその他の方法については、[HDInsight での Hive の使用](../../hdinsight/hadoop/hdinsight-use-hive.md)に関する記事をご覧ください。

Hive ジョブの一環として、.csv ファイルから **Delays** という名前の Hive テーブルにデータをインポートします。

1. HDInsight クラスター用に既に開いている SSH プロンプトから、次のコマンドを使用して **flightdelays.hql** という名前の新しいファイルを作成して編集します。

    ```bash
    nano flightdelays.hql
    ```

2. このファイルの内容として、次のテキストを使用します。

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. ファイルを保存するには、**Esc** キーを押した後 `:x` を入力します。

3. Hive を起動し、**flightdelays.hql** ファイルを実行するには、次のコマンドを使用します。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. __flightdelays.hql__ スクリプトの実行が完了したら、次のコマンドを使用して対話型 Beeline セッションを開きます。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. `jdbc:hive2://localhost:10001/>` プロンプトが表示されたら、次のクエリを使用してインポートされたフライト遅延データからデータを取得します。

    ```hiveql
    INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    このクエリにより、悪天候による遅延が発生した都市の一覧と平均遅延時間が取得され、`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` に保存されます。 その後、Sqoop がこの場所からデータを読み取り、Azure SQL Database にエクスポートします。

6. Beeline を終了するには、プロンプトで「 `!quit` 」と入力します。

## <a name="create-a-sql-database-table"></a>SQL データベース テーブルの作成

このセクションでは、Azure SQL データベースを既に作成していることを前提としています。 まだ SQL データベースがない場合は、「[Azure Portal で Azure SQL データベースを作成する](../../sql-database/sql-database-get-started.md)」の情報を使用して作成します。

SQL データベースが既にある場合は、サーバー名を入手する必要があります。 [Azure Portal](https://portal.azure.com) でサーバー名を見つけるには、**[SQL データベース]** を選択し、使用するデータベースの名前でフィルターを掛けます。 サーバー名は **[サーバー名]** 列に表示されます。

![Azure SQL サーバーの詳細を取得](./media/tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Azure SQL サーバーの詳細を取得")

> [!NOTE]
> SQL Database に接続してテーブルを作成するには、多くの方法があります。 次の手順では、HDInsight クラスターから [FreeTDS](http://www.freetds.org/) を使用します。


1. FreeTDS をインストールするには、クラスターへの SSH 接続から次のコマンドを使用します。

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. インストールが完了したら、次のコマンドを使用して SQL Database サーバーに接続します。 **serverName** は SQL Database サーバー名に置き換えてください。 **adminLogin** と **adminPassword** は SQL Database のログイン情報に置き換えてください。 **databaseName** はデータベース名に置き換えてください。

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
    ```

    メッセージが表示されたら、SQL Database 管理者ログインのパスワードを入力します。

    次のテキストのような出力が返されます。

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. `1>` プロンプトで、以下の行を入力します。

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    `GO` ステートメントを入力すると、前のステートメントが評価されます。 このクエリにより、クラスター化インデックス付きの、**delays** という名前のテーブルが作成されます。

    次のクエリを使用して、テーブルが作成されたことを確認します。

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    出力は次のテキストのようになります。

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. Enter `exit` at the `1>`」と入力して、tsql ユーティリティを終了します。


## <a name="export-data-to-sql-database-using-sqoop"></a>Sqoop を使用して SQL データベースにデータをエクスポートする

前のセクションで、変換済みデータを `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` にコピーしました。 このセクションでは、Sqoop を使用して、`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` のデータを、Azure SQL データベースに作成したテーブルにエクスポートします。 

1. 次のコマンドを使用して、Sqoop が SQL データベースを認識できることを確認します。

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
    ```

    このコマンドにより、先ほど delays テーブルを作成したデータベースを含むデータベースの一覧が返されます。

2. 次のコマンドを使って、hivesampletable から delays テーブルにデータをエクスポートします。

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' 
    --fields-terminated-by '\t' -m 1
    ```

    Sqoop は delays テーブルを含むデータベースに接続して、`/tutorials/flightdelays/output` ディレクトリから delays テーブルにデータをエクスポートします。

3. sqoop コマンドが完了したら、tsql ユーティリティを使ってデータベースに接続します。

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
    ```

    次のステートメントを使って、データが delays テーブルにエクスポートされたことを確認します。

    ```sql
    SELECT * FROM delays
    GO
    ```

    テーブル内のデータの一覧が表示されます。 テーブルには、都市の名前と、その都市のフライトの平均遅延時間が含まれます。 

    「 `exit` 」と入力して、tsql ユーティリティを終了します。

## <a name="next-steps"></a>次の手順

HDInsight でのデータ操作の詳細については、次の記事を参照してください。

* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [HDInsight での Hadoop 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]
* [HDInsight 用 Python ストリーミング MapReduce プログラムの開発][hdinsight-develop-streaming]
* [HDInsight での Oozie の使用][hdinsight-use-oozie]
* [HDInsight での Sqoop の使用][hdinsight-use-sqoop]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: ../../hdinsight/hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:../../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-provision]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: ../../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: ../../hdinsight/hdinsight-upload-data.md
[hdinsight-get-started]: ../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:../../hdinsight/hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:../../hdinsight/hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:../../hdinsight/hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:../../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
