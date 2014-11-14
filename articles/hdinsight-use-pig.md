<properties urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="在 HDInsight 上使用 Hadoop Pig | Azure" metaKeywords="" description="了解如何搭配 HDInsight 使用 Pig。撰寫 Pig Latin 陳述式以分析應用程式記錄檔，並對資料執行查詢以產生分析所需的輸出。" metaCanonical="" services="hdinsight" documentationCenter="" title="在 HDInsight 上使用 Hadoop Pig" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# 在 HDInsight 上將 Pig 與 Hadoop 搭配使用

在本教學課程中，您將了解如何在 HDInsight 上執行 [Apache Pig][Apache Pig] 工作，以分析大型資料檔案。Pig 所提供的指令碼語言可執行 *MapReduce* 工作，讓您在撰寫 Java 程式碼以外多了一項選擇。Pig 的指令碼語言稱為 *Pig Latin*。

**必要條件**

-   您必須已佈建 Azure HDInsight 叢集。如需指示，請參閱 [Azure HDInsight 使用者入門][Azure HDInsight 使用者入門]或[佈建 HDInsight 叢集][佈建 HDInsight 叢集]。進行教學課程時，您將需要叢集的名稱。

-   您的工作站必須已安裝並設定 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。

**預估完成時間：**30 分鐘

## 本文內容

-   [為何要使用 Pig？][為何要使用 Pig？]
-   [在本教學課程中將執行哪些作業？][在本教學課程中將執行哪些作業？]
-   [識別要分析的資料][識別要分析的資料]
-   [了解 Pig Latin][了解 Pig Latin]
-   [使用 PowerShell 提交 Pig 工作][使用 PowerShell 提交 Pig 工作]
-   [使用 HDInsight .NET SDK 提交 Pig 工作][使用 HDInsight .NET SDK 提交 Pig 工作]
-   [後續步驟][後續步驟]

## <span id="usage"></span></a>為何要使用 Pig？

使用關聯式資料庫和統計資料/虛擬化封裝來處理巨量資料，是很困難的。由於資料本身和資料運算的數量都很龐大，要在時限內計算巨量資料，常須在數十、數百甚或數千部伺服器上平行執行軟體，才有辦法。Hadoop 所提供的 *MapReduce* 架構可用來撰寫應用程式，讓您以非常可靠且可容錯的方式，平行處理大型機器叢集之間的大量結構化和非結構化資料。

![HDI.Pig.Architecture][HDI.Pig.Architecture]

[Apache *Pig*][Apache Pig] 會在 Java 型 MapReduce 架構上提供抽象層，讓使用者無需具備 Java 或 MapReduce 知識即可分析資料。Pig 是一個平台，可讓您透過易用的語言 (名為 *Pig Latin*，是一種資料流程語言) 分析大型資料集。Pig 可縮短撰寫對應程式和節流程式所需的時間。無需任何 Java 知識即可使用此平台。同時，您也擁有結合 Java 程式碼與 Pig 的彈性。許多複雜的演算法，都可用不到五行且易讀的 Pig 程式碼撰寫出來。

Pig Latin 陳述式的一般執行流程如下：

-   **載入**：從檔案系統讀取要處理的資料
-   **轉換**：處理資料
-   **傾印或儲存**：將資料輸出至畫面，或儲存以供處理

如需 Pig Latin 的詳細資訊，請參閱 [Pig Latin 參考手冊 1][Pig Latin 參考手冊 1] (英文) 和 [Pig Latin 參考手冊 2][Pig Latin 參考手冊 2] (英文)。

## <span id="what"></span></a>在本教學課程中將執行哪些作業？

在本教學課程中，您將分析 Apache 記錄檔 (*sample.log*)，以判斷不同記錄層級的數量，例如「資訊」、「偵錯」、「追蹤」等。您在本文中完成的工作將以下列兩個圖表顯示出來。第一個圖表顯示 sample.log 檔案的程式碼片段。

![Whole File Sample][Whole File Sample]

第二個圖表解說您在執行指令碼中的 Pig 程式碼行時的資料流程和轉換。

![HDI.PIG.Data.Transformation][HDI.PIG.Data.Transformation]

您在本教學課程中建立的 Pig 工作會依循相同的流程。

## <span id="data"></span></a>識別要分析的資料

HDInsight 使用 Azure Blob 儲存容器作為 Hadoop 叢集的預設檔案系統。有些範例資料檔案會新增至 Blob 儲存體，作為叢集佈建的一部分。您可以使用這些範例資料檔案，對叢集執行 Hive 查詢。如果需要，您也可以將自己的資料檔案上傳至與叢集相關聯的 Blob 儲存體帳戶。如需指示，請參閱[將資料上傳至 HDInsight][將資料上傳至 HDInsight]。如需如何搭配使用 Azure Blob 儲存體與 HDInsight 的詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]。

Blob 儲存體中的檔案的存取語法如下：

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 叢集 3.0 版僅支援 *wasb://* 語法。HDInsight 2.1 和 1.6 支援舊的 *asv://* 語法，但在 HDInsight 3.0 叢集中已不受支援，未來的版本也不加以支援。

儲存在預設檔案系統容器中的檔案，也可使用下列任一 URI (範例中使用 sample.log。此檔案是本教學課程中使用的資料檔案) 從 HDInsight 存取：

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

如果您要直接從儲存體帳戶存取檔案，檔案的Blob 名稱為：

    example/data/sample.log

本文使用隨附於 HDInsight 叢集的 *log4j* 範例檔，此檔案儲存在 *\\example\\data\\sample.log* 上。如需上傳專屬資料檔案的詳細資訊，請參閱[將資料上傳到 HDInsight][將資料上傳至 HDInsight]。

## <span id="understand"></span></a>了解 Pig Latin

在本節中，您將會個別檢閱幾個 Pig Latin 陳述式，以及執行這些陳述式之後的結果。在下一節中，您會使用 PowerShell 同時執行這些 Pig 陳述式，以分析範例記錄檔。個別的 Pig Latin 陳述式必須直接在 HDInsight 叢集上執行。

1.  遵循[使用 RDP 連接到 HDInsight 叢集][使用 RDP 連接到 HDInsight 叢集]上的指示，啟用適用於 HDInsight 叢集的遠端桌面。登入叢集節點，然後在桌面上按一下 [Hadoop 命令列]。

2.  在命令列中，導覽至 **Pig** 的安裝目錄。輸入：

        C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3.  在命令提示字元上輸入 *pig*，然後按 ENTER 鍵，以進入 *grunt* Shell。

        C:\apps\dist\pig-<version>\bin>pig
        ...
        grunt>  

4.  輸入下列內容以從檔案系統中的範例檔案載入資料，然後顯示結果：

        grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
        grunt> DUMP LOGS;

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

5.  查看資料檔案中的每一行，以尋找 6 個記錄層級上的相符項目：

        grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

6.  篩選掉沒有相符項目的資料列，然後顯示結果。這會捨棄掉空資料列。

        grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        grunt> DUMP FILTEREDLEVELS;

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

7.  將所有的記錄層級分組到其本身的資料列中，然後顯示結果：

        grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        grunt> DUMP GROUPEDLEVELS;

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

8.  為每個群組計算記錄層級的出現次數，然後顯示結果：

        grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        grunt> DUMP FREQUENCIES;

    輸出大致如下：

        (INFO,96)
        (WARN,11)
        (DEBUG,434)
        (ERROR,6)
        (FATAL,2)
        (TRACE,816)

9.  以遞減順序排序頻率，然後顯示結果：

        grunt> RESULT = order FREQUENCIES by COUNT desc;
        grunt> DUMP RESULT;   

    輸出大致如下：

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <span id="powershell"></span></a>使用 PowerShell 提交 Pig 工作

本節提供 PowerShell Cmdlet 的使用指示。在進行本節之前，您必須先設定本機環境，並設定對 Azure 的連接。如需詳細資訊，請參閱 [Azure HDInsight 使用者入門][Azure HDInsight 使用者入門]和[使用 PowerShell 管理 HDInsight][使用 PowerShell 管理 HDInsight]。

**使用 PowerShell 執行 Pig Latin**

1.  開啟 Windows PowerShell ISE。在 Windows 8 的 [開始] 畫面上輸入 **PowerShell\_ISE**，然後按一下 [Windows PowerShell ISE]。如需詳細資訊，請參閱[在 Windows 8 和 Windows 上啟動 Windows PowerShell][在 Windows 8 和 Windows 上啟動 Windows PowerShell]。

2.  在底部窗格中執行下列命令，以連接到您的 Azure 訂用帳戶：

        Add-AzureAccount

    系統會提示您輸入 Azure 帳號認證。這種新增訂用帳戶連線的方法會逾時，且在 12 小時後，您將必須重新執行 Cmdlet。

    > [WACOM.NOTE] 如果您有多個 Azure 訂用帳戶，且預設訂用帳戶並非您要使用的訂用帳戶，請使用 **Select-AzureSubscription** Cmdlet 選取目前的訂用帳戶。

3.  在指令碼窗格中，複製並貼上下列程式碼行：

        $clusterName = "<HDInsightClusterName>"     #Specify the cluster name
        $statusFolder = "/tutorials/usepig/status"  #Specify the folder to dump results

    如果您指定的狀態資料夾尚不存在，指令碼會加以建立。

4.  將下列程式碼行附加至指令碼窗格中。這些程式碼行會定義 Pig Latin 查詢字串，並建立 Pig 工作定義：

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

    您也可以使用 **-File** 參數，在 HDFS 上指定一個 Pig 指令碼檔案。**-StatusFolder** 參數可將標準錯誤記錄和標準輸出檔案放入資料夾。

5.  附加下列程式碼行，以提交 Pig 工作：

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

6.  附加下列程式碼行，以等候 Pig 工作完成：

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  附加下列程式碼行，以列印 Pig 工作輸出：

        # Print the standard error and the standard output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] 下列其中一個 Get-AzureHDInsightJobOut Cmdlet 已加上註解，以縮短下列螢幕擷取畫面的輸出時間。

8.  按 **F5** 以執行指令碼：

    ![HDI.Pig.PowerShell][HDI.Pig.PowerShell]

    Pig 工作會計算不同記錄類型的頻率。

## <span id="sdk"></span></a>使用 HDInsight .NET SDK 提交 Pig 工作

遵循下列步驟，可使用 C# 應用程式提交 Pig 工作。如需建立 C# 應用程式以提交 Hadoop 工作的指示，請參閱[以程式設計方式提交 Hadoop 工作][以程式設計方式提交 Hadoop 工作]。

1.  建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。如需指示，請參閱[建立自我簽署憑證][建立自我簽署憑證]。

2.  建立 Visual Studio 主控台應用程式，然後安裝 HDInsight 套件。在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。在出現提示時輸入下列內容：

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

3.  按兩下 Program.cs，然後貼上下列程式碼以提交 Pig 工作。提供變數的值。

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

-   [Azure HDInsight 使用者入門][Azure HDInsight 使用者入門]
-   [將資料上傳到 HDInsight][將資料上傳至 HDInsight]
-   [以程式設計方式提交 Hadoop 工作][以程式設計方式提交 Hadoop 工作]
-   [搭配 HDInsight 使用 Hive][搭配 HDInsight 使用 Hive]

  [Apache Pig]: http://pig.apache.org/
  [Azure HDInsight 使用者入門]: ../hdinsight-get-started/
  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [為何要使用 Pig？]: #usage
  [在本教學課程中將執行哪些作業？]: #what
  [識別要分析的資料]: #data
  [了解 Pig Latin]: #understand
  [使用 PowerShell 提交 Pig 工作]: #powershell
  [使用 HDInsight .NET SDK 提交 Pig 工作]: #sdk
  [後續步驟]: #nextsteps
  [HDI.Pig.Architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
  [Pig Latin 參考手冊 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Pig Latin 參考手冊 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Whole File Sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [將資料上傳至 HDInsight]: ../hdinsight-upload-data/
  [搭配 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [使用 RDP 連接到 HDInsight 叢集]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [使用 PowerShell 管理 HDInsight]: ../hdinsight-administer-use-powershell/
  [在 Windows 8 和 Windows 上啟動 Windows PowerShell]: http://technet.microsoft.com/zh-tw/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [以程式設計方式提交 Hadoop 工作]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [建立自我簽署憑證]: http://go.microsoft.com/fwlink/?LinkId=511138
  [搭配 HDInsight 使用 Hive]: ../hdinsight-use-hive/
