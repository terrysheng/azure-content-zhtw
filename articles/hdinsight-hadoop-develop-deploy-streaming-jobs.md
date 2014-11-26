<properties linkid="manage-services-hdinsight-develop-hadoop-streaming-programs-for-hdinsight" urlDisplayName="" pageTitle="Develop C# Hadoop streaming programs for HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="Learn how to develop Hadoop streaming MapReduce programs in C#, and how to deploy them to Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Develop C# Hadoop streaming programs for HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# 開發 HDInsight 的 C# Hadoop 串流程式

Hadoop 為 MapReduce 提供一個串流 API，可讓您以 Java 以外的語言撰寫 map 和 reduce 函數。本教學課程引導您完成一項端對端案例，從在 HDInsight 模擬器上使用 C# 來開發/測試 Hadoop 串流 MapReduce 程式開始，一直到在 Azure HDInsight 上執行 MapReduce 工作然後擷取結果為止。

**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：

-   安裝 Azure HDInsight Emulator。如需指示，請參閱＜[開始使用 HDInsight Emulator][開始使用 HDInsight Emulator]＞。
-   在模擬器電腦上安裝 Azure PowerShell。如需指示，請參閱＜[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]＞
-   取得 Azure 訂用帳戶。如需指示，請參閱＜[購買選項][購買選項]＞、＜[成員優惠][成員優惠]＞或＜[免費試用][免費試用]＞。

## 本文內容

-   [使用 C# 開發字數統計 Hadoop 串流程式][使用 C# 開發字數統計 Hadoop 串流程式]
-   [在模擬器上測試程式][在模擬器上測試程式]
-   [將資料和應用程式上傳至 Azure Blob 儲存體][將資料和應用程式上傳至 Azure Blob 儲存體]
-   [在 Azure HDInsight 上執行 MapReduce 程式][在 Azure HDInsight 上執行 MapReduce 程式]
-   [擷取 MapReduce 結果][擷取 MapReduce 結果]
-   [後續步驟][後續步驟]

## <a name="develop"></a>使用 C# 開發字數統計 Hadoop 串流程式

字數統計方案包含兩個主控台應用程式專案：對應器和歸納器。對應器應用程式可將每一個字串流傳送至主控台，而歸納器應用程式可計算從文件中串流傳送而來的字數。mapper 和 reducer 都會從標準輸入資料流 (stdin) 循行讀取字元並寫入至標準輸出資料流 (stdout)。

**建立 C# 主控台應用程式**

1.  開啟 Visual Studio 2013。
2.  依序按一下 [檔案]、[新增]、[專案]。
3.  輸入或選取下列值：

	<table border="1">
	<tr><td>欄位</td><td>值</td></tr>
	<tr><td>範本</td><td>Visual C#/Windows/主控台應用程式</td></tr>
	<tr><td>名稱</td><td>WordCountMapper</td></tr>
	<tr><td>位置</td><td>C:\Tutorials</td></tr>
	<tr><td>方案名稱</td><td>WordCount</td></tr>
	</table>


4.  按一下 [確定] 以建立專案。

**建立對應器程式**

1.  在 [方案總管] 中，以滑鼠右鍵按一下 [Program.cs]，然後按一下 [重新命名]。
2.  將檔案重新命名為 **WordCountMapper.cs**，然後按 **ENTER**。
3.  按一下 [是] 以確認重新命名所有參考。
4.  按兩下 [WordCountMapper.cs] 以開啟它。
5.  加入下列 using 陳述式：

        using System.IO;

6.  以下列程式碼取代 Main() 函數：

        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            string[] words;

            while ((line = Console.ReadLine()) != null)
            {
                words = line.Split(' ');

                foreach (string word in words)
                    Console.WriteLine(word.ToLower());
            }
        }

7.  按一下 [建置]，然後按一下 [建置方案] 來編譯對應器程式。

**建立歸納器程式**

1.  從 Visual Studio 2013 中，依序按一下 [檔案]、[加入]、[新專案]。
2.  輸入或選取下列值：

	<table border="1">
	<tr><td>欄位</td><td>值</td></tr>
	<tr><td>範本</td><td>Visual C#/Windows/主控台應用程式</td></tr>
	<tr><td>名稱</td><td>WordCountReducer</td></tr>
	<tr><td>位置</td><td>C:\Tutorials\WordCount</td></tr>
	</table>
3.  按一下 [確定] 以建立專案。
4.  從 [方案總管] 中，以滑鼠右鍵按一下 [Program.cs]，然後按一下 [重新命名]。
5.  將檔案重新命名為 **WordCountReducer.cs**，然後按 **ENTER**。
6.  按一下 [是] 以確認重新命名所有參考。
7.  按兩下 [WordCountReducer.cs] 以開啟它。
8.  加入下列 using 陳述式：

        using System.IO;

9.  以下列程式碼取代 Main() 函數：

        static void Main(string[] args)
        {
            string word, lastWord = null;
            int count = 0;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            while ((word = Console.ReadLine()) != null)
            {
                if (word != lastWord)
                {
                    if(lastWord != null)
                        Console.WriteLine("{0}[{1}]", lastWord, count);

                    count = 1;
                    lastWord = word;
                }
                else
                {
                    count += 1; 
                }
            }
            Console.WriteLine(count);
        }

10. 按一下 [建置]，然後按一下 [建置方案] 來編譯方案。

對應器和歸納器可執行檔位於：

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

## <a name="test"></a>在模擬器上測試程式

本節包含下列程序：

1.  將資料上傳至模擬器 HDFS
2.  將對應器和歸納器上傳至模擬器 HDFS
3.  提交字數統計 MapReduce 工作
4.  檢查工作狀態
5.  擷取工作結果

依預設，HDInsight 模擬器會使用 HDFS 做為預設檔案系統。您也可以選擇設定 HDInsight 模擬器來使用 Azure Blob 儲存體。如需詳細資料，請參閱＜[開始使用 HDInsight Emulator][1]＞。本節中，您將使用 HDFS copyFromLocal 命令來上傳檔案。下一節說明如何使用 Azure PowerShell 來上傳檔案。關於其他方案，請參閱＜[將資料上傳到 HDInsight][將資料上傳到 HDInsight]＞。

本教學課程使用下列資料夾結構：
<table border="1">
<tr><td>資料夾</td><td>注意</td></tr>
<tr><td>\WordCount</td><td>字數統計專案的根資料夾。 </td></tr>
<tr><td>\WordCount\Apps</td><td>對應器和歸納器可執行檔的資料夾。</td></tr>
<tr><td>\WordCount\Input</td><td>MapReduce 來源檔案資料夾。</td></tr>
<tr><td>\WordCount\Output</td><td>MapReduce 輸出檔案資料夾。</td></tr>
<tr><td>\WordCount\MRStatusOutput</td><td>工作輸出資料夾。</td></tr>
</table>


本教學課程使用 %hadoop\_home% 目錄中的 .txt 檔案。

> [WACOM.NOTE] Hadoop HDFS 命令區分大小寫。

**將文字檔複製到模擬器 HDFS**

1.  從 Hadoop 命令列視窗中，執行下列命令來建立輸入檔案的目錄：

        hadoop fs -mkdir /WordCount/Input

    這裡使用的路徑是相對路徑。此命令相當於：

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  執行下列命令將一些文字檔案複製到 HDFS 上的輸入資料夾：

        hadoop fs -copyFromLocal %hadoop_home%\*.txt \WordCount\Input

3.  執行下列命令來列出已上傳的檔案：

        hadoop fs -ls \WordCount\Input

    您會看到大約八個 .txt 檔案。

**將對應器和歸納器部署到模擬器 HDFS**

1.  從桌面開啟 Hadoop 命令列。
2.  執行以下命令：

        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  執行下列命令來列出已上傳的檔案

        hadoop fs -lsr /WordCount/Apps

    您應該會看到兩個 .exe 檔案。

**使用 HDInsight PowerShell 執行 MapReduce 工作**

1.  開啟 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。
2.  執行下列命令來設定變數：

        $clusterName = "http://localhost:50111"

        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "/WordCount/Input/"
        $mrOutput = "/WordCount/Output"
        $mrStatusOutput = "/WordCount/MRStatusOutput"

    HDInsight 模擬器叢集名稱是 "http://localhost:50111"。

3.  執行下列命令來定義串流工作：

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

4.  執行下列命令來建立認證物件：

        $creds = Get-Credential -Message "Enter password" -UserName "hadoop"

    系統會提示您輸入密碼。密碼可以是任何字串。使用者名稱必須為 "hadoop"。

5.  執行下列命令來提交 MapReduce 工作，並等待工作完成：

        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
        Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

    完成時，將會出現類似下方的輸出：

        StatusDirectory : /WordCount/MRStatusOutput
        ExitCode        : 
        Name            : mrWordCountStreamingJob
        Query           : 
        State           : Completed
        SubmissionTime  : 11/15/2013 7:18:16 PM
        Cluster         : http://localhost:50111
        PercentComplete : map 100%  reduce 100%
        JobId           : job_201311132317_0034

    這樣就可以知道工作識別碼為 job-201311132317-0034 (舉例來說)。

**檢查工作狀態**

1.  從桌面上，按一下 [Hadoop MapReduce Status]，或瀏覽至 **http://localhost:50030/jobtracker.jsp**。
2.  在下列三個區段下，利用工作識別碼來找出工作：[Completed Jobs]、[Running Jobs]、[Retired Jobs]。
3.  如果工作失敗，您可以開啟工作詳細資料，尋找一些有用的資訊來進行偵錯。

**從 HDFS 顯示輸出**

1.  開啟 Hadoop 命令列。
2.  執行下列命令來顯示輸出：

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    您可以在命令尾端附加 "|more" 來取得頁面檢視。

## <span id="upload"></span></a>將資料上傳至 Azure Blob 儲存體

Azure HDInsight 使用 Azure Blob 儲存體做為預設檔案系統。您可以設定 HDInsight 叢集使用其他 Blob 儲存體來儲存資料檔。本節中，您將建立儲存體帳戶，並將資料檔上傳至 Blob 儲存體。資料檔是位於 %hadoop\_home% 目錄中的 txt 檔案。

**建立 Blob 儲存體和容器**

1.  開啟 Azure PowerShell。
2.  設定變數，然後執行命令：

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  執行下列命令來建立儲存體帳戶，並在帳戶上建立 Blob 儲存體容器

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName -Context $destContext

4.  執行下列命令來驗證儲存體帳戶和容器：

        Get-AzureStorageAccount -StorageAccountName $storageAccountName
        Get-AzureStorageContainer -Context $destContext

**上傳資料檔案**

1.  開啟 Azure PowerShell。
2.  設定前三個變數，然後執行命令：

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
        $destFolder = "WordCount/Input"

    請注意，來源檔案資料夾是 **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**，目的地資料夾是 **WordCount/Input**。

3.  執行下列命令來取得來源檔案資料夾中的 txt 檔案清單：

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  執行下列命令來建立儲存體內容物件：

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5.  執行下列命令來複製檔案：

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
        }

6.  執行下列命令來列出已上傳的檔案：

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**上傳字數統計應用程式**

1.  開啟 Azure PowerShell。
2.  設定前三個變數，然後執行命令：

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
        $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
        $blobFolder = "WordCount/Apps"

    請注意，目的地資料夾是 **WordCount/Apps**。

3.  執行下列命令來建立儲存體內容物件：

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

4.  執行下列命令來複製應用程式：

        Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
        Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

5.  執行下列命令來列出已上傳的檔案：

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    您應該會看到兩個檔案都列在這裡。

## <a name="run"></a>在 Azure HDInsight 上執行 MapReduce 工作

下列 PowerShell 指令碼會執行下列工作：

1.  佈建 HDInsight 叢集

    1.  建立儲存體帳戶做為預設 HDInsight 叢集檔案系統
    2.  建立 Blob 儲存體容器
    3.  建立 HDInsight 叢集

2.  提交 MapReduce 工作

    1.  建立串流 MapReduce 工作定義
    2.  提交 MapReduce 工作
    3.  等待工作完成
    4.  顯示標準錯誤
    5.  顯示標準輸出

3.  刪除叢集

    1.  刪除 HDInsight 叢集
    2.  刪除做為預設 HDInsight 叢集檔案系統的儲存體帳戶

**執行 PowerShell 指令碼**

1.  開啟記事本。
2.  複製並貼上下列程式碼：

        # The storage account and the HDInsight cluster variables
        $subscriptionName = "<AzureSubscriptionName>"
        $serviceNameToken = "<ServiceNameTokenString>"
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = 1

        $clusterName = $serviceNameToken + "hdicluster"

        $storageAccountName_Default = $serviceNameToken + "hdistore"
        $containerName_Default =  $serviceNameToken + "hdicluster"

        # The streaming MapReduce job variables
        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        Select-AzureSubscription $subscriptionName

        #=============================
        # Create a storage account
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #=============================
        # Create a Blob storage container
        Write-Host "Create a Blob storage container" -ForegroundColor Green
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

        New-AzureStorageContainer -Name $containerName_Default -Context $destContext

        #=============================
        # Create an HDInsight cluster
        Write-Host "Create an HDInsight cluster" -ForegroundColor Green
        $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

        New-AzureHDInsightCluster -Subscription $subscriptionName  -Name $clusterName -Location $location -Config $config

        #=============================
        # Create a streaming MapReduce job definition
        Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

        #=============================
        # Run a streaming MapReduce job
        Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Subscription $subscriptionName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Subscription $subscriptionName -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardOutput

        #=============================
        # Delete the HDInsight cluster
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Remove-AzureHDInsightCluster -Name $clusterName -Subscription $subscriptionName 

        # Delete the storage account
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  設定指令碼中的前四個變數。$serviceNameToken 將做為 HDInsight 叢集名稱、儲存體帳戶名稱及 Blob 儲存體容器名稱。因為服務名稱必須為 3 到 24 個字元，且指令檔會將最多 10 個字元的字串附加至名稱，所以您必須將字串限制在 14 個字元以下。$serviceNameToken 必須全部為小寫。$storageAccountName\_Data 和 $containerName\_Data 是用來儲存資料檔案和應用程式的儲存體帳戶和容器。$location 必須符合資料儲存體帳戶位置。
4.  檢閱其餘變數。
5.  儲存指令碼檔案。
6.  開啟 Azure PowerShell。
7.  執行下列命令將執行原則設為 remotesigned：

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  出現提示時，輸入 HDInsight 叢集的使用者名稱和密碼。因為您在指令碼最後會刪除叢集，然後就不再需要使用者名稱和密碼，所以使用者名稱和密碼可以是任何字串。如果不希望出現認證提示，請參閱＜[在 Windows PowerShell 中使用密碼、安全字串和認證][在 Windows PowerShell 中使用密碼、安全字串和認證]＞(英文)

如需提交 Hadoop 串流工作的 HDInsight .NET SDK 範例，請參閱[以程式設計方式提交 Hadoop 工作][以程式設計方式提交 Hadoop 工作]。

## <a name="retrieve"></a>擷取 MapReduce 工作輸出

本節說明如何下載和顯示輸出。如需有關在 Excel 上顯示結果的詳細資訊，請參閱＜[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight]＞和＜[使用 Power Query 將 Excel 連接到 HDInsight][使用 Power Query 將 Excel 連接到 HDInsight]＞。

**擷取輸出**

1.  開啟 Azure PowerShell 視窗。
2.  設定值，然後執行命令：

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<TheDataStorageAccountName>"
        $containerName = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-00000"

3.  執行下列命令來建立 Azure 儲存體內容物件：

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  執行下列命令來下載和顯示輸出：

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

## <span id="nextsteps"></span></a>後續步驟

本教學課程中，您學到如何開發 Hadoop 串流 MapReduce 工作、如何在 HDInsight 模擬器上測試應用程式，以及如何撰寫 PowerShell 指令碼來佈建 HDInsight 叢集並於叢集上執行 MapReduce。若要深入了解，請參閱下列文章：

-   [Azure HDInsight 使用者入門][Azure HDInsight 使用者入門]
-   [開始使用 HDInsight Emulator][開始使用 HDInsight Emulator]
-   [開發 HDInsight 的 Java MapReduce 程式][開發 HDInsight 的 Java MapReduce 程式]
-   [在 HDInsight 上使用 Azure Blob 儲存體][在 HDInsight 上使用 Azure Blob 儲存體]
-   [使用 PowerShell 管理 HDInsight][使用 PowerShell 管理 HDInsight]
-   [將資料上傳到 HDInsight][將資料上傳到 HDInsight]
-   [搭配 HDInsight 使用 Hive][搭配 HDInsight 使用 Hive]
-   [搭配 HDInsight 使用 Pig][搭配 HDInsight 使用 Pig]

  [開始使用 HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [購買選項]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
  [使用 C# 開發字數統計 Hadoop 串流程式]: #develop
  [在模擬器上測試程式]: #test
  [將資料和應用程式上傳至 Azure Blob 儲存體]: #upload
  [在 Azure HDInsight 上執行 MapReduce 程式]: #run
  [擷取 MapReduce 結果]: #retrieve
  [後續步驟]: #nextsteps
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [將資料上傳到 HDInsight]: ../hdinsight-upload-data/
  [在 Windows PowerShell 中使用密碼、安全字串和認證]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [以程式設計方式提交 Hadoop 工作]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [使用 Power Query 將 Excel 連接到 HDInsight]: ../hdinsight-connect-excel-power-query/
  [Azure HDInsight 使用者入門]: ../hdinsight-get-started/
  [開發 HDInsight 的 Java MapReduce 程式]: ../hdinsight-develop-deploy-java-mapreduce/
  [在 HDInsight 上使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [使用 PowerShell 管理 HDInsight]: ../hdinsight-administer-use-powershell/
  [搭配 HDInsight 使用 Hive]: ../hdinsight-use-hive/
  [搭配 HDInsight 使用 Pig]: ../hdinsight-use-pig/
