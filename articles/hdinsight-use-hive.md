<properties  linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hive with HDInsight" pageTitle="Use Hive with HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hive with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# 在 HDInsight 上使用 Hive

[Apache Hive][1] 提供一種透過 SQL 式指令碼語言執行 MapReduce 工作的工具，稱為
*HiveQL*，可用來總結、查詢和分析大量資料。在本文中，您將使用 HiveQL 來查詢 Apache log4j
記錄檔的資料及報告基本統計資料。

**必要條件：**

* 您必須已佈建 **HDInsight 叢集**。有關如何使用 Azure 入口網站來執行此項作業的逐步解說，請參閱＜[開始使用 HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)＞。有關透過其他各種方法建立此類叢集的指示，請參閱＜[佈建 HDInsight 叢集](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)＞。

* 您的工作站必須已安裝 **Azure PowerShell**。如需執行此項作業之指示，請參閱＜[安裝和設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)＞。

**預估完成時間：**30 分鐘

## 本文內容

* [Hive 使用案例](#usage)
* [將資料檔上傳至 Azure Blob 儲存](#uploaddata)
* [使用 PowerShell 執行 Hive 查詢](#runhivequeries)
* [後續步驟](#nextsteps)

## <a id="usage" ></a>Hive 使用案例

如果管理的是規模較小的資料集，且可能產生低延遲查詢時，則比較適合使用資料庫。然而，在處理含有數 TB 資料的大型資料集時，傳統的 SQL 資料庫通常就不是理想的解決方案。當資料庫負載增加而效能不佳時，資料庫管理員會習慣購買更大型的硬體，以垂直擴充方式來處理較大型的資料集。

Hive 可讓使用者在查詢大型資料集時進行橫向擴充，以此解決巨量資料的相關問題。Hive 利用 MapReduce 在多個節點上並行查詢資料，隨著負載增加而將資料庫分散至更多主機上。

查詢資料時，除了以 Java 撰寫 MapReduce 工作，Hive 和 HiveQL 也提供另一種作法。它提供簡單的 SQL 包裝函式，可以用 HiveQL 撰寫來查詢，然後由 HDInsight 編譯成 MapReduce，再於叢集上執行。

Hive 也可讓熟悉 MapReduce 架構的程式設計人員插入自訂的 Mapper 和 Reducer，以執行 HiveQL
語言的內建功能無法處理的複雜分析。

Hive 最適合批次處理大量的固定資料 (例如網頁記錄)。它不適用於回應時間必須非常快的交易應用程式，例如資料庫管理系統。Hive 經過最佳化處理，能徹底發揮可擴充性 (可動態地將更多電腦加入至 Hadoop 叢集)、延展性 (在 MapReduce 架構內，搭配其他程式設計介面) 和容錯性。延遲性並非主要的設計考量。

應用程式通常會將錯誤、例外狀況和其他程式碼問題儲存在記錄檔中，系統管理員可利用記錄檔中的資料來檢閱可能發生的問題，並產生關於錯誤或效能之類其他問題的度量。這些記錄檔的大小通常會變得相當可觀且內含大量必須要處理和探查的資料，以取得應用程式的智慧資訊。

因此，記錄檔是巨量資料的典型例子。HDInsight 提供 Hive 資料倉儲系統，可輕鬆進行資料彙總、特定查詢和分析這些儲存在 Hadoop 相容檔案系統中的大型資料集 (如 Azure Blob 儲存)。

## <a id="uploaddata" ></a>將資料檔上傳至 Blob 儲存體

HDInsight 使用 Azure Blob 儲存容器做為預設檔案系統。如需詳細資訊，請參閱＜[在 HDInsight 上使用 Azure Blob 儲存](/en-us/manage/services/hdinsight/howto-blob-store)＞。

在本文中，您使用隨 HDInsight 叢集一起散發的 log4j 範例檔，此檔案儲存於 *\example\data\sample.log*
中。檔案中的每一筆記錄由一列欄位組成，包括以 `[LOG LEVEL]` 欄位來顯示類型和嚴重性。例如：

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

請使用下列語法來存取檔案：

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

例如：

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

請以容器名稱取代 *mycontainer*，並以 Blob 儲存體帳戶名稱取代 *mystorage*。

因為此檔案儲存在預設檔案系統中，您也可以使用下列語法來存取檔案：

    wasb:///example/data/sample.log
    /example/data/sample.log

若要產生您自己的 log4j 檔案，請使用 [Apache Log4j][2] 記錄公用程式。如需有關將資料上傳至 Azure Blob
儲存的詳細資訊，請參閱＜[將資料上傳至
HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)＞。

## <a id="runhivequeries" ></a> 使用 PowerShell 執行 Hive 查詢

在上一節，您將名為 sample.log 的 log4j 檔案上傳至預設檔案系統容器。在這一節，您將執行 HiveQL 來建立 Hive
資料表、將資料載入至 Hive 資料表，然後查詢資料來找出有多少錯誤記錄。

本文提供使用 Azure PowerShell Cmdlet 來執行 Hive 查詢的指示。開始進入本節之前，您必須先設定本機環境，並設定 Azure 的連線，請參閱本主題開頭的**必要條件**。

在 PowerShell 中，可使用 **Start-AzureHDInsightJob** Cmdlet 或 **Invoke-Hive**
Cmdlet 來執行 Hive 查詢

**使用 Start-AzureHDInsightJob 執行 Hive 查詢**

1.  開啟 Azure PowerShell 主控台視窗。相關指示位於＜[安裝和設定 Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/)＞。
2.  執行下列命令來連線到您的 Azure 訂閱：
    
         Add-AzureAccount
    
    系統會提示您輸入 Azure 帳戶認證。

3.  在下列指令碼中設定變數並執行：

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  執行下列指令碼來定義 HiveQL 查詢：

		# HiveQL queries
		# Use the internal table option. 
        $queryString = "DROP TABLE log4jLogs;" +
        "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
        "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

		# Use the external table option. 
        $queryString = "DROP TABLE log4jLogs;" +
        "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
    
    LOAD DATA HiveQL 命令會將資料檔移至 \hive\warehouse\ 資料夾。DROP TABLE
    命令會刪除資料表和資料檔。如果您使用內部資料表選項且想要再次執行指令碼，則必須重新上傳 sample.log
    檔案。如果您要保留資料檔，則必須使用指令碼中所示的 CREATE EXTERNAL TABLE 命令。
    
    當資料檔位於不同的容器或儲存體帳戶時，您也可以使用外部資料表。
    
    請先使用 DROP TABLE，以防您重新執行指令碼而 log4jlogs 資料表已存在的情況。

5.  執行下列指令碼來建立 Hive 工作定義：

		# Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 
    
    您也可以使用 -File 參數來指定 HDFS 上的 HiveQL 指令碼檔案。

6.  執行下列指令碼來提交 Hive 工作：

		# Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  執行下列指令碼來等待 Hive 工作完成：

		# Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  執行下列指令碼來列印標準輸出： 9.  

		# Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

 ![HDI.HIVE.PowerShell](./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png)     
The results is:
    
    	[ERROR] 3

**使用 Invoke-Hive 提交 Hive 查詢**

1.  開啟 Azure PowerShell 主控台視窗。 2.  執行下列命令來連線到您的 Azure 訂閱：
    
         Add-AzureAccount
    
    系統會提示您輸入 Azure 帳戶認證。

3.  設定變數，然後執行：
    
         $clusterName = "<HDInsightClusterName>"

4.  執行下列指令碼來叫用 HiveQL 查詢：
    
         Use-AzureHDInsightCluster $clusterName 
        $response = Invoke-Hive -Query @"
        SELECT * FROM hivesampletable
        WHERE devicemake LIKE "HTC%"
        LIMIT 10; 
         "@
        	
        Write-Host $response
    
    輸出如下：
    
    ![PowerShell Invoke-Hive
    output](./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png)
    
    如果 HiveQL 查詢的時間很長，建議使用 PowerShell Here-Strings 或 HiveQL
    指令碼檔案。下列範例顯示如何使用 Invoke-Hive Cmdlet 來執行 HiveQL 指令碼檔案。必須將 HiveQL 指令碼檔案上傳至 WASB。
    
         Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"
    
    如需 Here-Strings 的詳細資訊，請參閱＜[使用 Windows PowerShell
    Here-Strings][3]＞(英文)。

## <a id="nextsteps" ></a> 後續步驟

Hive 可讓您使用 SQL 型的查詢語言來輕鬆查詢資料，但 HDInsight
還有其他元件也提供補充的功能，例如資料移動和轉換。若要深入了解，請參閱下列文章：

* [開始使用 Azure
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
* [使用 HDInsight
  分析班機延誤資料](/en-us/documentation/articles/hdinsight-analyze-flight-delay-data/)
* [在 HDInsight 上使用
  Oozie](/en-us/documentation/articles/hdinsight-use-oozie/)
* [以程式設計方式提交 Hadoop
  工作](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
* [將資料上傳至
  HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
* [在 HDInsight 上使用
  Pig](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
* [Azure HDInsight SDK 文件][4]



[1]: http://hive.apache.org/
[2]: http://en.wikipedia.org/wiki/Log4j
[3]: http://technet.microsoft.com/en-us/library/ee692792.aspx
[4]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx