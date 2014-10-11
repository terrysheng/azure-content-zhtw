<properties linkid="manage-services-hdinsight-howto-mapreduce" urlDisplayName="MapReduce with Hadoop in HDInsight" pageTitle="Use Hadoop MapReduce in HDInsight | Azure" metaKeywords="" description="Learn how to use HDInsight to execute a simple Hadoop MapReduce job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop MapReduce in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# 在 HDInsight 上使用 Hadoop MapReduce

Hadoop MapReduce 是一種可撰寫應用程式來處理大量資料的軟體架構。在此教學課程中，您要使用您工作站中的 Azure PowerShell，將用來計算文字中單字出現次數的 MapReduce 程式提交至 HDInsight 叢集。該字數統計程式是以 Java 撰寫，且該程式隨附於 HDInsight 叢集。

**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：

-   HDInsight 叢集。如需各種建立此類叢集方式的相關指示，請參閱[佈建 HDInsight 叢集][]。

-   已安裝並設定 Azure PowerShell 的工作站。如需指示，請參閱[安裝並設定 Azure PowerShell][]。

**預估完成時間：** 30 分鐘

## 本教學課程內容

1.  [了解案例][]
2.  [使用 Azure PowerShell 執行範例][]
3.  [用來統計字數的 MapReduce 程式的 Java 程式碼][]
4.  [後續步驟][]

## <span id="scenario"></span></a>了解案例

下圖說明 MapReduce 如何處理字數統計案例：

![HDI.WordCountDiagram][]

MapReduce 工作的輸出是一組機碼值組。機碼是指定一個單字的字串，值是指定文字中該單字出現總次數的整數。此作業有兩個階段：

-   對應程式從輸入的文字中取得每一行當作一個輸入，然後將其打散成單字。每當單字後面接 1 的工作發生時，該程式就會發出機碼值組。此輸出會先排序，再傳送至 Reducer。

-   然後 Reducer 會將每個單字的這些個別計數總和，並發出單一機碼值組，其中包含該單字，後面接出現次數的總和。

執行 MapReduce 工作需要下列元素：

-   MapReduce 程式。在本教學課程中，您將使用 HDInsight 叢集所提供的字數統計範例，因此您不需要自行撰寫。它位在 */example/jars/hadoop-examples.jar*。在 3.0 版 HDInsight 叢集上，該檔案名稱為 *hadoop-mapreduce-examples.jar*。如需自行撰寫 MapReduce 工作的指示，請參閱[開發 HDInsight 的 Java MapReduce 程式][]。
-   輸入檔。您將使用 */example/data/gutenberg/davinci.txt* 作為輸入檔。如需上傳檔案的詳細資訊，請參閱[將資料上傳到 HDInsight][]。
-   輸出檔資料夾。您將使用 */example/data/WordCountOutput* 作為輸出檔資料夾。如果該資料夾不存在，系統將建立該資料夾。如果資料夾存在，MapReduce 工作將會失敗。如果您要第二次執行 MapReduce 工作，請務必刪除輸出資料夾，或指定其他輸出資料夾。

## <span id="run-sample"></span></a>使用 Azure PowerShell 執行範例

1.  開啟 **Azure PowerShell**。如需有關開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝和設定 Azure PowerShell][安裝並設定 Azure PowerShell]。

2.  在下列命令中設定 2 個變數，然後執行這些命令：

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  執行下列命令，並提供您的 Azure 帳戶資訊：

        Add-AzureAccount

4.  執行下列命令來建立 MapReduce 工作定義：

        # Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar* 隨附於 2.1 版 HDInsight 叢集。在 3.0 版 HDInsight 叢集上，該檔案已重新命名為 *hadoop-mapreduce.jar*。

    HDInsight 叢集發佈提供 hadoop-examples.jar 檔案。MapReduce 工作有兩個引數。第一個是來源檔案名稱，第二個是輸出檔案路徑。HDInsight 叢集發佈提供來源檔案，輸出檔案路徑則是在執行階段時建立。

5.  執行下列命令提交 MapReduce 工作：

        # Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    除了 MapReduce 工作定義，您也提供想要用來執行 MapReduce 工作的 HDInsight 叢集名稱，以及認證。Start-AzureHDInsightJob 是非同步呼叫。若要檢查工作是否完成，請使用 *Wait-AzureHDInsightJob* Cmdlet。

6.  執行下列命令來檢查 MapReduce 工作是否完成：

        Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

7.  執行下列命令來檢查 MapReduce 工作執行時有無任何錯誤：

        # Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**擷取 MapReduce 工作的結果**

1.  開啟 **Azure PowerShell**。
2.  執行下列命令，將目錄切換至 c:\\ root：

        cd \

    預設 Azure Powershell 目錄是 *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*。依預設，您沒有此資料夾的寫入權限。您必須將目錄切換至 C:\\ root 目錄或您有寫入權限的資料夾。

3.  在下列命令中設定 3 個變數，然後執行這些命令：

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name

        $storageAccountName = "<StorageAccountName>"   # Azure storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

        The Azure Storage account is the one you created earlier in the tutorial. The storage account is used to host the Blob container that is used as the default HDInsight cluster file system.  The Blob storage container name usually share the same name as the HDInsight cluster unless you specify a different name when you provision the cluster.

4.  執行下列命令來建立 Azure 儲存體內容物件：

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    在您有多個訂用帳戶，而且預設訂用帳戶不是要使用的訂用帳戶時，可使用 *Select-AzureSubscription* 設定目前的訂用帳戶。

5.  執行下列命令將 Blob 容器的 MapReduce 工作輸出下載至工作站：

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    */example/data/WordCountOutput* 資料夾是您執行 MapReduce 工作時指定的輸出資料夾。*part-r-00000* 是 MapReduce 工作輸出的預設檔案名稱。檔案會以相同資料夾結構下載至本機資料夾。例如，在下列螢幕擷取畫面中，目前的資料夾是 C 根資料夾。檔案會下載至 \*C:\\example\\data\\WordCountOutput\* 資料夾。

6.  執行下列命令來列印 MapReduce 工作輸出檔案：

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    MapReduce 工作會產生一個名為 *part-r-00000* 的檔案，內有文字和字數。指令碼使用 findstr 命令列出包含 *"there"* 的所有文字。

請注意，MapReduce 工作的輸出檔是固定不變的。因此，如果您重新執行此範例，將需要變更輸出檔的名稱。

## <span id="java-code"></span></a>用來統計字數的 MapReduce 程式的 Java 程式碼

以下是用來統計字數之 MapReduce 程式的原始程式碼：

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

## <span id="nextsteps"></span></a>後續步驟

雖然 MapReduce 提供強大的診斷功能，對 Master 而言還是頗具挑戰性。其他語言 (例如 Pig 和 Hive) 提供較簡單的方法來處理儲存在 HDInsight 中的資料。若要深入了解，請參閱下列文章：

-   [Azure HDInsight 使用者入門][]
-   [開發 HDInsight 的 Java MapReduce 程式][]
-   [開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式][]
-   [搭配 HDInsight 使用 Hive][]
-   [搭配 HDInsight 使用 Pig][]
-   [執行 HDInsight 範例][]

  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [安裝並設定 Azure PowerShell]: ../install-and-configure-powershell/
  [了解案例]: #scenario
  [使用 Azure PowerShell 執行範例]: #run-sample
  [用來統計字數的 MapReduce 程式的 Java 程式碼]: #java-code
  [後續步驟]: #next-steps
  [HDI.WordCountDiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif
  [開發 HDInsight 的 Java MapReduce 程式]: ../hdinsight-develop-deploy-java-mapreduce/
  [將資料上傳到 HDInsight]: ../hdinsight-upload-data/
  [Azure HDInsight 使用者入門]: ../hdinsight-get-started/
  [開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [搭配 HDInsight 使用 Hive]: ../hdinsight-use-hive/
  [搭配 HDInsight 使用 Pig]: ../hdinsight-use-pig/
  [執行 HDInsight 範例]: ../hdinsight-run-samples/
