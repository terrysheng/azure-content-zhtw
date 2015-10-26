<properties
	pageTitle="在 HDInsight Linux 叢集上使用色調與 Hadoop | Microsoft Azure"
	description="了解如何在 HDInsight Linux 上安裝及使用色調和 Hadoop 叢集。"
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
	ms.date="09/11/2015" 
	ms.author="nitinme"/>

# 在 HDInsight Hadoop 叢集上安裝和使用色調

了解如何在 HDInsight Linux 叢集上安裝色調，並且使用通道將要求路由至色調。

## 何謂色調？

色調是一組 Web 應用程式，用來與 Hadoop 叢集互動。您可以使用色調以瀏覽與 Hadoop 叢集 (在 HDInsight 叢集的案例中為 WASB) 相關聯的儲存體、執行 Hive 工作和 Pig 指令碼等等。HDInsight Hadoop 叢集上的色調安裝支援下列元件。

* Beeswax Hive 編輯器
* Pig
* Metastore 管理員
* Oozie
* FileBrowser (與 WASB 預設容器進行通訊)
* 工作瀏覽器


## 使用指令碼動作安裝色調

[https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh) 指令碼動作是用來在 HDInsight 叢集上安裝色調。本節提供有關如何在使用 Azure 入口網站佈建叢集時使用指令碼的指示。

> [AZURE.NOTE]您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，以使用此指令碼建立叢集。如需使用這些方法的詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

1. 使用[在 Linux 上佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)中的步驟開始佈建叢集，但是不完成佈建。

	> [AZURE.NOTE]若要在 HDInsight 叢集上安裝色調，建議的 HeadNode 大小為至少 A4 (8 核心、14 GB 記憶體)。

2. 在 [選用組態] 刀鋒視窗中，選取 [指令碼動作]，並提供下列資訊：

	* __名稱__：輸入指令碼動作的易記名稱。
	* __指令碼 URI__：https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh
	* __HEAD__：勾選此選項
	* __WORKER__：將此選項保留空白。
	* __ZOOKEEPER__：將此選項保留空白。
	* __參數__：指令碼預期 **cluster admin password** 是參數。這是您在佈建叢集時指定的密碼。您必須在單引號中指定密碼。


3. 在 [指令碼動作] 底部，使用 [選取] 按鈕以儲存組態。最後，使用 [選用組態] 刀鋒視窗底部的 [選取] 按鈕，儲存選用組態資訊。

4. 繼續如[在 Linux 上佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)中所述佈建叢集。

## 搭配使用色調與 HDInsight 叢集

執行色調之後，SSH 通道是在叢集上存取色調的唯一方式。透過 SSH 的通道允許直接至在其中執行色調之叢集的前端節點的流量。叢集完成佈建之後，請使用下列步驟，在 HDInsight Linux 叢集上使用色調。

1. 請使用[使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)中的資訊，建立用戶端系統至 HDInsight 叢集的 SSH 通道，然後設定 Web 瀏覽器將 SSH 通道當做 Proxy 使用。

2. 一旦您建立了 SSH 通道，並設定您的瀏覽器將流量以 Proxy 通過它來傳送，即可使用此瀏覽器來開啟色調入口網站，網址為 http://headnode0:8888。

    > [AZURE.NOTE]當您第一次登入時，系統會提示您建立帳戶來登入色調入口網站。您在此處指定的認證會限制為入口網站，並且與佈建叢集時您指定的系統管理員或 SSH 使用者認證不相關。

	![登入色調入口網站](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "指定色調入口網站的認證")

### 執行 HIVE 查詢

1. 從色調入口網站中，按一下 [查詢編輯器]，然後按一下 [Hive] 以開啟 Hive 編輯器。

	![使用 Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "使用 Hive")

2. 在 [協助] 索引標籤中，於 [資料庫] 底下，您應該會看到 **hivesampletable**。這是 HDInsight 上的所有 Hadoop 叢集隨附的範例資料表。在右窗格中輸入範例查詢，然後在下方窗格的 [結果] 索引標籤中查看輸出，如螢幕擷取畫面所示。

	![執行 Hive 查詢](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "執行 Hive 查詢")

	您也可以使用 [圖表] 索引標籤來查看結果的視覺表示。

### 瀏覽叢集儲存體

1. 從色調入口網站中，按一下功能表列右上角的 [檔案瀏覽器]。

2. 根據預設，檔案瀏覽器會在 **/user/myuser** 目錄中開啟。按一下路徑中使用者目錄前面的正斜線，以移至與叢集相關聯的 Azure 儲存體容器的根目錄。

	![使用檔案瀏覽器](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "使用檔案瀏覽器")

3. 以滑鼠右鍵按一下檔案或資料夾，以查看可用的作業。使用右邊的 [上傳] 按鈕，將檔案上傳至目前的目錄。使用 [新增] 按鈕以建立新的檔案或目錄。

> [AZURE.NOTE]色調檔案瀏覽器只會顯示與 HDInsight 叢集相關聯的預設容器的內容。已與叢集相關聯的任何額外儲存體帳戶/容器將無法使用檔案瀏覽器存取。不過，與叢集相關聯的其他容器一律可供 Hive 工作存取。例如，如果您在 Hive 編輯器中輸入命令 `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net`，您也可以看到其他容器的內容。在這個命令中，**newcontainer** 不是與叢集相關聯的預設容器。

## 重要考量︰

1. 用來安裝色調的指令碼只會在叢集的 HEADNODE0 上安裝它。

2. 在安裝期間，會重新啟動多個 Hadoop 服務 (HDFS、YARN、MR2、Oozie) 以更新與設定。指令碼完成安裝色調之後，可能需要一些時間讓其他 Hadoop 服務啟動。一開始可能會影響色調的效能。所有服務啟動之後，色調就可以完全正常運作。

3.	色調不了解 Tez 作業，這是 Hive 目前的預設值。如果您想要使用 MapReduce 做為 Hive 執行引擎，請更新指令碼以在您的指令碼中使用下列命令：

		set hive.execution.engine=mr;

4.	使用 Linux 叢集，您就可以在 HEADNODE0 上執行服務，而在 HEADNODE1 上執行資源管理員。使用色調以檢視叢集上「執行中」工作的詳細資料時，此類案例可能會導致錯誤 (如下所示)。不過，您可以在工作完成時檢視工作詳細資料。

	![色調入口網站錯誤](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "色調入口網站錯誤")

	這是由已知問題造成的。因應措施是修改 Ambari，讓作用中的資源管理員也在 HEADNODE0 上執行。

5.	當 HDInsight 叢集使用 Azure 儲存體 (使用 `wasb://`) 時，色調了解 WebHDFS。因此，搭配指令碼動作使用的自訂指令碼會安裝 WebWasb，這是針對與 WASB 通訊的 WebHDFS 相容服務。所以，即使色調入口網站說 HDFS 在位置上 (像是將滑鼠移至 [檔案瀏覽器])，它應該解譯為 WASB。


## 後續步驟

- [在 HDInsight 叢集上安裝和使用 Spark](hdinsight-hadoop-spark-install-linux.md) 的指示，其中說明如何使用叢集自訂，以在 HDinsight Hadoop 叢集上安裝和使用 Spark。Spark 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。

- [在 HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Giraph。Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。

- [在 HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。Solr 可讓您對儲存的資料執行功能強大的搜尋作業。

- [在 HDInsight 叢集上安裝 R](hdinsight-hadoop-r-scripts-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 R。R 是一個用於統計計算的開放原始碼語言和環境。它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。它也提供廣泛的圖形功能。

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=Oct15_HO3-->