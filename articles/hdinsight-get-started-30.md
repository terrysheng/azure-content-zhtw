<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using Hadoop 2.2 clusters with HDInsight | Azure" metaKeywords="" description="Get started using Hadoop 2.2 clusters with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

開始搭配 HDInsight 使用 Hadoop 2.2 叢集
=======================================

HDInsight 讓 [Apache Hadoop](http://hadoop.apache.org/) 成為雲端的服務。它使得 MapReduce 軟體架構可以在更簡單、更有擴充性且更符合成本效益的 Azure 環境中發揮作用。HDInsight 也提供使用 Azure Blob 儲存來管理和儲存資料的高成本效益作法。

在這個教學課程中，您將使用 Azure 管理入口網站佈建 HDInsight 叢集，並使用 PowerShell 提交 Hadoop MapReduce 工作，然後將 MapReduce 工作輸出資料匯入 Excel 以供檢查。

> [WACOM.NOTE] 本教學課程涵蓋在 HDInsight 使用 Hadoop 2.2 叢集。如需在 HDInsight 使用 Hadoop 1.2 叢集的教學課程，請參閱[開始使用 Azure HDInsight](/en-us/documentation/articles/hdinsight-get-started/)。

> [WACOM.NOTE] HDInsight 叢集 3.0 版不支援 *asv://* 語法，而且未來版本中也不支援。請改用 *wasb://* 語法。

除了全面供應 Azure HDInsight 之外，Microsoft 也推出 HDInsight Emulator for Azure，也就是過去的 Microsoft HDInsight Developer Preview。本產品適合開發人員使用，因此僅支援單一節點部署。關於使用 HDInsight Emulator，請參閱[開始使用 HDInsight Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/)。

**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：

-   Azure 訂閱。如需取得訂閱的詳細資訊，請參閱[購買選項](https://www.windowsazure.com/en-us/pricing/purchase-options/)、[成員優惠](https://www.windowsazure.com/en-us/pricing/member-offers/)或[免費試用](https://www.windowsazure.com/en-us/pricing/free-trial/)。
-   執行 Windows 8、Windows 7、Windows Server 2012 或 Windows Server 2008 R2 的電腦。這台電腦將用於提交 MapReduce 工作。
-   Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus。

**預估完成時間：** 30 分鐘

本教學課程內容
--------------

-   [設定執行 PowerShell 的本機環境](#setup)
-   [佈建 HDInsight 叢集](#provision)
-   [執行 WordCount MapReduce 程式](#sample)
-   [連接到 Microsoft 商業智慧工具](#powerquery)
-   [後續步驟](#nextsteps)

設定執行 PowerShell 的本機環境
------------------------------

有數種方式可以將 MapReduce 工作提交到 HDInsight。在本教學課程中，您將使用 Azure PowerShell。若要安裝 Azure PowerShell，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。出現提示時，按一下 **[執行]**，並按一下 **[安裝]**，然後按照指示進行。如需詳細資訊，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

PowerShell Cmdlet 需要有您的訂閱資訊，才能用來管理您的服務。

**使用 Azure AD 連接到您的訂閱**

1.  開啟 Azure PowerShell 主控台，依照[作法：安裝 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/#install) 的指示進行。
2.  執行以下命令：

         Add-AzureAccount

3.  在視窗中，輸入與您的帳戶相關聯的電子郵件地址和密碼。Azure 會驗證並儲存認證資訊，然後關閉視窗。

連線到訂閱的另一個方法是使用憑證方法。如需指示，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

佈建 HDInsight 叢集
-------------------

HDInsight 佈建程序需要 Azure 儲存體帳戶作為預設的檔案系統。儲存體帳戶必須位在 HDInsight 運算資源所在的同一個資料中心。目前，您只能在下列資料中心佈建 HDInsight 叢集：

-   東南亞
-   北歐
-   西歐
-   美國東部
-   美國西部

您必須對於您的 Azure 儲存體帳戶選擇 5 個資料中心的其中一個。

**建立 Azure 儲存體帳戶**

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。
2.  按一下左下角的 **[新增]**，並指向 **[資料服務]**，接著指向 **[儲存體]**，然後按一下 **[快速建立]**。

    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started-3.0/HDI.StorageAccount.QuickCreate.png)

3.  輸入 **[URL]**、**[位置]** 和 **[複寫]**，然後按一下 **[建立儲存體帳戶]**。不支援同質群組。您將在儲存體清單中看見新的儲存體帳戶。
4.  等候新儲存體帳戶的 **[狀態]** 變更為 **[線上]**。
5.  按一下清單中的新儲存體帳戶加以選取。
6.  按一下頁面底部的 **[MANAGE ACCESS KEYS]**。
7.  請記下 **[儲存體帳戶名稱]** 和 **[主要存取金鑰]**。稍後在教學課程中需要這些資訊。

如需詳細資訊，請參閱
[如何建立儲存體帳戶](/en-us/documentation/articles/storage-create-storage-account/)和[搭配 HDInsight 使用 Azure Blob 儲存體](/en-us/documentation/articles/hdinsight-use-blob-storage/)。

目前不支援使用自訂建立選項佈建 HDInsight 3.0 叢集。

**佈建 HDInsight 叢集**

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。

2.  按一下左邊的 **[HDINSIGHT]**，列出您的帳戶下的 HDInsight 叢集。在下列螢幕擷取畫面中，沒有任何現有的 HDInsight 叢集。

    ![HDI.ClusterStatus](./media/hdinsight-get-started-3.0/HDI.ClusterStatus.png)

3.  按一下左下角的 **[新增]**，然後依序按一下 **[資料服務]**、**[HDINSIGHT]**、**[自訂建立]**。

    ![HDI.CustomCreateCluster](./media/hdinsight-get-started-3.0/HDI.CustomCreateCluster.png)

4.  在 [叢集詳細資料] 索引標籤上，輸入或選取下列值：

	<table border="1">
	<tr><th>名稱</th><th>值</th></tr>
	<tr><td><strong>叢集名稱</strong></td><td>叢集的名稱。</td></tr>
	<tr><td><strong>資料節點</strong></td><td>您要部署的資料節點數。請建立單一節點叢集，以供測試之用。<br />叢集大小限制會隨著 Azure 訂閱而不同。若要提高限制，請與 Azure 帳務支援人員連絡。</td></tr>
	<tr><td><strong>HDInsight 版本</strong></td><td>請選擇 <strong>3.0</strong> ，以在 HDInsight 上建立 Hadoop 2.2 叢集。</td></tr>
	<tr><td><strong>區域</strong></td><td>	請選擇與您在前一個程序中建立的儲存體帳戶相同的區域。使用 HDInsight 時，儲存體帳戶必須位於相同的區域中。後續進行設定時，您只能選擇此處指定之相同區域中的儲存體帳戶。 
	</td></tr>
	</table>


5.  按一下位於右下角的向右箭頭，以設定叢集使用者。
6.  在 [設定叢集使用者] 索引標籤中，輸入 HDInsight 叢集使用者帳戶的 **[使用者名稱]** 和 **[密碼]**。除了此帳戶以外，您也可以在叢集佈建之後建立 RDP 使用者帳戶，以便從遠端桌面連接到叢集。如需指示，請參閱[使用管理入口網站來管理 HDInsight](/en-us/documentation/articles/hdinsight-administer-use-management-portal/)。
7.  按一下位於右下角的向右箭頭，以設定儲存體帳戶。
8.  在 [儲存體帳戶] 標籤上，輸入或選取下列值：

	<table border="1">
	<tr><th>名稱</th><th>值</th></tr>
	<tr><td>儲存體帳戶</td><td>選取 <strong>[使用現有儲存體]</strong>。如果您尚未建立新的儲存體帳戶，您也可以選擇使用管理入口網站來建立帳戶。</td></tr>
	<tr><td>帳戶名稱</td><td>指定您在本教學課程的上一個程序中建立的儲存體帳戶。請注意，清單方塊中只會顯示相同區域中的儲存體帳戶。</td></tr>
	<tr><td>預設容器</td><td>選取 <strong>[建立預設容器]</strong>。選擇此選項時，預設容器名稱會與叢集名稱相同。</td></tr>
	<tr><td>附加儲存體帳戶</td><td>選取 <strong>0</strong>。您可以選擇將叢集連接到多達 7 個附加儲存體帳戶。</td></tr>
	</table>

9.  按一下位於右下角的核取圖示，以建立叢集。佈建程序完成時，狀態欄將顯示 **[執行中]**。

執行 WordCount MapReduce 工作
-----------------------------

現在您已經佈建 HDInsight 叢集。下一個步驟是執行 MapReduce 工作，計算文字檔中的文字。

執行 MapReduce 工作需要下列元素：

-   MapReduce 程式。在本教學課程中，您將使用 HDInsight 叢集發佈所提供的 WordCount 範例，因此您不需要自行撰寫。它位在 */example/jars/hadoop-mapreduce-examples.jar* 中。如需自行撰寫 MapReduce 工作的指示，請參閱[開發 HDInsight 的 Java MapReduce 程式](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)。

-   輸入檔。您將使用 */example/data/gutenberg/davinci.txt* 作為輸入檔。如需上傳檔案的詳細資訊，請參閱[將資料上傳到 HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)。
-   輸出檔資料夾。您將使用 */example/data/WordCountOutput* 作為輸出檔資料夾。如果該資料夾不存在，系統將建立該資料夾。

定址 Blob 儲存體中的檔案時，使用的 URI 配置如下：

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] 依預設，預設檔案系統所用的 Blob 容器本身的名稱與 HDInsight 叢集相同。

URI 配置提供未加密存取，使用的是 *wasb:*首碼，也提供 SSL 加密存取，使用的是 wasbs。建議盡可能使用 wasbs，即使存取同一個 Azure 資料中心內的資料也是如此。

因為 HDInsight 使用 Blob 儲存體容器作為預設檔案系統，因此您可以使用相對或絕對路徑參照預設檔案系統內的檔案和目錄。

例如，若要存取 hadoop-mapreduce-examples.jar，您可以使用下列其中一個選項：

    ● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    ● wasb:///example/jars/hadoop-mapreduce-examples.jar
    ● /example/jars/hadoop-mapreduce-examples.jar

在這些檔案的路徑中使用 *wasb://* 首碼。需要這個首碼才能指出 Azure Blob 儲存體用於輸入檔和輸出檔。輸出目錄使用相對於 *wasb:///user/&lt;username\>* 資料夾的預設路徑。

如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](/en-us/documentation/articles/hdinsight-use-blob-storage/)。

**執行 WordCount 範例**

1.  開啟 **Azure PowerShell**。如需有關開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝和設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

2.  執行下列命令設定變數。

         $subscriptionName = "<SubscriptionName>" 
        $clusterName = "<HDInsightClusterName>"        

3.  執行下列命令來建立 MapReduce 工作定義：

         # Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    HDInsight 叢集發佈附有 hadoop-mapreduce-examples.jar 檔案。MapReduce 工作有兩個引數。第一個是來源檔案名稱，第二個是輸出檔案路徑。HDInsight 叢集發佈提供來源檔案，輸出檔案路徑則是在執行階段時建立。

4.  執行下列命令提交 MapReduce 工作：

         # Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    除了 MapReduce 工作定義之外，您也必須在要執行 MapReduce 工作的位置提供 HDInsight 叢集名稱。

    *Start-AzureHDInsightJob* 是非同步呼叫。若要檢查工作是否完成，請使用 *Wait-AzureHDInsightJob* Cmdlet。

5.  執行下列命令來檢查 MapReduce 工作是否完成：

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  執行下列命令來檢查 MapReduce 工作執行時有無任何錯誤：

         # Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

    下列螢幕擷取畫面顯示執行成功的輸出。不然您會看到一些錯誤訊息。

    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started-3.0/HDI.GettingStarted.RunMRJob.png)

**擷取 MapReduce 工作的結果**

1.  開啟 **Azure PowerShell**。
2.  執行下列命令建立 C:\\Tutorials 資料夾，並且將目錄變更為資料夾：

         mkdir \Tutorials
        cd \Tutorials

    預設 Azure Powershell 目錄是 *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*。依預設，您沒有此資料夾的寫入權限。您必須將目錄變更為您有寫入權限的資料夾。

3.  在下列命令中設定 3 個變數，然後執行這些命令：

         $subscriptionName = "<SubscriptionName>"       
        $storageAccountName = "<StorageAccountName>"   
        $containerName = "<ContainerName>"               

    Azure 儲存體帳戶是您在教學課程稍早建立的帳戶。儲存體帳戶用來代管當做預設 HDInsight 叢集檔案系統的 Blob 容器。Blob 儲存容器名稱通常與 HDInsight 叢集同名，除非您在佈建叢集時指定不同的名稱。

4.  執行下列命令來建立 Azure 儲存體內容物件：

         # Create the storage account context object
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    在您有多個訂閱，而且預設訂閱不是要使用的訂閱時，可使用 *Select-AzureSubscription* 設定目前的訂閱。

5.  執行下列命令將 Blob 容器的 MapReduce 工作輸出下載至工作站：

         # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    *example/data/WordCountOutput* 資料夾是您執行 MapReduce 工作時指定的輸出資料夾。*part-r-00000* 是 MapReduce 工作輸出的預設檔案名稱。檔案會以相同資料夾結構下載至本機資料夾。例如，在下列螢幕擷取畫面中，目前的資料夾是 C 根資料夾。檔案將下載到 *C:\\example\\data\\WordCountOutput\\* 資料夾。

6.  執行下列命令來列印 MapReduce 工作輸出檔案：

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started-3.0/HDI.GettingStarted.MRJobOutput.png)

    MapReduce 工作會產生一個名為 *part-r-00000* 的檔案，內有文字和字數。指令碼使用 findstr 命令列出包含 *"there"* 的所有文字。

> [WACOM.NOTE] 如果您在 [記事本] 中開啟 MapReduce 工作的多行輸出 *./example/data/WordCountOutput/part-r-00000*，您將注意到一個未正確輸入的分行符號。這是預期行為。

連接到 Microsoft 商業智慧工具
-----------------------------

Excel 的 Power Query 增益集可用來將 HDInsight 的輸出匯出到 Excel，以便使用 Microsoft 商業智慧 (BI) 工具進一步處理或顯示結果。先前建立 HDInsight 叢集時，已經建立與相關儲存體帳戶中建立的叢集相同名稱的預設容器。其中會自動填入一組檔案。其中一個檔案是範例 Hive 表格。在本節中，我們將顯示如何將此表格包含的資料匯入 Excel 進行檢視和其他處理。

您必須安裝 Excel 2010 或 2013，才能完成這個部分的教學課程。在這裡，我們將匯入 HDInsight 提供的預設 Hive 表格。

**下載 Microsoft PowerQuery for Excel**

-   從 [Microsoft 下載中心](http://www.microsoft.com/en-us/download/details.aspx?id=39379)下載並安裝 Microsoft Power Query for Excel。

**匯入 HDInsight 資料**

1.  開啟 Excel，並建立新的空白活頁簿。
2.  依序按一下 **[Power Query]** 功能表、**[From Other Sources]**、**[From Azure HDInsight]**。

    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  輸入叢集相關 Azure Blob 儲存體帳戶的 **[帳戶名稱]**，然後按一下 **[確定]**。這是您在教學課程稍早建立的儲存體帳戶。
4.  輸入 Azure Blob 儲存體帳戶的 **[帳戶金鑰]**，然後按一下 **[儲存]**。
5.  在右邊的瀏覽器窗格中，按兩下 Blob 儲存體容器名稱。依預設，容器名稱與叢集名稱相同。

6.  找出 **[名稱]** 欄中的 **part-r-00000** (路徑是 *.../example/data/WordCountOutput*)，然後按一下 **part-r-00000** 左邊的 **[二進位]**。

    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  以滑鼠右鍵按一下 **[Column1.1]**，然後選取 **[重新命名]**。
8.  將名稱變更為 **Word**。
9.  重複這個程序，將 **Column1.2** 重新命名為 **Count**。

    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. 按一下左上角的 **[Apply & Close]**。查詢隨即將 Hive 表格匯入 Excel。

後續步驟
--------

在本教學課程中，您已經了解如何使用 HDInsight 佈建叢集、在其中執行 MapReduce 工作，並且將結果匯入 Excel，以便使用 BI 工具進行後續處理並以圖形顯示。若要深入了解，請參閱下列文章：

-   [開始使用 HDInsight](/en-us/documentation/articles/hdinsight-get-started/)
-   [開始使用 HDInsight Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/)
-   [在 HDInsight 上使用 Azure Blob 儲存體](/en-us/documentation/articles/hdinsight-use-blob-storage/)
-   [使用 PowerShell 管理 HDInsight](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
-   [將資料上傳到 HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
-   [搭配 HDInsight 使用 Hive](/en-us/documentation/articles/hdinsight-use-hive/)
-   [搭配 HDInsight 使用 Pig](/en-us/documentation/articles/hdinsight-use-pig/)
-   [在 HDInsight 上使用 Oozie](/en-us/documentation/articles/hdinsight-use-oozie/)
-   [開發 HDInsight 的 C\# Hadoop 串流 MapReduce 程式](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [開發 HDInsight 的 Java MapReduce 程式](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

