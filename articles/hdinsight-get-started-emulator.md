<properties urlDisplayName="Get Started" pageTitle="開始使用 HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="了解如何使用 HDInsight Emulator for Azure。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="開始使用 HDInsight Emulator" author="nitinme" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="nitinme" />

# 開始使用 HDInsight Emulator

本教學課程協助您開始使用 Microsoft HDInsight Emulator for Azure (先前稱為 HDInsight Server Developer Preview) 中的 Hadoop 叢集。HDInsight Emulator 提供 Hadoop 生態系統 Azure HDInsight 的相同元件。如需詳細資訊，包括部署的版本資訊，請參閱 [Azure HDInsight 提供 Hadoop 的什麼版本？][Azure HDInsight 提供 Hadoop 的什麼版本？] (英文)。

> [WACOM.NOTE] HDInsight Emulator 只包含 Hadoop 叢集。不含 HBase。

HDInsight Emulator 提供 Azure HDInsight 的本機開發環境。如果您熟悉 Hadoop，可以開始透過 HDFS 使用該模擬器。在 HDInsight 中，預設的檔案系統是 Azure Blob 儲存體 (WASB，也就是 Azure Storage - Blob)。所以您最終會使用 WASB 來開發工作。若要使用 WASB 和 HDInsight Emulator，您必須變更 HDInsight Emulator 組態。

> [WACOM.NOTE] HDInsight Emulator 只會使用一個節點部署。

**先決條件**
 開始進行本教學課程之前，您必須具備下列條件：

-   HDInsight Emulator 需要 64 位元版本 Windows。必須滿足下列其中一項需求：

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack1
    -   Windows 8
    -   Windows Server 2012
-   安裝並設定 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。

## 本教學課程內容

-   [安裝 HDInsight Emulator][安裝 HDInsight Emulator]
-   [執行字數統計範例][執行字數統計範例]
-   [執行開始使用範例][執行開始使用範例]
-   [連線至 Azure Blob 儲存體][連線至 Azure Blob 儲存體]
-   [執行 HDInsight PowerShell][執行 HDInsight PowerShell]
-   [後續步驟][後續步驟]

## <a name="install"></a>安裝 HDInsight Emulator

可以透過 Microsoft Web Platform Installer 安裝 Microsoft HDInsight Emulator。

> [WACOM.NOTE] HDInsight Emulator 目前僅支援英文版作業系統。如果已安裝舊版的 Emulator，您必須從 [控制台/程式和功能] 中解除安裝下列兩個元件，再安裝最新版的 Emulator。
>
> -   Microsoft HDInsight Emulator for Windows Azure 或 HDInsight Developer Preview (視安裝何者而定)。
> -   Hortonworks Data Platform
>
> </p>

**安裝 HDInsight Emulator**

1.  開啟 Internet Explorer，然後瀏覽到 [Microsoft HDInsight Emulator for Azure 安裝頁面][Microsoft HDInsight Emulator for Azure 安裝頁面]。
2.  按一下 [立即安裝]。
3.  頁面底端提示 HDINSIGHT.exe 安裝時，按一下 [執行]。
4.  按一下彈出的 [使用者帳戶控制] 視窗中出現的 [是] 按鈕完成安裝。您應該會看見 Web Platform Installer 對話方塊。
5.  按一下頁面底部的 [安裝]。
6.  按一下 [我接受] 同意授權條款。
7.  確認 Web Platform Installer 顯示 [已成功安裝下列產品] 狀態，然後按一下 [完成]。
8.  按一下 [結束] 關閉 Web Platform Installer 視窗。

**驗證 HDInsight Emulator 安裝**

安裝程式應該已經在桌面上安裝 3 個圖示。3 個圖示的連結如下：

-   **Hadoop 命令列**：在 HDInsight Emulator 中執行 MapReduce、Pig 和 Hive 工作的 Hadoop 命令提示字元。

-   **Hadoop 名稱節點狀態**：NameNode 維持 HDFS 中所有檔案的樹狀目錄。它也追蹤 Hadoop 叢集中保留的所有檔案相關的資料。用戶端能夠與 NameNode 進行通訊，以便了解所有檔案的資料節點儲存於何處。

-   **Hadoop Yarn 狀態**：對於叢集之中的節點配置 MapReduce 工作的工作追蹤器。

安裝程式應該也已經安裝多個本機服務。以下是 [服務] 視窗的螢幕擷取畫面：

![HDI.Emulator.Services][HDI.Emulator.Services]

依預設不會啟動 HDInsight Emulator 的相關服務。若要啟動服務，請從 Hadoop 命令列執行 <system drive\>\\hdp 下的 **start\_local\_hdp\_services.cmd**。若要在電腦重新啟動之後自動啟動服務，請執行 **set-onebox-autostart.cmd**。

對於安裝和執行 HDInsight Server 的已知問題，請參閱 [HDInsight Emulator 版本資訊][HDInsight Emulator 版本資訊] (英文)。安裝記錄檔位於 **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**。

## <a name="runwordcount"></a>執行字數統計 MapReduce 工作

現在您已在工作站設定 HDInsight 模擬器，您可以開始執行 MapReduce 工作來測試安裝。您將先上傳一些資料檔到 HDFS，然後執行字數統計 MapReduce 工作，以統計這些檔案中特定單字出現的次數。

字數統計 MapReduce 程式已封裝在 *hadoop-mapreduce-examples-2.4.0.SNAPSHOT.jar* 中。這個 jar 檔位於 *C:\\hdp\\hadoop-2.4.0.SNAPSHOT\\share\\hadoop\\mapreduce* 資料夾。

統計字數的 MapReduce 工作使用兩個引數：

-   輸入資料夾。您將使用 *hdfs://localhost/user/HDIUser* 做為輸入資料夾。
-   輸出資料夾。您將使用 *hdfs://localhost/user/HDIUser/WordCount\_Output* 做為輸出資料夾。輸出資料夾不可以是現有資料夾，否則 MapReduce 工作將失敗。如果您要第二次執行 MapReduce 工作，您必須指定不同的輸出資料夾，或刪除現有的輸出資料夾。

**執行字數統計 MapReduce 工作**

1.  在桌面上，按兩下 **Hadoop 命令列** 開啟 Hadoop 命令列視窗目前資料夾應該是：

        c:\hdp\hadoop-2.4.0.SNAPSHOT

    如果不是，請執行下列命令：

        cd %hadoop_home%

2.  執行下列 Hadoop 命令建立 HDFS 資料夾來儲存輸入及輸出檔案：

        hadoop fs -mkdir /user
        hadoop fs -mkdir /user/HDIUser

3.  執行下列 Hadoop 命令將一些本機文字檔複製到 HDFS：

        hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.SNAPSHOT\share\doc\hadoop\common\*.txt /user/HDIUser

4.  執行下列命令列出 /user/HDIUser 資料夾中的檔案：

        hadoop fs -ls /user/HDIUser

    您應該會看見下列檔案：

        C:\hdp\hadoop-2.4.0.SNAPSHOT>hadoop fs -ls /user/HDIUser
        Found 4 items
        -rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
        -rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
        -rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
        -rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5.  執行下列命令執行字數統計 MapReduce 工作：

        C:\hdp\hadoop-2.4.0.SNAPSHOT> hadoop jar C:\hdp\hadoop-2.4.0.SNAPSHOT\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.SNAPSHOT.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  執行下列命令，從輸出檔列出含有「windows」的字數：

        hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    輸出應該是：

        C:\hdp\hadoop-2.4.0.SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
        windows 4
        windows.        2
        windows/cygwin. 1

如需 Hadoop 命令的詳細資訊，請參閱 [Hadoop 命令手冊][Hadoop 命令手冊] (英文)。

## <a name="rungetstartedsamples"></a> 執行開始使用範例

HDInsight Emulator 安裝提供一些範例，讓使用者開始學習 Windows 上以 Apache Hadoop 為基礎的服務。這些範例涵蓋處理巨量資料集時通常需要的一些工作。透過這些範例可協助您熟悉 MapReduce 程式設計模型及其生態系統相關的概念。

這些範例是按照處理 IIS W3C 記錄資料案例來編排。資料產生工具可用來建立各種大小的資料集並匯入到 HDFS 或 WASB (Azure Blob 儲存體)。如需詳細資訊，請參閱[對於 HDInsight 使用 Azure Blob 儲存體][對於 HDInsight 使用 Azure Blob 儲存體]。MapReduce、Pig 或 Hive 工作接著可以在 PowerShell 指令碼產生的資料頁面上執行。請注意，Pig 和 Hive 指令碼是 MapReduce 上的抽象層，最後會編譯成 MapReduce 程式。使用者可以執行一連串工作，以觀察使用這些不同的技術所產生的效果，以及資料大小如何影響處理工作的執行。

### 本節內容

-   [IIS w3c 記錄資料案例][IIS w3c 記錄資料案例]
-   [載入範例 w3c 記錄資料][載入範例 w3c 記錄資料]
-   [執行 Java MapReduce 工作][執行 Java MapReduce 工作]
-   [執行 Hive 工作][執行 Hive 工作]
-   [執行 Pig 工作][執行 Pig 工作]
-   [重建範例][重建範例]

### <a name="scenarios"></a>IIS w3c 記錄資料案例

w3c 案例會產生三種大小的 IIS W3C 記錄資料並匯入到 HDFS 或 WASB：1MB (小)、500MB (中) 和 2GB (大)。它提供三種工作類型，並且以 C#、Java、Pig 及 Hive 實作各個類型。

-   **totalhits**：計算指定頁面的要求總數
-   **avgtime**：計算對於各頁的一項要求所進行的平均時間 (秒數)
-   **errors**：計算各頁、每小時、對於狀態為 404 或 500 的要求出現的錯誤數

這些範例及其文件不提供關鍵 Hadoop 技術的深入研究或完整實作。使用的叢集只有一個節點，因此，對於這個版本，無法觀察新增更多節點的效果。

### <a name="loaddata"></a>載入範例 W3c 記錄資料

產生資料並匯入到 HDFS 均使用 PowerShell 指令碼 importdata.ps1 完成。

**匯入範例 w3c 記錄資料：**

1.  從桌面開啟 Hadoop 命令列。
2.  切換至 **C:\\hdp\\GettingStarted** 目錄。
3.  執行下列命令產生資料並匯入到 HDFS：

        powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    如果要將資料改為載入 WASB，請參閱[連線至 Azure Blob 儲存體][連線至 Azure Blob 儲存體]。

4.  從 Hadoop 命令列執行下列命令，在 HDFS 列出匯入的檔案：

        hadoop fs -ls -R /w3c

    輸出應該類似如下範例：

        C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
        drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
        drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
        -rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
        drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
        -rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
        drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
        -rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5.  如果您要驗證檔案內容，請執行下列命令，將其中一個資料檔顯示到主控台視窗：

        hadoop fs -cat /w3c/input/small/data_w3c_small.txt

現在您已經建立資料檔案並匯入到 HDFS。您可以開始執行不同的 Hadoop 工作。

### <a name="javamapreduce"></a> 執行 Java MapReduce 工作

MapReduce 是 Hadoop 的基本運算引擎。依預設，它是以 Java 實作，但是另外也有利用 .NET 及 C# Hadoop 串流的範例。執行 MapReduce 工作的語法：

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

jar 檔及原始程式檔位在 C:\\Hadoop\\GettingStarted\\Java 資料夾。

**執行 MapReduce 工作計算網頁點擊數**

1.  開啟 Hadoop 命令列。
2.  切換至 **C:\\hdp\\GettingStarted** 目錄。
3.  執行下列命令在資料夾存在的情況下移除輸出目錄。如果輸出資料夾已經存在，MapReduce 工作將失敗。

        hadoop fs -rm -r /w3c/output

4.  執行以下命令：

        hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

    下表說明命令的元素：

    <table border="1">

    <tr>
    <td>
    參數

    </td>
    <td>
    注意

    </td>
    </tr>

    <tr>
    <td>
    w3c\_scenarios.jar

    </td>
    <td>
    Jar 檔案位於 C:\\hdp\\GettingStarted\\Java 資料夾中。

    </td>
    </tr>

    <tr>
    <td>
    microsoft.hadoop.w3c.TotalHitsForPage

    </td>
    <td>
    該類型可以由下列其中一項取代：

    -   microsoft.hadoop.w3c.AverageTimeTaken
    -   microsoft.hadoop.w3c.ErrorsByPage

    </td>
    </tr>

    <tr>
    <td>
    /w3c/input/small/data\_w3c\_small.txt

    </td>
    <td>
    輸入檔可以由下列項目取代：

    -   /w3c/input/medium/data\_w3c\_medium.txt
    -   /w3c/input/large/data\_w3c\_large.txt

    </td>
    </tr>

    <tr>
    <td>
    /w3c/output

    </td>
    <td>
    這是輸出資料夾名稱。

    </td>
    </tr>

    </table>
    </p>
5.  執行下列命令顯示輸出檔：

        hadoop fs -cat /w3c/output/part-00000

    輸出應該類似：

        c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
        /Default.aspx   3360
        /Info.aspx      1156
        /UserService    1137

    Default.aspx 頁面有 3360 次點擊，依此類推。換成上表中建議的值來重新嘗試執行命令，並觀察輸出如何隨著工作類型和資料大小而改變。

### <a name="hive"></a>執行 Hive 工作

具備高超 SQL 技術的分析師可能對 Hive 查詢引擎感到很熟悉。它提供類似 SQL 的介面和 HDFS 的關聯式資料模型。Hive 採用稱為 HiveQL 的語言，非常類似 SQL。Hive 在 Java 型 MapReduce 架構上提供一個抽象層，Hive 查詢在執行階段會編譯成 MapReduce。

**執行 Hive 工作**

1.  開啟 Hadoop 命令列。
2.  切換至 **C:\\hdp\\GettingStarted** 目錄。
3.  執行下列命令在資料夾存在的情況下移除 **/w3c/hive/input** 資料夾。如果資料夾存在，hive 工作將失敗。

        hadoop fs -rmr /w3c/hive/input

4.  執行下列命令建立 **/w3c/hive/input** 資料夾，然後將資料檔複製到 /hive/input 資料夾：

        hadoop fs -mkdir /w3c/hive
        hadoop fs -mkdir /w3c/hive/input

        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  執行下列命令執行 **w3ccreate.hql** 指令碼檔案。該指令碼建立 Hive 表格，並且將資料載入到 Hive 表格：

        C:\hdp\hive-0.13.0.SNAPSHOT\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    輸出應該類似如下範例：

        Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.SNAPSHOT    /conf/hive-log4j.properties
        OK
        Time taken: 1.137 seconds
        OK
        Time taken: 4.403 seconds
        Loading data to table default.w3c
        Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
        Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
        OK
        Time taken: 2.881 seconds

6.  執行下列命令來執行 **w3ctotalhitsbypage.hql** HiveQL 指令碼檔案。

        C:\hdp\hive-0.13.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

    下表說明命令的元素：

    <table border="1">

    <tr>
    <td>
    檔案

    </td>
    <td>
    說明

    </td>
    </tr>

    <tr>
    <td>
    C:\\hdp\\hive-0.13.0.SNAPSHOT\\bin\\hive.cmd

    </td>
    <td>
    Hive 命令指令碼。

    </td>
    </tr>

    <tr>
    <td>
    C:\\hdp\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql

    </td>
    <td>
    您能夠以下列其中一項取代 Hive 指令碼檔案：

    -   C:\\hdp\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql
    -   C:\\hdp\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql

    </td>
    </tr>
    </p>
    </table>
    w3ctotalhitsbypage.hql HiveQL 指令碼是：

        SELECT filtered.cs_uri_stem,COUNT(*) 
        FROM (
          SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
        ) filtered
        GROUP BY (filtered.cs_uri_stem);

    輸出的結尾應該類似如下範例：

        MapReduce Total cumulative CPU time: 5 seconds 391 msec
        Ended Job = job_1410201800143_0008
        MapReduce Jobs Launched:
        Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
        Total MapReduce CPU Time Spent: 5 seconds 391 msec
        OK
        /Default.aspx   3360
        /Info.aspx      1156
        /UserService    1137
        Time taken: 49.304 seconds, Fetched: 3 row(s)

請注意，在各個工作的第一個步驟中，將建立表格，並且從稍早建立的檔案將資料加入到表格中。您可以使用下列命令查看 HDFS 的 /Hive 節點下，瀏覽建立的檔案。

    hadoop fs -lsr /apps/hive/

### <a name="pig"></a>執行 Pig 工作

Pig 處理使用稱為 *Pig Latin* 的資料流程語言。Pig Latin 抽象提供的資料結構比 MapReduce 豐富，而且對於 Hadoop 執行的工作與 SQL 對於 RDBMS 系統執行的工作相同。

**若要執行 pig 工作：**

1.  開啟 Hadoop 命令列。
2.  將目錄切換至 **C:\\hdp\\GettingStarted** 資料夾。
3.  執行下列命令提交 Pig 工作：

        C:\hdp\pig-0.12.1.SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    下表顯示命令的元素：

    <table border="1">

    <tr>
    <td>
    檔案

    </td>
    <td>
    說明

    </td>
    </tr>

    <tr>
    <td>
    C:\\hdp\\pig-0.12.1.SNAPSHOT\\bin\\pig.cmd

    </td>
    <td>
    Pig 命令指令碼。

    </td>
    </tr>

    <tr>
    <td>
    C:\\hdp\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig

    </td>
    <td>
    您能夠以下列其中一項取代 Pig Latin 指令碼檔案：

    -   C:\\hdp\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig
    -   C:\\hdp\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig

    </td>
    </tr>

    <tr>
    <td>
    /w3c/input/small/data\_w3c\_small.txt

    </td>
    <td>
    您可以使用較大的檔案取代參數：

    </p>
    -   /w3c/input/medium/data\_w3c\_medium.txt
    -   /w3c/input/large/data\_w3c\_large.txt

    </td>
    </tr>
    </table>
    輸出應該類似如下範例：

        (/Info.aspx,1156)
        (/UserService,1137)
        (/Default.aspx,3360)

請注意，由於 Pig 指令碼編譯為 MapReduce 工作，而且可能編譯為多個這類工作，因此您可能看見多個 MapReduce 工作在處理 Pig 工作的過程中執行。

### <a name="rebuild"></a>重建範例

範例目前包含全部所需的二進位檔案，因此不需要建置。如果要變更 Java 或 .NET 範例，您可以使用 msbuild 或內含的 PowerShell 指令碼重建範例。

**重建範例**

1.  開啟 Hadoop 命令列。
2.  執行以下命令：

        powershell -F buildsamples.ps1

## <a name="blobstorage"></a>連線至 Azure Blob 儲存體

HDInsight Emulator 使用 HDFS 做為預設檔案系統。不過，Azure HDInsight 使用 Azure Blob 儲存體做為預設檔案系統。可設定 HDInsight Emulator 來使用 Azure Blob 儲存體，代替本機儲存體。請依照下列指示，在 Azure 中建立儲存體空器，並將它連接到 HDInsight Emulator。

> [WACOM.NOTE] 如需有關 HDInsight 如何使用 Azure Blob 儲存體的詳細資訊，請參閱＜[在 HDInsight 上使用 Azure Blob 儲存體][對於 HDInsight 使用 Azure Blob 儲存體]＞。

開始依照下列指示執行之前，您必須先建立儲存體帳戶。如需相關指示，請參閱[如何建立儲存體帳戶][如何建立儲存體帳戶]。

**建立容器**

1.  登入[管理入口網站][管理入口網站]。
2.  按一下左邊的 [儲存]。您應該會在您的訂用帳戶下看見儲存體帳戶的清單。
3.  從清單中按一下要建立容器的儲存體帳戶。
4.  按一下頁面頂端的 [容器]。
5.  按一下頁面底部的 [新增]。
6.  輸入**名稱**，並選取 [存取]。您可以使用 3 個存取層級的任何一個。預設為 [私人]。
7.  按一下 [確定] 儲存變更。您應該會看見新容器列在入口網站上。

您存取 Azure 儲存體帳戶之前，必須將帳戶名稱和帳戶金鑰新增到組態檔。

**設定 Azure 儲存體帳戶的連線**

1.  在 [記事本] 中開啟 **C:\\hdp\\hadoop-2.4.0.SNAPSHOT\\etc\\hadoop\\core-site.xml**。
2.  在 NotificationsController.cs 檔案的頂端新增下列<property\> 標籤到其他<property\> 標籤旁邊：

        <property>
            <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
            <value><StorageAccountKey></value>
        </property>

    您必須以符合儲存體帳戶資訊的值取代 <storageaccountname\> 及 <storageaccountkey\>。

3.  儲存變更。您不需要重新啟動 Hadoop 服務。

使用下列語法存取儲存體帳戶：

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

例如：

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

## <a name="powershell"></a> 執行 HDInsight PowerShell

HDInsight Emulator 也支援部分 Azure HDInsight PowerShell Cmdlet。這些 Cmdlet 包括：

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

如需提交 Hadoop 工作的詳細資訊，請參閱[以程式設計方式提交 Hadoop 工作][以程式設計方式提交 Hadoop 工作]。如需 HDInsight PowerShell Cmdlet 的詳細資訊，請參閱 [HDInsight Cmdlet 參考文件][HDInsight Cmdlet 參考文件]。

## <a name="nextsteps"></a>後續步驟

在本教學課程中，您安裝 HDInsight Emulator 並執行一些 Hadoop 工作。若要深入了解，請參閱下列文章：

-   [開始使用 Azure HDInsight][開始使用 Azure HDInsight]
-   [開發 HDInsight 的 Java MapReduce 程式][開發 HDInsight 的 Java MapReduce 程式]
-   [開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式][開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式]
-   [HDInsight Emulator 版本資訊][HDInsight Emulator 版本資訊]
-   [討論 HDInsight 的 MSDN 論壇][討論 HDInsight 的 MSDN 論壇]

  [Azure HDInsight 提供 Hadoop 的什麼版本？]: ../hdinsight-component-versioning/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [安裝 HDInsight Emulator]: #install
  [執行字數統計範例]: #runwordcount
  [執行開始使用範例]: #rungetstartedsamples
  [連線至 Azure Blob 儲存體]: #blobstorage
  [執行 HDInsight PowerShell]: #powershell
  [後續步驟]: #nextsteps
  [Microsoft HDInsight Emulator for Azure 安裝頁面]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
  [HDI.Emulator.Services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png
  [HDInsight Emulator 版本資訊]: ../hdinsight-emulator-release-notes/
  [Hadoop 命令手冊]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html
  [對於 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [IIS w3c 記錄資料案例]: #scenarios
  [載入範例 w3c 記錄資料]: #loaddata
  [執行 Java MapReduce 工作]: #javamapreduce
  [執行 Hive 工作]: #hive
  [執行 Pig 工作]: #pig
  [重建範例]: #rebuild
  [如何建立儲存體帳戶]: ../storage-create-storage-account/
  [管理入口網站]: https://manage.windowsazure.com/
  [以程式設計方式提交 Hadoop 工作]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight Cmdlet 參考文件]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn479228.aspx
  [開始使用 Azure HDInsight]: ../hdinsight-get-started/
  [開發 HDInsight 的 Java MapReduce 程式]: ../hdinsight-develop-deploy-java-mapreduce/
  [開發 HDInsight 的 C# Hadoop 串流 MapReduce 程式]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [討論 HDInsight 的 MSDN 論壇]: http://social.msdn.microsoft.com/Forums/en-US/hdinsight
