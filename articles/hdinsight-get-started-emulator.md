<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" authors="" />

開始使用 HDInsight Emulator
===========================

本教學課程協助您開始使用 Microsoft HDInsight Emulator for Azure (先前稱為 HDInsight Server Developer Preview)。HDInsight Emulator 提供 Hadoop 生態系統 Azure HDInsight 的相同元件。如需詳細資訊，包括部署的版本資訊，請參閱 [Azure HDInsight 提供 Hadoop 的什麼版本？](http://www.windowsazure.com/en-us/manage/services/hdinsight/howto-hadoop-version/ "HDInsight 元件及版本") (英文)。

HDInsight Emulator 提供 Azure HDInsight 的本機部署環境。如果您熟悉 Hadoop，可以開始透過 HDFS 使用該模擬器。不過，在 HDInsight 中，預設的檔案系統是 Azure Blob 儲存體 (WASB，也就是 Azure Storage - Blobs)，因此，您會想要使用 WASB 開發您的工作。您可以開始使用 Azure 儲存模擬器針對 WASB 進行開發 - 可能只想要使用一小部分的資料 (不需要在 HDInsight Emulator 中進行設定變更，只有儲存體帳戶名稱不同)。然後，您將在本機再次針對 Azure 儲存體測試您的工作，但是只使用一小部分的資料 (需要在 HDInsight Emulator 中進行設定變更)。最後，您準備將工作的運算部分移到 HDInsight，並針對生產資料執行工作。

> [WACOM.NOTE] HDInsight Emulator 只會使用一個節點部署。

關於使用 HDInsight 的教學課程，請參閱[開始使用 Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)。

**先決條件**	
開始進行本教學課程之前，您必須具備下列條件：

-   HDInsight Emulator 需要 64 位元版本 Windows。必須滿足下列其中一項需求：

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack1
    -   Windows 8
    -   Windows Server 2012。
-   安裝並設定 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。

本教學課程內容
--------------

-   [安裝 HDInsight Emulator](#install)
-   [執行字數統計範例](#runwordcount)
-   [執行開始使用範例](#rungetstartedsamples)
-   [連線至 Azure Blob 儲存體](#blobstorage)
-   [執行 HDInsight PowerShell](#powershell)
-   [後續步驟](#nextsteps)

安裝 HDInsight Emulator
-----------------------

可以透過 Microsoft Web Platform Installer 安裝 Microsoft HDInsight Emulator。

> [WACOM.NOTE] HDInsight Emulator 目前僅支援英文版作業系統。

> [WACOM.NOTE] 如果您已經安裝 Microsoft HDInsight Developer Preview，您必須先從 [控制台] 的 [程式和功能] 中將下列兩個元件解除安裝。
><ul><li>HDInsight Developer Preview</li><li>Hortonworks Data Platform Developer Preview</li></ul\>

**安裝 HDInsight Emulator**

1.  開啟 Internet Explorer，然後瀏覽到 [Microsoft HDInsight Emulator for Azure 安裝頁面](http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT)。
2.  按一下 **[立即安裝]**。
3.  頁面底端提示 HDINSIGHT.exe 安裝時，按一下 **[執行]**。
4.  按一下彈出的 **[使用者帳戶控制]** 視窗中出現的 **[是]** 按鈕完成安裝。您應該會看見 Web Platform Installer 4.6 視窗。
5.  按一下頁面底部的 **[安裝]**。
6.  按一下 **[我接受]** 同意授權條款。
7.  確認 Web Platform Installer 顯示 **[已成功安裝下列產品]**，然後按一下 **[完成]**。
8.  按一下 **[結束]** 關閉 Web Platform Installer 4.6 視窗。

    安裝程式應該已經在桌面上安裝 3 個圖示。3 個圖示的連結如下：

    -   **Hadoop 命令列**：在 HDInsight Emulator 中執行 MapReduce、Pig 和 Hive 工作的 Hadoop 命令提示字元。

    -   **Hadoop 名稱節點狀態**：NameNode 維持 HDFS 中所有檔案的樹狀目錄。它也追蹤 Hadoop 叢集中保留的所有檔案相關的資料。用戶端能夠與 NameNode 進行通訊，以便了解所有檔案的資料節點儲存於何處。

    -   **Hadoop MapReduce 狀態**：對於叢集之中的節點配置 MapReduce 工作的工作追蹤器。

    安裝程式應該也已經安裝多個本機服務。以下是 [服務] 視窗的螢幕擷取畫面：

    ![HDI.Emulator.Services](./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png)

    對於安裝和執行 HDInsight Server 的已知問題，請參閱 [HDInsight Emulator 版本資訊](http://gettingstarted.hadooponazure.com/releaseNotes.html) (英文)。安裝記錄檔位於 **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**。

執行字數統計 MapReduce 工作
---------------------------

現在您已經在工作站設定 HDInsight Emulator。您可以執行 MapReduce 工作測試安裝。您將先上傳一些文字檔到 HDFS，然後執行字數統計 MapReduce 工作統計這些檔案的文字頻率。

字數統計 MapReduce 程式已封裝在 *hadoop-examples.jar* 中。這個 jar 檔位於 *C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT* 資料夾。

jar 命令的語法是：

    hadoop jar <jar> [mainClass] args...

您也將使用一些 fs 命令。如需 Hadoop 命令的詳細資訊，請參閱 [Hadoop 命令手冊](http://hadoop.apache.org/docs/r1.1.1/commands_manual.html) (英文)。

字數統計 MapReduce 工作使用兩個引數：輸入資料夾及輸出資料夾。您將使用 *hdfs://localhost/user/HDIUser* 做為輸入資料夾，並使用 *hdfs://localhost/user/HDIUser/WordCount\_Output* 做為輸出目錄。輸出資料夾不可以是現有資料夾，否則 MapReduce 工作將失敗。如果您要第二次執行 MapReduce 工作，您必須指定不同的輸出資料夾，或刪除現有的輸出資料夾。

**執行字數統計 MapReduce 工作**

1.  在桌面上，按兩下 **Hadoop 命令列** 開啟 Hadoop 命令列視窗目前資料夾應該是：

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    如果不是，請執行下列命令：

        cd %hadoop_home%

2.  執行下列 Hadoop 命令設定 HDFS 資料夾儲存輸入及輸出檔案：

        hadoop fs -mkdir /user/HDIUser

3.  執行下列 Hadoop 命令將一些本機檔案複製到 HDFS：

        hadoop fs -copyFromLocal *.txt /user/HDIUser/

4.  執行下列命令列出 /user/HDIUser 資料夾中的檔案：

        hadoop fs -ls /user/HDIUser

    您應該會看見下列檔案：

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
        Found 8 items
        -rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
        -rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
        -rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
        -rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
        -rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
        -rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
        -rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
        -rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5.  執行下列命令執行字數統計 MapReduce 工作：

        hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  執行下列命令從輸出檔列出含「windows」的文字：

        hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    輸出應該是：

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
        rt-r-00000 | findstr "windows"
        windows 12
        windows+java6.  1
        windows.        3

執行開始使用範例
----------------

HDInsight Emulator 安裝提供一些範本讓使用者開始快速學習 Windows 以 Apache Hadoop 為基礎的服務。這些範例涵蓋處理巨量資料集時一般需要的一些工作。使用這些範例能夠熟悉 MapReduce 程式設計模型及其生態系統相關的概念。

這些範例是按照處理 IIS W3C 記錄資料案例進行分類。資料產生工具可用來建立各種大小的資料集並匯入到 HDFS 或 WASB (Azure Blob 儲存體)。如需詳細資訊，請參閱[對於 HDInsight 使用 Azure Blob 儲存體](/en-us/manage/services/hdinsight/howto-blob-store/)。MapReduce、Pig 或 Hive 工作接著可以在 PowerShell 指令碼產生的資料頁面上執行。請注意，使用的 Pig 及 Hive 指令碼兩者均編譯為 MapReduce 程式。使用者可以執行一系列工作自行觀察使用這些不同的技術所產生的效果，以及資料大小對於執行處理工作所產生的效果。

### 本節內容

-   [IIS w3c 記錄資料案例](#scenarios)
-   [載入範例 w3c 記錄資料](#loaddata)
-   [執行 Java MapReduce 工作](#javamapreduce)
-   [執行 Hive 工作](#hive)
-   [執行 Pig 工作](#pig)
-   [重建範例](#rebuild)

### IIS w3c 記錄資料案例

w3c 案例會產生三種大小的 IIS W3C 記錄資料並匯入到 HDFS 或 WASB：1MB、500MB 及 2GB。它提供三種工作類型，並且以 C\#、Java、Pig 及 Hive 實作各個類型。

-   **totalhits**：計算指定頁面的要求總數
-   **avgtime**：計算對於各頁的一項要求所進行的平均時間 (秒數)
-   **errors**：計算各頁、每小時、對於狀態為 404 或 500 的要求出現的錯誤數

這些範例及其文件不提供關鍵 Hadoop 技術的深入研究或完整實作。使用的叢集只有一個節點，因此，對於這個版本，無法觀察新增更多節點的效果。

### 載入範例 W3c 記錄資料

產生資料並匯入到 HDFS 均使用 PowerShell 指令碼 importdata.ps1 完成。

**匯入範例 w3c 記錄資料：**

1.  從桌面開啟 Hadoop 命令列。
2.  執行下列命令將目錄切換為 **C:\\Hadoop\\GettingStarted**：

        cd \Hadoop\GettingStarted

3.  執行下列命令產生資料並匯入到 HDFS：

        powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    如果要將資料改為載入 WASB，請參閱[連線至 Azure Blob 儲存體](#blobstorage)。

4.  從 Hadoop 命令列執行下列命令，在 HDFS 列出匯入的檔案：

        hadoop fs -lsr /w3c

    輸出應該類似如下範例：

        c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
        -rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
        -rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
        -rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5.  執行下列命令向主控台視窗顯示其中一個資料檔案：

        hadoop fs -cat /w3c/input/small/data_w3c_small.txt

現在您已經建立資料檔案並匯入到 HDFS。您可以執行不同的 Hadoop 工作。

### 執行 Java MapReduce 工作

MapReduce 是 Hadoop 的基本運算引擎。依預設，它是以 Java 實作，但是另外也有利用 .NET 及 C\# Hadoop 串流的範例。執行 MapReduce 工作的語法：

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

jar 檔及原始程式檔位在 C:\\Hadoop\\GettingStarted\\Java 資料夾。

**執行 MapReduce 工作計算網頁點擊數**

1.  開啟 Hadoop 命令列。
2.  執行下列命令將目錄切換為 **C:\\Hadoop\\GettingStarted**：

        cd \Hadoop\GettingStarted

3.  執行下列命令在資料夾存在的情況下移除輸出目錄。如果輸出資料夾已經存在，MapReduce 工作將失敗。

        hadoop fs -rmr /w3c/output

4.  執行以下命令：

        hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	下表說明命令的元素:
	<table border="1">
	<tr><td>參數</td><td>注意</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>jar 檔位在 C:\\Hadoop\\GettingStarted\\Java 資料夾。</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>該類型可以由下列其中一項取代：
	<ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
	<li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul></td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>輸入檔可以由下列項目取代：
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul></td></tr>
	<tr><td>/w3c/output</td><td>這是輸出資料夾名稱。</td></tr>
	</table>


5.  執行下列命令顯示輸出檔：

        hadoop fs -cat /w3c/output/part-00000

    輸出應該類似：

        c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130

    因此，Default.aspx 頁面有 3409 次點擊，依此類推。

### 執行 Hive 工作

Hive 查詢引擎感覺就像是具備高超 SQL 技術的分析師。它提供類似 SQL 的介面和 HDFS 的關聯式資料模型。Hive 使用稱為 HiveQL (或 HQL) 的語言，這是 SQL 的方言。

**執行 Hive 工作**

1.  開啟 Hadoop 命令列。
2.  將目錄切換到 **C:\\Hadoop\\GettingStarted** 資料夾
3.  執行下列命令在資料夾存在的情況下移除 **/w3c/hive/input** 資料夾。如果資料夾存在，hive 工作將失敗。

        hadoop fs -rmr /w3c/hive/input

4.  執行下列命令建立 **/w3c/hive/input** 資料夾，並且從工作站將資料檔案複製到 HDFS：

        hadoop fs -mkdir /w3c/hive/input
        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  執行下列命令執行 **w3ccreate.hql** 指令碼檔案。該指令碼建立 Hive 表格，並且將資料載入到 Hive 表格：

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    HiveQL 指令碼是：

        DROP TABLE w3c;

        CREATE TABLE w3c(
         logdate string,
         logtime string,
         c_ip string,
         cs_username string,
         s_ip string,
         s_port string,
         cs_method string,
         cs_uri_stem string,
         cs_uri_query string,
         sc_status int,
         sc_bytes int,
         cs_bytes int,
         time_taken int,
         cs_agent string, 
         cs_Referrer string)
        ROW FORMAT delimited
        FIELDS TERMINATED BY ' ';

        LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    輸出應該類似如下範例：

        c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
        Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
        Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
        OK
        Time taken:0.616 seconds
        OK
        Time taken:0.139 seconds
        Loading data to table default.w3c
        Moved to trash:hdfs://localhost:8020/apps/hive/warehouse/w3c
        OK
        Time taken:0.573 seconds

6.  執行下列命令執行 **w3ctotalhitsbypate.hql** HiveQL 指令碼檔案。

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	下表說明命令的元素：
	<table border="1">
	<tr><td>檔案</td><td>說明</td></tr>
	<tr><td>C:\Hadoop\hive-0.9.0\bin\hive.cmd</td><td>Hive 命令指令碼。</td></tr>
	<tr><td>C:\Hadoop\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td>您能夠以下列其中一項取代 Hive 指令碼檔案：
	<ul>
	<li>C:\Hadoop\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\Hadoop\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>

	</table>


    w3ctotalhitsbypage.hql HiveQL 指令碼是：

        SELECT filtered.cs_uri_stem,COUNT(*) 
        FROM (
          SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
        ) filtered
        GROUP BY (filtered.cs_uri_stem);

    輸出的結尾應該類似如下範例：

        MapReduce Total cumulative CPU time:3 seconds 47 msec
        Ended Job = job_201310291309_0006
        MapReduce Jobs Launched:
        Job 0:Map:1  Reduce:1   Cumulative CPU:3.047 sec   HDFS Read:1058546 HDFS W
        rite:53 SUCCESS
        Total MapReduce CPU Time Spent:3 seconds 47 msec
        OK
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130
        Time taken:34.68 seconds

請注意，在各個工作的第一個步驟中，將建立表格，並且從稍早建立的檔案將資料加入到表格中。您可以使用下列命令查看 HDFS 的 /Hive 節點下，瀏覽建立的檔案。

    hadoop fs -lsr /apps/hive/

### 執行 Pig 工作

Pig 處理使用稱為 *Pig Latin* 的資料流程語言。Pig Latin 抽象提供的資料結構比 MapReduce 豐富，而且對於 Hadoop 執行的工作與 SQL 對於 RDBMS 系統執行的工作相同。

**若要執行 pig 工作：**

1.  開啟 Hadoop 命令列。
2.  將目錄切換到 C:\\Hadoop\\GettingStarted 資料夾。
3.  執行下列命令提交 Pig 工作：

        C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	下表顯示命令的元素：
	<table border="1">
	<tr><td>檔案</td><td>說明</td></tr>
	<tr><td>C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd</td><td>Pig 命令指令碼。</td></tr>
	<tr><td>C:\Hadoop\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td>您能夠以下列其中一項取代 Pig Latin 指令碼檔案：
	<ul>
	<li>C:\Hadoop\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\Hadoop\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>您可以使用較大的檔案取代參數：
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

    輸出應該類似如下範例：

        (/Info.aspx,1115)
        (/UserService,1130)
        (/Default.aspx,3409)

請注意，由於 Pig 指令碼編譯為 MapReduce 工作，而且可能編譯為多個這類工作，因此使用者可能看見多個 MapReduce 工作在處理 Pig 工作的過程中執行。

### 重建範例

範例目前包含全部所需的二進位檔案，因此不需要建立範例。如果要變更 Java 或 .NET 範例，您可以使用 msbuild 或內含的 PowerShell 指令碼重建範例。

**重建範例**

1.  開啟 Hadoop 命令列。
2.  執行以下命令：

        powershell -F buildsamples.ps1

連線至 Azure Blob 儲存體
------------------------

Azure HDInsight 使用 Azure Blob 儲存體做為預設檔案系統。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](/en-us/manage/services/hdinsight/howto-blob-store/)。

可以在 HDInsight Emulator 中設定本機叢集使用 Azure Blob 儲存體，而非本機儲存體。本節涵蓋：

-   連線至儲存體模擬器
-   連線至 Azure Blob 儲存體
-   設定 Azure Blob 儲存體做為 HDInsight Emulator 的預設檔案系統

### 連線至儲存體模擬器

Azure 儲存體模擬器隨附於 [Azure SDK for .NET](http://www.windowsazure.com/en-us/downloads/)。儲存體模擬器不會自動啟動。您必須手動將它啟動。應用程式名稱是 *Azure 儲存體模擬器*。若要啟動/停止模擬器，請以滑鼠右鍵按一下 Windows 系統匣中的藍色 Azure 圖示，然後按一下 [Show Storage Emulator UI]。

> [WACOM.NOTE] 您啟動儲存體模擬器時，可能看見下列錯誤訊息：

> ``` {}
> The process cannot access the file because it is being used by another process.
> ```

> 這是因為其中一個 Hadoop Hive 服務也使用連接埠 10000。若要解決問題，請使用下列程序：
><ol>
<li>使用 services.msc 停止 2 個 Hadoop Hive 服務：Apache Hadoop hiveserver 及 Apache Hadoop Hiveserver2。</li>
<li>啟動 Blob 儲存體模擬器。</li>
<li>重新啟動 2 個 Hadoop Hive 服務。</li>
</ol>


存取儲存體模擬器的語法是：

    wasb://<ContainerName>@storageemulator

例如：

    hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] 如果看見下列錯誤訊息：

> ``` {}
> ls:No FileSystem for scheme:wasb
> ```

> 這是因為您仍然使用 Developer Preview 版本。請按照本文的＜安裝 HDInsight Emulator＞一節中提供的指示，將 Developer Preview 版本解除安裝，然後重新安裝該應用程式。

### 連線至 Azure Blob 儲存體

如需建立儲存體帳戶的指示，請參閱[如何建立儲存體帳戶](/en-us/manage/services/storage/how-to-create-a-storage-account/)。

**建立容器**

1.  登入[管理入口網站](https://manage.windowsazure.com/)。
2.  按一下左邊的 **[儲存]**。您應該會在您的訂閱下看見儲存體帳戶的清單。
3.  從清單中按一下要建立容器的儲存體帳戶。
4.  按一下頁面頂端的 **[容器]**。
5.  按一下頁面底部的 **[新增]**。
6.  輸入**名稱**，並選取 **[存取]**。您可以使用 3 個存取層級的任何一個。預設為 **[私人]**。
7.  按一下 **[確定]** 儲存變更。您應該會看見新容器列在入口網站上。

您存取 Azure 儲存體帳戶之前，必須將帳戶名稱和帳戶金鑰新增到組態檔。

**設定 Azure 儲存體帳戶的連線**

1.  在 [記事本] 中開啟 **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml**。
2.  在另一個 &lt;property\> 標籤旁邊新增下列 &lt;property\> 標籤：

        <property>
            <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
            <value><StorageAccountKey></value>
        </property>

    您必須以符合儲存體帳戶資訊的值取代 &lt;StorageAccountName\> 及 &lt;StorageAccountKey\>。

3.  儲存變更。您不需要重新啟動 Hadoop 服務。

使用下列語法存取儲存體帳戶：

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

例如：

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

### 使用 Azure Blob 儲存體容器做為預設檔案系統

另外，也可以使用 Azure Blob 儲存體容器做為預設檔案系統，這和在 Azure HDInsight 中是相同的情況。

**使用 Azure Blob 儲存體容器設定預設檔案系統**

1.  在 [記事本] 中開啟 **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml**。
2.  找到下列 &lt;property\> 標籤：

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <value>hdfs://localhost:8020</value>
          <description>The name of the default file system.Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

3.  以下列 2 個 &lt;property\> 標籤取代它：

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <!--<value>hdfs://localhost:8020</value>-->
          <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
          <description>The name of the default file system.Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>
            
        <property>
          <name>dfs.namenode.rpc-address</name>
          <value>hdfs://localhost:8020</value>
          <description>A base for other temporary directories.</description>
        </property>

    您必須以符合儲存體帳戶資訊的值取代 &lt;StorageAccountName\> 及 &lt;StorageAccountKey\>。

4.  儲存變更。
5.  在桌面上於提高權限的模式下開啟 Hadoop 命令列 (以系統管理員身分執行)
6.  執行下列命令重新啟動 Hadoop 服務：

        C:\Hadoop\stop-onebox.cmd
        C:\Hadoop\start-onebox.cmd

7.  執行下列命令測試預設檔案系統的連線：

        hadoop fs -ls /

    下列命令將列出同一個資料夾中的內容：

        hadoop fs -ls wasb:///
        hadoop fs -ls wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/
        hadoop fs -ls wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

    若要存取 HDFS，請使用下列命令：

        hadoop fs -ls hdfs://localhost:8020/

執行 HDInsight PowerShell
-------------------------

HDInsight Emulator 支援部分 HDInsight PowerShell Cmdlet。這些 Cmdlet 包括：

-   HDInsight 工作定義 Cmdlet

    -   New-AzureHDInsightSqoopJobDefinition
    -   New-AzureHDInsightStreamingMapReduceJobDefinition
    -   New-AzureHDInsightPigJobDefinition
    -   New-AzureHDInsightHiveJobDefinition
    -   New-AzureHDInsightMapReduceJobDefinition
-   Start-AzureHDInsightJob
-   Get-AzureHDInsightJob
-   Wait-AzureHDInsightJob

以下是提交 Hadoop 工作的範例：

    $creds = Get-Credential (hadoop as username, password can be anything)
    $hdinsightJob = <JobDefinition>
    Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

您將在呼叫 Get-Credential 時看見提示。您必須使用 **hadoop** 做為使用者名稱。密碼可以是任何字串。叢集名稱一律是 **http://localhost:50111**。

如需提交 Hadoop 工作的詳細資訊，請參閱[以程式設計方式提交 Hadoop 工作](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)。如需 HDInsight PowerShell Cmdlet 的詳細資訊，請參閱 [HDInsight Cmdlet 參考文件](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx)。

後續步驟
--------

在本教學課程中，您安裝 HDInsight Emulator 並執行一些 Hadoop 工作。若要深入了解，請參閱下列文章：

-   [開始使用 Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [開發 HDInsight 的 Java MapReduce 程式](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [開發 HDInsight 的 C\# Hadoop 串流 MapReduce 程式](/en-us/manage/services/hdinsight/develop-deploy-hadoop-streaming-jobs/)
-   [HDInsight Emulator 版本資訊](https://gettingstarted.hadooponazure.com/releaseNotes.html)
-   [討論 HDInsight 的 MSDN 論壇](http://social.msdn.microsoft.com/Forums/en-US/hdinsight)

