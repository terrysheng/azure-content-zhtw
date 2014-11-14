<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="在 HDInsight 上使用 Hadoop 分析航班延誤資料 | Azure" metaKeywords="" description="了解如何將資料上傳至 HDInsight、如何使用 Hive 來處理資料，以及如何使用 Sqoop 將結果匯出至 SQL Database。" metaCanonical="" services="hdinsight" documentationCenter="" title="在 HDInsight 上使用 Hadoop 分析航班延誤資料 " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# 在 HDInsight 上使用 Hadoop 分析航班延誤資料

Hive 可透過一種稱為 *[HiveQL][HiveQL]* 的 SQL 式指令碼語言來執行 Hadoop MapReduce 工作，可用來彙總、查詢和分析大量資料。本教學課程說明如何使用 Hive 來計算機場的平均誤點時間，以及如何使用 Sqoop 將結果匯出至 Azure SQL Database。

HDInsight 的其中一個主要優點就是區隔資料儲存和運算。HDInsight 使用 Azure Blob 儲存體來儲存資料。一般的 HDInsight 程序可分成 3 部分：

1.  將資料儲存在 Azure Blob 儲存體中。這可能是持續的程序。例如，將天氣資料、感應器資料、Web 記錄，以及此案例中的航班延誤資料儲存到 Blob 儲存體中。
2.  執行工作。在該處理資料時，您就要執行 PowerShell 指令碼 (或用戶端應用程式) 來佈建 HDInsight 叢集、執行工具，然後刪除叢集。工作會將輸出資料儲存至 Azure Blob 儲存體。即使在刪除叢集之後，輸出資料仍會保留。因此，您只需要對已耗用的部分付費。
3.  從 Blob 儲存體擷取輸出，或在此案例中將資料匯出至 Azure SQL Database。

下圖說明本文的案例和結構：

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

注意：圖表中的號碼對應至章節標題。

**預估完成時間：**30 分鐘

## 本教學課程內容

-   [必要條件][必要條件]
-   [在新的或現有 HDInsight 叢集上執行 Hive 工作 (M1)][在新的或現有 HDInsight 叢集上執行 Hive 工作 (M1)]
-   [使用 Sqoop 將輸出匯出至 Azure SQL Database (M2)][使用 Sqoop 將輸出匯出至 Azure SQL Database (M2)]
-   [後續步驟][後續步驟]
-   [附錄 A：將航班延誤資料上傳至 Azure Blob 儲存體 (A1)][附錄 A：將航班延誤資料上傳至 Azure Blob 儲存體 (A1)]
-   [附錄 B：建立及上傳 HiveQL 指令碼 (A2)][附錄 B：建立及上傳 HiveQL 指令碼 (A2)]

## <span id="prerequisite"></span></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

-   已安裝並設定 Azure PowerShell 的工作站。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。
-   Azure 訂閱。 \*\*\*

**了解 HDInsight 儲存體**

HDInsight 中的 Hadoop 叢集使用 Azure Blob 儲存體來儲存資料。我們稱之為 *WASB* 或 *Azure 儲存體 - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 *HDFS* 實作。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]。

佈建 HDInsight 叢集時，一個 Azure 儲存體帳戶的 Blob 儲存體容器會指定為預設檔案系統，像就 HDFS 一樣。此儲存體帳戶稱為「預設儲存體帳戶」，而此 Blob 容器就稱為「預設 Blob 容器」或「預設容器」。預設儲存體帳戶必須與 HDInsight 叢集並存於相同的資料中心。刪除 HDInsight 叢集並不會刪除預設容器或預設儲存體帳戶。

除了預設儲存體帳戶，在佈建程序期間，其他 Azure 儲存體帳戶也可以繫結至 HDInsight 叢集。此繫結會將儲存體帳戶和儲存體帳戶金鑰加入至組態檔。因此，叢集可以在執行階段存取這些儲存體帳戶。如需有關新增其他儲存體帳戶的詳細資訊，請參閱＜[在 HDInsight 中佈建 Hadoop 叢集][在 HDInsight 中佈建 Hadoop 叢集]＞。

WASB 語法如下：

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] WASB 路徑為虛擬路徑。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]。

對於儲存在預設容器中的檔案，可使用下列任何 URI 從 HDInsight 存取 (以 flightdelays.hql 當做例子)：

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

若要直接從儲存體帳戶存取檔案，檔案的 Blob 名稱為：

    tutorials/flightdelays/flightdelays.hql

請注意，Blob 名稱前面沒有 "/"。

**本教學課程中使用的檔案**

下表列出本教學課程中使用的檔案：

| 檔案                                                                      | 說明                                            |
|---------------------------------------------------------------------------|-------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | 您將執行的 Hive 工作所需的 HiveQL 指令碼檔案。  |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | Hive 工作的輸入資料。                           |
| \\tutorials\\flightdelays\\output                                         | Hive 工作的輸出路徑。預設容器用來儲存輸出資料。 |
| \\tutorials\\flightdelays\\jobstatus                                      | Hive 工作狀態資料夾                             |

本教學課程使用取自[創新技術研究管理部運輸統計處][創新技術研究管理部運輸統計處] (RITA) 的飛行航班準點率資料。資料已上傳至具有「公用」Blob 存取權限的 Azure Blob 儲存體容器。因為是公用 Blob 容器，您不需要將此儲存體帳戶繫結至 HDInsight 叢集。HiveQL 指令碼也會上傳至相同的 Blob 容器。如需了解如何將資料放入/上傳至您自己的儲存體帳戶，以及如何建立/上傳 HiveQL 指令碼檔案，請參閱＜[附錄][附錄]＞。

**了解 Hive 內部資料表和外部資料表**

關於 Hive 內部資料表和外部資料表，有若干事項您必須了解：

-   CREATE TABLE 命令會建立內部資料表。資料檔案必須位於預設容器中。
-   CREATE TABLE 命令會將資料檔案移至 /hive/warehouse/<tablename> 資料夾。
-   CREATE EXTERNAL TABLE 命令會建立外部資料表。資料檔案可位於預設容器外。
-   CREATE EXTERNAL TABLE 命令不會移動資料檔案。
-   CREATE EXTERNAL TABLE 命令不允許在 LOCATION 中放置任何資料夾。因此，此教學課程複製了 sample.log 檔案。

如需詳細資訊，請參閱 [HDInsight：Hive 內部和外部資料表簡介][HDInsight：Hive 內部和外部資料表簡介]。

> [WACOM.NOTE] 其中一個 HiveQL 陳述式會建立 Hive 外部資料表。Hive 外部資料表會將資料檔案保存在原始位置中。Hive 內部資料表會將資料檔案移至 hive\\warehouse。Hive 內部資料表要求資料檔案必須位於預設容器中。對於儲存在預設 Blob 容器之外的資料，您必須使用 Hive 外部資料表。

## <span id="runjob"></span></a>在新的或現有 HDInsight 叢集上執行 Hive 工作

Hadoop 是批次處理。執行 Hive 工作時，最具成本效益的方法是佈建工作的叢集，並於工作完成之後刪除工作。下列指令碼涵蓋整個程序。如需有關佈建 HDInsight 叢集和執行 Hive 工作的詳細資訊，請參閱＜[在 HDInsight 中佈建 Hadoop 叢集][在 HDInsight 中佈建 Hadoop 叢集]＞和＜[在 HDInsight 上使用 Hive][在 HDInsight 上使用 Hive]＞。

**使用 PowerShell 執行 Hive 查詢**

1.  開啟 PowerShell ISE。
2.  將下列指令碼複製並貼到指令碼窗格中：

        *** add script here

3.  按 **F5** 以執行指令碼。輸出應該類似：

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

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
    **變數名稱**

    </td>
    <td>
    **注意**

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
    Sqoop 會將資料匯出到的 SQL Database。預設名稱為 \*HDISqoop"。

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
-   [搭配 HDInsight 使用 Hive][在 HDInsight 上使用 Hive]
-   [在 HDInsight 上使用 Oozie][在 HDInsight 上使用 Oozie]
-   [搭配 HDInsight 使用 Sqoop][搭配 HDInsight 使用 Sqoop]
-   [搭配 HDInsight 使用 Pig][搭配 HDInsight 使用 Pig]
-   [開發 HDInsight 的 Java MapReduce 程式][開發 HDInsight 的 Java MapReduce 程式]
-   [開發 HDInsight 的 C# Hadoop 串流程式][開發 HDInsight 的 C# Hadoop 串流程式]

## <span id="appendix-a"></span></a>附錄 A - 將航班誤點資料上傳至 Azure Blob 儲存體

**下載航班資料**

1.  瀏覽至[創新技術研究管理部運輸統計處][創新技術研究管理部運輸統計處] (RITA)。
2.  在此頁面上選取下列值：

    | 名稱     | 值                                                                                                                                                                                                                                                                                                                                                                                   |
    |----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 篩選年份 | 2013                                                                                                                                                                                                                                                                                                                                                                                 |
    | 篩選期間 | 一月                                                                                                                                                                                                                                                                                                                                                                                 |
    | 欄位：   | \*Year\*、\*FlightDate\*、\*UniqueCarrier\*、\*Carrier\*、\*FlightNum\*、\*OriginAirportID\*、\*Origin\*、\*OriginCityName\*、\*OriginState\*、\*DestAirportID\*、\*Dest\*、\*DestCityName\*、\*DestState\*、\*DepDelayMinutes\*、\*ArrDelay\*、\*ArrDelayMinutes\*、\*CarrierDelay\*、\*WeatherDelay\*、\*NASDelay\*、\*SecurityDelay\*、\*LateAircraftDelay\* (請清除其餘所有欄位) |

3.  按一下 [下載]。
4.  將檔案解壓縮至 **C:\\Tutorials\\FlightDelays\\Data** 資料夾。每個檔案皆為 CSV 檔案，大小約為 60 GB。
5.  將檔案重新命名為檔案資料所屬月份的名稱。例如，包含一月份資料的檔案，應命名為 *January.csv*。
6.  重複步驟 2 到 5，以下載 2013 年各個月份的檔案。您至少要有一個檔案，才能執行此教學課程。

**將航班誤點資料上傳至 Azure Blob 儲存體**

1.  開啟 PowerShell ISE。
2.  將下列指令碼貼到指令碼窗格中：

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
        $destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
        #EndRegion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        if (-not (Get-AzureAccount)){ Add-AzureAccount}
        #EndRegion

        #Region - Validate user inpute
        # Validate the storage account
        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }

        # Validate the container
        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion

        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"

                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion

3.  按 **F5** 以執行指令碼。

## <span id="appendix-b"></span></a>附錄 B - 建立及上傳 HiveQL 指令碼

使用 Azure PowerShell 可讓您逐一執行多個 HiveQL 陳述式，或將 HiveQL 陳述式封裝到指令碼檔案中。本節說明如何建立 HiveQL 指令碼，以及使用 PowerShell 將指令碼上傳至 Azure Blob 儲存體。Hive 要求 HiveQL 指令碼必須儲存在 WASB。

HiveQL 指令碼將執行下列作業：

1.  **捨棄 delays\_raw 資料表** (若此資料表已存在)。
2.  **建立 delays\_raw 外部 Hive 資料表** (指向含有航班誤點檔案的 WASB 位置)。此查詢會指定欄位將以 "," 分隔，且每一行都會以 "\\n" 結尾。如此，當欄位值*含有*逗號時，就會產生問題，因為 Hive 無法區分作為欄位分隔符號的逗號，與屬於欄位值的逗號 (在 ORIGIN\_CITY\_NAME 和 DEST\_CITY\_NAME 的欄位值中，就會出現此狀況)。為解決此問題，此查詢會建立 TEMP 資料行來放置不當分割為資料行的資料。
3.  **捨棄 delays 資料表** (若此資料表已存在)；
4.  **建立 delays 資料表**。此資料表有助於您在進一步處理之前先清除資料。此查詢會從 *delays\_raw* 資料表建立新資料表 *delays*。請注意，TEMP 資料行 (如前所述) 並不會複製，並且會使用 *substring* 函數來移除資料中的引號。
5.  **計算天候誤點平均值，並依城市名稱將結果分組。**此作業也會將結果輸出至 WASB。請注意，查詢將會移除資料中的撇號，並排除 *weather\_delay* 值為 *null* 的資料列。這是必要的，因為後續在本教學課程中使用的 Sqoop 依預設無法適當處理這些值。

如需 HiveQL 命令的完整清單，請參閱 [Hive 資料定義語言][HiveQL]。每個 HiveQL 命令都必須以分號結尾。

**建立 HiveQL 指令碼檔案**

1.  開啟 Azure PowerShell ISE。
2.  將下列指令碼複製並貼到指令碼窗格中：

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green

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

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    以下是指令碼中使用的三個常數：

    -   **$hqlLocalFileName**：指令碼會先將 HiveQL 指令碼檔案儲存在本機，然後才上傳至 WASB。這是檔名。預設值為 <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>。
    -   **$hqlBlobName**：這是 Azure Blob 儲存體中使用的 HiveQL 指令碼檔案 Blob 名稱。預設值為 <u>tutorials/flightdelays/flightdelays.hql</u>。因為檔案會直接寫入 Azure Blob 儲存體，所以 Blob 名稱開頭「沒有」"/"。如果您要從 WASB 存取檔案，則必須在檔名開頭加上 "/"。
    -   **$srcDataFolder** 和 **$dstDataFolder**：= "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

4.  設定前兩個變數，然後執行命令。

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
    **變數名稱**

    </td>
    <td>
    **說明**

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
    這是在 WASB 上具有路徑的指令碼檔案名稱

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
5.  執行下列命令以定義 HiveQL 陳述式：

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

6.  執行下列命令，將 Hive 指令碼檔案寫入至工作站，並將其上傳至 WASB：

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

## 準備 SQL 資料庫

**準備 SQL 資料庫 (將這部分與 Sqoop 指令碼合併)**

1.  開啟 PowerShell ISE。
2.  將下列指令碼複製並貼到指令碼窗格中：

        [CmdletBinding()]
        Param(

            # SQL database server variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseUsername,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"

        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                    [origin_city_name] ASC
                )
                )"

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
            $sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress   
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
        }
        else
        {
            $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
            if (! $dbServer)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green

            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 

            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
        }
        else
        {
            $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
            if (! $db)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    > WACOM.NOTE 指令碼使用 REST 服務 http://bot.whatismyipaddress.com 來擷取外部 IP 位址。IP 位址用來建立 SQL Database 伺服器的防火牆規則。

    以下是指令碼中使用的一些常數：

    -   **$ipAddressRestService**：預設值為 <u>http://bot.whatismyipaddress.com</u>。這是用來取得外部 IP 位址的公用 IP 位址 Rest 服務。想要的話，您可以使用其他服務。使用此服務所擷取的外部 IP 位址將用來建立 Azure SQL Database 伺服器的防火牆規則，讓您能夠從工作站存取資料庫 (使用 PowerShell 指令碼)。
    -   **$fireWallRuleName**：這是 Azure SQL Database 伺服器防火牆規則名稱。預設名稱為 <u>FlightDelay</u>。想要的話，您可以將它重新命名。
    -   **$sqlDatabaseMaxSizeGB**：只有在建立新的 Azure SQL Database 伺服器時才會使用此值。預設值為 <u>10GB</u>。10GB 足夠供本教學課程使用。
    -   **$sqlDatabaseName**：只有在建立新的 Azure SQL Database 時才會使用此值。預設值為 <u>HDISqoop</u>。如果將它重新命名，則必須相應地更新 Sqoop PowerShell 指令碼。

3.  按 **F5** 以執行指令碼。您需要輸入 Azure 訂用帳戶認證和下列值：

    -   **sqlDatabaseServer**：輸入您要將 Hive 輸出匯出到的 Azure SQL Database 伺服器名稱。不輸入則會建立伺服器名稱。
    -   **sqlDatabaseUsername**：輸入資料庫登入。不論是想要建立新的資料庫伺服器，還是使用現有的資料庫伺服器，都必須指定此值。
    -   **sqlDatabasePassword**：輸入資料庫登入密碼。不論是想要建立新的資料庫伺服器，還是使用現有的資料庫伺服器，都必須指定此值。
    -   **sqlDatabaseLocation**：輸入區域。只有在您想要建立新的資料庫伺服器時才會使用此值。
    -   **sqlDatabaseName**：輸入 Azure SQL Database 名稱。不輸入則會建立新的資料庫名稱預設資料庫名稱為 **HDISqoop**。

4.  驗證指令碼輸出。請確定指令碼已成功執行。

如果您選擇使用不同的方法來上傳檔案，請確定檔案路徑為 *tutorials/flightdelays/data*。存取檔案的語法為：

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* 是您在上傳檔案時所建立的虛擬資料夾。請確認共有 12 個檔案，每個月份各一個。

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [必要條件]: #prerequisite
  [在新的或現有 HDInsight 叢集上執行 Hive 工作 (M1)]: #runjob
  [使用 Sqoop 將輸出匯出至 Azure SQL Database (M2)]: #exportdata
  [後續步驟]: #nextsteps
  [附錄 A：將航班延誤資料上傳至 Azure Blob 儲存體 (A1)]: #appdendix-a
  [附錄 B：建立及上傳 HiveQL 指令碼 (A2)]: #appendix-b
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [搭配 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [在 HDInsight 中佈建 Hadoop 叢集]: ../hdinsight-provision-clusters/
  [創新技術研究管理部運輸統計處]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [附錄]: #appendix
  [HDInsight：Hive 內部和外部資料表簡介]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [在 HDInsight 上使用 Hive]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [開始使用 HDInsight]: ../hdinsight-get-started/
  [在 HDInsight 上使用 Oozie]: ../hdinsight-use-oozie/
  [搭配 HDInsight 使用 Sqoop]: ../hdinsight-use-sqoop/
  [搭配 HDInsight 使用 Pig]: ../hdinsight-use-pig/
  [開發 HDInsight 的 Java MapReduce 程式]: ../hdinsight-develop-deploy-java-mapreduce/
  [開發 HDInsight 的 C# Hadoop 串流程式]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
