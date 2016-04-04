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
	ms.date="02/29/2016" 
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

下列指令碼動作可以用來在 Linux 型的 HDInsight 叢集上安裝 Hue。https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    
本節提供如何在使用 Azure 入口網站佈建叢集時使用指令碼的指示。

> [AZURE.NOTE] Azure PowerShell、HDInsight .NET SDK 或 Azure Resource Manager 範本也可用來套用指令碼動作。您也可以將指令碼動作套用到執行中的叢集上。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

1. 使用[在 Linux 上佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)中的步驟開始佈建叢集，但是不完成佈建。

	> [AZURE.NOTE] 若要在 HDInsight 叢集上安裝色調，建議的 HeadNode 大小為至少 A4 (8 核心、14 GB 記憶體)。

2. 在 [選用組態] 刀鋒視窗中，選取 [指令碼動作]，並提供如下所示的資訊：

	![提供 Hue 的指令碼動作參數](./media/hdinsight-hadoop-hue-linux/hue_script_action.png "提供 Hue 的指令碼動作參數")

	* __名稱__：輸入指令碼動作的易記名稱。
	* __指令碼 URI__：https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
	* __前端__：勾選此選項。
	* __背景工作角色__：將此選項保留空白。
	* __ZOOKEEPER__：將此選項保留空白。
	* __參數__：將此選項保留空白。

3. 在 [指令碼動作] 底部，使用 [選取] 按鈕以儲存組態。最後，使用 [選用組態] 刀鋒視窗底部的 [選取] 按鈕，儲存選用組態資訊。

4. 繼續如[在 Linux 上佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)中所述佈建叢集。

## 搭配使用色調與 HDInsight 叢集

執行色調之後，SSH 通道是在叢集上存取色調的唯一方式。透過 SSH 的通道允許直接至在其中執行色調之叢集的前端節點的流量。叢集完成佈建之後，請使用下列步驟，在 HDInsight Linux 叢集上使用色調。

1. 請以[使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md) 中的資訊，建立用戶端系統至 HDInsight 叢集的 SSH 通道，然後設定網頁瀏覽器以便將 SSH 通道當做 Proxy 使用。

2. 一旦您建立了 SSH 通道，並設定您的瀏覽器將流量以 Proxy 通過該通道傳送後，您必須找到前端節點的主機名稱。使用下列步驟從 Ambari 取得這項資訊：

    1. 在瀏覽器中，移至 https://CLUSTERNAME.azurehdinsight.net。出現提示時，使用系統管理員使用者名稱和密碼來向網站進行驗證。
    
    2. 在頁面頂端的功能表中，選取 [主機]。
    
    3. 選取以 __hn0__ 開頭的項目。當頁面開啟時，主機名稱會顯示在頂端。主機名稱的格式為 __hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__。這是您連接到色調時必須使用的主機名稱。

2. 一旦您建立了 SSH 通道，並設定您的瀏覽器透過該通道代理流量，即可使用此瀏覽器來開啟色調入口網站，網址為 http://HOSTNAME:8888。以您在先前步驟從 Ambari 取得的名稱取代 HOSTNAME：

    > [AZURE.NOTE] 當您第一次登入時，系統會提示您建立帳戶來登入色調入口網站。您在此處指定的認證會限制為入口網站，並且與佈建叢集時您指定的系統管理員或 SSH 使用者認證不相關。

	![登入色調入口網站](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "指定色調入口網站的認證")

### 執行 HIVE 查詢

1. 從色調入口網站中，按一下 [查詢編輯器]，然後按一下 [Hive] 開啟 Hive 編輯器。

	![使用 Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "使用 Hive")

2. 在 [協助] 索引標籤中，於 [資料庫] 底下，您應該會看到 **hivesampletable**。這是 HDInsight 上的所有 Hadoop 叢集隨附的範例資料表。在右窗格中輸入範例查詢，然後在下方窗格的 [結果] 索引標籤中查看輸出，如螢幕擷取畫面所示。

	![執行 Hive 查詢](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "執行 Hive 查詢")

	您也可以使用 [圖表] 索引標籤來查看結果的視覺表示。

### 瀏覽叢集儲存體

1. 從色調入口網站中，按一下功能表列右上角的 [檔案瀏覽器]。

2. 根據預設，檔案瀏覽器會在 **/user/myuser** 目錄中開啟。按一下路徑中使用者目錄前面的正斜線，以移至與叢集相關聯的 Azure 儲存體容器的根目錄。

	![使用檔案瀏覽器](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "使用檔案瀏覽器")

3. 以滑鼠右鍵按一下檔案或資料夾，以查看可用的作業。使用右邊的 [上傳] 按鈕，將檔案上傳至目前的目錄。使用 [新增] 按鈕建立新的檔案或目錄。

> [AZURE.NOTE] 色調檔案瀏覽器只會顯示與 HDInsight 叢集相關聯的預設容器的內容。已與叢集相關聯的任何額外儲存體帳戶/容器將無法使用檔案瀏覽器存取。不過，與叢集相關聯的其他容器一律可供 Hive 工作存取。例如，如果您在 Hive 編輯器中輸入 `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` 命令，則您也可以看到其他容器的內容。在這個命令中，**newcontainer** 不是與叢集相關聯的預設容器。

## 重要考量︰

1. 用來安裝色調的指令碼只會在叢集的前端節點 0 上安裝它。

2. 在安裝期間，會重新啟動多個 Hadoop 服務 (HDFS、YARN、MR2、Oozie) 以更新與設定。指令碼完成安裝色調之後，可能需要一些時間讓其他 Hadoop 服務啟動。一開始可能會影響色調的效能。所有服務啟動之後，色調就可以完全正常運作。

3.	色調不了解 Tez 作業，這是 Hive 目前的預設值。如果您想要使用 MapReduce 做為 Hive 執行引擎，請更新指令碼以在您的指令碼中使用下列命令：

		set hive.execution.engine=mr;

4.	使用 Linux 叢集，您就可以在前端節點 0 上執行服務，同時在前端節點 1 上執行資源管理員。使用色調以檢視叢集上「執行中」工作的詳細資料時，此類案例可能會導致錯誤 (如下所示)。不過，您可以在工作完成時檢視工作詳細資料。

	![色調入口網站錯誤](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "色調入口網站錯誤")

	這是由已知問題造成的。因應措施是修改 Ambari，讓作用中的資源管理員也在前端節點 0 上執行。

5.	當 HDInsight 叢集使用 Azure 儲存體 (使用 `wasb://`) 時，色調能了解 WebHDFS。因此，搭配指令碼動作使用的自訂指令碼會安裝 WebWasb，這是針對與 WASB 通訊的 WebHDFS 相容服務。所以，即使在色調入口網站顯示有 HDFS (例如將滑鼠移至 [檔案瀏覽器] 時)，應將它解讀成 WASB。


## 後續步驟

- [在 HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Giraph。Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。

- [在 HDInsight 叢集上安裝 Solr](hdinsight-hadoop-solr-install-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Solr。Solr 可讓您對儲存的資料執行功能強大的搜尋作業。

- [在 HDInsight 叢集上安裝 R](hdinsight-hadoop-r-scripts-linux.md)。在 HDInsight Hadoop 叢集上使用叢集自訂安裝 R。R 是一個用於統計計算的開放原始碼語言和環境。它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。它也提供廣泛的圖形功能。

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0323_2016-->