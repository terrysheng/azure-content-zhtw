<properties 
	pageTitle="使用 HDInsight 中的指令碼動作在 Hadoop 叢集上安裝 Solr | Azure" 
	description="了解如何自訂 HDInsight 叢集以安裝 Solr。您將使用指令碼動作組態選項，以使用指令碼來安裝 Solr" 
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
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# 在 HDInsight Hadoop 叢集上安裝和使用 Solr

您可以在 HDInsight 上的 Hadoop 中任何類型的叢集上使用**指令碼動作**叢集自訂來安裝 Solr。只有在建立叢集時，指令碼動作才可讓您執行指令碼以自訂叢集。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。

在本主題中，您將學習如何使用指令碼動作來安裝 Solr。Solr 是功能強大的搜尋平台，可對 Hadoop 所管理的資料提供企業級的搜尋功能。一旦您在 HDInsight 叢集上安裝 Solr，您也將了解如何使用 Solr 搜尋資料。

> [AZURE.NOTE] 本主題中使用的範例指令碼會使用特定組態建立 Solr 叢集。如果您想要使用不同的集合、分區、結構描述、複本等設定 Solr 叢集，您必須據以修改指令碼和 Solr 二進位檔。


## 本文內容

- [什麼是 Solr？](#whatis)
- [如何安裝 Solr？](#install)
- [如何在 HDInsight 中使用 Solr？](#usesolr)
- [使用 PowerShell 在 HDInsight Hadoop 叢集上安裝 Solr](#usingPS)
- [使用 .NET SDK 在 HDInsight Hadoop 叢集上安裝 Solr](#usingSDK) 


## <a name="whatis"></a>什麼是 Solr？

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> 是企業搜尋平台，可為資料提供強大的全文檢索搜尋。Hadoop 可讓您儲存和管理大量資料，Apache Solr 則提供快速擷取資料的搜尋功能。本主題說明如何自訂 HDInsight 叢集以安裝 Solr。   

## <a name="install"></a>如何安裝 Solr？

您可以從一個唯讀的 Azure 儲存體 Blob 取得在 HDInsight 叢集上安裝 Solr 的範例指令碼，網址為 [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)。本節提供有關如何在使用 Azure 管理入口網站佈建叢集時使用範例指令碼的指示。 


> [AZURE.NOTE] 範例指令碼只能與 HDInsight 叢集版本 3.1 搭配使用。如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-component-versioning/)。


1. 依照[使用自訂選項佈建叢集](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-provision-clusters/#portal)中的說明，使用**自訂建立**選項開始佈建叢集。 
2. 在精靈的 [**指令碼動作**] 頁面上，按一下 [**加入指令碼動作**] 提供有關指令碼動作的詳細資料，如下所示：

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>名稱</td>
			<td>指定指令碼動作的名稱。例如，<b>安裝 Solr</b>。</td></tr>
		<tr><td>指令碼 URI</td>
			<td>指定要叫用來自訂叢集的指令碼的 URI。例如， <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
		<tr><td>節點類型</td>
			<td>指定執行自訂指定碼的節點。您可以選擇<b>所有節點</b>、<b>僅前端節點</b>或僅<b>背景工作節點</b>。
		<tr><td>參數</td>
			<td>如果指令碼要求，請指定參數。安裝 Solr 的指令碼不需要任何參數，因此您可以在此保留空白。</td></tr>
	</table>	

	您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，按一下核取記號以開始佈建叢集。

您也可以使用指令碼在 HDInsight 上使用 PowerShell 或 HDInsight .NET SDK 安裝 Solr。本主題稍後會提供這些程序的指示。

## <a name="usesolr"></a>如何在 HDInsight 中使用 Solr？

您必須從對 Solr 建立幾個資料檔案的索引開始。之後便可以使用 Solr 對建立索引的資料執行搜尋查詢。執行下列步驟，以在 HDInsight 叢集中使用 Solr：

1. **RDP 到已安裝 Solr 的 HDInsight 叢集**。從「Azure 管理入口網站」，針對您所建立已安裝 Solr 的叢集啟用遠端桌面，然後從遠端連線至叢集。如需相關指示，請參閱 <a href="http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">使用遠端桌面連線到 HDInsight 叢集</a>。

2. **上傳資料檔案來對 Solr 建立索引**。當您為 Solr 建立索引時，您會放入您可能需要搜尋的文件。為了對 Solr 建立索引，請 RDP 進入叢集，導覽至桌面，開啟 Hadoop 命令列，並導覽至 **C:\apps\dist\solr-4.7.2\example\exampledocs**。執行以下命令： 
	
		java -jar post.jar solr.xml monitor.xml

	您可在主控台上看到以下輸出。

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	post.jar 公用程式會建立兩份範例文件 **solr.xml** 和 **monitor.xml** 來對 Solr 建立索引。post.jar 公用程式和範例文件會隨著 Solr 安裝提供。

3. **使用 Solr 儀表板在建立索引的文件中搜尋**。在 HDInsight 叢集的 RDP 工作階段中，開啟 Internet Explorer，並啟動在 Solr 儀表板，網址是：**http://headnodehost:8983/solr/#/**。在左窗格，從核心選取器下拉式清單，選取 **collection1**，並在其中按一下 [**查詢**]。例如，若要選取並傳回 Solr 中的所有文件，請提供下列值：
	1. 在 **q** 文字方塊中，輸入 **\*:**\*。這會傳回 Solr 中已建立索引的所有文件。如果您想要搜尋文件中的特定字串，您可以在這裡輸入該字串。
	2. 在 **wt** 文字方塊中，選取輸出格式。預設值是 **json**。按一下 [**執行查詢**]。

		![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Run a query on Solr dashboard")
	
	輸出會傳回我們用於為 Solr 建立索引的兩個文件。輸出看起來如下：

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **建議：將索引資料從 Solr 備份到與 HDInsight 叢集相關聯的 Azure 儲存體 Blob (WASB)**。很好的作法是，您應該從 Solr 叢集節點將索引資料備份至 WASB。執行下列步驟來執行這項操作：

	1. 從 RDP 工作階段中，開啟 Internet Explorer，並指向下列 URL：

			http://localhost:8983/solr/replication?command=backup

		您應該會看到如下的回應

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. 在遠端工作階段中，瀏覽至 {SOLR_HOME}\{Collection}\data。針對使用範例指令碼建立的叢集，這應該是 **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**。在這個位置，您應該會看到使用類似 **snapshot.*timestamp*** 名稱建立的快照集資料夾。
	
	3. 將快照集資料夾壓縮，並上傳至 WASB。從 Hadoop 命令列，使用下列命令導覽至快照集資料夾的位置：

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		此命令會將快照集複製到與叢集預設儲存體帳戶相關聯的容器下的 /example/data/。

## <a name="usingPS"></a>使用 PowerShell 在 HDInsight Hadoop 叢集上安裝 Solr

在本節中，我們使用 **<a href = "http://msdn.microsoft.com/zh-tw/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** Cmdlet，以使用「指令碼動作」來叫用指令碼以自訂叢集。在繼續之前，請確定您已安裝並設定 PowerShell。如需設定工作站以執行 HDInsight Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。

執行下列步驟：

1. 開啟 Azure PowerShell 視窗，並宣告下列變數：

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. 指定設定值，例如要使用的叢集中節點和預設儲存體。

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. 使用 **Add-AzureHDInsightScriptAction** Cmdlet 將「指令碼動作」新增至叢集設定。稍後，在建立叢集時，將會執行「指令碼動作」。 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

	**Add-AzureHDInsightScriptAction** Cmdlet 可接受下列參數：

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">參數</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">要在其中新增指令碼動作資訊的設定物件</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼動作的名稱</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定執行自訂指定碼的節點。有效值為 HeadNode (在前端節點上安裝) 或 DataNode (在所有資料節點上安裝)。您可以使用其中一個或兩個值。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定執行指令碼的 URI。</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">參數</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼所需的參數。本主題中使用的範例指令碼不需要任何參數，因此您在上述程式碼片段中看不到此參數。
	</td></tr>
	</table>
	
4. 最後，開始佈建已安裝 Solr 的自訂叢集。  
	
		# START PROVISIONING A CLUSTER WITH SOLR INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

出現提示時，請輸入叢集的認證。建立叢集可能需要幾分鐘的時間。


## <a name="usingSDK"></a>使用 .NET SDK 在 HDInsight Hadoop 叢集上安裝 Solr

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 應用程式使用 HDInsight。本節說明如何使用來自 SDK 的指令碼動作來佈建已安裝 Solr 的叢集。必須執行下列程序：

- 安裝 HDInsight .NET SDK
- 建立自我簽署憑證
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**

您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立自我簽署憑證**

建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。如需指示，請參閱[建立自我簽署憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。 


**建立 Visual Studio 應用程式**

1. 開啟 Visual Studio 2013。

2. 在 [檔案] 功能表中按一下 [新增]，然後按一下 [專案]。

3. 在 [新增專案] 中，輸入或選取下列值：
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">屬性</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">值</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">類別</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">範本/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">範本</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">主控台 應用程式</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
	</table>

4. 按一下 [確定] 以建立專案。

5. 在 [工具] 功能表中按一下 [Nuget 封裝管理員]，然後按一下 [封裝管理員主控台]。

6. 在主控台中執行下列命令，以安裝封裝。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令會將 .NET 程式庫及其參考從目前的 Visual Studio 專案加入。

	
7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8. 在檔案頂端新增下列 using 陳述式：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. 在 Main() 函數中，複製及貼上下列程式碼，並提供變數的值：
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. 將下列程式碼附加至 Main() 函數中，以使用 [ScriptAction](http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 類別來叫用自訂指令碼以安裝 Solr。

		// ADD THE SCRIPT ACTION TO INSTALL Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //because the script used does not require any parameters.
        ));

11. 最後，建立叢集。

		client.CreateCluster(clusterInfo);

11. 儲存對應用程式所做的變更，然後建置方案。 

**執行應用程式**

開啟 PowerShell 主控台、瀏覽至您儲存 Visual Studio 專案的位置、瀏覽至專案內的 \bin\debug 目錄，然後執行下列命令：

	.\CreateSolrCluster <cluster-name>

請提供叢集名稱，然後按 ENTER 以佈建已安裝 Solr 的叢集。


## 另請參閱##
- [在 HDInsight 叢集上安裝和使用 Spark ][hdinsight-install-spark]使用叢集自訂在 HDInsight Hadoop 叢集上安裝 Spark。Spark 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。
- [在 HDInsight 叢集上安裝 R][hdinsight-install-r]。使用叢集自訂在 HDInsight Hadoop 叢集上安裝 R。R 是一個用於統計計算的開放原始碼語言和環境，提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。它也提供廣泛的圖形功能。
- [在 HDInsight 叢集上安裝 Giraph](../hdinsight-hadoop-giraph-install)。使用叢集自訂在 HDInsight Hadoop 叢集上安裝 Giraph。Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。




[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
<!--HONumber=42-->
