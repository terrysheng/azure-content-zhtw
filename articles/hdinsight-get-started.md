<properties  linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using HDInsight | Azure" metaKeywords="" description="Get started with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# 開始使用 Azure HDInsight

HDInsight 讓 [Apache Hadoop][1] 成為雲端的服務。它使得 MapReduce 軟體架構可以在更簡單、更有擴充性且更符合成本效益的 Azure 環境中發揮作用。HDInsight 也提供使用 Azure Blob 儲存來管理和儲存資料的高成本效益作法。

在這個教學課程中，您將使用 Azure 管理入口網站佈建 HDInsight 叢集，並使用 PowerShell 提交 Hadoop MapReduce 工作，然後將 MapReduce 工作輸出資料匯入 Excel 以供檢查。

> [WACOM.NOTE] 本教學課程涵蓋在 HDInsight 使用 Hadoop 1.2 叢集。如需在 HDInsight 使用
> Hadoop 2.2 叢集的教學課程，請參閱[開始搭配 HDInsight 使用 Hadoop 2.2
> 叢集](/en-us/documentation/articles/hdinsight-get-started-30/)。

除了全面供應 Azure HDInsight 之外，Microsoft 也推出 HDInsight Emulator for Azure，也就是過去的 Microsoft HDInsight Developer Preview。本產品適合開發人員使用，因此僅支援單一節點部署。關於使用 HDInsight Emulator，請參閱[開始使用 HDInsight Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/)。

**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：

* Azure 訂閱。如需取得訂閱的詳細資訊，請參閱[購買選項][2]、[成員優惠][3]或[免費試用][4]。
* 執行 Windows 8、Windows 7、Windows Server 2012 或 Windows Server 2008 R2
  的電腦。這台電腦將用於提交 MapReduce 工作。
* Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013
  Standalone 或 Office 2010 Professional Plus。

**預估完成時間：**30 分鐘

## 本教學課程內容

* [設定執行 PowerShell 的本機環境](#setup)
* [佈建 HDInsight 叢集](#provision)
* [執行 WordCount MapReduce 程式](#sample)
* [連接到 Microsoft 商業智慧工具](#powerquery)
* [後續步驟](#nextsteps)

## <a  id="setup" ></a>設定執行 PowerShell 的本機環境

有數種方式可以將 MapReduce 工作提交到 HDInsight。在本教學課程中，您將使用 Azure PowerShell。若要安裝 Azure PowerShell，請執行 [Microsoft Web Platform Installer][5]。出現提示時，按一下 **執行**，並按一下 **安裝**，然後按照指示進行。如需詳細資訊，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

PowerShell Cmdlet 需要有您的訂閱資訊，才能用來管理您的服務。

**使用 Azure AD 連接到您的訂閱**

1.  開啟 Azure PowerShell 主控台，依照[作法：安裝 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/#install) 的指示進行。
2.  執行以下命令：
    
         Add-AzureAccount

3.  在視窗中，輸入與您的帳戶相關聯的電子郵件地址和密碼。Azure 會驗證並儲存認證資訊，然後關閉視窗。

連線到訂閱的另一個方法是使用憑證方法。如需指示，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

## <a name="provision"></a>佈建 HDInsight 叢集

HDInsight 佈建程序需要 Azure 儲存體帳戶作為預設的檔案系統。儲存體帳戶必須位在 HDInsight 運算資源所在的同一個資料中心。目前，您只能在下列資料中心佈建 HDInsight 叢集：

* 東南亞
* 北歐
* 西歐
* 美國東部
* 美國西部

您必須對於您的 Azure 儲存體帳戶選擇 5 個資料中心的其中一個。

**建立 Azure 儲存體帳戶**

1.  登入 [Azure 管理入口網站][6]。 2.  按一下左下角的 **新增**，並指向 **資料服務**，接著指向 **儲存體**，然後按一下 **快速建立**。
    
    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png)

3.  輸入 **URL**、**位置** 和 **複寫**，然後按一下 **建立儲存體帳戶**。不支援同質群組。您將在儲存體清單中看見新的儲存體帳戶。
4.  等候新儲存體帳戶的 **狀態** 變更為 **線上**。 5.  按一下清單中的新儲存體帳戶加以選取。 6.  按一下頁面底部的 **MANAGE ACCESS KEYS**。 7.  請記下 **儲存體帳戶名稱** 和 **主要存取金鑰**。稍後在教學課程中需要這些資訊。

如需詳細資訊，請參閱[如何建立儲存體帳戶](/en-us/documentation/articles/storage-create-storage-account/)和[搭配HDInsight 使用 Azure Blob 儲存體](/en-us/documentation/articles/hdinsight-use-blob-storage/)。

**佈建 HDInsight 叢集**

1.  登入 [Azure 管理入口網站][6]。

2.  按一下左邊的 **HDInsight** 列出您帳戶中的叢集狀態。在下列螢幕擷取畫面中，沒有任何現有的 HDInsight 叢集。
    
    ![HDI.ClusterStatus](./media/hdinsight-get-started/HDI.ClusterStatus.png)

3.  按一下左下角的 **新增**，然後依序按一下 **資料服務**、**HDInsight**、**快速建立**。
    
    ![HDI.QuickCreateCluster](./media/hdinsight-get-started/HDI.QuickCreateCluster.png)

4.  輸入或選取下列值：
    
    <table  border="1">
    <tr><th>名稱</th>
    <th>值</th>
    </tr>
    
    <tr><td>叢集名稱</td>
    <td>叢集的名稱</td>
    </tr>
    
    <tr><td>叢集大小</td>
    <td>您要部署的資料節點數。預設值為 4。不過，下拉式功能表也提供 8、16 和 32 個資料節點叢集。使用 [Custom Create]<strong  />
     選項可以指定任何數目的資料節點。另外也提供各種叢集大小的費率定價詳細資料。按一下下拉式方塊正上方的 [?]<strong  />
    ，並按一下快顯功能表上的連結。</td>
    </tr>
    
    <tr><td>密碼 (叢集管理)</td>
    <td>帳戶 <i>admin</i>
     的密碼。使用 [快速建立] 選項時，叢集使用者名稱預設指定為「admin」。只有使用 [Custom Create]<strong  />
     精靈才能變更這個名稱。密碼欄位至少必須有 10 個字元，而且必須包含大寫字母、小寫字母、數字和特殊字元。</td>
    </tr>
    
    <tr><td>儲存體帳戶</td>
    <td>從下拉式方塊中，選取您建立的儲存體帳戶。 <br  />
    
    
    > [WACOM.NOTE] > 選擇儲存體帳戶之後，則無法變更。如果移除儲存體帳戶，則無法再使用叢集。
    
    HDInsight 叢集位置必須與儲存體帳戶相同。
     </td>
    </tr>
    
     </table>

5.  按一下右下角的 **Create HDInsight Cluster**。佈建程序完成時，狀態欄將顯示 **執行中**。

如需使用 **CUSTOM CREATE** 選項的資訊，請參閱[佈建 HDInsight 叢集](/en-us/documentation/articles/hdinsight-provision-clusters/)。

## <a name="sample"></a>執行 WordCount MapReduce 工作

現在您已經佈建 HDInsight 叢集。下一個步驟是執行 MapReduce 工作，計算文字檔中的文字。

執行 MapReduce 工作需要下列元素：

* MapReduce 程式。在本教學課程中，您將使用 HDInsight 叢集發佈所提供的 WordCount 範例，因此您不需要自行撰寫。它位在 */example/jars/hadoop-examples.jar*。如需自行撰寫 MapReduce 工作的指示，請參閱[開發 HDInsight 的 Java MapReduce 程式](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)。

* 輸入檔。您將使用 */example/data/gutenberg/davinci.txt* 作為輸入檔。如需上傳檔案的詳細資訊，請參閱[將資料上傳到
  HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)。
* 輸出檔資料夾。您將使用 */example/data/WordCountOutput* 作為輸出檔資料夾。如果該資料夾不存在，系統將建立該資料夾。

存取 Blob 儲存體中的檔案所用的 URI 配置是：

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] 依預設，預設檔案系統所用的 Blob 容器本身的名稱與 HDInsight 叢集相同。

URI 配置提供 *wasb:* 首碼的未加密存取，以及 WASBS 的 SSL 加密存取。建議盡可能使用 wasbs，即使存取同一個 Azure 資料中心內的資料也是如此。

因為 HDInsight 使用 Blob 儲存體容器作為預設檔案系統，因此您可以使用相對或絕對路徑參照預設檔案系統內的檔案和目錄。

例如，若要存取 hadoop-examples.jar，您可以使用下列其中一個選項：

    â—? wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-examples.jar
    â—? wasb:///example/jars/hadoop-examples.jar
    â—? /example/jars/hadoop-examples.jar

在這些檔案的路徑中使用 *wasb://* 首碼。需要這個首碼才能指出 Azure Blob 儲存體用於輸入檔和輸出檔。輸出目錄使用相對於 *wasb:///user/<username>* 資料夾的預設路徑。

如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](/en-us/documentation/articles/hdinsight-use-blob-storage/)。

**執行 WordCount 範例**

1.  開啟 **Azure PowerShell**。如需開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

2.  執行下列命令設定變數。
    
         $subscriptionName = "<SubscriptionName>" 
        $clusterName = "<HDInsightClusterName>"        

3.  執行下列命令建立 MapReduce 工作定義：
  
		# Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
    
    HDInsight 叢集發佈提供 hadoop-examples.jar 檔案。MapReduce 工作有兩個引數。第一個是來源檔案名稱，第二個是輸出檔案路徑。HDInsight 叢集發佈提供來源檔案，輸出檔案路徑則是在執行階段時建立。

4.  執行下列命令提交 MapReduce 工作：
  
		# Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
    
    除了 MapReduce 工作定義之外，您也必須在要執行 MapReduce 工作的位置提供 HDInsight 叢集名稱。
    
    *Start-AzureHDInsightJob* 是非同步呼叫。若要檢查工作是否完成，請使用 *Wait-AzureHDInsightJob* Cmdlet。

5.  執行下列命令檢查 MapReduce 工作是否完成：
    
         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  執行下列命令檢查執行 MapReduce 工作的任何錯誤：

		# Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError
    
    下列螢幕擷取畫面顯示成功執行的輸出。否則，您將看見錯誤訊息。
    
    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png)

**擷取 MapReduce 工作的結果**

1.  開啟 **Azure PowerShell**。 2.  執行下列命令建立 C:\\Tutorials 資料夾，並且將目錄變更為資料夾：
    
         mkdir \Tutorials
        cd \Tutorials
    
    預設 Azure Powershell 目錄是 *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*。依預設，您沒有此資料夾的寫入權限。您必須將目錄變更為您有寫入權限的資料夾。

3.  在下列命令中設定 3 個變數，然後執行這些命令：
    
         $subscriptionName = "<SubscriptionName>"       
        $storageAccountName = "<StorageAccountName>"   
        $containerName = "<ContainerName>"			   
    
    Azure 儲存體帳戶是您在教學課程稍早建立的帳戶。儲存體帳戶將用來代管作為預設 HDInsight 叢集檔案系統的 Blob 容器。Blob 儲存體容器名稱通常使用與 HDInsight 叢集相同的名稱，除非您在佈建叢集時指定不同的名稱。

4.  執行下列命令建立 Azure 儲存體內容物件：
		
		# Create the storage account context object
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    在您有多個訂閱，而且預設訂閱不是要使用的訂閱時，可使用 *Select-AzureSubscription* 設定目前的訂閱。

5.  執行下列命令從 Blob 容器將 MapReduce 工作輸出下載到工作站：

		# Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
    
    *example/data/WordCountOutput* 資料夾是您執行 MapReduce 工作時指定的輸出資料夾。*part-r-00000* 是 MapReduce
    工作輸出的預設檔案名稱。檔案將下載到本機資料夾的相同資料夾結構。例如，在下列螢幕擷取畫面中，目前的資料夾是 C 根資料夾。檔案將下載到 *C:\\example\\data\\WordCountOutput\\* 資料夾。

6.  執行下列命令列印 MapReduce 工作輸出檔案：
    
         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    
    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png)
    
    MapReduce 工作將以文字和計數產生名稱為 *part-r-00000* 的檔案。指令碼使用 findstr 命令列出包含 *"there"* 的所有文字。

> [WACOM.NOTE] 如果您在 [記事本] 中開啟 MapReduce 工作的多行輸出
> *./example/data/WordCountOutput/part-r-00000*，您將注意到一個未正確輸入的分行符號。這是預期結果。

## <a name="powerquery"></a>連接到 Microsoft 商業智慧工具

Excel 的 Power Query 增益集可用來將 HDInsight 的輸出匯出到 Excel，以便使用 Microsoft 商業智慧 (BI) 工具進一步處理或顯示結果。先前建立 HDInsight 叢集時，已經建立與相關儲存體帳戶中建立的叢集相同名稱的預設容器。其中會自動填入一組檔案。其中一個檔案是範例 Hive 表格。在本節中，我們將顯示如何將此表格包含的資料匯入 Excel 進行檢視和其他處理。

您必須安裝 Excel 2010 或 2013，才能完成這個部分的教學課程。在這裡，我們將匯入 HDInsight 提供的預設 Hive 表格。

**下載 Microsoft PowerQuery for Excel**

* 從 [Microsoft 下載中心][7]下載並安裝 Microsoft Power Query for Excel。

**匯入 HDInsight 資料**

1.  開啟 Excel，並建立新的空白活頁簿。 2.  依序按一下 **Power Query** 功能表、**From Other Sources**、**From Azure HDInsight**。
    
    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  輸入叢集相關 Azure Blob 儲存體帳戶的 **帳戶名稱**，然後按一下 **確定**。這是您在教學課程稍早建立的儲存體帳戶。
4.  輸入 Azure Blob 儲存體帳戶的 **帳戶金鑰**，然後按一下 **儲存**。
5.  在右邊的瀏覽器窗格中，按兩下 Blob 儲存體容器名稱。依預設，容器名稱與叢集名稱相同。

6.  找出 **名稱** 欄中的 **part-r-00000** (路徑是 *.../example/data/WordCountOutput*)，然後按一下 **part-r-00000** 左邊的 **二進位**。
    
    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  以滑鼠右鍵按一下 **Column1.1**，然後選取 **重新命名**。
8.  將名稱變更為 **Word**。
9.  重複這個程序，將 **Column1.2** 重新命名為 **Count**。
    
    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. 按一下左上角的 **Apply & Close**。查詢隨即將 Hive 表格匯入 Excel。

## <a name="nextsteps"></a>後續步驟

在本教學課程中，您已經了解如何使用 HDInsight 佈建叢集、在其中執行 MapReduce 工作，並且將結果匯入 Excel，以便使用 BI 工具進行後續處理並以圖形顯示。若要深入了解，請參閱下列文章：

* [開始搭配 HDInsight 使用 Hadoop 2.2
  叢集](/en-us/documentation/articles/hdinsight-get-started-30/)
* [開始使用 HDInsight
  Emulator](/en-us/documentation/articles/hdinsight-get-started-emulator/)
* [搭配 HDInsight 使用 Azure Blob
  儲存體](/en-us/documentation/articles/hdinsight-use-blob-storage/)
* [使用 PowerShell 管理
  HDInsight](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
* [將資料上傳到
  HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
* [搭配 HDInsight 使用
  MapReduce](/en-us/documentation/articles/hdinsight-use-mapreduce)
* [搭配 HDInsight 使用
  Hive](/en-us/documentation/articles/hdinsight-use-hive/)
* [搭配 HDInsight 使用
  Pig](/en-us/documentation/articles/hdinsight-use-pig/)
* [搭配 HDInsight 使用
  Oozie](/en-us/documentation/articles/hdinsight-use-oozie/)
* [開發 HDInsight 的 C# Hadoop
  串流程式](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
* [開發 HDInsight 的 Java MapReduce
  程式](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)



[1]: http://hadoop.apache.org/
[2]: https://www.windowsazure.com/en-us/pricing/purchase-options/
[3]: https://www.windowsazure.com/en-us/pricing/member-offers/
[4]: https://www.windowsazure.com/en-us/pricing/free-trial/
[5]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[6]: https://manage.windowsazure.com/
[7]: http://www.microsoft.com/en-us/download/details.aspx?id=39379