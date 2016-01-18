<properties 
	pageTitle="在 HDInsight Linux 上搭配使用 Zeppelin Notebook 和 Spark 叢集 | Azure" 
	description="如何在 HDInsight Linux 上搭配使用 Zeppelin Notebook 和 Spark 叢集的逐步指示" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="nitinme"/>


# 在 HDInsight 上搭配使用 Zeppelin Notebook 和 Spark 叢集

了解如何在 Spark 叢集上安裝 Zeppelin Notebook 以及如何使用 Zeppelin Notebook。

**必要條件：**

* 開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-provision-clusters.md)。
* SSH 用戶端。若為 Linux 和 Unix 發佈或 Macintosh OS X，`ssh` 命令會隨作業系統提供。若為 Windows，我們建議使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

	> [AZURE.NOTE]如果您想要使用 `ssh` 或 PuTTY 以外的 SSH 用戶端，請參考您用戶端的說明文件，了解如何建立 SSH 通道。

* 可以設定為使用 SOCKS Proxy 的網頁瀏覽器。

* __(選擇性)__：如 [FoxyProxy](http://getfoxyproxy.org/,) 之類的外掛程式，其可套用的規則是只將特定要求透過通道進行路由傳送。

	> [AZURE.WARNING]若無 FoxyProxy 之類的外掛程式，所有透過瀏覽器建立的要求可能都會透過通道進行路由傳送。這會導致瀏覽器中的網頁載入速度較慢。

## 在建立叢集時安裝 Zeppelin

您可以使用指令碼動作在 Spark 叢集上安裝 Zeppelin。指令碼動作會使用自訂指令碼在叢集上安裝不是預設可用的元件。用以在 Spark 叢集上安裝 Zeppelin 的自訂指令碼可從 ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh** 取得。

### 使用 Azure 入口網站

如需有關如何使用 HDInsight .NET SDK 來執行指令碼動作以安裝 Zeppelin 的指示，請參閱[使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal)。您必須對該文中的指示進行一些變更。

* 您必須使用指令碼來安裝 Zeppelin。要使用的指令碼是 ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**。

* 您只須在前端節點上執行此指令碼動作。

* 此指令碼不需要任何參數。

### 使用 HDInsight .NET SDK

如需有關如何使用 HDInsight .NET SDK 來執行指令碼動作以安裝 Zeppelin 的指示，請參閱[使用指令碼動作來自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk)。您必須對該文中的指示進行一些變更。

* 您必須使用指令碼來安裝 Zeppelin。要使用的指令碼是 ****https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**。

* 此指令碼不需要任何參數。

* 將您要建立的叢集類型設為 Spark。

### 使用 Azure PowerShell

使用下列 PowerShell 程式碼片段，在已安裝 Zeppelin 的 HDInsight Linux 上建立 Spark 叢集。繼續之前，請確定您已安裝 PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。

	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## 設定 SSH 通道以存取 Zeppelin Notebook

您將使用 SSH 通道來存取在 HDInsight Linux 上的 Spark 叢集上執行的 Zeppelin Notebook。下列步驟示範如何使用 ssh 命令列 (Linux) 和 PuTTY (Windows) 建立 SSH 通道。

### 使用 SSH 命令建立通道 (Linux)

使用下列命令，利用 `ssh` 命令建立 SSH 通道。以您 HDInsight 叢集的 SSH 使用者取代 __USERNAME__，並以您 HDInsight 叢集的名稱取代 __CLUSTERNAME__。

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

這會建立透過 SSH 將流量由本機連接埠 9876 路由傳送至叢集的連線。可用選項包括：

* **D 9876** - 會透過通道路由傳送流量的本機連接埠。

* **C** - 壓縮所有資料，因為網路流量大多是文字。

* **2** - 強制 SSH 僅嘗試通訊協定第 2 版。

* **q** - 無訊息模式。

* **T** - 停用虛擬 tty 配置，因為我們只是轉送連接埠。

* **n** - 防止讀取 STDIN，因為我們只是轉送連接埠。

* **N** - 不執行遠端命令，因為我們只是轉送連接埠。

* **f** - 在背景中執行。

如果您使用 SSH 金鑰設定叢集，您可能需要使用 `-i` 參數並指定 SSH 私密金鑰的路徑。

在命令完成後，傳送至本機電腦上連接埠 9876 的流量將透過安全通訊端層 (SSL) 路由傳送至叢集前端節點，看起來就像是在該處產生。

### 使用 PuTTY 建立通道 (Windows)

使用下列步驟，利用 PuTTY 建立 SSH 通道。

1. 開啟 PuTTY，並輸入連線資訊。如果您不熟悉 PuTTY，請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)，以取得如何搭配 HDInsight 使用 PuTTY 的資訊。

2. 在對話方塊左側的 [**類別**] 區段中，依序展開 [**連接**] 和 [**SSH**]，最後選取 [**通道**]。

3. 在 [**控制 SSH 連接埠轉送的選項**] 表單中提供下列資訊：

	* **來源連接埠** - 您想要轉送之用戶端上的連接埠。例如，**9876**。

	* **目的地** - 以 Linux 為基礎的 HDInsight 叢集的 SSH 位址。例如，**mycluster-ssh.azurehdinsight.net**。

	* **動態** - 啟用動態 SOCKS Proxy 路由。

	![通道選項的映像](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. 按一下 [**新增**] 以新增設定，然後按一下 [**開啟**] 開啟 SSH 連線。

5. 出現提示時，登入伺服器。這會建立 SSH 工作階段，並啟用通道。

### 從瀏覽器使用通道

> [AZURE.NOTE]本節中的步驟使用 FireFox 瀏覽器，因為它在 Linux、Unix、Macintosh OS X 和 Windows 系統上均可隨意運用。其他最新的瀏覽器，如 Google Chrome、Microsoft Edge 或 Apple Safari 等應該也可以運作；不過，某些步驟中所使用的 FoxyProxy 外掛程式可能無法適用於所有瀏覽器。

1. 將瀏覽器設定為使用 **localhost:9876** 做為 **SOCKS v5** Proxy。Firefox 的設定如下所示。如果您使用與 9876 不同的連接埠，請將連接埠變更為您所用的連接埠：

	![Firefox 設定的映像](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE]選取 [**遠端 DNS**] 會使用 HDInsight 叢集解析網域名稱系統 (DNS) 要求。若未選取，則會在本機解析 DNS。

2. 在 Firefox 中啟用和停用 Proxy 設定的情況下造訪網站，例如 [http://www.whatismyip.com/](http://www.whatismyip.com/)，即可驗證是否透過通道路由傳送流量。在啟用設定時，是使用 Microsoft Azure 資料中心內之機器的 IP 位址。

### 瀏覽器延伸模組

當設定瀏覽器使用通道的功能在運作時，您通常不會想透過通道傳送所有流量。[FoxyProxy](http://getfoxyproxy.org/) 等瀏覽器延伸模組支援 URL 要求的模式比對 (僅限 FoxyProxy Standard 或 Plus)，以便只有特定 URL 的要求會透過通道傳送。

如果您已安裝 FoxyProxy Standard，請使用下列步驟將它設定為只透過通道轉送 HDInsight 的流量。

1. 在您的瀏覽器中開啟 FoxyProxy 延伸模組。比方說，在 Firefox 中選取 [位址] 欄位旁的 FoxyProxy 圖示。

	![foxyproxy 圖示](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. 選取 [**加入新的 Proxy**]、選取 [**一般**] 索引標籤，然後輸入 **HDInsightProxy** 的 Proxy 名稱。

	![foxyproxy 一般](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. 選取 [**Proxy 詳細資料**] 索引標籤並填入下列欄位：

	* **主機或 IP 位址** - 這是 localhost，因為我們使用本機電腦上的 SSH 通道。

	* **連接埠** - 這是用於 SSH 通道的連接埠。

	* **SOCKS Proxy** - 選取此選項讓瀏覽器使用通道做為 Proxy。

	* **SOCKS v5** - 選取此選項以設定 Proxy 的要求版本。

	![foxyproxy proxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. 選取 [**URL 模式**] 索引標籤，然後選取 [**加入新的模式**]。使用下列欄位定義模式，然後按一下 [**確定**]：

	* **模式名稱** - **zeppelinnotebook** - 這是易記的模式名稱。

	* **URL 模式** - ***hn0*** - 這會定義符合 Zeppelin Notebook 裝載所在端點之內部完整網域名稱的模式。因為 Zeppelin Notebook 僅適用於叢集的 headnode0，而且端點通常是 `http://hn0-<string>.internal.cloudapp.net`，所以使用模式 **hn0** 可確保要求會重新導向至 Zeppelin 端點。

		![foxyproxy 模式](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. 選取 [**確定**] 以加入 Proxy 並關閉 [**Proxy 設定**]。

5. 在 FoxyProxy 對話方塊頂端，將 [**選取模式**] 變更為 [**根據預先定義的模式和優先順序使用 Proxy**]，然後按一下 [**關閉**]。

	![foxyproxy 選取模式](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

在執行這些步驟後，只有包含 __internal.cloudapp.net__ 字串之 URL 的要求會透過 SSL 通道路由傳送。

## 存取 Zeppelin Notebook

設定 SSH 通道之後，您可以遵循下列步驟，在 Spark 叢集上存取 Zeppelin Notebook。

1. 從網頁瀏覽器開啟下列端點：

		http://hn0-myspar:9995

	* **hn0** 代表 headnode0
	* **myspar** 是 Spark 叢集的前五個字母。
	* **9995** 是 Zeppelin Notebook 可存取的連接埠。

2. 建立新的 Notebook。按一下標頭窗格中的 [Notebook]，然後按一下 [建立新 Note]。

	![建立新的 Zeppelin Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.createnewnote.png "建立新的 Zeppelin Notebook")

	在同一個頁面的 [Notebook] 標頭下方，您應該會看到名稱開頭為「Note XXXXXXXXX」的新 Notebook。按一下新的 Notebook。

3. 在新 Notebook 的網頁上按一下標題，需要的話可以變更 Notebook 的名稱。按下 ENTER 以儲存名稱變更。此外，請確定 Notebook 標頭的右上角顯示 [已連線] 狀態。

	![Zeppelin Notebook 狀態](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.newnote.connected.png "Zeppelin Notebook 狀態")

4. 將範例資料載入暫存資料表。當您在 HDInsight 中建立 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶中 (位於 **\\HdiSamples\\SensorSampleData\\hvac**)。

	將以下程式碼片段貼入新 Notebook 中預設建立的空白段落。

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	按下 **SHIFT + ENTER**，或是按一下 [播放] 按鈕來讓段落執行程式碼片段。段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。輸出會顯示在同一個段落的底部。螢幕擷取畫面如下所示：

	![從原始資料建立暫存資料表](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.loaddataintotable.png "從原始資料建立暫存資料表")

	您也可以為每個段落提供標題。按一下右下角的設定圖示，然後按一下 [顯示標題]。

5. 現在，您可以針對 **hvac** 資料表執行 Spark SQL 陳述式。將以下查詢貼入新段落。此查詢會擷取建築物識別碼，以及在指定日期當天每棟建築物之目標溫度與實際溫度間的差異。按下 **SHIFT + ENTER**。

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Spark SQL 解譯器。您可以在 Notebook 標頭的 [解譯器] 索引標籤中，查看已定義的解譯器。

	以下螢幕擷取畫面顯示輸出。

	![使用 Notebook 執行 Spark SQL 陳述式](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery1.png "使用 Notebook 執行 Spark SQL 陳述式")

	 按一下顯示選項 (以矩形反白顯示) 以針對相同輸出切換不同的表示法。按一下 [設定] 以選擇構成輸出中索引鍵和值的項目。在上方的螢幕擷取畫面中，索引鍵為 **buildingID**，而值為 **temp\_diff** 的平均值。

	
6. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。下一個程式碼片段示範如何利用您想要查詢的可能值，來定義查詢中的變數 **Temp**。當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	將此程式碼片段貼到新的段落中，然後按下 **SHIFT + ENTER**。以下螢幕擷取畫面顯示輸出。

	![使用 Notebook 執行 Spark SQL 陳述式](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery2.png "使用 Notebook 執行 Spark SQL 陳述式")

	對於後續的查詢，您可以從下拉式清單選取新的值，然後再次執行查詢。按一下 [設定] 以選擇構成輸出中索引鍵和值的項目。上述螢幕擷取畫面使用 **buildingID** 做為索引鍵、平均 **temp\_diff** 做為值，而 **targettemp** 做為群組。

7. 重新啟動 Spark SQL 解譯器以結束應用程式。按一下頂端的 [解譯器] 索引標籤，然後針對 Spark 解譯器按一下 [重新啟動]。

	![重新啟動 Zeppelin 解譯器](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.zeppelin.restart.interpreter.png "重新啟動 Zeppelin 解譯器")


## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 建立及執行應用程式

* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)

* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### 擴充功能

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0107_2016-->