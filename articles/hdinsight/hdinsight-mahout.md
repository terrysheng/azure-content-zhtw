<properties
	pageTitle="使用 Mahout 與以 Windows 為基礎的 HDInsight 產生推薦 | Microsoft Azure"
	description="了解如何搭配以 Windows 為基礎的 HDInsight (Hadoop) 使用 Apache Mahout 機器學習庫來產生電影推薦。"
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
	ms.date="10/16/2015"
	ms.author="larryfr"/>

#透過在 HDInsight 上將 Apache Mahout 與 Hadoop 搭配使用來產生電影推薦

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

了解如何使用搭配 Azure HDInsight 的 [Apache Mahout](http://mahout.apache.org) 機器學習庫產生電影推薦。

> [AZURE.NOTE]此文件中的步驟需要 Windows 用戶端和 Windows 架構的 HDInsight 叢集。如需搭配使用來自 Linux、OS X 或 Unix 用戶端的 Mahout 與 Linux 架構之 HDInsight 叢集的相關資訊，請參閱[透過在 HDInsight 上將 Apache Mahout 與 Linux 架構的 Hadoop 搭配使用來產生電影推薦清單](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>您將了解

Mahout 是 Apache Hadoop 的[機器學習][ml]庫。Mahout 包含可處理資料的演算法，例如篩選、分類和叢集化。在本文中，您將使用推薦引擎，其將根據朋友看過的電影來產生電影推薦。您也將了解如何利用決策森林來進行分類。本文將會告訴您：

* 如何使用 Windows PowerShell 執行 Mahout 工作

* 如何從 Hadoop 命令列執行 Mahout 工作

* 如何在 HDInsight 3.0 和 HDInsight 2.0 叢集上安裝 Mahout

	> [AZURE.NOTE]Mahout 提供 HDInsight 3.1 版的叢集。如果您正在使用舊版的 HDInsight，請參閱[安裝 Mahout](#install) 後再繼續。

##先決條件

* **HDInsight 中 Windows 架構的 Hadoop 叢集**。如需建立此叢集的相關資訊，請參閱[開始在 HDInsight 中使用 Hadoop][getstarted]。

- **具有 Azure PowerShell 的工作站**。請參閱[安裝及使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。


##<a name="recommendations"></a>使用 Windows PowerShell 產生推薦

> [AZURE.NOTE]雖然本節中使用的工作能夠利用 Windows PowerShell 來執行，但 Mahout 隨附的許多類別目前仍無法搭配 Windows PowerShell 運作，而必須使用 Hadoop 命令列來執行。如需無法搭配 Windows PowerShell 使用的類別清單，請參閱[疑難排解](#troubleshooting)一節。
>
> 如需使用 Hadoop 命令列執行 Mahout 工作的範例，請參閱[使用 Hadoop 命令列將資料分類](#classify)。

Mahout 提供的其中一項功能是推薦引擎。這個引擎接受 `userID``itemId` 和 `prefValue` (使用者偏好的項目) 格式的資料。Mahout 接著可以執行共生分析判斷出：_偏好某項目的使用者同時也偏好其他這些項目_。接著 Mahout 會以偏好的類似項目判斷使用者，並以此做出推薦。

以下使用電影做一個很簡單的範例：

* __共生__：Joe、Alice 和 Bob 都喜歡_《星際大戰》_、_《帝國大反擊》_和_《絕地大反攻》_。Mahout 將判斷喜歡上述任何一部電影的使用者，也會喜歡另外兩部電影。

* __共生__：Bob 和 Alice 同時也喜歡_《威脅潛伏》_、_《複製人全面進攻》_和_《西斯大帝的復仇》_。Mahout 將判斷喜歡前三部電影的使用者，也會喜歡這三部電影。

* __相似性推薦__：因為 Joe 喜歡前三部電影，Mahout 會查看具有相似偏好的其他使用者所喜歡但 Joe 還沒看過 (喜歡/評價) 的電影。在此情況下，Mahout 將會推薦_《威脅潛伏》_、_《複製人全面進攻》_和_《西斯大帝的復仇》_。

###載入資料

[GroupLens 研究][movielens] 提供與 Mahout 相容之格式的電影評價資料，相當方便。

1. 下載 [MovieLens 100k][100k] 封存檔，其包含 1000 位使用者針對 1700 部電影的 100,000 個評價。

2. 將封存檔解壓縮。應該有一個 __ml-100k__ 目錄，其中包含開頭為 __u.__ 的許多資料檔。Mahout 將分析的檔案是 __u.data__。此檔案的資料結構是 `userID`、`movieID`、`userRating` 和 `timestamp`。以下是資料範例：


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596


3. 將 __u.data__ 檔案上傳至 HDInsight 叢集上的 __example/data/u.data__。下列命令使用 PowerShell 來上傳檔案。關於其他檔案上傳方式，請參閱[將資料上傳至 HDInsight][upload]。

        # Put your cluster name below
        $clusterName="Your HDInsight cluster name"
        # Put the path to the u.data file below
        $fileToUpload="The path to the u.data file"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/data/u.data" `
            -Container $container `
            -Context $context
    
    這會將 __u.data__ 檔案上傳至叢集之預設儲存體中的 __example/data/u.data__。接著您便可以從 HDInsight 工作使用 __wasb:///example/data/u.data__ URI 來存取此資料。

###執行工作

使用下列 Windows PowerShell 指令碼執行工作，其透過 Mahout 推薦引擎來處理先前上傳的 __u.data__ 檔案。

	# The HDInsight cluster name.
	$clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
            
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	# NOTE: The version number portion of the file path
	# may change in future versions of HDInsight.
	$jarFile = "file:///C:/apps/dist/mahout-0.9.0.2.1.15.1-1234/examples/target/mahout-examples-0.9.0.2.1.15.1-1234-job.jar"
    #
	# If you are using an earlier version of HDInsight,
	# set $jarFile to the jar file you
	# uploaded.
	# For example,
	# $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

	# The arguments for this job
	# * input - the path to the data uploaded to HDInsight
	# * output - the path to store output data
	# * tempDir - the directory for temp files
	$jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
	                "--input", "wasb:///example/data/u.data",
	                "--output", "wasb:///example/out",
	                "--tempDir", "wasb:///example/temp"

	# Create the job definition
	$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
	# Write out any error information
	Write-Host "STDERR"
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE]Mahout 工作不會移除處理工作時所建立的暫存資料。範例工作中所指定的 `--tempDir` 參數會將暫存檔隔離到特定的目錄。

Mahout 工作不會將輸出傳回 STDOUT。相反地，其會將該輸出儲存在指定的輸出目錄 __part-r-00000__ 中。指令碼會下載這個檔案到您工作站上目前目錄的 __output.txt__ 檔。

以下是此檔案的內容範例：

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

第一欄是`userID`。'[' 和 ']' 中包含的值是 `movieId`:`recommendationScore`。

###檢視輸出

雖然產生的輸出可以在應用程式中使用，但非常難以讓人判讀。先前解壓縮至 __ml-100k__ 資料夾的其他部分檔案，可用來將 `movieId` 解析成電影名稱，這就是下列 PowerShell 指令碼所執行的動作：

	<#
	.SYNOPSIS
	    Displays recommendations for movies.
	.DESCRIPTION
	    Displays recommendations generated by Mahout
	    with HDInsight example in a human readable format.
	.EXAMPLE
	    .\Show-Recommendation -userId 4
	        -userDataFile "u.data"
	        -movieFile "u.item"
	        -recommendationFile "output.txt"
	#>

	[CmdletBinding(SupportsShouldProcess = $true)]
	param(
	    #The user ID
	    [Parameter(Mandatory = $true)]
	    [String]$userId,

	    [Parameter(Mandatory = $true)]
	    [String]$userDataFile,

	    [Parameter(Mandatory = $true)]
	    [String]$movieFile,

	    [Parameter(Mandatory = $true)]
	    [String]$recommendationFile
	)
	# Read movie ID & description into hash table
	Write-Host "Reading movies descriptions" -ForegroundColor Green
	$movieById = @{}
	foreach($line in Get-Content $movieFile)
	{
	    $tokens = $line.Split("|")
	    $movieById[$tokens[0]] = $tokens[1]
	}
	# Load movies user has already seen (rated)
	# into a hash table
	Write-Host "Reading rated movies" -ForegroundColor Green
	$ratedMovieIds = @{}
	foreach($line in Get-Content $userDataFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        # Resolve the ID to the movie name
	        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
	    }
	}
	# Read recommendations generated by Mahout
	Write-Host "Reading recommendations" -ForegroundColor Green
	$recommendations = @{}
	foreach($line in get-content $recommendationFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        #Trim leading/treailing [] and split at ,
	        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
	        foreach($movieIdAndScore in $movieIdAndScores)
	        {
	            #Split at : and store title and score in a hash table
	            $idAndScore = $movieIdAndScore.Split(":")
	            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
	        }
	        break
	    }
	}

	Write-Host "Rated movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	               @{Expression={$_.Value};Label="Rating"}
	$ratedMovieIds | format-table $ratedFormat
	Write-Host "---------------------------" -ForegroundColor Green

	write-host "Recommended movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	                        @{Expression={$_.Value};Label="Score"}
	$recommendations | format-table $recommendationFormat

若要使用此指令碼，您必須擁有先前所擷取的 __ml-100k__ 資料夾。以下是執行此指令碼的範例：

	PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

輸出應該類似下列所示：

	Reading movies descriptions
	Reading rated movies
	Reading recommendations
	Rated movies
	---------------------------
	Movie                                    Rating
	-----                                    ------
	Devil's Own, The (1997)                  1
	Alien: Resurrection (1997)               3
	187 (1997)                               2
	(lines ommitted)

	---------------------------
	Recommended movies
	---------------------------

	Movie                                    Score
	-----                                    -----
	Good Will Hunting (1997)                 4.6504064
	Swingers (1996)                          4.6862745
	Wings of the Dove, The (1997)            4.6666665
	People vs. Larry Flynt, The (1996)       4.834559
	Everyone Says I Love You (1996)          4.707071
	Secrets & Lies (1996)                    4.818182
	That Thing You Do! (1996)                4.75
	Grosse Pointe Blank (1997)               4.8235292
	Donnie Brasco (1997)                     4.6792455
	Lone Star (1996)                         4.7099237  

##<a name="classify"></a>使用 Hadoop 命令列將資料分類

Mahout 可用的其中一個分類方法是建置[隨機森林][forest]。這是多重步驟的程序，其包含使用訓練資料來產生決策樹，再用以將資料分類。此程序使用 Mahout 提供的 __org.apache.mahout.classifier.df.tools.Describe__ 類別。目前必須使用 Hadoop 命令列來執行。

###載入資料

1. 從 [NSL-KDD 資料集](http://nsl.cs.unb.ca/NSL-KDD/)下載下列檔案。

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff)：訓練檔案

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff)：測試資料

2. 開啟每一個檔案並移除頂端以 '@' 開頭的各行，然後儲存檔案。如果未移除，則在 Mahout 中使用此資料時會收到錯誤訊息。

2. 將檔案上傳至 __example/data__。您可以使用下列指令碼來達成目的：使用 HDInsight 叢集的名稱取代 __CLUSTERNAME__。使用要上傳檔案的名稱取代 FILENAME。

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###執行工作

1. 這項工作需要 Hadoop 命令列。依照[使用 RDP 連線到 HDInsight 叢集](hdinsight-administer-use-management-portal.md#rdp)中的指示，為 HDInsight 叢集啟用遠端桌面，然後進行連線。

3. 連線之後，使用 [Hadoop 命令列] 圖示開啟 Hadoop 命令列：

	![hadoop cli][hadoopcli]

3. 使用下列命令，並利用 Mahout 產生檔案描述元 (__KDDTrain+.info__)。

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	`N 3 C 2 N C 4 N C 8 N 2 C 19 N L` 描述檔案中的資料屬性。例如，L 表示標籤。

4. 使用下列命令建置決策樹的森林：

		hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    此作業的輸出將儲存在 __nsl-forest__ 目錄，其位於您 HDInsight 叢集的儲存體中，位於：__wasb://user/&lt;username>/nsl-forest/nsl-forest.seq。&lt;username> 是您遠端桌面工作階段的使用者名稱。此檔案無法讓人判讀。

5. 將 __KDDTest+.arff__ 資料集分類來測試森林。使用下列命令：

    	hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    此命令會傳回分類流程的摘要資訊，類似下列所示：

	    14/07/02 14:29:28 INFO mapreduce.TestForest:

	    =======================================================
	    Summary
	    -------------------------------------------------------
	    Correctly Classified Instances          :      17560       77.8921%
	    Incorrectly Classified Instances        :       4984       22.1079%
	    Total Classified Instances              :      22544

	    =======================================================
	    Confusion Matrix
	    -------------------------------------------------------
	    a       b       <--Classified as
	    9437    274      |  9711        a     = normal
	    4710    8123     |  12833       b     = anomaly

	    =======================================================
	    Statistics
	    -------------------------------------------------------
	    Kappa                                       0.5728
	    Accuracy                                   77.8921%
	    Reliability                                53.4921%
	    Reliability (standard deviation)            0.4933

  此工作也會產生一個位於：__wasb:///example/data/predictions/KDDTest+.arff.out__ 的檔案。不過，此檔案無法讓人判讀。

> [AZURE.NOTE]Mahout 工作不會覆寫檔案。如果您想要重新執行這些工作，則必須刪除先前的工作所建立的檔案。

##<a name="troubleshooting"></a>疑難排解

###<a name="install"></a>安裝 Mahout

Mahout 安裝於 HDInsight 3.1 叢集上，且可使用下列步驟來手動安裝到 HDInsight 3.0 或 HDInsight 2.1 叢集上：

1. 使用的 Mahout 版本視叢集的 HDInsight 版本而定。您可以使用下列 [Azure PowerShell][aps] 命令來找出叢集版本：

    	PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select version


  * __針對 HDInsight 2.1__，您可以下載包含 [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar) 的 Java 封存 (JAR) 檔案。

  * __針對 HDInsight 3.0__，您必須[從原始檔建置 Mahout][build]，並指定 HDInsight 所提供的 Hadoop 版本。安裝建置頁面上列出的必要條件、下載原始檔，然後使用下列命令建立 Mahout jar 檔案：

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

    	> [AZURE.NOTE] 當 Mahout 1.0 發行時，您應能搭配 HDInsight 3.0 使用預先建置的封裝。

2. 將 jar 檔案上傳至叢集預設儲存庫中的 __example/jars__。使用您 HDInsight 叢集的名稱來取代下列程式碼中的 CLUSTERNAME，並且使用指向 __mahout-coure-0.9-job.jar__ 檔案的路徑來取代 FILENAME。

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###無法覆寫檔案

Mahout 工作不會清除在處理期間所建立的暫存檔。此外，工作也不會覆寫現有的輸出檔。

為了避免執行 Mahout 工作時發生錯誤，請在每次執行之前刪除暫存檔和輸出檔，或使用唯一的暫存和輸出目錄名稱。

###找不到 JAR 檔案

HDInsight 3.1 叢集包含 Mahout。路徑和檔案名稱包含叢集上安裝之 Mahout 的版本號碼。本教學課程中的 Windows PowerShell 範例指令碼使用在 2014 年 7 月時有效的路徑，但版本號碼將隨著未來 HDInsight 更新而有所變更。若要判斷您叢集的 Mahout JAR 檔案的目前路徑，請使用下列 Windows PowerShell 命令，然後將指令碼修改為參考傳回的檔案路徑：

	Use-AzureHDInsightCluster -Name $clusterName
	$jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target*-job.jar'

###<a name="nopowershell"></a>不適用於 Windows PowerShell 的類別

如果從 Windows PowerShell 中使用的 Mahout 工作利用到下列類別，則會傳回各種錯誤訊息：

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

若要執行用到這些類別的工作，請連接到 HDInsight 叢集，然後使用 Hadoop 命令列執行工作。相關範例請參閱[使用 Hadoop 命令列將資料分類](#classify)。

##後續步驟

您現在已了解如何使用 Mahout，請繼續探索在 HDInsight 上使用資料的其他方法：

* [搭配 HDInsight 使用 Hive](../hadoop-use-hive.md)
* [搭配 HDInsight 使用 Pig](../hadoop-use-pig.md)
* [搭配 HDInsight 使用 MapReduce](../hadoop-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: ../hdinsight-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!----HONumber=Oct15_HO4-->