<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight-Hadoop" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for Hadoop in HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for Hadoop in HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# 在 HDInsight 上開發 Hadoop 的 Java MapReduce 程式

本教學課程引導您完成一個端對端案例，目的是在 HDInsight Emulator 上開發和測試採用 Java 的字數統計 Hadoop MapReduce 工作，然後在 Azure HDInsight 上部署並執行此工作。

**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：

-   安裝 Azure HDInsight Emulator。如需指示，請參閱＜[開始使用 HDInsight Emulator][開始使用 HDInsight Emulator]＞。
-   在模擬器電腦上安裝 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。
-   取得 Azure 訂用帳戶。如需指示，請參閱＜[購買選項][購買選項]＞、＜[成員優惠][成員優惠]＞或＜[免費試用][免費試用]＞。

## 本文內容

-   [在 Java 中開發字數統計 MapReduce 程式][在 Java 中開發字數統計 MapReduce 程式]
-   [在模擬器上測試程式][在模擬器上測試程式]
-   [將資料檔和應用程式上傳至 Azure Blob 儲存體][將資料檔和應用程式上傳至 Azure Blob 儲存體]
-   [在 Azure HDInsight 上執行 MapReduce 程式][在 Azure HDInsight 上執行 MapReduce 程式]
-   [擷取 MapReduce 結果][擷取 MapReduce 結果]
-   [後續步驟][後續步驟]

## <a name="develop"></a>在 Java 中開發字數統計 MapReduce 程式

字數統計是一個簡單的應用程式，可計算給定輸入集中每個字的出現次數。

**在 Java 中撰寫字數統計 MapReduce 工作**

1.  開啟記事本。
2.  將下列程式複製並貼到記事本中。

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

    請注意，封裝名稱是 **org.apache.hadoop.examples**，類別名稱是 **WordCount**。提交 MapReduce 工作時會用到這些名稱。

3.  將檔案儲存為 **c:\\Tutorials\\WordCountJava\\WordCount.java**。建立資料夾結構 (若不存在的話)。

HDInsight 模擬器隨附 *javac* 編譯器。

**編譯 MapReduce 程式**

1.  開啟命令提示字元。
2.  切換至 **c:\\Tutorials\\WordCountJava** 目錄。這是字數統計 MapReduce 程式的資料夾。
3.  執行下列命令來檢查兩個 jar 檔案是否存在：

        dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
        dir %hadoop_home%\lib\commons-cli-1.2.jar

4.  執行下列命令來編譯程式：

        C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

    javac 位於 C:\\Hadoop\\java\\bin 資料夾中。最後一個參數是目前資料夾中的 java 程式。編譯器會在目前資料夾中建立 3 個類別檔案。

5.  執行下列命令來建立 jar 檔案：

        C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

    此命令會在目前資料夾中建立 WordCount.jar 檔案。

    ![HDI.EMulator.WordCount.Compile][HDI.EMulator.WordCount.Compile]

## <a name="test"></a>在模擬器上測試程式

在模擬器上測試 MapReduce 工作時需要執行下列程序：

1.  將資料檔案上傳至模擬器上的 HDFS
2.  提交字數統計 MapReduce 工作
3.  檢查工作狀態
4.  擷取工作結果

依預設，HDInsight 模擬器會使用 HDFS 做為預設檔案系統。您也可以選擇設定 HDInsight 模擬器來使用 Azure Blob 儲存體。如需詳細資料，請參閱＜[開始使用 HDInsight Emulator][1]＞。

本教學課程中，您將使用 HDFS *copyFromLocal* 命令將資料檔案上傳至 HDFS。下一節說明如何使用 Azure PowerShell 將檔案上傳至 Azure Blob 儲存體。如需有關將檔案上傳至 Azure Blob 儲存體的其他方法，請參閱＜[將資料上傳到 HDInsight][將資料上傳到 HDInsight]＞。

本教學課程使用下列 HDFS 資料夾結構：

<table border="1">
<tr><td>資料夾</td><td> 注意</td></tr>
<tr><td>/WordCount</td><td>字數統計專案的根資料夾。 </td></tr>
<tr><td>/WordCount/Apps</td><td>對應器和歸納器可執行檔的資料夾。</td></tr>
<tr><td>/WordCount/Input</td><td>MapReduce 來源檔案資料夾。</td></tr>
<tr><td>/WordCount/Output</td><td>MapReduce 輸出檔案資料夾。</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>工作輸出資料夾。</td></tr>
</table>

本教學課程使用 %hadoop\_home% 目錄中的 .txt 檔案做為資料檔案。

> [WACOM.NOTE] Hadoop HDFS 命令區分大小寫。

**將資料檔案複製到模擬器 HDFS**

1.  從桌面開啟 Hadoop 命令列。Hadoop 命令是由模擬器安裝程式來安裝。
2.  從 Hadoop 命令列視窗中，執行下列命令來建立輸入檔案的目錄：

        hadoop fs -mkdir /WordCount/Input

    這裡使用的路徑是相對路徑。此命令相當於：

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  執行下列命令將一些文字檔案複製到 HDFS 上的輸入資料夾：

        hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

    MapReduce 工作將計算這些檔案中的字數。

4.  使用下列命令來列出並驗證已上傳的檔案：

        hadoop fs -ls /WordCount/Input

    您會看到大約八個 .txt 檔案。

**使用 Hadoop 命令列執行 MapReduce 工作**

1.  從桌面開啟 Hadoop 命令列。
2.  執行下列命令從 HDFS 中刪除 /WordCount/Output 資料夾結構。/WordCount/Output 是字數統計 MapReduce 工作的輸出資料夾。如果此資料夾已存在，MapReduce 工作會失敗。如果是第二次執行工作，則這是必要的步驟。

        hadoop fs -rmr /WordCount/Output

3.  執行以下命令：

        hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    如果工作順利完成，應該會得到類似下列螢幕擷取畫面的輸出：

    ![HDI.EMulator.WordCount.Run][HDI.EMulator.WordCount.Run]

    從螢幕擷取畫面中，您可以看到對應和歸納都已 100% 完成。其中也列出工作識別碼 job\_201312092021\_0002。從桌面開啟 [Hadoop MapReduce 狀態] 捷徑並尋找工作識別碼，也可擷取同樣的報告。

另一個執行 MapReduce 工作的方法是使用 Azure PowerShell。如需指示，請參閱＜[開始使用 HDInsight Emulator][開始使用 HDInsight Emulator]＞。

**從 HDFS 顯示輸出**

1.  開啟 Hadoop 命令列。
2.  執行下列命令來顯示輸出：

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    您可以在命令尾端附加 "|more" 來取得頁面檢視。或者，使用 findstr 命令來尋找字串模式：

        hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

目前為止，您已開發字數統計 MapReduce 工作，並於模擬器上成功測試。下一步是在 Azure HDInsight 上部署並執行。

## <span id="upload"></span></a>將資料上傳至 Azure Blob 儲存體

Azure HDInsight 使用 Azure Blob 儲存體來儲存資料。佈建 HDInsight 叢集時，Azure Blob 儲存體容器用來儲存系統檔案。您可以使用此預設容器或不同的容器 (在相同的 Azure 儲存體帳戶上，或與叢集相同的資料中心上的不同儲存體帳戶) 來儲存資料檔案。

本教學課程中，您將在另一個儲存體帳戶上建立容器來儲存資料檔案和 MapReduce 應用程式。資料檔案是工作站的 %hadoop\_home% 目錄中的文字檔案。

**建立 Blob 儲存體和容器**

1.  開啟 Azure PowerShell。
2.  設定變數，然後執行命令：

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

    **$subscripionName** 與您的 Azure 訂用帳戶相關聯。您必須命名 **$storageAccountName\_Data** 和 **$containerName\_Data**。關於命名限制，請參閱＜[命名和參考容器、Blob 及中繼資料][命名和參考容器、Blob 及中繼資料]＞。

3.  執行下列命令來建立儲存體帳戶，並在帳戶上建立 Blob 儲存體容器

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  Run the following commands to verify the storage account and the container:

        Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
        Get-AzureStorageContainer -Context $destContext

**上傳資料檔案**

1.  開啟 Azure PowerShell。
2.  設定前三個變數，然後執行命令：

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"

        $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
        $destFolder = "WordCount/Input"

    **$storageAccountName\_Data** 和 **$containerName\_Data** 與您在上一個程序中的定義相同。

    請注意，來源檔案資料夾是 **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**，目的地資料夾是 **WordCount/Input**。

3.  執行下列命令來取得來源檔案資料夾中的 txt 檔案清單：

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  執行下列命令來建立儲存體內容物件：

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  執行下列命令來複製檔案：

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
        }

6.  執行下列命令來列出已上傳的檔案：

        # List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
        Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    您應該會看到大約 8 個文字資料檔案。

**上傳字數統計應用程式**

1.  開啟 Azure PowerShell。
2.  設定前三個變數，然後執行命令：

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"

        $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
        $blobFolder = "WordCount/jars"

    **$storageAccountName\_Data** 和 **$containerName\_Data** 與您在上一個程序中的定義相同，這表示您會將資料檔案和應用程式上傳至相同儲存體帳戶上的相同容器。

    請注意，目的地資料夾是 **WordCount/jars**。

3.  執行下列命令來建立儲存體內容物件：

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  執行下列命令來複製應用程式：

        Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  執行下列命令來列出已上傳的檔案：

        # List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
        Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    您應該會看到這裡列出的 jar 檔案。

## <a name="run"></a>在 Azure HDInsight 上執行 MapReduce 工作

下列 PowerShell 指令碼會執行下列工作：

1.  佈建 HDInsight 叢集

    1.  建立儲存體帳戶做為預設 HDInsight 叢集檔案系統
    2.  建立 Blob 儲存體容器
    3.  建立 HDInsight 叢集

2.  提交 MapReduce 工作

    1.  建立 MapReduce 工作定義
    2.  提交 MapReduce 工作
    3.  等待工作完成
    4.  顯示標準錯誤
    5.  顯示標準輸出

3.  刪除叢集

    1.  刪除 HDInsight 叢集
    2.  刪除做為預設 HDInsight 叢集檔案系統的儲存體帳戶

**執行 PowerShell 指令碼**

1.  開啟記事本。
2.  複製並貼上下列程式碼：

        # The storage account and the HDInsight cluster variables
        $subscriptionName = "<AzureSubscriptionName>"
        $serviceNameToken = "<ServiceNameTokenString>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = <NumberOFNodesInTheCluster>

        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"

        $clusterName = $serviceNameToken + "hdicluster"

        $storageAccountName_Default = $serviceNameToken + "hdistore"
        $containerName_Default =  $serviceNameToken + "hdicluster"

        # The MapReduce job variables
        $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
        $className = "org.apache.hadoop.examples.WordCount"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        # Create a PSCredential object. The username and password are hardcoded here.  You can change them if you want.
        $password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 

        Select-AzureSubscription $subscriptionName

        #=============================
        # Create a storage account used as the default file system
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #=============================
        # Create a Blob storage container used as teh default file system
        Write-Host "Create a Blob storage container" -ForegroundColor Green
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

        New-AzureStorageContainer -Name $containerName_Default -Context $destContext

        #=============================
        # Create an HDInsight cluster
        Write-Host "Create an HDInsight cluster" -ForegroundColor Green
        $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

        New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config

        #=============================
        # Create a MapReduce job definition
        Write-Host "Create a MapReduce job definition" -ForegroundColor Green
        $mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus

        #=============================
        # Run the MapReduce job
        Write-Host "Run the MapReduce job" -ForegroundColor Green
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

        #=============================
        # Delete the HDInsight cluster
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Remove-AzureHDInsightCluster -Name $clusterName  

        # Delete the default file system storage account
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  設定指令碼中的前六個變數。**$serviceNameToken** 將做為 HDInsight 叢集名稱、預設儲存體帳戶名稱及預設 Blob 儲存體容器名稱。因為服務名稱必須為 3 到 24 個字元，且指令檔會將最多 10 個字元的字串附加至名稱，所以您必須將字串限制在 14 個字元以下。$serviceNameToken 必須為小寫。**$storageAccountName\_Data** 和 **$containerName\_Data** 是用來儲存資料檔案和應用程式的儲存體帳戶和容器。**$location** 必須符合資料儲存體帳戶位置。
4.  檢閱其餘變數。
5.  儲存指令碼檔案。
6.  開啟 Azure PowerShell。
7.  執行下列命令將執行原則設為 remotesigned：

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  出現提示時，輸入 HDInsight 叢集的使用者名稱和密碼。因為您在指令碼最後會刪除叢集，然後就不再需要使用者名稱和密碼，所以使用者名稱和密碼可以是任何字串。如果不希望出現認證提示，請參閱＜[在 Windows PowerShell 中使用密碼、安全字串和認證][在 Windows PowerShell 中使用密碼、安全字串和認證]＞(英文)

## <a name="retrieve"></a>擷取 MapReduce 工作輸出

本節說明如何下載和顯示輸出。如需有關在 Excel 上顯示結果的詳細資訊，請參閱＜[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight]＞和＜[使用 Power Query 將 Excel 連接到 HDInsight][使用 Power Query 將 Excel 連接到 HDInsight]＞。

**擷取輸出**

1.  開啟 Azure PowerShell 視窗。
2.  切換至 **C:\\Tutorials\\WordCountJava** 目錄。預設 Azure PowerShell 資料夾是 **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**。您執行的 Cmdlet 會將輸出檔案下載至目前的資料夾。您沒有權限將檔案下載至系統資料夾。
3.  執行下列命令來設定值：

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-r-00000"

4.  執行下列命令來建立 Azure 儲存體內容物件：

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

5.  執行下列命令來下載和顯示輸出：

        Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

工作完成之後，您可以選擇使用 [Sqoop][Sqoop] 將資料匯出至 SQL Server 或 Azure SQL 資料庫，或將資料匯出至 Excel。

## <span id="nextsteps"></span></a>後續步驟

本教學課程中，您學到如何開發 Java MapReduce 工作、如何在 HDInsight 模擬器上測試應用程式，以及如何撰寫 PowerShell 指令碼來佈建 HDInsight 叢集並於叢集上執行 MapReduce。若要深入了解，請參閱下列文章：

-   [開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式][開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式]
-   [Azure HDInsight 使用者入門][Azure HDInsight 使用者入門]
-   [開始使用 HDInsight Emulator][開始使用 HDInsight Emulator]
-   [在 HDInsight 上使用 Azure Blob 儲存體][在 HDInsight 上使用 Azure Blob 儲存體]
-   [使用 PowerShell 管理 HDInsight][使用 PowerShell 管理 HDInsight]
-   [將資料上傳到 HDInsight][將資料上傳到 HDInsight]
-   [搭配 HDInsight 使用 Hive][搭配 HDInsight 使用 Hive]
-   [搭配 HDInsight 使用 Pig][搭配 HDInsight 使用 Pig]
-   [使用 Power Query 將 Excel 連接到 HDInsight][使用 Power Query 將 Excel 連接到 HDInsight]
-   [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight]

  [開始使用 HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [購買選項]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
  [在 Java 中開發字數統計 MapReduce 程式]: #develop
  [在模擬器上測試程式]: #test
  [將資料檔和應用程式上傳至 Azure Blob 儲存體]: #upload
  [在 Azure HDInsight 上執行 MapReduce 程式]: #run
  [擷取 MapReduce 結果]: #retrieve
  [後續步驟]: #nextsteps
  [HDI.EMulator.WordCount.Compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [將資料上傳到 HDInsight]: ../hdinsight-upload-data/
  [HDI.EMulator.WordCount.Run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png
  [命名和參考容器、Blob 及中繼資料]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd135715.aspx
  [在 Windows PowerShell 中使用密碼、安全字串和認證]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [使用 Power Query 將 Excel 連接到 HDInsight]: ../hdinsight-connect-excel-power-query/
  [Sqoop]: ../hdinsight-use-sqoop/
  [開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Azure HDInsight 使用者入門]: ../hdinsight-get-started/
  [在 HDInsight 上使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [使用 PowerShell 管理 HDInsight]: ../hdinsight-administer-use-powershell/
  [搭配 HDInsight 使用 Hive]: ../hdinsight-use-hive/
  [搭配 HDInsight 使用 Pig]: ../hdinsight-use-pig/
