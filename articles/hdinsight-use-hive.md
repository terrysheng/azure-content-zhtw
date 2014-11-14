<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="在 HDInsight 上使用 Hadoop Hive | Azure" metaKeywords="" description="了解如何搭配 HDInsight 使用 Hive。您將使用記錄檔作為 HDInsight 資料表中的輸入，並使用 HiveQL 來查詢資料及報告基本統計資料。" metaCanonical="" services="hdinsight" documentationCenter="" title="在 HDInsight 上使用 Hadoop Hive" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# 在 HDInsight 上將 Hive 與 Hadoop 搭配使用

[Apache Hive][Apache Hive] 提供了可透過類 SQL 指令碼語言 (名為 *HiveQL*) 來執行 MapReduce 工作的機制。Hive 是適用於 Hadoop 的資料倉儲系統，可用來執行資料彙總、查詢和大量資料分析。在本文中，您會使用 HiveQL 查詢隨附於 HDInsight 叢集佈建中的範例資料檔案。

**必要條件：**

-   您必須已佈建 **HDInsight 叢集**。有關如何使用 Azure 入口網站來執行此項作業的逐步解說，請參閱＜[開始使用 HDInsight][開始使用 HDInsight]＞。有關透過其他各種方法建立此類叢集的指示，請參閱＜[佈建 HDInsight 叢集][佈建 HDInsight 叢集]＞。

-   您的工作站必須已安裝 **Azure PowerShell**。如需執行此項作業之指示，請參閱＜[安裝和設定 Azure PowerShell][安裝和設定 Azure PowerShell]＞。

**預估完成時間：**30 分鐘

## 本文內容

-   [Hive 使用案例][Hive 使用案例]
-   [上傳 Hive 資料表的資料][上傳 Hive 資料表的資料]
-   [使用 PowerShell 執行 Hive 查詢][使用 PowerShell 執行 Hive 查詢]
-   [使用 Tez 以提升效能][使用 Tez 以提升效能]
-   [後續步驟][後續步驟]

## <span id="usage"></span></a>Hive 使用案例

![HDI.HIVE.Architecture][HDI.HIVE.Architecture]

Hive 會將某種類型的結構放置在大量非結構化資料集上 (Hadoop 的用途即在此)，然後讓您使用 HiveQL 查詢該資料。Hive 會在 Java 型 MapReduce 架構上提供抽象層，讓使用者無需具備 Java 或 MapReduce 知識即可查詢資料。HiveQL 提供簡單的類 SQL 包裝函式，可以用 HiveQL 撰寫來查詢，然後由 HDInsight 編譯成 MapReduce，再於叢集上執行。Hive 的其他優點包括：

-   Hive 可讓熟悉 MapReduce 架構的程式設計人員插入自訂的對應程式和節流程式，以執行 HiveQL 語言的內建功能無法處理的複雜分析。
-   Hive 最適合批次處理大量的固定資料 (例如網頁記錄)。它不適用於回應時間必須非常快的交易應用程式，例如資料庫管理系統。
-   Hive 經過最佳化處理，能徹底發揮可擴充性 (可動態地將更多電腦加入至 Hadoop 叢集)、延展性 (在 MapReduce 架構內，搭配其他程式設計介面) 和容錯性。延遲性並非主要的設計考量。

## <span id="uploaddata"></span></a>上傳 Hive 資料表的資料

HDInsight 使用 Azure Blob 儲存容器作為 Hadoop 叢集的預設檔案系統。有些範例資料檔案會新增至 Blob 儲存體，作為叢集佈建的一部分。您可以使用這些範例資料檔案，對叢集執行 Hive 查詢。如果需要，您也可以將自己的資料檔案上傳至與叢集相關聯的 Blob 儲存體帳戶。如需指示，請參閱[將資料上傳至 HDInsight][將資料上傳至 HDInsight]。如需如何搭配使用 Azure Blob 儲存體與 HDInsight 的詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]。

本文使用隨 HDInsight 叢集配送的 *log4j* 範例檔，此檔案儲存在您 Blob 儲存體容器下的 *\\example\\data\\sample.log* 中。您也可以使用 [Apache Log4j][Apache Log4j] 記錄公用程式產生自己的 log4j 檔案，然後將該檔案上傳至 Blob 容器。檔案中的每一筆記錄由一列欄位組成，包括以 `[LOG LEVEL]` 欄位來顯示類型和嚴重性。例如：

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

在前述範例中，記錄層級是「錯誤」。

您可以從應用程式中使用下列語法來存取檔案：

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

因此，若要存取 sample.log 檔案，您應使用下列語法：

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

請將 *mycontainer* 取代為 Azure Blob 容器名稱，並將 *mystorage* 取代為 Azure 儲存體帳戶名稱。

由於檔案儲存在預設檔案系統中，因此您也可以使用下列語法來存取檔案：

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> 使用 PowerShell 執行 Hive 查詢

在最後一節中，您指定了將用來執行 Hive 查詢的 *sample.log* 檔案。在本節中，您將執行 HiveQL 來建立 Hive 資料表、將範例資料載入至 Hive 資料表，然後查詢資料以了解檔案中有多少錯誤記錄。

本文提供使用 Azure PowerShell Cmdlet 來執行 Hive 查詢的指示。開始進入本節之前，您必須先設定本機環境，並設定 Azure 的連線，請參閱本主題開頭的「必要條件」。

在 PowerShell 中，可使用 **Start-AzureHDInsightJob** Cmdlet 或 **Invoke-Hive** Cmdlet 來執行 Hive 查詢

**使用 Start-AzureHDInsightJob 執行 Hive 查詢**

1.  開啟 Azure PowerShell 主控台視窗。相關指示位於＜[安裝和設定 Azure PowerShell][安裝和設定 Azure PowerShell]＞。
2.  執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

    系統會提示您輸入 Azure 帳號認證。

3.  在下列指令碼中設定變數並執行：

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  執行下列指令碼以定義 HiveQL 查詢。您可以選擇建立*內部*或*外部* Hive 資料表。

    -   使用內部資料表時，您所使用的資料會從現有位置移至 \\hive\\warehouse\\\<*tablename\>* 資料夾。因此，當您捨棄內部資料表時，相關聯的資料也會隨之刪除。如果您使用內部資料表且想要再次執行指令碼，則必須重新上傳 *sample.log* 檔案。
    -   使用外部資料表時，資料不會從原始位置移出。當資料檔位於不同的容器或儲存體帳戶時，您也可以使用外部資料表。

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

    當資料表已存在時，DROP TABLE 命令會刪除資料表和資料檔。LOAD DATA HiveQL 命令會將資料檔案從 \\example\\data 移至 \\hive\\warehouse\\\<*tablename\>* 資料夾。

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

8.  執行下列指令碼來列印標準輸出：

        # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    結果如下：

        [ERROR] 3

    這表示 *sample.log* 檔案中有三個錯誤記錄執行個體。

如有必要，您也可以將查詢的輸出匯入 Microsoft Excel 中，以進行深入分析。如需指示，請參閱[使用 Power Query 將 Excel 連接到 Hadoop][使用 Power Query 將 Excel 連接到 Hadoop]。

**使用 Invoke-Hive 提交 Hive 查詢**

1.  開啟 Azure PowerShell 主控台視窗。
2.  執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

    系統會提示您輸入 Azure 帳號認證。

3.  在下列指令碼中設定變數並執行：

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  連接到 HDInsight 叢集。

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  執行下列指令碼來叫用 HiveQL 查詢：

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  在主控台上列印輸出。

        # print the output on the console
        Write-Host $response

    輸出顯示如下：

    ![PowerShell Invoke-Hive output][PowerShell Invoke-Hive output]

    如果 HiveQL 查詢的時間很長，您可以使用 PowerShell Here-Strings 或 HiveQL 指令碼檔案。下列程式碼片段說明如何使用 *Invoke-Hive* Cmdlet 來執行 HiveQL 指令碼檔案。必須將 HiveQL 指令碼檔案上傳至 WASB。

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    如需 Here-Strings 的詳細資訊，請參閱＜[使用 Windows PowerShell Here-Strings][使用 Windows PowerShell Here-Strings]＞(英文)。

如有必要，您也可以將查詢的輸出匯入 Microsoft Excel 中，以進行深入分析。如需指示，請參閱[使用 Power Query 將 Excel 連接到 Hadoop][使用 Power Query 將 Excel 連接到 Hadoop]。

## <span id="usetez"></span></a>使用 Tez 以提升效能

[Apache Tez][Apache Tez] 是可讓您使用資料高用量應用程式 (例如 Hive) 大規模而高效能地執行作業的架構。在最新版的 HDInsight 中，Hive 現已支援在 Tez 上執行。此功能目前預設為關閉，而必須啟用。在未來的叢集版本中，此功能將預設為開啟。若要充分發揮 Tez 的效益，您必須設定 Hive 查詢的下列值：

        set hive.execution.engine=tez;
        

此值可就個別查詢逐一提交，只要將值放在查詢開頭處即可。您也可以在建立叢集時設定組態值，而在叢集上將此值預設為開啟。您可以在[佈建 HDInsight 叢集][佈建 HDInsight 叢集]中找到詳細資訊。

[Tez 上的 Hive 設計文件][Tez 上的 Hive 設計文件]包含實作選擇和調整組態的詳細資料。

## <span id="nextsteps"></span></a>後續步驟

Hive 可讓您使用 SQL 型的查詢語言來輕鬆查詢資料，但 HDInsight 還有其他元件也提供補充的功能，例如資料移動和轉換。若要深入了解，請參閱下列文章：

-   [在 HDInsight 上使用 Oozie][在 HDInsight 上使用 Oozie]
-   [以程式設計方式提交 Hadoop 工作][以程式設計方式提交 Hadoop 工作]
-   [在 HDInsight 上使用 Pig][在 HDInsight 上使用 Pig]
-   [使用 HDInsight 分析班機延誤資料][使用 HDInsight 分析班機延誤資料]
-   [Azure HDInsight SDK 文件][Azure HDInsight SDK 文件]
-   [將資料上傳到 HDInsight][將資料上傳至 HDInsight]
-   [Azure HDInsight 使用者入門][開始使用 HDInsight]

  [Apache Hive]: http://hive.apache.org/
  [開始使用 HDInsight]: ../hdinsight-get-started/
  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [安裝和設定 Azure PowerShell]: ../install-configure-powershell/
  [Hive 使用案例]: #usage
  [上傳 Hive 資料表的資料]: #uploaddata
  [使用 PowerShell 執行 Hive 查詢]: #runhivequeries
  [使用 Tez 以提升效能]: #usetez
  [後續步驟]: #nextsteps
  [HDI.HIVE.Architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [將資料上傳至 HDInsight]: ../hdinsight-upload-data/
  [搭配 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [使用 Power Query 將 Excel 連接到 Hadoop]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-connect-excel-power-query/
  [PowerShell Invoke-Hive output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [使用 Windows PowerShell Here-Strings]: http://technet.microsoft.com/zh-tw/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [Tez 上的 Hive 設計文件]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [在 HDInsight 上使用 Oozie]: ../hdinsight-use-oozie/
  [以程式設計方式提交 Hadoop 工作]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [在 HDInsight 上使用 Pig]: ../hdinsight-use-pig/
  [使用 HDInsight 分析班機延誤資料]: ../hdinsight-analyze-flight-delay-data/
  [Azure HDInsight SDK 文件]: http://msdnstage.redmond.corp.microsoft.com/zh-tw/library/dn479185.aspx
