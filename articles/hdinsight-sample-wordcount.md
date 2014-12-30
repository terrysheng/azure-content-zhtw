<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="在 HDInsight 中執行 Hadoop MapReduce 字數統計範例 | Azure" metaKeywords="hdinsight, hdinsight sample, hadoop, mapreduce" description="Run a MapReduce word count example on an Hadoop cluster in HDInsight. The program, written in Java, counts word occurrences in a text file." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The WordCount Hadoop sample in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

#在 HDInsight 的 Hadoop 叢集上執行 MapReduce 字數統計範例
 
本教學課程說明如何在 HDInsight 的 Hadoop 叢集上執行 MapReduce 字數統計範例。此程式是以 Java 撰寫，可統計文字檔中出現的字數，並輸出內含每個文字及其出現頻率配對的新文字檔。本範例中所分析的文字檔，是 The Notebooks of Leonardo Da Vinci 的古騰堡計劃 (Project Gutenberg) 電子書版本。

 
**您將了解：**
		
* 如何使用 Azure PowerShell 在 HDInsight 叢集上執行 MapReduce 程式。
* 如何以 Java 撰寫 MapReduce 程式。


**必要條件**：	

- 您必須具有 Azure 帳戶。如需帳戶註冊方式的相關資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/zh-tw/pricing/free-trial/) 頁面。

- 您必須已佈建 HDInsight 叢集。如需此類叢集之各種建立方式的相關指示，請參閱[開始使用 Azure HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集](../hdinsight-provision-clusters/)

- 您必須已安裝 Azure PowerShell 並加以設定，使其可用於您的帳戶。如需執行此項作業之指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]

##本文內容	
本文將說明如何執行範例，介紹適用於 MapReduce 程式的 Java 程式碼、彙總您的學習成果，以及列出部分的後續步驟。其中包含下列幾節。
	
1. [使用 Azure PowerShell 執行範例](#run-sample)	
2. [適用於 WordCount MapReduce 程式的 Java 程式碼](#java-code)
3. [摘要](#summary)	
4. [後續步驟](#next-steps)	

<h2><a id="run-sample"></a>使用 Azure PowerShell 執行範例</h2> 

**提交 MapReduce 工作**

1.	開啟 **Azure PowerShell**。如需有關開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。

3. 在下列命令中設定 2 個變數，然後執行這些命令：
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
5. 執行下列命令以建立 MapReduce 工作定義：

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

	> [WACOM.NOTE] *hadoop-examples.jar* 隨附於 2.1 版 HDInsight 叢集。在 3.0 版 HDInsight 叢集上，該檔案已重新命名為 *hadoop-mapreduce.jar*。
	
	HDInsight 叢集附有 hadoop-examples.jar 檔案。MapReduce 工作有兩個引數。第一個是來源檔案名稱，第二個是輸出檔案路徑。HDInsight 叢集附有來源檔案，輸出檔案路徑則是在執行階段中建立。

6. 執行下列命令提交 MapReduce 工作：

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	除了 MapReduce 工作定義，您也提供想要用來執行 MapReduce 工作的 HDInsight 叢集名稱。

8. 執行下列命令來檢查 MapReduce 工作執行時有無任何錯誤：	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
		
**擷取 MapReduce 工作的結果**

1. 開啟 **Azure PowerShell**。
2. 在下列命令中設定 3 個變數，然後執行這些命令：

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Azure 儲存體帳戶是您在教學課程稍早建立的帳戶。儲存體帳戶用來代管當做預設 HDInsight 叢集檔案系統的 Blob 容器。Blob 儲存容器名稱通常與 HDInsight 叢集同名，除非您在佈建叢集時指定不同的名稱。

3. 執行下列命令來建立 Azure 儲存體內容物件：
		
		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Auch die Eigenschaften *Select-AzureSubscription* 設定目前的訂用帳戶。 

4. 執行下列命令將 Blob 容器的 MapReduce 工作輸出下載至工作站：

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Auch die Eigenschaften */example/data/WordCountOutput* 資料夾是您執行 MapReduce 工作時指定的輸出資料夾。*part-r-00000* 是 MapReduce 工作輸出的預設檔案名稱。檔案會以相同資料夾結構下載至本機資料夾。例如，在下列螢幕擷取畫面中，目前的資料夾是 C 根資料夾。檔案會下載至 *C:\example\data\WordCountOutput* 資料夾。 

5. 執行下列命令來列印 MapReduce 工作輸出檔案：

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	MapReduce 工作會產生一個名為 *part-r-00000* 的檔案，內有文字和字數。指令碼使用 findstr 命令列出包含 *"there"*的所有文字。

WordCount 指令碼的輸出應會在 cmd 視窗中顯示如下：

![Word frequency results in PowerShell from the Hadoop MapReduce word count example in HDInsight.][image-hdi-sample-wordcount-output]

請注意，MapReduce 工作的輸出檔是固定不變的。因此，如果您重新執行此範例，將需要變更輸出檔的名稱。

<h2><a id="java-code"></a>適用於 WordCount MapReduce 程式的 Java 程式碼</h2>



	package org.apache.hadoop.examples;
	import java.io.IOException;
	import java.util.StringTokenizer;
	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;
	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
	import org.apache.hadoop.util.GenericOptionsParser;

	public class WordCount {

  	public static class TokenizerMapper 
       extends Mapper<Object, Text, Text, IntWritable>{
    
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();
      
    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      	}
      }
  	}
  
  	public static class IntSumReducer 
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values, 
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }
  	}

  	public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
    	}
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  	}
  	}



<h2><a id="summary"></a>摘要</h2>

在此教學課程中，您已了解如何透過 HDInsight 使用 Azure PowerShell 執行 MapReduce 程式，以計算文字檔中的文字出現次數。

<h2><a id="next-steps"></a>後續步驟</h2>

如需執行其他範例，及提供在 Azure HDInsight 上透過 Azure PowerShell 使用 Pig、Hive 和 MapReduce 工作的相關指示，請參閱下列主題：

* [Azure HDInsight 使用者入門][hdinsight-get-started]
* [範例：10GB GraySort][hdinsight-sample-10gb-graysort]
* [範例：Pi 估算器][hdinsight-sample-pi-estimator]
* [範例：C# 串流][hdinsight-sample-cs-streaming]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
* [搭配 HDInsight 使用 Hive][hdinsight-use-hive]
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/zh-tw/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-cs-streaming]: ../hdinsight-sample-csharp-streaming/


[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
 
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png



<!--HONumber=35_1-->
