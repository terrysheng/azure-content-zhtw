<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# 在 HDInsight 上將 Pig 與 Hadoop 搭配使用

了解如何在 HDInsight 上執行 [Apache Pig][] 工作，以分析 Apache log4j 記錄檔。

**預估完成時間：** 30 分鐘

## 本文內容

-   [Pig 使用案例][]
-   [必要條件][]
-   [了解 Pig Latin][]
-   [使用 PowerShell 提交 Pig 工作][]
-   [使用 HDInsight .NET SDK 提交 Pig 工作][]
-   [後續步驟][]

## <span id="usage"></span></a>Pig 使用案例

資料庫很適合小型資料集和低延遲查詢使用。但對於以 TB 計的巨量資料集和大型資料集而言，傳統的 SQL 資料庫就不是理想的方案了。在過去，當資料庫負載增加，效能下降時，資料庫管理員就必須購買更大的硬體。

一般而言，所有應用程式都會將錯誤、例外狀況和其他程式碼問題儲存在記錄檔中，讓系統管理員能夠檢閱問題，或是從記錄檔資料產生特定度量。這些記錄檔通常都會變得很大，且包含需要處理和留意的大量資料。

記錄檔是很具代表性的巨量資料。使用關聯式資料庫和統計資料/虛擬化封裝來處理巨量資料，是很困難的。由於資料本身和資料運算的數量都很龐大，要在時限內計算巨量資料，常須在數十、數百甚或數千部伺服器上平行執行軟體，才有辦法。Hadoop 所提供的 MapReduce 架構可用來撰寫應用程式，讓您以非常可靠且可容錯的方式，平行處理大型機器叢集之間的大量結構化和非結構化資料。

[Apache *Pig*][Apache Pig] 所提供的指令碼語言可執行 *MapReduce* 工作，而讓您在撰寫 Java 程式碼以外多了一項選擇。Pig 的指令碼語言稱為 *Pig Latin*。Pig Latin 陳述式的一般執行流程如下：

-   **載入**：從檔案系統讀取要處理的資料
-   **轉換**：處理資料
-   **傾印或儲存**：將資料輸出至畫面，或儲存以供處理

使用 Pig 可縮短撰寫對應程式和節流程式所需的時間。這表示沒有必要使用 Java，也不需要編寫程式碼。同時，您也擁有結合 Java 程式碼與 Pig 的彈性。許多複雜的演算法，都可用不到五行且易讀的 Pig 程式碼撰寫出來。

您在本文中的執行成果，可用下列兩個圖表呈現出來。這兩個圖表會顯示資料集的代表性範例，以解說在您透過指令碼中的 Pig 程式碼行執行時的資料流程和轉換。第一個圖表顯示 log4j 檔案的範例：

![Whole File Sample][]

第二個圖表顯示資料轉換：

![HDI.PIG.Data.Transformation][]

如需 Pig Latin 的詳細資訊，請參閱 [Pig Latin 參考手冊 1][] (英文) 和 [Pig Latin 參考手冊 2][] (英文)。

## <span id="prerequisites"></span></a>必要條件

開始進行本文之前，請注意下列需求：

-   Azure HDInsight 叢集。如需指示，請參閱 [Azure HDInsight 使用者入門][]或[佈建 HDInsight 叢集][]。進行教學課程時，您將需要下列資料：

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">叢集屬性</th>
    <th align="left">PowerShell 變數名稱</th>
    <th align="left">值</th>
    <th align="left">說明</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight 叢集名稱</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">您將用來執行此教學課程的 HDInsight 叢集。</td>
    </tr>
    </tbody>
    </table>

-   安裝並設定 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell][]。

**了解 HDInsight 儲存體**

HDInsight 會使用 Azure Blob 儲存體來儲存資料。我們稱之為 *WASB* 或 *Azure 儲存體 - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][]。

佈建 HDInsight 叢集時，會將一個 Azure 儲存體帳戶及其下的特定 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。除了此儲存體帳戶，您也可以在佈建過程中新增相同 Azure 訂用帳戶或不同 Azure 訂用帳戶的其他儲存體帳戶。如需有關新增其他儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight 叢集][]。為簡化本教學課程中使用的 PowerShell 指令碼，所有檔案都會儲存在位於 */tutorials/usepig* 的預設檔案系統容器中。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。
WASB 語法如下：

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 叢集 3.0 版僅支援 *wasb://* 語法。HDInsight 2.1 和 1.6 支援舊的 *asv://* 語法，但在 HDInsight 3.0 叢集中已不受支援，未來的版本也不加以支援。

> [WACOM.NOTE] WASB 路徑為虛擬路徑。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][]。

儲存在預設檔案系統容器中的檔案，可使用下列任一 URI (範例中使用 sample.log。此檔案是本教學課程中使用的資料檔案) 從 HDInsight 存取：

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

如果您要直接從儲存體帳戶存取檔案，檔案的Blob 名稱為：

    example/data/sample.log

在本文中，您將使用 HDInsight 叢集隨附並儲存在 *\\example\\data\\sample.log* 中的 log4j 範例檔案。如需上傳專屬資料檔案的詳細資訊，請參閱[將資料上傳到 HDInsight][]。

## <span id="understand"></span></a>了解 Pig Latin

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

## <span id="powershell"></span></a>使用 PowerShell 提交 Pig 工作

本節提供 PowerShell Cmdlet 的使用指示。在進行本節之前，您必須先設定本機環境，並設定對 Azure 的連接。如需詳細資訊，請參閱 [Azure HDInsight 使用者入門][]和[使用 PowerShell 管理 HDInsight][]。

**使用 PowerShell 執行 Pig Latin**

1.  開啟 Windows PowerShell ISE (在 Windows 8 的 [開始] 畫面上輸入 **PowerShell\_ISE**，然後按一下 [Windows PowerShell ISE]。請參閱[在 Windows 8 和 Windows 上啟動 Windows PowerShell][])。
2.  在底部窗格中執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

    系統會提示您輸入 Azure 帳號認證。這種新增訂用帳戶連線的方法會逾時，且在 12 小時後，您將必須重新執行 Cmdlet。

    > [WACOM.NOTE] 如果您有多個 Azure 訂用帳戶，且預設訂用帳戶並非您要使用的訂用帳戶，請使用 **Select-AzureSubscription** Cmdlet 選取目前的訂用帳戶。

3.  在指令碼窗格中，複製並貼上下列程式碼行：

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  設定變數 $clusterName。

5.  將下列程式碼行附加至指令碼窗格中。這些程式碼行會定義 Pig Latin 查詢字串，並建立 Pig 工作定義：

        # Create the Pig job definition
        $0 = '$0';
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;" 

        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

    您也可以使用 -File 參數，在 HDFS 上指定一個 Pig 指令碼檔案。-StatusFolder 開關可將標準錯誤記錄和標準輸出檔案放入資料夾。

6.  附加下列程式碼行，以提交 Pig 工作：

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  附加下列程式碼行，以等候 Pig 工作完成：

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  附加下列程式碼行，以列印 Pig 工作輸出：

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] 下列其中一個 Get-AzureHDInsightJobOut Cmdlet 已加上註解，以縮短下列螢幕擷取畫面的輸出時間。

9.  按 **F5** 以執行指令碼：
    ![HDI.Pig.PowerShell][]

    Pig 工作會計算不同記錄類型的頻率。

## <span id="sdk"></span></a>使用 HDInsight .NET SDK 提交 Pig 工作

下列是使用 HDInsight .NET SDK 提交 Pig 工作的範例。如需建立 C# 應用程式以提交 Hadoop 工作的指示，請參閱[以程式設計方式提交 Hadoop 工作][]。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    using System.IO;
    using System.Threading;
    using System.Security.Cryptography.X509Certificates;

    using Microsoft.WindowsAzure.Management.HDInsight;
    using Microsoft.Hadoop.Client;

    namespace SubmitPigJobs
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Set the variables
                string subscriptionID = "<Azure subscription ID>";
                string certFriendlyName = "<certificate friendly name>";
        
                string clusterName = "<HDInsight cluster name>";
                string statusFolderName = @"/tutorials/usepig/status";

                string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                    "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                    "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                    "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                    "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                    "RESULT = order FREQUENCIES by COUNT desc;" +
                    "DUMP RESULT;";

                // Define the Pig job
                PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
                {
                    Query = queryString,
                    StatusFolder = statusFolderName
                };

                // Get the certificate object from certificate store using the friendly name to identify it
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Create a hadoop client to connect to HDInsight
                var jobClient = JobSubmissionClientFactory.Connect(creds);

                // Run the MapReduce job
                Console.WriteLine("----- Submit the Pig job ...");
                JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);

                // Wait for the job to complete
                Console.WriteLine("----- Wait for the Pig job to complete ...");
                WaitForJobCompletion(mrJobResults, jobClient);

                // Display the error log
                Console.WriteLine("----- The Pig job error log.");
                using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                // Display the output log
                Console.WriteLine("----- The Pig job output log.");
                using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                Console.WriteLine("----- Press ENTER to continue.");
                Console.ReadLine();
            }

            private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
            {
                JobDetails jobInProgress = client.GetJob(jobResults.JobId);
                while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
                {
                    jobInProgress = client.GetJob(jobInProgress.JobId);
                    Thread.Sleep(TimeSpan.FromSeconds(10));
                }
            }
        }
    }

## <span id="nextsteps"></span></a>後續步驟

除了可讓您執行資料分析的 Pig 以外，HDInsight 隨附的其他語言也可能對您有所幫助。Hive 提供類似於 SQL 的查詢語言，可讓您輕鬆地對儲存於 HDInsight 中的資料進行查詢，而以 Java 撰寫的 MapReduce 工作則可讓您執行複雜的資料分析。如需詳細資訊，請參閱下列主題：

-   [Azure HDInsight 使用者入門][]
-   [將資料上傳到 HDInsight][]
-   [以程式設計方式提交 Hadoop 工作][]
-   [搭配 HDInsight 使用 Hive][]

  [Apache Pig]: http://pig.apache.org/
  [Pig 使用案例]: #usage
  [必要條件]: #prerequisites
  [了解 Pig Latin]: #understand
  [使用 PowerShell 提交 Pig 工作]: #powershell
  [使用 HDInsight .NET SDK 提交 Pig 工作]: #sdk
  [後續步驟]: #nextsteps
  [Whole File Sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Pig Latin 參考手冊 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Pig Latin 參考手冊 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Azure HDInsight 使用者入門]: ../hdinsight-get-started/
  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [搭配 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [將資料上傳到 HDInsight]: ../hdinsight-upload-data/
  [使用 PowerShell 管理 HDInsight]: ../hdinsight-administer-use-powershell/
  [在 Windows 8 和 Windows 上啟動 Windows PowerShell]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [以程式設計方式提交 Hadoop 工作]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [搭配 HDInsight 使用 Hive]: ../hdinsight-use-hive/
