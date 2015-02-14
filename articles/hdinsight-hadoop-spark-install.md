<properties 
	pageTitle="使用 HDInsight 中的指令碼動作在 Hadoop 叢集上安裝 Spark | Azure" 
	description="了解如何自訂 HDInsight 叢集以安裝 Spark。您將使用指令碼動作組態選項，以使用指令碼來安裝 Spark" 
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

# 在 HDInsight Hadoop 叢集上安裝和使用 Spark 1.0

您可以在 HDInsight 上的 Hadoop 中任何類型的叢集上使用**指令碼動作**叢集自訂來安裝 Spark。只有在建立叢集時，指令碼動作才可讓您執行指令碼以自訂叢集。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-cluster-customize]。

在本主題中，您將學習如何使用「指令碼動作」來安裝 Spark 1.0。安裝 Spark 之後，您也將學習如何在 HDInsight 叢集上執行 Spark 查詢。


## 本文內容

- [什麼是 Spark？](#whatis)
- [如何安裝 Spark？](#install)
- [如何在 HDInsight 中使用 Spark？](#usespark)
- [使用 PowerShell 在 HDInsight Hadoop 叢集上安裝 Spark](#usingPS)
- [使用 .NET SDK 在 HDInsight Hadoop 叢集上安裝 Spark](#usingSDK) 


## <a name="whatis"></a>什麼是 Spark？

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。Spark 的記憶體內計算功能，使其成為機器學習和圖表計算中反覆演算法的絕佳選擇 。

Spark 也可用來執行傳統的磁碟型資料處理。Spark 以避免在中繼階段寫入磁碟的方式，在傳統 MapReduce 架構上做了改進。此外，Spark 與 HDFS 和 WASB 相容，因此可以輕鬆地使用 Spark 來處理現有的資料。 

本主題說明如何自訂 HDInsight 叢集以安裝 Spark。   

## <a name="install"></a>如何安裝 Spark？

您可以從一個唯讀的 Azure 儲存體 Blob 取得在 HDInsight 叢集上安裝 Spark 的範例指令碼，網址為 [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1)。本節提供有關如何在使用 Azure 管理入口網站佈建叢集時使用範例指令碼的指示。 

> [AZURE.NOTE] 範例指令碼只能與 HDInsight 叢集版本 3.1 搭配使用。  如需 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 叢集版本](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-component-versioning/)。

1. 依照[使用自訂選項佈建叢集](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-provision-clusters/#portal)中的說明，使用**自訂建立**選項開始佈建叢集。 
2. 在精靈的 [**指令碼動作**] 頁面上，按一下 [**加入指令碼動作**] 提供有關指令碼動作的詳細資料，如下所示：

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>名稱</td>
			<td>指定指令碼動作的名稱。例如，<b>安裝 Spark</b>。</td></tr>
		<tr><td>指令碼 URI</td>
			<td>指定要叫用來自訂叢集的指令碼的 URI。例如， <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1</i></td></tr>
		<tr><td>節點類型</td>
			<td>指定執行自訂指定碼的節點。您可以選擇<b>所有節點</b>、<b>僅前端節點</b>或僅<b>背景工作節點</b>。
		<tr><td>參數</td>
			<td>如果指令碼要求，請指定參數。安裝 Spark 的指令碼不需要任何參數，因此您可以在此保留空白。</td></tr>
	</table>	

	您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，按一下核取記號以開始佈建叢集。

您也可以使用指令碼在 HDInsight 上使用 PowerShell 或 HDInsight.NET SDK 安裝 Spark。本主題稍後會提供這些程序的指示。

## <a name="usespark"></a>如何在 HDInsight 中使用 Spark？
Spark 提供以 Scala、Python 及 Java 撰寫的 API。您也可以使用互動式 Spark 殼層來執行 Spark 查詢。本節說明如何透過這兩種方法來使用 Spark：

- [使用  Spark 殼層](#sparkshell)
- [使用獨立 Scala 程式](#standalone)

###<a name="sparkshell"></a>使用 Spark 殼層
請執行下列步驟以從互動式 Spark 殼層執行 Spark 查詢。在本節中，我們將對 HDInsight 叢集預設提供的範例資料檔案 (/example/data/gutenberg/davinci.txt) 執行 Spark 查詢。

1. 從「Azure 管理入口網站」，針對您所建立已安裝 Spark 的叢集啟用遠端桌面，然後從遠端連線至叢集。如需相關指示，請參閱 <a href="http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">使用遠端桌面連線到 HDInsight 叢集</a>。

2. 在 RDP 工作階段中，從「桌面」開啟 [Hadoop 命令列]，然後瀏覽至 Spark 的安裝位置，例如 **C:\apps\dist\spark-1.0.2**。


3. 執行下列命令以啟動 Spark 殼層。

		 .\bin\spark-shell --master yarn

	在命令完成執行之後，您應該會看到 Scala 提示。

		 scala>

5. 出現 Scala 提示時，輸入下方所示的 Spark 查詢。此查詢會計算 davinci.txt 檔案中每個單字的出現次數，該檔案位於與叢集關聯之 WASB 儲存體上的 /example/data/gutenberg/ 位置中。

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. 輸出應該如以下所示：

	![Output from running Scala interactive shell in an HDInsight cluster][img-hdi-scala-interactive]
		

7. 輸入 :q 以結束 Scala 提示。

		:q

### <a name="standalone"></a>使用獨立 Scala 程式

在本節中，我們會撰寫 Scala 應用程式，以計算 HDInsight 叢集上預設提供的範例資料檔 (/example/data/gutenberg/davinci.txt) 中含有字母  'a' and 'b' 的行數。若要撰寫獨立 Scala 程式並與已安裝 Spark 的自訂叢集搭配使用，您必須執行下列步驟：

- 撰寫 Scala 程式
- 建置此程式以取得 .jar 檔案
- 在叢集上執行工作

#### 撰寫 Scala 程式
在本節中，您將撰寫一個 Scala 應用程式，用來計算範例資料檔案中含有  'a' and 'b' 的行數。 

1. 開啟文字編輯器，並貼上下列程式碼：


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//location of sample data file on WASB
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. 以 **SimpleApp.scala** 名稱儲存檔案。

#### 建置 Scala 程式
在本節中，您將使用 <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">簡單建置工具</a> (或稱 sbt) 來建置 Scala 程式。sbt 需要 Java 1.6 或更新版本，因此請先確定您已安裝正確版本的 Java，再繼續進行本節。

1. 從 http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html 安裝 sbt。
2. 建立一個稱為 **SimpleScalaApp** 的資料夾，並在此資料夾中建立一個稱為 **simple.sbt** 的檔案。這是包含 Scala 版本、程式庫相依性等相關資訊的組態檔。將下列程式碼貼到 simple.sbt 檔案中並儲存。


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.0.2"



	>[AZURE.NOTE] 請確定在檔案中保留那些空白行。

	
3. 在 [SimpleScalaApp] 資料夾下，建立目錄結構 **\src\main\scala**，然後將您稍早建立的 Scala 程式 (**SimpleApp.scala**) 貼到 \src\main\scala 資料夾下。
4. 開啟命令提示字元，瀏覽至 SimpleScalaApp 目錄，並輸入下列命令：


		sbt package


	在編譯應用程式之後，您會看到 **simpleapp_2.10-1.0.jar** 建立在根 [SimpleScalaApp] 資料夾內的 **\target\scala-2.10** 目錄下。


#### 在叢集上執行工作
在本節中，您將從遠端連線至已安裝 Spark 的叢集，然後複製 SimpleScalaApp 專案的目標資料夾。接著，您將使用 **spark-submit** 命令來提交叢集上的工作。

1. 從遠端連線至已安裝 Spark 的叢集。從您撰寫並建置 SimpleApp.scala 程式的電腦上複製 **SimpleScalaApp\target** 資料夾，並將它貼到叢集上的位置。
2. 在 RDP 工作階段中，從「桌面」開啟 [Hadoop 命令列]，然後瀏覽至您貼上 [target] 資料夾的位置。
3. 輸入下列命令來執行 SimpleApp.scala 程式：


		C:\apps\dist\spark-1.0.2\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. 當程式執行完成時，輸出會顯示在主控台上。


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>使用 PowerShell 在 HDInsight Hadoop 叢集上安裝 Spark

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
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1

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
	
4. 最後，開始佈建已安裝 Spark 的自訂叢集。  
	
		# START PROVISIONING A CLUSTER WITH SPARK INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

出現提示時，請輸入叢集的認證。建立叢集可能需要幾分鐘的時間。


## <a name="usingSDK"></a>使用 .NET SDK 在 HDInsight Hadoop 叢集上安裝 Spark

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 應用程式使用 HDInsight。本節說明如何使用來自 SDK 的「指令碼動作」來佈建已安裝 Spark 的叢集。必須執行下列程序：

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
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

10. 將下列程式碼附加至 Main() 函數中，以使用 [ScriptAction](http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) 類別來叫用自訂指令碼以安裝 Spark。

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1"), // Location of the script to install Spark
		  null //because the script used does not require any parameters.
        ));

11. 最後，建立叢集。

		client.CreateCluster(clusterInfo);

11. 儲存對應用程式所做的變更，然後建置方案。 

**執行應用程式**

開啟 PowerShell 主控台、瀏覽至您儲存 Visual Studio 專案的位置、瀏覽至專案內的 \bin\debug 目錄，然後執行下列命令：

	.\CreateSparkCluster <cluster-name>

請提供叢集名稱，然後按 ENTER 以佈建已安裝 Spark 的叢集。


## 另請參閱##
- [在 HDInsight 叢集上安裝 R][hdinsight-install-r] 說明如何使用叢集自訂，以在 HDInsight Hadoop 叢集上安裝和使用 R。R 是一個用於統計計算的開放原始碼語言和環境，提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。它也提供廣泛的圖形功能。
- [在 HDInsight 叢集上安裝 Giraph](../hdinsight-hadoop-giraph-install)。使用叢集自訂在 HDInsight Hadoop 叢集上安裝 Giraph。Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。
- [在 HDInsight 叢集上安裝 Solr](../hdinsight-hadoop-solr-install)。使用叢集自訂在 HDInsight Hadoop 叢集上安裝 Solr。Solr 可讓您對儲存的資料執行搜尋功能強大的搜尋作業。





[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[powershell-install-configure]: ../install-configure-powershell/
<!--HONumber=42-->
