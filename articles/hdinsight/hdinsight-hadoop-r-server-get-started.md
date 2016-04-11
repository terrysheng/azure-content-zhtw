<properties
   pageTitle="開始使用 HDInsight 中的 R Server | Azure"
   description="了解如何在包含 R Server 的 HDInsight (Hadoop) 叢集上建立 Apache Spark，並在叢集上送出 R 指令碼。"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/25/2016"
   ms.author="jeffstok"/>

# 開始使用 HDInsight 中的 R Server

Hdinsight 的進階層供應項目包括隨附於 HDInsight 叢集的 R Server。這可讓 R 指令碼使用 MapReduce 和 Spark 來執行分散式計算。在本文中，您將了解如何在 HDInsight 上建立新的 R Server，然後執行 R 指令碼，以使用 Spark 進行分散式 R 計算。

![這份文件的工作流程圖表](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## 必要條件

* __Azure 訂用帳戶__：開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)以取得詳細資訊。

* __安全殼層 (SSH) 用戶端__：SSH 用戶端可用來從遠端連線至 HDInsight 叢集，並直接在叢集上執行命令。Linux、Unix 和 OS X 系統可透過 `ssh` 命令提供 SSH 用戶端。如果是 Windows 系統，我們建議使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

    * __SSH 金鑰 (選擇性)__：您可以使用密碼或公開金鑰，保護用來連線到叢集的 SSH 帳戶。使用密碼比較簡單，因為您不需要建立公開/私密金鑰組即可開始使用。不過，使用金鑰更加安全。
    
        本文中的步驟是假設您使用密碼來進行作業。如需如何建立 SSH 金鑰並與 HDInsight 搭配使用的資訊，請參閱下文：
        
        * [從 Linux、Unix 或 OS X 用戶端搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [從 Windows 用戶端搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

## 建立叢集

> [AZURE.NOTE] 本文中的步驟會使用基本設定資訊在 HDInsight 上建立 R Server。如需其他叢集設定設定 (例如，新增其他儲存體帳戶、使用 Azure 虛擬網路或建立 Hive 中繼存放區) 的資訊，請參閱[建立以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 依序選取 [新增]、[資料 + 分析] 和 [HDInsight]。

    ![建立新叢集的影像](./media/hdinsight-getting-started-with-r/newcluster.png)

3. 在 [叢集名稱] 欄位中，輸入叢集的名稱。如果您有多個 Azure 訂用帳戶，請使用 [訂用帳戶] 項目選取要使用的訂用帳戶。

    ![叢集名稱和訂用帳戶選取項目](./media/hdinsight-getting-started-with-r/clustername.png)

4. 選取 [選取叢集類型]。在 [叢集類型] 刀鋒視窗中，選取下列選項︰

    * __叢集類型__：Spark 上的 R Server
    
    * __叢集層__︰進階

    其他選項保留為預設值，然後使用 [選取] 按鈕以儲存叢集類型。
    
    ![叢集類型刀鋒視窗螢幕擷取畫面](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] 您也可以依序選取叢集類型和 [進階]，將 R Server 加入其他 HDInsight 叢集類型 (例如 Hadoop 或 HBase) 中。

5. 選取 [資源群組]，查看現有資源群組清單，然後選取其中一個項目以在其中建立叢集。或者，您可以選取 [建立新群組]，然後輸入新資源群組的名稱。出現綠色核取記號即表示可以使用這個新群組名稱。

    > [AZURE.NOTE] 如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。
    
    使用 [選取] 按鈕，儲存資源群組。

6. 選取 [認證]，然後輸入 [叢集登入使用者名稱] 和 [叢集登入密碼]。

    輸入 [SSH 使用者名稱]，選取 [密碼]，然後輸入 [SSH 密碼] 以設定 SSH 帳戶。透過安全殼層 (SSH) 用戶端從遠端連線到叢集時，會使用 SSH。
    
    使用 [選取] 按鈕來儲存認證。
    
    ![認證刀鋒視窗](./media/hdinsight-getting-started-with-r/clustercredentials.png)

7. 選取 [資料來源] 以選取叢集的資料來源。若要選取現有的儲存體帳戶，您可以選取 [選取儲存體帳戶]，然後選取帳戶，或使用 [選取儲存體帳戶] 區段中的 [新增] 連結，以建立新的帳戶。

    如果您選取 [新增]，則必須輸入新儲存體帳戶的名稱。如果系統接受該名稱，就會出現綠色核取記號。

    叢集名稱的預設值為 [預設容器]。請不要更動此值。
    
    選取 [位置] 以選取要在其中建立儲存體帳戶的區域。
    
    > [AZURE.IMPORTANT] 選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。叢集和預設資料來源必須位於相同區域中。

    使用 [選取] 以儲存資料來源設定。
    
    ![資料來源刀鋒視窗](./media/hdinsight-getting-started-with-r/datastore.png)

8. 選取 [節點定價層] 會顯示將針對此叢集建立之節點的相關資訊。除非您確定需要更大的叢集，否則請保留背景工作節點數目的預設值 `4`。該叢集的預估成本將會顯示在此刀鋒視窗內。

    ![節點定價層刀鋒視窗](./media/hdinsight-getting-started-with-r/pricingtier.png)

    使用 [選取] 按鈕以儲存節點定價設定。
    
9. 在 [新的 HDInsight 叢集] 刀鋒視窗中，確認已選取 [釘選到開始面板]，然後選取 [建立]。這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。該圖示會顯示叢集正在建立中，並會在叢集建立完成時變更為 HDInsight 圖示。

    | 建立時 | 建立完成 |
    | ------------------ | --------------------- |
    | ![「開始面板」上的建立指示器](./media/hdinsight-getting-started-with-r/provisioning.png) | ![已建立叢集磚](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] 建立叢集需要一些時間，通常約 15 分鐘左右。請使用「開始面板」上的磚，或頁面左邊的 [通知] 項目來以檢查建立進度。

## 連線到 R Server 邊緣節點

使用 SSH 連線到 HDInsight 叢集的 R Server 邊緣節點：

    ssh USERNAME@rserver.CLUSTERNAME.ssh.azurehdinsight.net
    
> [AZURE.NOTE] 您也可以依序選取您的叢集、[所有設定]、[應用程式] 和 [RServer]，在 Azure 入口網站中找到 `RServer.CLUSTERNAME.ssh.azurehdinsight.net` 位址。如此即可顯示邊緣節點的 SSH 端點資訊。
>
> ![邊緣節點 SSH 端點的影像](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
如果您已經使用密碼保護您 SSH 使用者帳戶的安全，系統會提示您輸入密碼。如果您使用的是公開金鑰，您可能必須使用 `-i` 參數來指定對應的私密金鑰。例如，`ssh -i ~/.ssh/id_rsa USERNAME@RServer.CLUSTERNAME.ssh.azurehdinsight.net`。
    
如需搭配使用 SSH 與以 Linux 為基礎的 HDInsight 的詳細資訊，請參閱下列文章：

* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

連線之後，您將看見類似以下的提示：

    username@ed00-myrser:~$

## 使用 R 主控台

1. 在 SSH 工作階段中，使用以下命令啟動 R 主控台：

        R
    
    您將看到類似以下的輸出：
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.

        >

2. 您可以透過 `>` 提示，輸入 R 程式碼。R Server 包含可讓您輕鬆與 Hadoop 互動並執行分散式計算的封裝。例如，若要檢視 HDInsight 叢集的預設檔案系統根目錄，可使用下列命令：

        rxHadoopListFiles("/")
    
    您也可以使用 WASB 樣式定址。
    
        rxHadoopListFiles("wasb:///")

##使用計算內容

計算內容可讓您控制要在邊緣節點上本機執行計算，或將計算分散到 HDInsight 叢集的節點中。
        
1. 在 R 主控台中，使用下列項目將範例資料載入 HDInsight 的預設儲存體。

        # Set the NameNode and port for the cluster
        myNameNode <- "default"
        myPort <- 0
        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # Source for the data to load
        source <- system.file("SampleData/AirlineDemoSmall.csv", package="RevoScaleR")
        # Directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirlineDemoSmall") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, inputDir)

2. 接下來，我們要建立幾個因數，並定義資料來源，以便使用資料。

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, 
                                   port=myPort)
        # Create Factors for the days of the week
        colInfo <- list(DayOfWeek = list(type = "factor",
             levels = c("Monday", 
                        "Tuesday", 
                        "Wednesday", 
                        "Thursday", 
                        "Friday", 
                        "Saturday", 
                        "Sunday")))
        # Define the data source
        airDS <- RxTextData(file = inputDir, 
                            missingValueString = "M",
                            colInfo  = colInfo, 
                            fileSystem = hdfsFS)

3. 現在，我們來使用本機計算內容執行資料的線性迴歸。

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a linear regression
        system.time(
            modelLocal <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek,
                                   data = airDS)
        )
        # Display a summary 
        summary(modelLocal) 

    您應該會看到結尾類似以下幾行的輸出：
    
        Residual standard error: 40.39 on 582620 degrees of freedom
        Multiple R-squared: 0.01465
        Adjusted R-squared: 0.01464
        F-statistic:  1238 on 7 and 582620 DF,  p-value: < 2.2e-16
        Condition number: 10.6542

4. 接著，我們使用 Spark 內容來執行相同的線性迴歸。Spark 內容會將處理作業分散到 HDInsight 叢集的所有背景工作節點中。

        # Define the Spark compute context 
        mySparkCluster <- RxSpark(consoleOutput=TRUE) 
        # Set the compute context 
        rxSetComputeContext(mySparkCluster) 
        # Run a linear regression 
        system.time(  
            modelSpark <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS) 
        )
        # Display a summary
        summary(modelSpark)

    由於我們設定為 `consoleOutput=TRUE`，因此 Spark 處理的輸出會寫入主控台。
    
    > [AZURE.NOTE] 您也可以使用 MapReduce，將計算分散到叢集節點。如需計算內容的詳細資訊，請參閱[適用於 HDInsight 進階層 R Server 的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)。

##將 R 程式碼分散到多個節點

使用 R Server 時，您可以輕鬆採用現有的 R 程式碼並利用 `rxExec` 以跨多個叢集節點加以執行。執行參數掃掠或模擬時，這非常有用。以下是使用 `rxExec` 的範例：

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
如果您仍在使用 Spark 或 MapReduce 內容，這麼做會針對執行程式碼 (`Sys.info()["nodename"]`) 的背景工作節點傳回 nodename 值。比方說，若是在四個節點叢集上，您可能會收到類似下列的輸出：

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"

##安裝 R 封裝

如果您想要在邊緣節點上安裝其他 R 封裝，可以在透過 SSH 連線至邊緣節點時，直接從 R 主控台內使用 `install.packages()`。不過，如果您需要在叢集的背景工作節點上安裝 R 封裝，就必須使用指令碼動作。

指令碼動作是一種 Bash 指令碼，可用來變更 HDInsight 叢集的設定或安裝其他軟體。在此案例中，則是用來安裝其他 R 封裝。若要使用指令碼動作安裝其他封裝，請使用下列步驟：

> [AZURE.IMPORTANT] 僅有在建立叢集之後，才可以使用指令碼動作來安裝其他 R 封裝。您不應在叢集建立期間使用，因為指令碼是相依於已完整安裝與設定的 R Server。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取您 HDInsight 叢集上的 R Server。

2. 在叢集刀鋒視窗中，依序選取 [所有設定] 和 [指令碼動作]。在 [指令碼動作] 刀鋒視窗中，選取 [送出新的] 以送出新的指令碼動作。

    ![指令碼動作刀鋒視窗的影像](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. 在 [送出指令碼動作] 刀鋒視窗中，提供下列資訊。

    * __名稱__︰用來識別此指令碼的易記名稱
    * __Bash 指令碼 URI__：http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh
    * __前端__︰這應該是__未核取__狀態
    * __背景工作角色__︰這應該是__核取__狀態
    * __Zookeeper__︰這應該是__未核取__狀態
    * __參數__︰要安裝的 R 封裝。例如，`bitops stringr arules`
    * __保存此指令碼...__︰這應該是__核取__狀態
    
    > [AZURE.IMPORTANT] 如果您安裝的 R 封裝需要加入系統程式庫，則必須下載此處所使用的基底指令碼，並加入安裝系統程式庫的步驟。接下來，您必須將修改過的指令碼上傳至 Azure 儲存體中的公用 Blob 容器，並使用修改過的指令碼來安裝封裝。
    >
    >如需開發指令碼動作的詳細資訊，請參閱[指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。
    
    ![新增指令碼動作](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. 按一下 [建立]，執行指令碼。指令碼完成之後，即可在所有的背景工作節點上使用 R 封裝。
    
## 後續步驟

現在您已了解如何建立包含 R Server 的新 HDInsight 叢集，以及透過 SSH 工作階段使用 R 主控台的基本概念，請參閱下列內容以探索在 HDInsight 上使用 R Server 的其他方式。

- [將 RStudio 伺服器加入 HDInsight 進階層中](hdinsight-hadoop-r-server-install-r-studio.md)

- [適用於 HDInsight 進階層 R Server 的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)

- [適用於 HDInsight 進階層 R Server 的 Azure 儲存體選項](hdinsight-hadoop-r-server-storage.md)

### Azure 資源管理員範本

如果您想要使用 Azure Resource Manager 範本，在 HDInsight 上自動建立 R Server，請參閱下列的範例範本：

* [使用 SSH 公開金鑰，在 HDInsight 叢集上建立 R Server](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [使用 SSH 密碼，在 HDInsight 叢集上建立 R Server](http://go.microsoft.com/fwlink/p/?LinkID=780810)

這兩種範本皆會建立新的 HDInsight 叢集和已與其建立關聯的儲存體帳戶，您可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站來使用。

如需使用 ARM 範本的一般資訊，請參閱[使用 ARM 範本在 HDInsight 中建立 Linux 型 Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。

<!---HONumber=AcomDC_0330_2016-->