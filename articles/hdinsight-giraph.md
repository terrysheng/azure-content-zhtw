<properties title="How to use Giraph with HDInsight" pageTitle="如何使用 Apache Giraph 搭配 Azure HDInsight" description="了解如何使用 Apache Giraph 以搭配 Azure HDInsight 執行圖表處理功能" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

#了解如何搭配 Azure HDInsight (Hadoop) 使用 Apache Giraph

[Apache Giraph][giraph] 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。 

圖形可以模型化物件之間的關聯，例如大型網路 (像是網際網路) 上的路由器之間的連線，或社交網路上的人際關係 (有時稱為社交圖形)。圖形處理可讓您分析圖形中物件之間的關聯，例如：

* 根據目前的人際關係找出可能的朋友

* 識別網路中兩台電腦之間的最短路線

* 計算網頁的頁面排名

##您將了解如何

* [建置 Apache Giraph 並部署到 HDInsight 叢集](#build)

* [執行 SimpleShortestPathsComputation 範例](#run)

	如需 Giraph 隨附的其他範例清單，請參閱[封裝 org.apache.giraph.examples] (英文)(https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html)。

* [對您可能遭遇的問題進行疑難排解](#tshoot)

##需求

* Azure HDInsight 叢集 3.0 或 3.1 版

* [Git](http://git-scm.com/)

* Java 1.6

* [Maven](http://maven.apache.org/) 3 或更新版本

##<a id="build"></a>建置和部署 Giraph

Giraph 並未隨附於 HDInsight 叢集，必須從原始檔建置。如需有關建置 Giraph 的詳細資訊，請參閱 [Giraph 儲存機制](https://github.com/apache/giraph)。

1. 目前 (7-14-2014)，Giraph 需要一個修補程式，才能與 HDInsight 所用的 WASB 檔案儲存器一起使用。此修補程式已提交至 Apache Giraph 專案，但尚未得到認可。從 [GIRAPH-930](https://issues.apache.org/jira/browse/GIRAPH-930) 的 __attachments__ 區段下載修補程式，並將它儲存到本機磁碟為 __giraph-930.diff__。

1. 從命令列，使用下列 Git 命令建立 Giraph 儲存機制的複本。

		git clone https://github.com/apache/giraph.git

2. 切換至步驟 2 中由複製作業所建立的 __giraph__ 目錄。

		cd giraph

3. 使用下列命令，將修補程式合併至本機儲存機制。

		git apply giraph-930.diff

	以您在步驟 1 中建立的檔案的檔案取代 __giraph-930.diff__。

3. 使用下列命令，為您的 HDInsight 叢集版本建置 Giraph。

	* __HDInsight 3.0__ (Hadoop 2.2)

			mvn package -Phadoop_0.20.203 - DskipTests

	* __HDInsight 3.1__ (Hadoop 2.4)

			mvn package -Phadoop_0.23 -DskipTests

	建置完成之後，您可以在 __\\giraph\\giraph-examples\\target__ 中找到範例 JAR 檔案。

4. 使用 [Azure PowerShell][aps] 和 [HDInsight-Tools][tools]，將範例 JAR 檔案上傳至 HDInsight 叢集的主要儲存體。

		Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

	請將 __giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar__ 換成上一個步驟中產生的 JAR 檔案的路徑和名稱，將 __clustername__ 換成您的 HDInsight 叢集名稱。例如，如果您以 `-Phadoop_0.20.203` 參數建置封裝，則 JAR 的檔名會包含 __hadoop-0.20.203__。

	指令完成時，JAR 檔案已上傳至 wasb:///example/jars/giraph.jar。

	> [WACOM.NOTE] 如需可將檔案上傳至 HDInsight 的公用程式清單，請參閱[將資料上傳至 HDInsight](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-upload-data/)。

##<a id="run"></a>執行範例

SimpleShortestPathsComputation 可示範基本的 [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) 實作，用於尋找圖表中物件之間最短的路徑。請使用下列步驟來上傳範例資料，使用 SimpleShortestPathsComputation 範例執行工作，然後檢視結果。

> [WACOM.NOTE] 這個範例的來源與其他範例，可在 [release-1.1 分支](https://github.com/apache/giraph/tree/release-1.1) 取得 ([GitHub 儲存機制])(https://github.com/apache/giraph)。

1. 建立名為 __tiny\_graph.txt__ 的新檔案。應該包含下列幾行。

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	此資料描述[有向圖形](http://en.wikipedia.org/wiki/Directed_graph)中物件之間的關聯，採用格式 `[source_id,source_value,[[dest_id], [edge_value],...]]`。每一條線代表一個 __source\_id__ 與一或多個 __dest\_id__ 物件之間的關聯。__edge\_value__ (或權數) 可視為 __source\_id__ 與 __dest\_id__ 之間的連線強度或距離。 

	如果使用值 (或權數) 當做物件之間的距離繪製出來，上述資料可能如下圖所示。

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-giraph\giraph-graph.png)


2. 使用 [Azure PowerShell][aps] 和 [HDInsight-Tools][tools]，將 __tiny\_graph.txt__ 檔案上傳至 HDInsight 叢集的主要儲存體。

		Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

	請將叢集名稱換成您的 HDInsight 叢集名稱。

3. 使用下列 PowerShell 執行 __SimpleShortstPathsComputation__ 範例，並以 __tiny\_graph.txt__ 檔案當做輸入。您必須已安裝和設定 [Azure PowerShell][aps]。

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
		                "-ca", "mapred.job.tracker=headnodehost:9010", `
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
		                "-vip", "wasb:///example/data/tinygraph.txt", `
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
		                "-op",  "wasb:///example/output/shortestpaths", `
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		  -JarFile $jarFile `
		  -ClassName "org.apache.giraph.GiraphRunner" `
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	在上述範例中，請將 __clustername__ 換成您的 HDInsight 叢集名稱。

###檢視結果

當工作完成之後，結果會在 __wasb:///example/out/shotestpaths__ 資料夾中儲存為 __part-m-#####__ 檔案。使用 [Azure PowerShell][aps] 和 [HDInsight-Tools][tools] 下載輸出檔案。

	Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
	Cat example/output/shortestpaths/part*

這將會在目前的目錄中建立 __example/output/shortestpaths__ 目錄結構，並下載以 __part__ 開頭的檔案。然後，__Cat__ Cmdlet 會顯示檔案的內容，應該如下所示。

	0	1.0
	4	5.0
	2	2.0
	1	0.0
	3	1.0

SimpleShortestPathComputation 範例已刻意設計成從物件識別碼 1 開始，尋找前往其他物件的最短路徑。因此，輸出應該會顯示 `destination_id distance`，其中 distance 是物件識別碼 1 與目標識別碼之間經過的邊緣的值 (或權數)。

顯現為圖形後，您可以走過識別碼 1 與其他所有物件之間的最短路徑來驗證結果。請注意，識別碼 1 和識別碼 4 之間的最短路徑為 5。這是 <span style="color:orange">識別碼 1 和 3</span>，以及 <span style="color:red">識別碼 3 和 4</span>。

![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-giraph\giraph-graph-out.png)

##<a id="tshoot"></a>疑難排解

###輸出目錄已存在

Giraph 工作會在執行階段建立指定的輸出目錄。如果目錄已存在，則會出現錯誤指出輸出目錄已存在。

如果一個工作要執行多次，您必須在工作之間移除輸出目錄，或為每一個工作指定不同的輸出目錄。

###<a id="cmd"></a>使用 Hadoop 命令列

本文示範如何透過 PowerShell 執行 Giraph 工作，但您也可以使用 Hadoop 命令列來執行工作。

> [WACOM.NOTE] 只有在使用遠端桌面連接到 HDInsight 叢集之後，才能使用 Hadoop 命令列。
> 
> 只有從 Windows 架構的遠端桌面用戶端，才能對 Azure 計算資源 (例如 HDInsight 叢集) 建立遠端桌面工作階段。

若要連接到 HDInsight 叢集，請執行下列步驟：

1. 使用 [Azure 管理入口網站](https://manage.windowsazure.com)，選取您的 HDInsight 叢集，然後選取 [__組態__]。

2. 在頁面底部，選取 [__啟用遠端__]，並提供用於遠端桌面連線的使用者名稱、密碼和到期日。

3. 處理完啟用遠端桌面的要求之後，頁面底部會出現 __連接__ 這個新項目。請選取此項目來下載遠端桌面工作階段的 .RDP 檔案。

4. 可儲存 .RDP 檔案，或立即開啟以啟動遠端桌面用戶端。在連線過程中，將要求您提供您啟用遠端桌面連線時所使用的使用者名稱和密碼。

5. 連線之後，請使用桌面上的 __Hadoop 命令列__ 圖示來啟動 Hadoop 命令列。

6. 下列範例示範如何將 __giraph.jar__ 檔案複製到叢集前端節點，然後使用 Hadoop 命令列來執行工作。

		hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
		hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2


###舊版的 HDInsight

如果您要搭配舊版的 HDInsight 來使用 Giraph，您必須針對該版本支援的特定 Hadoop 版本來編譯它。請參閱[HDInsight 叢集版本的新功能](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-component-versioning/) 以判斷與您的 HDInsight 版本對應的 Hadoop 版本。

此外，舊版的 HDInsight 可能會要求您從 Hadoop 命令列執行 Giraph 工作。如果您從 PowerShell 執行工作時發生錯誤，請嘗試從 [Hadoop 命令列]執行工作(#cmd)。

##後續步驟

您現在已了解如何使用 Giraph 搭配 HDInsight，接下來請嘗試使用 [Pig][] 和 [Hive][] 搭配 HDInsight。

[giraph]: http://giraph.apache.org
[工具]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/zh-tw/documentation/articles/install-configure-powershell/
[pig]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-use-pig/
[hive]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-use-hive/

<!--HONumber=35.1-->
