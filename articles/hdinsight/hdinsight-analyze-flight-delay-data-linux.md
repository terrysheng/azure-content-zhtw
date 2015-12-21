<properties 
	pageTitle="在以 Linux 為基礎的 HDInsight 上使用 Hive 分析航班延誤資料 | Microsoft Azure" 
	description="了解如何在以 Linux 為基礎的 HDInsight 上使用 Hive 分析航班資料，然後使用 Sqoop 將資料匯出至 SQL Database。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/04/2015" 
	ms.author="larryfr"/>

#在 HDInsight 上使用 Hadoop 分析航班延誤資料

了解如何在以 Linux 為基礎的 HDInsight 上使用 Hive 分析航班延誤資料，然後使用 Sqoop 將資料匯出到 Azure SQL Database。

> [AZURE.NOTE]本文章中的個別部分適用於以 Windows 為基礎的 HDInsight 叢集 (例如 Python 與 Hive)，許多步驟則針對以 Linux 為基礎的叢集。如需適用於以 Windows 為基礎的叢集的步驟，請參閱[在 HDInsight 中使用 Hive 分析航班延誤資料](hdinsight-analyze-flight-delay-data.md)

###必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __HDInsight 叢集__。請參閱[在 Linux 上於 HDInsight 中搭配使用 Hadoop 與 Hive 入門](hdinsight-hadoop-linux-tutorial-get-started.md)，取得建立新的以 Linux 為基礎的 HDInsight 叢集的步驟。

- __Azure SQL Database__。您會使用 Azure SQL Database 做為目的地資料存放區。如果您還沒有 SQL Database，請參閱[如何建立與設定 Azure SQL Database](../sql-database/sql-database-create.md) 以建立一個。

- __Azure CLI__。如果您尚未安裝 Azure CLI，請參閱[安裝與設定 Azure CLI](../xplat-cli-install.md) 的詳細步驟。


##下載航班資料

1. 瀏覽至[創新技術研究管理部運輸統計處][rita-website]。
2. 在此頁面上選取下列值：

	| 名稱 | 值| | Filter Year | 2013 | | Filter Period | January | | Fields | Year、FlightDate、UniqueCarrier、Carrier、FlightNum、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。清除所有其他欄位 |

3. 按一下 [下載]。

##上傳資料

1. 使用以下命令將 zip 檔案上傳到 HDInsight 叢集前端節點：

		scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	以 zip 檔案名稱取代 __FILENAME__。以 HDInsight 叢集的 SSH 登入取代 __USERNAME__。將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。
	
	> [AZURE.NOTE]如果您使用密碼來驗證您的 SSH 登入，系統會提示您輸入密碼。如果您使用的是公開金鑰，您可能必須使用 `-i` 參數並指定對應的私密金鑰路徑。例如 `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`。

2. 完成上傳之後，使用 SSH 連線到叢集：

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	如需搭配使用 SSH 與以 Linux 為基礎的 HDInsight 的詳細資訊，請參閱下列文章：
	
	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)
	
3. 連線之後，請使用以下命令解壓縮 .zip 檔案：

		unzip FILENAME.zip
	
	這會解壓縮一個 .csv 檔案 (大小大約為 60MB)。
	
4. 使用以下命令在 WASB (HDInsight 所使用的分散式資料存放區) 上建立新目錄，並複製檔案：

	hadoop fs -mkdir -p /tutorials/flightdelays/data hadoop fs -copyFromLocal FILENAME.csv /tutorials/flightdelays/data/FILENAME.csv
	
##建立並執行 HiveQL

使用下列步驟將 CSV 檔案的資料匯入到名為 __Delays__ 的 Hive 資料表。

1. 使用以下命令建立並編輯名為 __flightdelays.hql__ 的新檔案：

		nano flightdelays.hql
		
	使用下列項目做為此檔案的內容：
	
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
		LOCATION '/tutorials/flightdelays/data';
		
		-- Drop the delays table if it exists
		DROP TABLE delays;
		-- Create the delays table and populate it with data
		-- pulled in from the CSV file (via the external table defined previously)
		CREATE TABLE delays AS
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
		
2. 依序按 __Ctrl + X__，然後 __Y__ 儲存檔案。

3. 使用以下命令啟動 Hive 並執行 __flightdelays.hql__ 檔案：

		hive -i flightdelays.hql
		
	這會執行檔案，然後傳回 `hive>` 提示字元。

4. 當您接收 `hive>` 提示字元時，請使用以下命令從匯入的航班延誤資料抓取資料。

		INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
		SELECT regexp_replace(origin_city_name, '''', ''),
			avg(weather_delay)
		FROM delays
		WHERE weather_delay IS NOT NULL
		GROUP BY origin_city_name;

	這會抓取經歷過因氣候因素而延誤的城市清單以及平均延誤時間，並會儲存到 `/tutorials/flightdelays/output`。稍後，Sqoop 會從此位置讀取該資料，並匯出到 Azure SQL Database。

##建立 SQL Database

使用下列步驟建立 Azure SQL Database。這會用來保留透過 Sqoop 從 HDInsight 匯出的資料。

1. 在您安裝 Azure CLI 的開發環境中，使用以下命令建立新的 Azure SQL Database：

		azure sql server create <adminLogin> <adminPassword> <region>

	例如，`azure sql server create admin password "West US"`

	命令完成時，您應該會收到類似這樣的回應：

		info:    Executing command sql server create
		+ Creating SQL Server
		data:    Server Name i1qwc540ts
		info:    sql server create command OK

> [AZURE.IMPORTANT]請注意此命令傳回的伺服器名稱。這是所建立的 SQL Database 伺服器的簡短名稱。完整網域名稱 (FQDN) 為 `<shortname>.database.windows.net`。

2. 使用下列命令，在 SQL Database 伺服器上建立名為 **sqooptest** 的資料庫：

        azure sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    完成時會傳回 [確定] 訊息。

	> [AZURE.NOTE]如果您收到的錯誤指出您沒有存取權，您可能需要使用下列命令，將用戶端工作站的 IP 位址加入至 SQL Database 防火牆：
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##建立 SQL Database 資料表

> [AZURE.NOTE]連接至 SQL Database 建立資料表的方法有很多種。下列步驟會從 HDInsight 叢集使用 [FreeTDS](http://www.freetds.org/)。

1. 使用 SSH 連線到以 Linux 為基礎的 HDInsight，並從 SSH 工作階段執行下列步驟。

3. 使用下列命令來安裝 FreeTDS：

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. 安裝 FreeTDS 後，請使用下列命令來連接至先前建立的 SQL Database 伺服器：

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    您將收到類似以下的輸出：

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. 在 `1>` 提示字元輸入下列幾行：

        CREATE TABLE [dbo].[delays](
		[origin_city_name] [nvarchar](50) NOT NULL,
		[weather_delay] float,
		CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
		([origin_city_name] ASC))
		GO

    輸入 `GO` 陳述式後，將評估先前的陳述式。這會建立名為 __delays__ 的新資料表，包含叢集索引 (SQL Database 所需)。

    使用下列命令來確認已建立資料表：

        SELECT * FROM information_schema.tables
        GO

    您應該會看到如下所示的輸出：

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     delays      BASE TABLE

8. 在 `1>` 提示字元輸入 `exit` 以結束 tsql 公用程式。
	
##使用 Sqoop 匯出資料

1. 使用下列命令來建立從 Sqoop lib 目錄到 SQL Server JDBC 驅動程式的連結。這可讓 Sqoop 使用此驅動程式來聯繫 SQL Database：

		sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

2. 使用下列命令以確認 Sqoop 看得見您的 SQL Database：

		sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

	這應該會傳回一份資料庫清單，其中包含您稍早建立的 sqooptest 資料庫。

3. 使用以下命令，將資料從 hivesampletable 匯出至 mobiledata 資料表：

		sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasb:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

	這會指示 Sqoop 連線到 SQL Database、sqooptest 資料庫，並從 wasb:///tutorials/flightdelays/output (我們稍早儲存 Hive 查詢輸出的位置) 匯出資料到延誤資料表。

4. 在命令完成後，使用下列程式碼連接至使用 TSQL 的資料庫：

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

	連線之後，使用下列陳述式來確認資料已匯出到 mobiledata 資料表：
	
		SELECT * FROM delays
		GO

	您應會看到資料表中的資料清單。輸入 `exit` 以結束 tsql 公用程式。

##<a id="nextsteps"></a> 後續步驟
現在您已了解如何將檔案上傳至 Azure Blob 儲存體、如何使用 Azure Blob 儲存體中的資料填入 Hive 資料表、如何執行 Hive 查詢，以及如何使用 Sqoop 將資料從 HDFS 匯出至 Azure SQL Database。若要深入了解，請參閱下列文章：

* [開始使用 HDInsight][hdinsight-get-started]
* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [在 HDInsight 上使用 Oozie][hdinsight-use-oozie]
* [搭配 HDInsight 使用 Sqoop][hdinsight-use-sqoop]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [開發 HDInsight 的 Java MapReduce 程式][hdinsight-develop-mapreduce]
* [開發適用於 HDInsight 的 Python Hadoop 串流程式][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 

<!-------HONumber=AcomDC_1210_2015--->