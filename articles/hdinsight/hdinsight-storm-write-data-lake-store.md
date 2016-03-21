<properties
pageTitle="搭配使用 Azure 資料湖存放區與 Azure HDInsight 上的 Apache Storm"
description="了解如何從 HDInsight 上的 Apache Storm 拓撲將資料寫入到 Azure 資料湖存放區。本文件與相關聯的範例會示範如何使用 HdfsBolt 元件來寫入至資料湖存放區。"
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="01/28/2016"
ms.author="larryfr"/>

#搭配使用 Azure 資料湖存放區與 HDInsight 上的 Apache Storm

Azure 資料湖存放區是 HDFS 相容的雲端儲存體服務，可為資料提供高輸送量、可用性、持久性及可靠性。在本文件中，您將學習如何使用 Java 型 Storm 拓撲，透過 Apache Storm 中所提供的 [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 元件將資料寫入至 Azure 資料湖存放區。

> [AZURE.IMPORTANT] 本文件中使用的範例拓撲需依賴 Storm on HDInsight 叢集隨附的元件，而且可能需要進行修改才能在搭配其他 Apache Storm 叢集使用時使用 Azure 資料湖存放區。

##必要條件

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更新版本
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Azure 訂用帳戶
* Storm on HDInsight 叢集。本文件中包含可使用 Azure 資料湖存放區之叢集的建立資訊。

###設定環境變數

當您在開發工作站上安裝 Java 和 JDK 時可能會設定下列環境變數。不過，您應該檢查它們是否存在，以及它們是否包含您系統的正確值。

* __JAVA\_HOME__ - 應該指向已安裝 Java 執行階段環境 (JRE) 的目錄。例如，在 Unix 或 Linux 散發套件上，它的值應該類似 `/usr/lib/jvm/java-7-oracle`在 Windows 中，它的值應該類似 `c:\Program Files (x86)\Java\jre1.7`。

* __PATH__ - 應該包含下列路徑：

    * __JAVA\_HOME__ (或對等的路徑)
    
    * __JAVA\_HOME\\bin__ (或對等的路徑)
    
    * 已安裝 Maven 的目錄

##拓撲實作

本文件中使用的範例是以 Java 撰寫，並使用下列元件：

* __TickSpout__：產生拓撲中其他元件所使用的資料。

* __PartialCount__：計算 TickSpout 所產生的事件。

* __FinalCount__：彙總來自 PartialCount 的計數資料。

* __ADLStoreBolt__：使用 [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 元件將資料寫入至 Azure 資料湖存放區。

包含此拓撲的專案可從 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下載。

###了解 ADLStoreBolt

ADLStoreBolt 是用於拓撲中寫入至 Azure 資料湖之 HdfsBolt 執行個體的名稱。這不是由 Microsoft 所建立的 HdfsBolt 特殊版本，不過它的確仰賴核心網站組態值，以及 Azure HDInsight 中用來與資料湖通訊的隨附 Hadoop 元件。

具體來說，當您建立 HDInsight 叢集時，您可以將其關聯至 Azure 資料湖存放區。這會將項目寫入至所選取資料湖存放區的核心網站，hadoop-client 和 hadoop-hdfs 等元件會使用這些項目來與資料湖存放區進行通訊。

> [AZURE.NOTE] Microsoft 已為 Apache Hadoop 和 Storm 專案提供可與 Azure 資料湖存放區和 Azure Blob 儲存體進行通訊的程式碼，但其他 Hadoop 和 Storm 散發套件依預設不一定會包含這項功能。

拓撲中的 HdfsBolt 組態如下所示：

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
		.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
      	.withRecordFormat(recordFormat)
      	.withFileNameFormat(fileNameFormat)
      	.withRotationPolicy(rotationPolicy)
      	.withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
如果您已熟悉 HdfsBolt 的使用方式，您會發現除了 URL 不一樣以外，這全都是相當標準的組態。URL 會提供 Azure 資料湖存放區的根目錄路徑。

因為寫入資料湖存放區時是使用 HdfsBolt，並且只需變更 URL，您應該能夠取得使用 HdfsBolt 寫入到 HDFS 或 WASB 的現有拓撲，並輕鬆地將它變更為使用 Azure 資料湖存放區。

##建立 HDInsight 叢集和資料湖存放區

使用[使用 Azure 來搭配使用 HDInsight 與資料湖存放區](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)文件中的步驟建立新的 Storm on HDInsight 叢集。本文件中的步驟將逐步引導您建立新的 HDInsight 叢集和 Azure 資料湖存放區。

> [AZURE.IMPORTANT] 當您建立 HDInsight 叢集時，您必須選取 __Storm__ 做為叢集類型。作業系統可以是 Windows 或 Linux。

##建置和封裝拓撲

1. 從 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store
](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 下載範例專案到開發環境。

2. 在編輯器中開啟 `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` 檔案，然後尋找包含 `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")` 的程式碼行。將 __MYDATALAKE__ 變更為建立 HDInsight 伺服器時所使用的 Azure 資料湖存放區名稱。

3. 從命令提示字元、終端機或 Shell 工作階段，將目錄變更為所下載專案的根目錄，然後執行下列命令來建置和封裝拓撲。

        mvn compile
        mvn package
    
    建置和封裝完成之後，會有名為 `target` 的新目錄，其中包含名為 `StormToDataLakeStore-1.0-SNAPSHOT.jar` 的檔案。這會包含已編譯的拓撲。

##在 Linux 型 HDInsight 上部署和執行

如果您建立了 Linux 型 Storm on HDInsight 叢集，請使用下列步驟來部署和執行拓撲。

1. 使用下列命令將拓撲複製到 HDInsight 叢集。將 __USER__ 取代為建立叢集時所使用的 SSH 使用者名稱。將 __CLUSTERNAME__ 取代為叢集的名稱。

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    出現提示時，請輸入建立叢集的 SSH 使用者時所使用的密碼。如果您使用公開金鑰而非密碼，則可能必須使用 `-i` 參數來指定相對應私密金鑰的路徑。
    
    > [AZURE.NOTE] 如果您使用 Windows 用戶端進行開發，則可能沒有 `scp` 命令。若是如此，您可以使用 `pscp`，其可從下列位置取得：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

2. 上傳完成後，使用下列命令來透過 SSH 連接到 HDInsight 叢集。將 __USER__ 取代為建立叢集時所使用的 SSH 使用者名稱。將 __CLUSTERNAME__ 取代為叢集的名稱。

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    出現提示時，請輸入建立叢集的 SSH 使用者時所使用的密碼。如果您使用公開金鑰而非密碼，則可能必須使用 `-i` 參數來指定相對應私密金鑰的路徑。
    
    > [AZURE.NOTE] 如果您使用 Windows 用戶端進行開發，請依照[從 Windows 使用 SSH 連接至 Linux 型 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) 中的資訊，來取得使用 PuTTY 用戶端連接到叢集的資訊。
    
3. 連線之後，使用下列命令啟動拓撲：

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    這會啟動擁有易記名稱 `datalakewriter` 的拓撲。

##在 Windows 型 HDInsight 上部署和執行

1. 開啟網頁瀏覽器並移至 HTTPS://CLUSTERNAME.azurehdinsight.net，其中 __CLUSTERNAME__ 是 HDInsight 叢集的名稱。出現提示時，提供建立叢集時用於此帳戶的系統管理員使用者名稱 (`admin`) 和密碼。

2. 在 Storm 儀表板中，從 [Jar 檔案] 下拉式清單中選取 [瀏覽]，然後選取 `target` 目錄中的 StormToDataLakeStore-1.0-SNAPSHOT.jar 檔案。針對表單上的其他項目使用下列值：

    * 類別名稱：com.microsoft.example.StormToDataLakeStore
    * 其他參數：datalakewriter
    
    ![Storm 儀表板的影像](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. 選取 [提交] 按鈕來上傳和啟動拓撲。拓撲啟動之後，[提交] 按鈕底下的結果欄位應該會顯示類似下面的資訊：

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##檢視輸出資料

有數種方法能夠檢視資料。在本節中，我們使用 Azure 入口網站和 `hdfs` 命令來檢視資料。

> [AZURE.NOTE] 您應該讓拓撲先執行幾分鐘後再檢查輸出資料，這樣才能讓資料同步至 Azure 資料湖存放區上的數個檔案。

* __從 [Azure 入口網站](https://portal.azure.com)__：在入口網站中，選取搭配 HDInsight 使用的 Azure 資料湖存放區。

    > [AZURE.NOTE] 如果未將資料湖存放區固定至 Azure 入口網站的儀表板，其尋找方式是選取左側清單底部的 [瀏覽]，然後是 [資料湖存放區]，最後再選取該存放區。
    
    從資料湖存放區頂端的圖示，選取 [資料總管]。
    
    ![資料瀏覽圖示](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    接下來，選取 __stormdata__ 資料夾。此時應該會顯示文字檔案清單。
    
    ![文字檔](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    選取其中一個檔案以檢視其內容。

* __從叢集__：如果您已使用 SSH 連接到 HDInsight 叢集 (Linux 叢集) 或連接到遠端桌面 (Windows 叢集)，您可以使用下列命令來檢視資料。將 __DATALAKE__ 取代為資料湖存放區的名稱

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    這會串連目錄中儲存的文字檔案，並顯示類似下面的資訊：
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##停止拓撲

Storm 拓撲會一直執行，直到其停止或叢集遭到刪除。若要停止拓撲，請使用下列資訊。

__若為 Linux 型 HDInsight__：

在連往叢集的 SSH 工作階段中，使用下列命令：

    storm kill datalakewriter

__若為 Windows 型 HDInsight__：

1. 在 [Storm 儀表板] (https://CLUSTERNAME.azurehdinsight.net) 中，選取頁面頂端的 [Storm UI] 連結。

2. Storm UI 載入後，選取 [datalakewriter] 連結。

    ![datalakewriter 的連結](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. 在 [拓撲動作] 區段中，選取 [刪除]，然後選取所出現對話方塊上的 [確定]。

    ![拓撲動作](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## 刪除叢集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##後續步驟

現在，您已了解如何使用 Storm 來寫入至 Azure 資料湖存放區，接下來請探索其他 [HDInsight 的 Storm 範例](hdinsight-storm-example-topology.md)。

<!---HONumber=AcomDC_0309_2016--->