<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="HDInsight 中的 C# 串流字數 Hadoop 範例 | Azure" metaKeywords="hadoop, hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a sample TBD." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The C# streaming wordcount Hadoop sample in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

# HDInsight 中的 C# 串流字數 Hadoop 範例
 
Hadoop 為 MapReduce 提供一個串流 API，可讓您以 Java 以外的語言撰寫 map 和 reduce 函數。本教學課程說明如何以 C# 撰寫使用 Hadoop 串流介面的 MapReduce 程式，以及如何使用 Azure PowerShell Cmdlet 在 Azure HDInsight 上執行那些程式。 

在範例中，mapper 和 reducer 是從 [stdin][stdin-stdout-stderr] 讀取輸入 (循行) 並將輸出發出到 [stdout][stdin-stdout-stderr] 的可執行檔。程式會計算內容中的所有文字。

在已為 mappers 指定可執行檔的情況下，當 **mapper** 初始化時，每個 mapper 工作都會將可執行檔啟動成為個別的處理程序。當 mapper 工作執行時，它會將其輸入傳換成行，並將這些行饋送至處理程序的 [stdin][stdin-stdout-stderr]。在此同時，mapper 會從處理程序的 stdout 收集行導向輸出，並將每一行傳換成索引鍵/值組，而且會收集此類內容做為 mapper 的輸出。根據預設，從一行的前置詞一直到第一個定位字元即是索引鍵，行的其餘部分 (不包含定位字元) 則為值。如果行中沒有定位字元，則整行都會被視為索引鍵，而值則為 null。 

在已為 reducer 指定可執行檔的情況下，當 **reducer** 初始化時，每個 reducer 工作都會將可執行檔啟動成為個別的處理程序。當 reducer 工作執行時，它會將其輸入索引鍵/值組傳換成行，並將這些行饋送至處理程序的 [stdin][stdin-stdout-stderr]。在此同時，reducer 會從處理程序的 [stdout][stdin-stdout-stderr] 收集行導向輸出，並將每一行傳換成索引鍵/值組，而這會被收集來做為 reducer 的輸出。根據預設，從一行的前置詞一直到第一個定位字元即是索引鍵，行的其餘部分 (不包含定位字元) 則為值。 

如需有關 Hadoop 串流介面的詳細資訊，請參閱 [Hadoop 串流][hadoop-streaming] (英文)。 
 
**您將了解：**	
	
* Ausführen eines C#-Streamingprogramms zur Analyse von Daten in einer Datei in HDInsight mithilfe von Azure PowerShell.		
* 如何撰寫使用 Hadoop 串流介面的 C# 程式碼。


**必要條件**：	

- 您必須具有 Azure 帳號。如需帳號註冊方式的相關資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/zh-tw/pricing/free-trial/) 頁面。

- 您必須已佈建 HDInsight 叢集。如需各種建立此類叢集方式的相關指示，請參閱[佈建 HDInsight 叢集](../hdinsight-provision-clusters/)

- 您必須已安裝 Azure PowerShell 並加以設定，使其可用於您的帳號。如需執行此項作業之指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。


##本文內容
本文將說明如何執行範例，介紹適用於 MapReduce 程式的 Java 程式碼、彙總您的學習成果，以及列出部分的後續步驟。其中包含下列幾節。
	
1. [使用 Azure PowerShell 執行範例](#run-sample)	
2. [Hadoop 串流的 C# 程式碼](#java-code)
3. [摘要](#summary)	
4. [後續步驟](#next-steps)	

<h2><a id="run-sample"></a>使用 Azure PowerShell 執行範例</h2>

**執行 MapReduce 工作**

1.	開啟 **Azure PowerShell**。如需有關開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。

3. 在下列命令中設定 2 個變數，然後執行這些命令：
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. 執行下列命令以定義 MapReduce 工作。
 
		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

	參數指定了 mapper 和 reducer 函數，以及輸入檔和輸出檔。
                 
5. 執行下列命令以執行 MapReduce 工作，等待工作完成，然後列印標準錯誤：

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

6. 執行下列命令以顯示字數的結果。

		$subscriptionName = "<SubscriptionName>"   
		$storageAccountName = "<StorageAccountName>" 
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
              
		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary }
      $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
 
		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	請注意，MapReduce 工作的輸出檔是固定不變的。因此，如果您重新執行此範例，將需要變更輸出檔的名稱。
	
<h2><a id="java-code"></a>Hadoop 串流的 C# 程式碼</h2>

MapReduce 程式碼使用 cat.exe 應用程式做為對應介面以將文字串流至主控台，並使用 wc.exe 應用程式做為縮減介面以計算從文件串流的字數。mapper 和 reducer 都會從標準輸入資料流 (stdin) 循行讀取字元並寫入至標準輸出資料流 (stdout)。 



	// The source code for the cat.exe (Mapper). 
	 
	using System;
	using System.IO;
	
	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0])); 
	            }
	
	            string line;
	            while ((line = Console.ReadLine()) != null) 
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}

 

cat.cs 檔案中的 mapper 程式碼會使用 StreamReader 物件將連入資料流的字元讀取至主控台，再由主控台以靜態 Console.Writeline 方法將資料流寫入至標準輸出資料流。


	// The source code for wc.exe (Reducer) is:
	
	using System;
	using System.IO;
	using System.Linq;
	
	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;
	
	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }
	
	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


wc.cs 檔案中的 reducer 程式碼會使用 [StreamReader][streamreader] 物件，從已經由 cat.exe mapper 輸出的標準輸入資料流讀取字元。由於它是使用 [Console.Writeline][console-writeline] 方法來讀取字元，因此它會透過計算每個文字結尾的空格和行結尾字元來計算字數，然後使用 [Console.Writeline][console-writeline] 方法將總計寫入至標準輸出資料流。 

<h2><a id="summary"></a>摘要</h2>

在本教學課程中，您已看到如何使用 Hadoop 串流在 HDInsight 部署 MapReduce 工作。

<h2><a id="next-steps"></a>後續步驟</h2>

關於執行其他範例的教學課程，以及如何以 Azure PowerShell 在 Azure HDInsight 上使用 Pig、Hive 和 MapReduce 工作的指示，請參閱下列主題：

* [Azure HDInsight 使用者入門][hdinsight-get-started]
* [範例：Pi 估算器][hdinsight-sample-pi-estimator]
* [範例：字數統計][hdinsight-sample-wordcount]
* [範例：10GB GraySort][hdinsight-sample-10gb-graysort]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [搭配 HDInsight 使用 Hive][hdinsight-use-hive]
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/zh-tw/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/zh-tw/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/zh-tw/library/3x292kth(v=vs.110).aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/



<!--HONumber=35_1-->
