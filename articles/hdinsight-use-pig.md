<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Pig with HDInsight" pageTitle="Use Pig with HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Pig with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

搭配 HDInsight 使用 Pig
=======================

[Apache *Pig*](http://pig.apache.org/) 所提供的指令碼語言可執行 *MapReduce* 工作，而讓您在撰寫 Java 程式碼以外多了一項選擇。在此教學課程中，您將會使用 PowerShell 執行某些 Pig Latin 陳述式以分析 Apache log4j 記錄檔，並對資料執行各種查詢而產生輸出。此教學課程將說明 Pig 的優點，以及如何使用 Pig 來簡化 MapReduce 工作。

Pig 的指令碼語言稱為 *Pig Latin*。Pig Latin 陳述式的一般執行流程如下：

-   **載入**：從檔案系統讀取要處理的資料
-   **轉換**：處理資料
-   **傾印或儲存**：將資料輸出至畫面，或儲存以供處理

如需 Pig Latin 的詳細資訊，請參閱 [Pig Latin 參考手冊 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) (英文) 和 [Pig Latin 參考手冊 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html) (英文)。

**必要條件**

開始進行本文之前，請注意下列需求：

-   Azure HDInsight 叢集。如需指示，請參閱 [Azure HDInsight 使用者入門](/en-us/manage/services/hdinsight/get-started-hdinsight/)或[佈建 HDInsight 叢集](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)。
-   安裝並設定 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

**預估完成時間：**30 分鐘

本文內容
--------

-   [Pig 使用案例](#usage)
-   [將資料檔案上傳至 Azure Blob 儲存體](#uploaddata)
-   [了解 Pig Latin](#understand)
-   [使用 PowerShell 執行 Pig Latin](#powershell)
-   [後續步驟](#nextsteps)

Pig 使用案例
------------

資料庫很適合小型資料集和低延遲查詢使用。但對於以 TB 計的巨量資料集和大型資料集而言，傳統的 SQL 資料庫就不是理想的方案了。在過去，當資料庫負載增加，效能下降時，資料庫管理員就必須購買更大的硬體。

一般而言，所有應用程式都會將錯誤、例外狀況和其他程式碼問題儲存在記錄檔中，讓系統管理員能夠檢閱問題，或是從記錄檔資料產生特定度量。這些記錄檔通常都會變得很大，且包含需要處理和留意的大量資料。

記錄檔是很具代表性的巨量資料。使用關聯式資料庫和統計資料/虛擬化封裝來處理巨量資料，是很困難的。由於資料本身和資料運算的數量都很龐大，要在時限內計算巨量資料，常須在數十、數百甚或數千部伺服器上平行執行軟體，才有辦法。Hadoop 所提供的 MapReduce 架構可用來撰寫應用程式，讓您以非常可靠且可容錯的方式，平行處理大型機器叢集之間的大量結構化和非結構化資料。

使用 Pig 可縮短撰寫對應程式和節流程式所需的時間。這表示沒有必要使用 Java，也不需要編寫程式碼。同時，您也擁有結合 Java 程式碼與 Pig 的彈性。許多複雜的演算法，都可用不到五行且易讀的 Pig 程式碼撰寫出來。

您在本文中的執行成果，可用下列兩個圖表呈現出來。這兩個圖表會顯示資料集的代表性範例，以解說在您透過指令碼中的 Pig 程式碼行執行時的資料流程和轉換。第一個圖表顯示 log4j 檔案的範例：

![Whole File Sample](./media/hdinsight-use-pig/HDI.wholesamplefile.png)

第二個圖表顯示資料轉換：

![HDI.PIG.Data.Transformation](./media/hdinsight-use-pig/HDI.DataTransformation.gif)

將資料檔案上傳至 Blob 儲存體
----------------------------

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](/en-us/manage/services/hdinsight/howto-blob-store/)。在本文中，您將使用隨附於 HDInsight 叢集、儲存在 *\\example\\data\\sample.log* 中的 log4j 範例檔案。如需上傳資料的詳細資訊，請參閱[將資料上傳到 HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)。

若要存取檔案，請使用下列語法：

     wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

例如：

     wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

請將 *mycontainer* 取代為容器名稱，並將 *mystorage* 取代為 Blob 儲存帳號名稱。

由於檔案儲存在預設檔案系統中，因此您也可以使用下列語法來存取檔案：

     wasb:///example/data/sample.log
        /example/data/sample.log

了解 Pig Latin
--------------

在這個階段，您將會檢閱幾個 Pig Latin 陳述式，以及執行陳述式之後的結果。在下個階段中，您會使用 PowerShell 執行 Pig 陳述式。

1.  從檔案系統載入資料，然後顯示結果

         LOGS = LOAD 'wasb:///example/data/sample.log';
        DUMP LOGS;

    輸出大致如下：

         (2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
        (2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
        (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
        (2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
        (2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
        (2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
        (2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
        (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
         ...

2.  查看資料檔案中的每一行，以尋找 6 個記錄層級上的相符項目：

         LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  篩選掉沒有相符項目的資料列。例如空白資料列。

         FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        DUMP FILTEREDLEVELS;

    輸出大致如下：

         (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
         ...

4.  將所有的記錄層級分組到其本身的資料列中：

         GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        DUMP GROUPEDLEVELS;

    輸出大致如下：

         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE), ...

5.  為每個群組計算記錄層級的出現次數。以下是每個記錄層級的頻率：

         FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        DUMP FREQUENCIES;

    輸出大致如下：

         (INFO,3355)
        (WARN,361)
        (DEBUG,15608)
        (ERROR,181)
        (FATAL,37)
        (TRACE,29950)

6.  以遞減順序排序頻率：

         RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;   

    輸出大致如下：

         (TRACE,29950)
        (DEBUG,15608)
        (INFO,3355)
        (WARN,361)

    (ERROR,181)

         (FATAL,37)

使用 PowerShell 執行 Pig Latin
------------------------------

本節提供 PowerShell Cmdlet 的使用指示。在進行本節之前，您必須先設定本機環境，並設定對 Azure 的連接。如需詳細資訊，請參閱 [Azure HDInsight 使用者入門](/en-us/manage/services/hdinsight/get-started-hdinsight/)和[使用 PowerShell 管理 HDInsight](/en-use/manage/services/hdinsight/administer-hdinsight-using-powershell/)。

**使用 PowerShell 執行 Pig Latin**

1.  開啟 Azure PowerShell 主控台視窗。如需指示，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。
2.  執行下列命令，以連接到您的 Azure 訂閱：

         Add-AzureAccount

    系統會提示您輸入 Azure 帳號認證。

3.  設定下列指令碼中的變數，並加以執行：

         $clusterName = "<HDInsightClusterName>" 

4.  執行下列命令，以定義 Pig Latin 查詢字串：

         # Create the Pig job definition
         $0 = '$0';
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;" 
            
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

    您也可以使用 -File 參數，在 HDFS 上指定一個 Pig 指令碼檔案。

5.  執行下列指令碼，以提交 Pig 工作：

         # Submit the Pig job
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

6.  執行下列指令碼，以等候 Pig 工作完成：

         # Wait for the Pig job to complete
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  執行下列指令碼，以列印 Pig 工作輸出：

         # Print the standard error and the standard output of the Pig job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    ![HDI.Pig.PowerShell](./media/hdinsight-use-pig/hdi.pig.powershell.png)

    Pig 工作會計算不同記錄類型的頻率。

後續步驟
--------

除了可讓您執行資料分析的 Pig 以外，HDInsight 隨附的其他語言也可能對您有所幫助。Hive 提供類似於 SQL 的查詢語言，可讓您輕鬆地對儲存於 HDInsight 中的資料進行查詢，而以 Java 撰寫的 MapReduce 工作則可讓您執行複雜的資料分析。如需詳細資訊，請參閱下列主題：

-   [Azure HDInsight 使用者入門](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [將資料上傳到 HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [搭配 HDInsight 使用 Hive](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)

