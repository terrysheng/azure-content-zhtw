<properties
	pageTitle="HDInsight 中的 Hadoop MapReduce 字數統計範例 | Microsoft Azure"
	description="在 HDInsight 的 Hadoop 叢集上執行 MapReduce 字數統計範例。以 Java 撰寫的程式將計算文字在文字檔中出現的次數。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="jgao"/>

#在 HDInsight 的 Hadoop 叢集上執行字數統計 MapReduce 程式

了解如何使用 Azure PowerShell 在 HDInsight 中的 Hadoop 叢集上執行 MapReduce 程式。這個程式是以 Java 撰寫，會統計文字檔中文字出現的次數，然後輸出內含每個文字及其出現頻率的新文字檔。

程式會安裝在叢集上。本教學課程中所分析的文字檔，是 The Notebooks of Leonardo Da Vinci 的古騰堡計劃 (Project Gutenberg) 電子書版本。

> [AZURE.NOTE]本文件中的步驟需要 Windows 用戶端。如需利用 Linux 架構的 HDInsight 叢集從 Linux、OS X 或 Unix 用戶端使用字數統計範例的步驟，請參閱[搭配使用 MapReduce 與 HDInsight 上的 Hadoop 和 SSH](hdinsight-hadoop-use-mapreduce-ssh.md) 或[利用 Curl 搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-hadoop-use-mapreduce-curl.md)。

**如需其他相關文章，請參閱：**

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [在 HDInsight 上開發 Hadoop 的 Java MapReduce 程式](hdinsight-develop-deploy-java-mapreduce.md)
* [在 HDInsight 上提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
* [範例：10GB GraySort][hdinsight-sample-10gb-graysort]
* [範例：Pi 估算器][hdinsight-sample-pi-estimator]
* [範例：C# 串流][hdinsight-sample-cs-streaming]

**必要條件**：

- **HDInsight 叢集**。如需各種建立此類叢集方式的相關指示，請參閱[開始使用 Azure HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)
- **具有 Azure PowerShell 的工作站**。請參閱[安裝和使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。

## 使用 Azure PowerShell 執行範例

**提交 MapReduce 工作**

1. 開啟 **Windows PowerShell ISE**。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。
2. 貼上下列 PowerShell 指令碼：

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. 設定前 3 個變數，並執行指令碼。
		
**擷取 MapReduce 工作的結果**

1. 開啟 **Windows PowerShell ISE**。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。
2. 貼上下列 PowerShell 指令碼：

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	MapReduce 工作會產生一個名為 *part-r-00000* 的檔案，內有文字和字數。指令碼使用 **findstr** 命令列出包含 *"there"* 的所有文字。

WordCount 指令碼的輸出應會在命令視窗中顯示如下：

![來自 HDInsight 中 Hadoop MapReduce 字數統計範例的 PowerShell 文字頻率結果。][image-hdi-sample-wordcount-output]

請注意，MapReduce 工作的輸出檔是固定不變的。因此，如果您重新執行此範例，則需要變更輸出檔的名稱。

##Java 原始程式碼

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

## 後續步驟

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [在 HDInsight 上開發 Hadoop 的 Java MapReduce 程式](hdinsight-develop-deploy-java-mapreduce.md)
* [在 HDInsight 上提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
* [範例：10GB GraySort][hdinsight-sample-10gb-graysort]
* [範例：Pi 估算器][hdinsight-sample-pi-estimator]
* [範例：C# 串流][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->