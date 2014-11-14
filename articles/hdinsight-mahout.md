<properties title="使用 Mahout 來產生電影推薦" pageTitle="搭配 Microsoft Azure HDInsight (Hadoop) 使用 Mahout 來產生電影推薦" description="了解如何搭配 HDInsight (Hadoop) 使用 Apache Mahout 機器學習庫來產生電影推薦" metaKeywords="Azure hdinsight mahout, Azure hdinsight machine learning, azure hadoop mahout, azure hadoop machine learning" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# 搭配 HDInsight (Hadoop) 使用 Apache Mahout 來產生電影推薦

了解如何搭配 Microsoft Azure HDInsight (Hadoop) 使用 [Apache Mahout][Apache Mahout] 機器學習庫來產生電影推薦。

> [WACOM.NOTE] 您必須有 HDInsight 叢集，才能使用本文的資訊。如需有關建立此叢集的資訊，請參閱[開始使用 Azure HDInsight][開始使用 Azure HDInsight]。
>
> Mahout 隨附於 HDInsight 3.1 叢集。如果您使用舊版的 HDInsight，請參閱[安裝 Mahout][安裝 Mahout] 再繼續。

## <a name="learn"></a>學習目標

Mahout 是 Apache Hadoop 的[機器學習][機器學習]庫。Mahout 包含可處理資料的演算法，例如篩選、分類和叢集化。在本文中，您將使用推薦引擎，根據朋友看過的電影來產生電影推薦。您也將了解如何以決策樹來進行分類。本文將會教您：

-   如何從 PowerShell 執行 Mahout 工作

-   如何從 Hadoop 命令列執行 Mahout 工作

-   如何在 HDInsight 2.0 和 3.0 叢集上安裝 Mahout

## 本文內容

-   [使用 PowerShell 產生推薦][使用 PowerShell 產生推薦]
-   [使用 Hadoop 命令列將資料分類][使用 Hadoop 命令列將資料分類]
-   [疑難排解][疑難排解]

## <a name="recommendations"></a>使用 PowerShell 產生推薦

> [WACOM.NOTE] 本節中使用的工作可以在 PowerShell 中執行，但 Mahout 隨附的許多類別目前無法在 PowerShell 中運作，而必須使用 Hadoop 命令列來執行。關於無法在 PowerShell 中運作的類別清單，請參閱[疑難排解][疑難排解]一節。
>
> 如需有關使用 Hadoop 命令列來執行 Mahout 工作的範例，請參閱[使用 Hadoop 命令列將資料分類][使用 Hadoop 命令列將資料分類]。

Mahout 提供的其中一項功能是推薦引擎。它接受 `userID`、`itemId`、`prefValue` (使用者的影片偏好) 格式的資料。Mahout 接著可以執行共現性分析，以決定「喜歡某部影片的使用者也喜歡其他這些影片」。Mahout 會找出具有相似影片偏好的使用者，然後做出推薦。

以下是一個使用電影的極簡單例子：

-   **共現性** - Joe、Alice 和 Bob 都喜歡《星際大戰》、《帝國大反擊》和《絕地大反攻》。Mahout 會判斷喜歡上述任何一部電影的使用者，也喜歡另外兩部電影。

-   **共現性** - Bob 和 Alice 也喜歡《威脅潛伏》、《複製人全面進攻》和《西斯大帝的復仇》。Mahout 會判斷喜歡前三部電影的使用者也喜歡這三部電影。

-   **相似性推薦** - 因為 Joe 喜歡前三部電影，Mahout 會查看具有相似偏好的其他人所喜歡但 Joe 還沒看過 (喜歡/考慮) 的電影。在此情況下，Mahout 會推薦《威脅潛伏》、《複製人全面進攻》和《西斯大帝的復仇》。

### 載入資料

GroupLens Research 提供 Mahout 相容格式的[電影評價資料][電影評價資料] (英文)，相當方便。

1.  下載 [MovieLens 100k][MovieLens 100k] 封存檔，其中包含 1000 位使用者對於 1700 部電影的 100,000 個評價。

2.  將封存檔解壓縮。應該有一個 **ml-100k** 目錄，其中包含開頭為 **u.** 的許多資料檔。Mahout 將分析的檔案是 **u.data**。此檔案的資料結構是 `userID`、`movieID`、`userRating` 和 `timestamp`。以下是資料範例。

        196 242 3   881250949
        186 302 3   891717742
        22  377 1   878887116
        244 51  2   880606923
        166 346 1   886397596

3.  將 **u.data** 檔案上傳至 HDInsight 叢集上的 **example/data/u.data**。如果您有 [Azure PowerShell][Azure PowerShell]，則可以使用 [HDInsight-Tools][HDInsight-Tools] PowerShell 模組來上傳檔案。關於其他檔案上傳方式，請參閱[將資料上傳至 HDInsight][將資料上傳至 HDInsight]。以下示範使用 `Add-HDInsightFile` 來上傳檔案

        PS C:PS C:\> Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"gt; Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"

    這會將 **u.data** 檔案上傳至叢集的預設儲存體中的 **example/data/u.data**。然後，我們就可以從 HDInsight 工作中，使用 **wasb:///example/data/u.data** URI 來存取此資料。

### 執行工作

使用下列 PowerShell 指令碼執行工作，透過 Mahout 推薦引擎來處理先前上傳的 **u.data** 檔案。

    # The HDInsight cluster name.
    $clusterName = "the cluster name"

    # The location of the Mahout jar file.
    $jarFile = "file:///c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar"
    # NOTE: The version number portion of the file path
    # may change in future versions of HDInsight.
    # Use the following to find the location and name
    # of the Mahout jar on HDInsight 3.1, and modify
    # the $jarFile= line to point to it.
    #
    # Use-AzureHDInsightCluster -Name $clusterName
    # $jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'
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
    $jobArguments = "-s", "SIMILARITY_COOCCURRENCE",
                    "--input", "wasb:///example/data/u.data",
                    "--output", "wasb:///example/out",
                    "--tempDir", "wasb:///temp/mahout"

    # Create the job definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job

    # Write out any error information
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError

> [WACOM.NOTE] Mahout 工作不會將處理工作時所建立的暫存資料移除。這就是為何範例工作中指定 `--tempDir` 參數的原因 - 將暫存檔隔離到特定路徑中以方便刪除。
>
> 若要移除這些檔案，您可以使用[將資料上傳至 HDInsight][將資料上傳至 HDInsight] 中所提及的其中一個公用程式。或使用 [HDInsight-Tools][HDInsight-Tools] PowerShell 指令碼中的 `Remove-HDInsightFile` 函數。
>
> 如果您未移除暫存檔或輸出檔，則重新執行工作時會出現錯誤。

Mahout 工作不會將輸出傳回到 STDOUT，而是在指定的輸出目錄中儲存為 **part-r-00000**。若要下載和檢視此檔案，請使用 [HDInsight-Tools][HDInsight-Tools] PowerShell 模組中的 `Get-HDInsightFile` 函數。

以下是此檔案的內容範例：

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

第一欄是 `userID`。'[' 和 ']' 包含的值是 `movieId`:`recommendationScore`。

### 檢視輸出

雖然產生的輸出可以在應用程式中使用，但非常難以讓人判讀。先前解壓縮至 **ml-100k** 資料夾的其他一些檔案，可以將 `movieId` 解析成電影名稱。**ml-100k** 資料夾中有一個 Python 指令碼可這樣做 (**show\_recommendations.py**)，但您也可以使用 PowerShell 指令碼。

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

若要使用此指令碼，您必須有先前已解壓縮的 **ml-100k** 資料夾，以及 Mahout 工作產生的 **part-r-00000** 輸出檔的本機複本。以下是執行此指令碼的例子。

    PS C:PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txtgt; show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

> [WACOM.NOTE] 範例 Python 指令碼 **show\_recommendations.py** 接受相同的參數。

輸出應該如下所示：

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

## <a name="classify"></a>使用 Hadoop 命令列將資料分類

Mahout 可用的其中一個分類方法是建置[隨機森林][隨機森林]。這是多步驟的程序，包含使用訓練資料來產生決策樹，再用來將資料分類。此程序使用 Mahout 提供的 **org.apache.mahout.classifier.df.tools.Describe** 類別，且目前必須使用 Hadoop 命令列來執行。

### 載入資料

Mahout 目前的實作與 University of California, Irvine (UCI) 儲存機制格式相容 [這有何重要、這是什麼格式]

1.  從 <http://nsl.cs.unb.ca/NSL-KDD/> 下載下列檔案。

-   [KDDTrain+.ARFF][KDDTrain+.ARFF] - 訓練檔案

-   [KDDTest+.ARFF][KDDTest+.ARFF] - 測試資料

1.  開啟每一個檔案並移除頂端以 '@' 開頭的各行，然後儲存檔案。如果未移除，則在 Mahout 中使用此資料時會發生錯誤。

2.  將檔案上傳至 **example/data**。您可以使用 [HDInsight-Tools][HDInsight-Tools] PowerShell 模組中的 `Add-HDInsightFile` 函數來完成此動作。

### 執行工作

1.  因為此工作需要 Hadoop 命令列，您必須透過 [Azure 管理入口網站][Azure 管理入口網站]啟用遠端桌面。在入口網站中，選取 HDInsight 叢集，然後在 [設定] 頁面底部選取 [啟用遠端]。

    ![enable remote][enable remote]

    出現提示時，輸入要用於遠端工作階段的使用者名稱和密碼。

2.  啟用遠端存取之後，選取 [連接] 開始連線。這樣會下載 **.rdp** 檔案，此檔案可用來啟動遠端桌面工作階段。

    ![connect][connect]

3.  連線之後，使用 [Hadoop 命令列] 圖示開啟 Hadoop 命令列。

    ![hadoop cli][hadoop cli]

4.  使用下列命令，利用 Mahout 產生檔案描述元 (**KDDTrain+.info**)。

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` 描述檔案中的資料屬性。一個數值屬性、2 個類別等。L 表示標籤。

5.  使用下列命令建置決策樹的樹系。

        hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    此作業的輸出會儲存在 **nsl-forest** 目錄中，此目錄位於 HDInsight 叢集的儲存體中，網址是 \_\_wasb://user/\<username\>/nsl-forest/nsl-forest.seq。\<username\> 代表用於遠端桌面工作階段的使用者名稱。此檔案無法讓人判讀。

6.  使用下列命令，將 **KDDTest+.arff** 資料集分類來測試樹系。

        hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    此命令會傳回分類過程的摘要資訊，如下所示。

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

此工作也會產生一個位於 **wasb:///example/data/predictions/KDDTest+.arff.out** 的檔案，但此檔案無法讓人判讀。

> [WACOM.NOTE] Mahout 工作不會覆寫檔案。如果您想要重新執行這些工作，則必須刪除先前的工作所建立的檔案。

## <a name="troubleshooting"></a>疑難排解

### <a name="install"></a>安裝 Mahout

Mahout 安裝在 HDInsight 3.1 叢集上，但可使用下列步驟來手動安裝在 3.0 或 2.1 叢集。

1.  使用的 Mahout 版本視叢集的 HDInsight 版本而定。您可以在 [Azure PowerShell][Azure PowerShell] 中使用下列命令來找出叢集版本：

        PS C:PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select versiongt; Get-AzureHDInsightCluster -Name YourClusterName | Select version

-   **若為 HDInsight 2.1**，您可以下載含有 [Mahout 0.9][Mahout 0.9] 的 jar 檔案。

-   **若為 HDInsight 3.0**，您必須[從原始檔建置 Mahout][從原始檔建置 Mahout] (英文)，並指定 HDInsight 所提供的 Hadoop 版本。安裝建置頁面列出的必要元件，下載原始檔，然後使用下列命令建立 Mahout jar 檔案。

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        Once the build completes, the jar file will be created at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [WACOM.NOTE] Once Mahout 1.0 is released, you should be able to use the pre-built packages with HDInsight 3.0.

1.  將 jar 檔案上傳至叢集預設儲存庫中的 **example/jars**。下列範例使用 [send-hdinsight][sendhdinsight] 指令碼來上傳檔案。

        PS C:PS C:\> .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"gt; .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"

### 無法覆寫檔案

Mahout 工作不會清除在處理期間所建立的暫存檔。此外，工作也不會覆寫現有的輸出檔。

為了避免執行 Mahout 工作時發生錯誤，請在每次執行之前刪除暫存檔和輸出檔，或使用唯一的暫存和輸出目錄名稱。

### 找不到 jar 檔案

雖然 HDInsight 3.1 包含 Mahout，但路徑和檔案中是叢集上安裝之 Mahout 的版本號碼。本教學課程中的範例 PowerShell 指令碼使用 2014 年 7 月起生效的路徑，但版本號碼會隨著未來 HDInsight 更新而變更。若要查明您叢集的 Mahout jar 檔案的目前路徑，請使用下列 PowerShell 命令，然後將指令碼修改為參考傳回的檔案路徑。

    Use-AzureHDInsightCluster -Name $clusterName
    $jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

### <a name="nopowershell"></a>不適用於 PowerShell 的類別

如果從 PowerShell 中使用的 Mahout 工作用到下列類別，則會傳回各種錯誤。

-   org.apache.mahout.utils.clustering.ClusterDumper
-   org.apache.mahout.utils.SequenceFileDumper
-   org.apache.mahout.utils.vectors.lucene.Driver
-   org.apache.mahout.utils.vectors.arff.Driver
-   org.apache.mahout.text.WikipediaToSequenceFile
-   org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
-   org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
-   org.apache.mahout.classifier.sgd.TrainLogistic
-   org.apache.mahout.classifier.sgd.RunLogistic
-   org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
-   org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
-   org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
-   org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
-   org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
-   org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
-   org.apache.mahout.classifier.df.tools.Describe

若要執行用到這些類別的工作，請連接至 HDInsight 叢集，然後使用 Hadoop 命令列執行工作。相關範例請參閱[使用 Hadoop 命令列將資料分類][使用 Hadoop 命令列將資料分類]。

  [Apache Mahout]: http://mahout.apache.org
  [開始使用 Azure HDInsight]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-get-started/
  [安裝 Mahout]: #install
  [機器學習]: http://en.wikipedia.org/wiki/Machine_learning
  [使用 PowerShell 產生推薦]: #recommendations
  [使用 Hadoop 命令列將資料分類]: #classify
  [疑難排解]: #troubleshooting
  [電影評價資料]: http://grouplens.org/datasets/movielens/
  [MovieLens 100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
  [Azure PowerShell]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/
  [HDInsight-Tools]: https://github.com/Blackmist/hdinsight-tools
  [將資料上傳至 HDInsight]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-upload-data/
  [隨機森林]: http://en.wikipedia.org/wiki/Random_forest
  [KDDTrain+.ARFF]: http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff
  [KDDTest+.ARFF]: http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [enable remote]: ./media/hdinsight-mahout/enableremote.png
  [connect]: ./media/hdinsight-mahout/connect.png
  [hadoop cli]: ./media/hdinsight-mahout/hadoopcli.png
  [Mahout 0.9]: http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar
  [從原始檔建置 Mahout]: http://mahout.apache.org/developers/buildingmahout.html
