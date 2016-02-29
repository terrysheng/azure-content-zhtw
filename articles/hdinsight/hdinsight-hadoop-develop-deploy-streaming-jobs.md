
<properties
	pageTitle="開發 HDInsight 的 C# Hadoop 串流程式 | Microsoft Azure"
	description="了解如何使用 C# 開發 Hadoop 串流 MapReduce 程式，以及如何部署至 Azure HDInsight。"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="jgao"/>



# 開發 HDInsight 的 C# Hadoop 串流程式

Hadoop 為 MapReduce 提供一個串流 API，可讓您以 Java 以外的語言撰寫 map 和 reduce 函數。本教學課程逐步引導您完成建立一個 C# 字數統計程式，從您提供的輸入資料中計算給定單字的出現次數。下圖顯示 MapReduce 架構如何執行字數統計。

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

> [AZURE.NOTE] 本文章的步驟只適用於 Windows 型 Azure HDInsight 叢集。如需 Linux 型 HDInsight 的串流範例，請參閱[開發適用於 HDInsight 的 Python 串流程式](hdinsight-hadoop-streaming-python.md)。

本教學課程說明如何：

- 使用 C# 開發 Hadoop 串流 MapReduce 程式 
- 在 Azure HDInsight 上執行 MapReduce 工作
- 擷取 MapReduce 工作的結果

###先決條件

開始進行本教學課程之前，您必須完成下列工作：

- 具有 [Azure PowerShell][powershell-install] 和 [Microsoft Visual Studio](https://www.visualstudio.com/) 的工作站。
- 取得 Azure 訂用帳戶。如需指示，請參閱＜[購買選項][azure-purchase-options]＞、＜[成員優惠][azure-member-offers]＞或＜[免費試用][azure-free-trial]＞。


##使用 C&#35 開發字數統計 Hadoop 串流程式

字數統計方案包含兩個主控台應用程式專案：對應器和歸納器。對應器應用程式可將每一個字串流傳送至主控台，而歸納器應用程式可計算從文件中串流傳送而來的字數。對應器和歸納器都會從標準輸入資料流 (stdin) 循行讀取字元並寫入至標準輸出資料流 (stdout)。

**建立對應器程式**

1. 開啟 Visual Studio，建立名為 **WordCountMapper** 的 C# 主控台應用程式。
2. 在 [方案總管] 中，將 **Program.cs** 重新命名為 **WordCountMapper.cs**。按一下 [是] 以確認重新命名所有參考。
3. 使用下列程式碼取代 WordCountMapper.cs 中的程式碼：

        using System;
        using System.IO;

        namespace WordCountMapper
        {
            class WordCountMapper
            {
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
            }
        }

4. 建置方案，並確定沒有任何編譯錯誤。

**建立歸納器程式**

1. 將另一個名為 **WordCountReducer** 的 C# 主控台應用程式加入至方案。位置|C:\\Tutorials\\WordCount
2. 在 [方案總管] 中，將 **Program.cs** 重新命名為 **WordCountReducer.cs**。按一下 [是] 以確認重新命名所有參考。
3. 使用下列程式碼取代 WordCountReducer.cs 中的程式碼：

        using System;
        using System.IO;

        namespace WordCountReducer
        {
            class WordCountReducer
            {
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
                            if (lastWord != null)
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
            }
        }

4. 建置方案，並確定沒有任何編譯錯誤。

您應該會得到對應器和歸納器可執行檔：

- ..\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
- ..\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe


##將資料上傳至 Azure Blob 儲存體
Azure HDInsight 使用 Azure Blob 儲存體做為預設檔案系統。您可以設定 HDInsight 叢集使用其他 Blob 儲存體來儲存資料檔。本節中，您將建立 Azure 儲存體帳戶，並將資料檔上傳至 Blob 儲存體。資料檔是位於 %hadoop\_home%\\share\\doc\\hadoop\\common 目錄中的 .txt 檔案。


**建立儲存體帳戶和容器**

1. 開啟 Azure PowerShell。
2. 設定變數，然後執行命令：

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. 執行下列命令來建立儲存體帳戶，並在帳戶上建立 Blob 儲存體容器：

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. 執行下列命令以驗證儲存體帳戶和容器：

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**上傳資料檔案**

1. 在 Azure PowerShell 視窗中，設定本機資料夾和目的地資料夾的值：

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	請注意，本機來源檔案資料夾是 **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common**，目的地資料夾是 **WordCount/Input**。來源位置是 .txt 檔案在 HDInsight Emulator 上的位置。目的地是反映在 Azure Blob 容器下的資料夾結構。

3. 執行下列命令來取得來源檔案資料夾中的 .txt 檔案清單：

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

5. 執行下列程式碼片段來複製檔案：

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. 執行下列命令來列出已上傳的檔案：

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**上傳字數統計應用程式**

1. 在 Azure PowerShell 視窗中，設定下列變數：

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	請注意，目的地資料夾是 **WordCount/Apps**，這是將反映在 Azure Blob 容器中的結構。

2. 執行下列命令來複製應用程式：

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. 執行下列命令來列出已上傳的檔案：

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	您應該會看到兩個應用程式都列在這裡。


##在 Azure HDInsight 上執行 MapReduce 工作

本節提供的 Azure PowerShell 指令碼會執行與 MapReduce 工作執行有關的所有工作。工作清單包括：

1. 佈建 HDInsight 叢集

	1. 建立儲存體帳戶做為預設 HDInsight 叢集檔案系統
	2. 建立 Blob 儲存體容器
	3. 建立 HDInsight 叢集

2. 提交 MapReduce 工作

	1. 建立串流 MapReduce 工作定義
	2. 提交 MapReduce 工作
	3. 等待工作完成
	4. 顯示標準錯誤
	5. 顯示標準輸出

3. 刪除叢集

	1. 刪除 HDInsight 叢集
	2. 刪除做為預設 HDInsight 叢集檔案系統的儲存體帳戶


**執行 Azure PowerShell 指令碼**

1. 開啟記事本。
2. 複製並貼上下列程式碼：

		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### Prefix to cluster, Storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data storage account location
		$clusterNodes = 1

		$clusterName = $stringPrefix + "hdicluster"

		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

		Select-AzureSubscription $subscriptionName

		#====== CREATE A STORAGE ACCOUNT ======
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

		#====== CREATE A BLOB STORAGE CONTAINER ======
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

		New-AzureStorageContainer -Name $containerName_Default -Context $destContext

		#====== CREATE AN HDINSIGHT CLUSTER ======
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

		Select-AzureSubscription $subscriptionName
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config

		#====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

		#====== RUN A STREAMING MAPREDUCE JOB ======
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600

		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

		#====== DELETE THE HDINSIGHT CLUSTER ======
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		Remove-AzureHDInsightCluster -Name $clusterName

		#====== DELETE THE STORAGE ACCOUNT ======
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. 設定指令碼中的前四個變數。**$stringPrefix** 變數是用於對 HDInsight 叢集名稱、儲存體帳戶名稱和 Blob 儲存體容器名稱的指定字串加上首碼。因為這些項目的名稱必須為 3 到 24 個字元，請確定您指定的字串和此指令碼使用的名稱，合計不超過名稱的字元限制。**$stringPrefix** 必須全部為小寫。

	**$storageAccountName\_Data** 和 **$containerName\_Data** 變數是您已在先前步驟中建立的儲存體帳戶和容器。因此，您必須提供這些項目的名稱。這些是用來儲存資料檔案和應用程式。**$location** 變數必須符合資料儲存體帳戶位置。

4. 檢閱其餘變數。
5. 儲存指令碼檔案。
6. 開啟 Azure PowerShell。
7. 執行下列命令將執行原則設為 RemoteSigned：

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. 出現提示時，輸入 HDInsight 叢集的使用者名稱和密碼。請確定密碼至少為 10 個字元，且包含一個大寫字母、一個小寫字母、一個數字和一個特殊字元。如果不希望出現認證提示，請參閱[在 Windows PowerShell 中使用密碼、安全字串和認證][powershell-PSCredential]。

如需提交 Hadoop 串流工作的 HDInsight .NET SDK 範例，請參閱[以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]。


##擷取 MapReduce 工作輸出
本節說明如何下載和顯示輸出。如需有關在 Excel 上顯示結果的詳細資訊，請參閱[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][hdinsight-ODBC] 和[使用 Power Query 將 Excel 連接到 HDInsight][hdinsight-power-query]。


**擷取輸出**

1. 開啟 Azure PowerShell 視窗。
2. 設定值，然後執行命令：

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"

3. 執行下列命令來建立 Azure 儲存體內容物件：

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4. 執行下列命令來下載和顯示輸出：

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"



##後續步驟
本教學課程中，您學到如何開發 Hadoop 串流 Java MapReduce 工作、如何在 HDInsight Emulator 上測試應用程式，以及如何撰寫 Azure PowerShell 指令碼來佈建 HDInsight 叢集並於叢集上執行 MapReduce 工作。若要深入了解，請參閱下列文章：

- [開始使用 Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [開始使用 HDInsight Emulator][hdinsight-get-started-emulator]
- [開發 HDInsight 的 Java MapReduce 程式][hdinsight-develop-mapreduce]
- [在 HDInsight 上使用 Azure Blob 儲存體][hdinsight-storage]
- [使用 Azure PowerShell 管理 HDInsight][hdinsight-admin-powershell]
- [將資料上傳到 HDInsight][hdinsight-upload-data]
- [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
- [搭配 HDInsight 使用 Pig][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "MapReduce 字數統計應用程式流程"

<!---HONumber=AcomDC_0218_2016-->