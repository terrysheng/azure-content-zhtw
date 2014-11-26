<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analyze flight delay data using Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# 在 HDInsight 上使用 Hadoop 分析航班延誤資料

Hive 提供一種透過 SQL 式指令碼語言執行 Hadoop MapReduce 工作的工具，稱為 *[HiveQL][HiveQL]*，可用來總結、查詢和分析大量資料。本教學課程說明如何使用 Hive 來計算機場的平均誤點時間，以及如何使用 Sqoop 將結果匯出至 SQL Database。

**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：

-   Azure HDInsight 叢集。如需佈建 HDInsight 叢集的相關資訊，請參閱[開始使用 HDInsight][開始使用 HDInsight] 或[佈建 HDInsight 叢集][佈建 HDInsight 叢集]。
-   已安裝並設定 Azure PowerShell 的工作站。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。

**預估完成時間：** 30 分鐘

## 本教學課程內容

-   [教學課程前置工作][教學課程前置工作]
-   [建立及上傳 HiveQL 指令碼][建立及上傳 HiveQL 指令碼]
-   [執行 HiveQL 指令碼][執行 HiveQL 指令碼]
-   [將輸出匯出至 Azure SQL Database][將輸出匯出至 Azure SQL Database]
-   [後續步驟][後續步驟]

## <span id="prepare"></span></a>教學課程前置工作

本教學課程將在您的工作站上使用取自[創新技術研究管理部運輸統計處][創新技術研究管理部運輸統計處] (RITA) 的飛行航班準點率資料。您將執行下列作業：

1.  使用網頁瀏覽器，將準點率資料從 RITA 下載至您的工作站
2.  使用 Azure PowerShell 將資料上傳至 HDInsight
3.  使用 Azure PowerShell 進行 SQL Database 的資料匯出準備工作

**了解 HDInsight 儲存體**

HDInsight 會使用 Azure Blob 儲存體來儲存資料。我們稱之為 *WASB* 或 *Azure 儲存體 - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]。

當您佈建 HDInsight 叢集時，會將一個 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。除了此容器以外，您也可以在佈建過程中，從相同的 Azure 儲存體帳戶或不同的 Azure 儲存體帳戶新增其他容器。如需有關新增其他儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight 叢集][佈建 HDInsight 叢集]。

為簡化本教學課程中使用的 PowerShell 指令碼，所有檔案都會儲存在位於 */tutorials/flightdelays* 的預設檔案系統容器中。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。

WASB 語法如下：

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 叢集 3.0 版僅支援 *wasb://* 語法。HDInsight 2.1 和 1.6 支援舊的 *asv://* 語法，但在 HDInsight 3.0 叢集中已不受支援，未來的版本也不加以支援。

> The WASB path is virtual path.如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]。

儲存在預設檔案系統容器中的檔案，可使用下列任一 URI (範例中使用 flightdelays.hql) 從 HDInsight 存取：

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

如果您要直接從儲存體帳戶存取檔案，檔案的Blob 名稱為：

    tutorials/flightdelays/flightdelays.hql

下表列出本教學課程中使用的檔案：

<table border="1">
<tr><td><strong>檔案</strong></td><td><strong>說明</strong></td></tr>
<tr><td>\tutotirals\flightdelays\data</td><td>Hive 的輸入航班資料</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Hive 的輸出</td></tr>
<tr><td>\tutorials\flightdelays\flightdelays.hql</td><td>HiveQL 指令碼檔案 </td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Hadoop 工作狀態status</td></tr>
</table>


**了解 Hive 內部資料表和外部資料表**

關於 Hive 內部資料表和外部資料表，有若干事項您必須了解：

-   CREATE TABLE 命令會建立內部資料表。資料檔案必須位於預設容器中。
-   CREATE TABLE 命令會將資料檔案移至 /hive/warehouse/<tablename> 資料夾。
-   CREATE EXTERNAL TABLE 命令會建立外部資料表。資料檔案可位於預設容器外。
-   CREATE EXTERNAL TABLE 命令不會移動資料檔案。
-   CREATE EXTERNAL TABLE 命令不允許在 LOCATION 中放置任何資料夾。因此，此教學課程複製了 sample.log 檔案。

如需詳細資訊，請參閱 [HDInsight：Hive 內部和外部資料表簡介][cindygross-hive-tables]。

> [WACOM.NOTE] 其中一個 HiveQL 陳述式會建立 Hive 外部資料表。Hive 外部資料表會將資料檔案保存在原始位置中。Hive 內部資料表會將資料檔案移至 hive\\warehouse。使用 Hive 外部資料表時，資料檔案必須位於預設檔案系統 WASB 容器中。如果您選擇將航班資料檔案儲存在預設 Blob 容器以外的容器中，則必須使用 Hive 內部資料表。

**下載航班資料**

1.  瀏覽至[創新技術研究管理部運輸統計處][創新技術研究管理部運輸統計處] (RITA)。
2.  在此頁面上選取下列值：

	<table border="1">
	<tr><th>名稱</th><th>值 </th></tr>
	<tr><td>篩選年份</td><td>2012</td></tr>
	<tr><td>篩選期間</td><td>一月 </td></tr>
	<tr><td>欄位：</td><td><i>Year</i>、<i>FlightDate</i>、<i>UniqueCarrier</i>、<i>Carrier</i>、<i>FlightNum</i>、<i>OriginAirportID</i>、<i>Origin</i>、<i>OriginCityName</i>、
	<i>OriginState</i>、<i>DestAirportID</i>、<i>Dest</i>、<i>DestCityName</i>、<i>DestState</i>、<i>DepDelayMinutes</i>、<i>ArrDelay</i>、
	<i>ArrDelayMinutes</i>、<i>CarrierDelay</i>、<i>WeatherDelay</i>、<i>NASDelay</i>、<i>SecurityDelay</i>、<i>LateAircraftDelay</i> (請清除其餘所有欄位)</td></tr>
	</table>
3.  按一下 [下載]。每個檔案可能需要 15 分鐘的下載時間。
4.  將檔案解壓縮至 **C:\\Tutorials\\FlightDelays\\Data** 資料夾。每個檔案皆為 CSV 檔案，大小約為 60 GB。
5.  將檔案重新命名為檔案資料所屬月份的名稱。例如，包含一月份資料的檔案，應命名為 *January.csv*。
6.  重複步驟 2 到 5，以下載 2012 年各個月份的檔案。您至少要有一個檔案，才能執行此教學課程。

**將航班誤點資料上傳至 Azure Blob 儲存體**

1.  開啟 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。
2.  執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

    系統會提示您輸入 Azure 帳號認證。

3.  設定前三個變數，然後執行命令。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $ContainerName = "<BlobStorageContainerName>"

        $localFolder = "C:\Tutorials\FlightDelays\Data"
        $destFolder = "tutorials/flightdelays/data"

        $month = "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    以下是變數及其說明：

    <table border="1">

    <tr>
    <td>
    <strong>變數名稱</strong>

    </td>
    <td>
    <strong>說明</strong>

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    您的 Azure 訂用帳戶名稱。

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    用以儲存航班資料檔案的 Azure 儲存體帳戶。建議您使用預設儲存體帳戶。

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    用以儲存航班資料檔案的 Azure Blob 儲存體容器。建議您使用預設 HDInsight 叢集檔案系統 Blob 容器。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。

    </td>
    </tr>

    <tr>
    <td>
    $localFolder

    </td>
    <td>
    這是您的工作站上用來儲存航班誤點檔案的資料夾。

    </td>
    </tr>

    <tr>
    <td>
    $destFolder

    </td>
    <td>
    這是航班誤點資料所將上傳到的 WASB 路徑。Hadoop (HDInsight) 路徑須區分大小寫。

    </td>
    </tr>

    <tr>
    <td>
    $month

    </td>
    <td>
    如果您未將 12 個檔案全數下載，則必須更新此變數。

    </td>
    </tr>

    </table>
    </p>
4.  執行下列命令，以上傳並列出檔案：

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB        
        foreach ($item in $month){
            $fileName = "$localFolder\$item.csv"
            $blobName = "$destFolder/$item.csv"

            Write-Host "Copying $fileName to $blobName" -BackgroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder

如果您選擇使用不同的方法來上傳檔案，請確定檔案路徑為 *tutorials/flightdelays/data*。存取檔案的語法為：

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* 是您在上傳檔案時所建立的虛擬資料夾。請確認共有 12 個檔案，每個月份各一個。

**準備 SQL 資料庫**

1.  開啟 Azure PowerShell。
2.  執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

    系統會提示您輸入 Azure 帳號認證。

3.  在下列指令碼中設定前六個變數，然後執行命令：

        # Azure subscription name
        $subscriptionName = "<AzureSubscriptionName>"

        # SQL database server variables
        $sqlDatabaseServer = ""  # specify the Azure SQL database server name if you have one created. Otherwise use "".
        $sqlDatabaseUsername = "<SQLDatabaseUserName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseLocation = "<MicrosoftDataCenter>"   #For example, West US.

        # SQL database variables
        $sqlDatabaseName = ""  # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        $sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        $sqlDatabaseTableName = "AvgDelays"
        $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
            (
                [origin_city_name] ASC
            )
            )"

    以下是變數及其說明：

    <table border="1">

    <tr>
    <td>
    <strong>變數名稱</strong>

    </td>
    <td>
    <strong>說明</strong>

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    您的 Azure 訂用帳戶名稱。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Sqoop 在匯出資料時所使用的 SQL Database 伺服器名稱。若將其保留為空白，指令碼將會為您建立此名稱。否則，請指定現有的 SQL Datbase 或 SQL Server。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    SQL Database/SQL Server 使用者名稱。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    SQL Database/SQL Server 密碼。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseLocation

    </td>
    <td>
    只有在要讓指令碼為您建立 SQL Database 伺服器時，才應使用此變數。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Sqoop 在匯出資料時所使用的 SQL Database 名稱。若將其保留為空白，指令碼將會為您建立此名稱。否則，請指定現有的 SQL Datbase 或 SQL Server。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseMaxSizeGB

    </td>
    <td>
    只有在要讓指令碼為您建立 SQL Database 時，才應使用此變數。

    </td>
    </tr>

    </table>
    </p>
4.  執行下列命令，以建立 SQL Database 伺服器/資料庫/資料表。

        # Select the current Azure subscription in case there are multiple subscriptions
        Select-AzureSubscription $subscriptionName

        # create a new Azure SQL Database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
            Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
        }

        # Create a new SQL database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB

            Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
        }

        #Create AvgDelays table
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

## <span id="createscript"></span></a>建立及上傳 HiveQL 指令碼

使用 Azure PowerShell 可讓您逐一執行多個 HiveQL 陳述式，或將 HiveQL 陳述式封裝到指令碼檔案中。在本教學課程中，您將會建立 HiveQL 指令碼。指令碼檔案必須上傳至 WASB。在下一節中，您將使用 Azure PowerShell 執行指令碼檔案。

HiveQL 指令碼將執行下列作業：

1.  **捨棄 delays\_raw 資料表** (若此資料表已存在)。
2.  **建立 delays\_raw 外部 Hive 資料表** (指向含有航班誤點檔案的 WASB 位置)。此查詢會指定欄位將以 "," 分隔，且每一行都會以 "\\n" 結尾。如此，當欄位值*含有*逗號時，就會產生問題，因為 Hive 無法區分作為欄位分隔符號的逗號，與屬於欄位值的逗號 (在 ORIGIN\_CITY\_NAME 和 DEST\_CITY\_NAME 的欄位值中，就會出現此狀況)。為解決此問題，此查詢會建立 TEMP 資料行來放置不當分割為資料行的資料。
3.  **捨棄 delays 資料表** (若此資料表已存在)；
4.  **建立 delays 資料表**。此資料表有助於您在進一步處理之前先清除資料。此查詢會從 *delays\_raw* 資料表建立新資料表 *delays*。請注意，TEMP 資料行 (如前所述) 並不會複製，並且會使用 *substring* 函數來移除資料中的引號。
5.  **計算天候誤點平均值，並依城市名稱將結果分組。**此作業也會將結果輸出至 WASB。請注意，查詢將會移除資料中的撇號，並排除 *weather\_delay* 值為 *null* 的資料列。這是必要的，因為後續在本教學課程中使用的 Sqoop 依預設無法適當處理這些值。

如需 HiveQL 命令的完整清單，請參閱 [Hive 資料定義語言][HiveQL]。每個 HiveQL 命令都必須以分號結尾。

**建立 HiveQL 指令碼檔案**

1.  開啟 Azure PowerShell。
2.  執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

3.  設定前兩個變數，然後執行命令。

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    以下是變數及其說明：

    <table border="1">

    <tr>
    <td>
    <strong>變數名稱</strong>

    </td>
    <td>
    <strong>說明</strong>

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    用以儲存 HiveQL 指令碼檔案的 Azure 儲存體帳戶。在本教學課程提供的 PowerShell 指令碼中，航班資料檔案和指令碼檔案必須位於相同的 Azure 儲存體帳戶和 Blob 儲存體容器中。

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    用以儲存 HiveQL 指令碼檔案的 Azure Blob 儲存體容器。在本教學課程提供的 PowerShell 指令碼中，航班資料檔案和指令碼檔案必須位於相同的 Azure 儲存體帳戶和 Blob 儲存體容器中。

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    HiveQL 指令碼在上傳至 WASB 之前的本機檔案名稱。為簡化 PowerShell 指令碼，我們將在本機寫入檔案，然後使用 Set-AzureStorageBlobContent Cmdlet 將指令碼檔案上傳至 HDInsight。

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    這是在 WASB 上具有路徑的指令碼檔案名稱。

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    這是 HiveQL 指令碼在 WASB 上從中提取資料的資料夾。

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    這是 HiveQL 指令碼將輸出至 WASB 的目的地資料夾。後續在本教學課程中，您將使用 Sqoop 將此資料夾上的資料匯出至 Azure SQL Database。

    </td>
    </tr>

    </table>
    </p>
4.  執行下列命令以定義 HiveQL 陳述式：

        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

        $hqlDropDelays = "DROP TABLE delays;"

        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;" 

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

5.  執行下列命令，將 Hive 指令碼檔案寫入至工作站，並將其上傳至 WASB：

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    輸出應該類似：

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

## <span id="executehqlscript"></span></a>執行 HiveQL 指令碼

您可以使用數種 Azure PowerShell Cmdlet 來執行 Hive。本教學課程使用 Invoke-Hive。若要使用其他方法，請參閱[搭配 HDInsight 使用 Hive][搭配 HDInsight 使用 Hive]。使用 Invoke-Hive 可讓您執行 HiveQL 陳述式或 HiveQL 指令碼。您將會使用先前建立並上傳至 Azure Blob 儲存體的 HiveQL 指令碼。

Hive 路徑有已知問題。如需修正此問題的指示，請參閱 [TechNet Wiki][TechNet Wiki]。

**使用 PowerShell 執行 Hive 查詢**

1.  開啟 Azure PowerShell。
2.  執行下列命令以變更目前的目錄：

    cd \\Tutorials\\FlightDelays\\

    這是必要步驟，因為您要將 Hive 輸出的複本下載至工作站。根據預設，您沒有 PowerShell 資料夾的寫入權限。

3.  執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

4.  設定前三個變數，然後加以執行：

        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureBlobStorageContainerName>"
        $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
        $outputBlobName = "tutorials/flightdelays/output/000000_0"

    以下是變數及其說明：

    <table border="1">

    <tr>
    <td>
    <strong>變數名稱</strong>

    </td>
    <td>
    <strong>說明</strong>

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    將會執行 Hive 指令碼和 Sqoop 匯出的 HDInsight 叢集。

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    用以儲存 HiveQL 指令碼的 Azure 儲存體帳戶。請參閱<a href="#createscript">[建立及上傳 HiveQL 指令碼][1]</a>。

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    用以儲存 HiveQL 指令碼的 Azure Blob 儲存體容器。請參閱<a href="#createscript">[建立及上傳 HiveQL 指令碼][1]</a>。

    </td>
    </tr>

    <tr>
    <td>
    $hqlScriptFile

    </td>
    <td>
    這是 HiveQL 指令碼檔案的 URI。

    </td>
    </tr>

    <tr>
    <td>
    $outputBlobName

    </td>
    <td>
    這是 HiveQL 指令碼輸出檔案。預設名稱為 <i>000000\_0</i>。

    </td>
    </tr>

    </table>
    </p>
5.  執行下列命令以叫用 hive：

        Use-AzureHDInsightCluster $clusterName

        Invoke-Hive –File $hqlScriptFile

6.  執行下列命令以驗證結果：

        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 

        cat ".\$outputBlobName" | findstr /B "Ch"

    輸出應該類似：

        Champaign/Urbana 19.023255813953487
        Charleston 24.93975903614458
        Charleston/Dunbar 16.954545454545453
        Charlotte 16.88616981831665
        Charlotte Amalie 12.88888888888889
        Charlottesville 25.444444444444443
        Chattanooga 19.883561643835616
        Cheyenne 9.875
        Chicago 16.77321649680922
        Chico 12.340909090909092
        Christiansted 12.318181818181818

    在每個城市與誤點時間之間，都會有一個在 PowerShell 輸出視窗中未顯示出來的分隔符號。這是 "\\001"。您在執行 Sqoop 匯出時將會使用此分隔符號。

## <span id="exportdata"></span></a>將 Hive 工作輸出匯出至 Azure SQL Database

最後的步驟，是執行 Sqoop 匯出以將資料匯出至 SQL Database。您先前已在本教學課程中建立 SQL Database 和 AvgDelays 資料表。

**將資料匯出至 SQL Database**

1.  開啟 Azure PowerShell。
2.  執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

3.  設定前五個變數，然後執行命令：

        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    以下是變數及其說明：

    <table border="1">

    <tr>
    <td>
    <strong>變數名稱</strong>

    </td>
    <td>
    <strong>注意</strong>

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    HDInsight 叢集名稱。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Sqoop 會將資料匯出到的 SQL Database 伺服器。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    SQL Database 使用者名稱。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    SQL Database 使用者密碼。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Sqoop 會將資料匯出到的 SQL Database。預設名稱為 <i>HDISqoop</i>。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    Sqoop 會將資料匯出到的 SQL Database。預設名稱為 AvgDelays。這是您先前在教學課程中建立的資料表。

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    這是 Hive 輸出檔案位置。Sqoop 會將此位置中的檔案匯出至 SQL Database。

    </td>
    </tr>

    </table>
    </p>
4.  執行下列命令以匯出資料：

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  連接到您的 SQL Database，然後在 *AvgDelays* 資料表中依城市檢視航班誤點平均值。

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>後續步驟

現在您已了解如何將檔案上傳至 Blob 儲存體、如何使用 Blob 儲存體中的資料填入 Hive 資料表、如何執行 Hive 查詢，以及如何使用 Sqoop 將資料從 HDFS 匯出至 Azure SQL Database。若要深入了解，請參閱下列文章：

-   [開始使用 HDInsight][開始使用 HDInsight]
-   [搭配 HDInsight 使用 Hive][搭配 HDInsight 使用 Hive]
-   [在 HDInsight 上使用 Oozie][在 HDInsight 上使用 Oozie]
-   [搭配 HDInsight 使用 Sqoop][搭配 HDInsight 使用 Sqoop]
-   [搭配 HDInsight 使用 Pig][搭配 HDInsight 使用 Pig]
-   [開發 HDInsight 的 Java MapReduce 程式][開發 HDInsight 的 Java MapReduce 程式]
-   [開發 HDInsight 的 C# Hadoop 串流程式][開發 HDInsight 的 C# Hadoop 串流程式]

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [開始使用 HDInsight]: ../hdinsight-get-started/
  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [教學課程前置工作]: #prepare
  [建立及上傳 HiveQL 指令碼]: #createscript
  [執行 HiveQL 指令碼]: #executehqlscript
  [將輸出匯出至 Azure SQL Database]: #exportdata
  [後續步驟]: #nextsteps
  [創新技術研究管理部運輸統計處]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [搭配 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [搭配 HDInsight 使用 Hive]: ../hdinsight-use-hive/
  [TechNet Wiki]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [1]: #createScript
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [在 HDInsight 上使用 Oozie]: ../hdinsight-use-oozie/
  [搭配 HDInsight 使用 Sqoop]: ../hdinsight-use-sqoop/
  [搭配 HDInsight 使用 Pig]: ../hdinsight-use-pig/
  [開發 HDInsight 的 Java MapReduce 程式]: ../hdinsight-develop-deploy-java-mapreduce/
  [開發 HDInsight 的 C# Hadoop 串流程式]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
