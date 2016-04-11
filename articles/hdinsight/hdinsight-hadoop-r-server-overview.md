<properties
	pageTitle="什麼是 HDInsight 中的 R？ HDInsight 中的 R 伺服器簡介 | Microsoft Azure"
	description="什麼是 HDInsight 中的 R 以及如何使用 R 建立巨量資料分析的應用程式。"
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/29/2016"
   ms.author="jeffstok"/>


# 概觀：HDInsight 上的 R 伺服器

有了HDInsight Premium 之後，在 Azure 中建立 HDInsight 叢集時，即可選擇使用 R 伺服器。這項新功能可讓資料科學家、統計學家以及 R 程式設計人員隨其所需存取 HDInsight 上可調整大小的分散式分析方法。您可依據手邊的專案和工作調整叢集大小，並於不再需要時加以終止。這些叢集隸屬於 Azure HDInsight，具備企業級全年無休支援、99.9% 執行時間的 SLA，同時具備與 Azure 生態系統其他元件相整合的彈性。

>[AZURE.NOTE] 只有 HDInsight Premium 提供 R 伺服器，且目前 HDInsight Premium 僅提供給 Hadoop 與 Spark 叢集使用。因此，在 HDInsight 上目前只可搭配 Hadoop 與 Spark 叢集使用 R 伺服器。如需詳細資訊，請參閱 [HDInsight 提供的不同服務等級與 Hadoop 元件](hdinsight-component-versioning.md)。

HDInsight 上的 R 伺服器能為在載入 Azure Blob (WASB) 的大型資料集上進行之 R 型分析，提供最新的功能。因為 R 伺服器的建置基礎為開放原始碼 R，因此您所建置的 R 應用程式可以任意運用 8000 多種開放原始碼 R 封裝，以及 ScaleR 中的常式與 R 伺服器隨附的 Microsoft 巨量資料分析封裝。Premium 叢集的邊緣節點提供便利的登陸區域，可連線到叢集並執行 R 指令碼。有了邊緣節點之後，即可選擇透過使用 ScaleR 的 Hadoop Map Reduce 或 Spark 計算內容，跨邊緣節點伺服器核心或叢集節點，執行 ScaleR 的平行分散式函數。例如，可以透過 [Azure Machine Learning Studio](http://studio.azureml.net) (AML) [Web 服務](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)，下載分析結果所產生的模型或預測，以供內部部署使用或在 Azure 其他位置中運作。

## 開始在 HDInsight 中使用 R

若要在 HDInsight 叢集中納入 R 伺服器，必須在使用 Azure 入口網站建立叢集時，使用 Premium 選項建立 Hadoop 或 Spark 叢集。這兩種叢集類型皆使用相同的組態，其中包括叢集資料節點上的 R 伺服器，以及作為 R 伺服器型分析登陸區域的邊緣節點。請參閱[開始使用 HDInsight 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)，以了解建立叢集的深入逐步解說。

## 資料儲存體選項

HDInsight 叢集的預設儲存體位於 Azure Blob (WASB) 中，並含有與 Blob 容器相對應的 HDFS 檔案系統。如此可確保在分析的過程中，任何上傳至叢集儲存體或寫入叢集儲存體的資料，皆保有永續性。使用 [AzCopy](../storage/storage-use-azcopy/) 公用程式，即可在 Blob 之間輕鬆複製資料。

您也可以將 [Azure 檔案服務](../storage/storage-how-to-use-files-linux.md)作為在邊緣節點上使用的儲存體選項。Azure 檔案服務可讓您將建立於 Azure 儲存體帳戶的檔案共用，掛接至 Linux 檔案系統。如需適用於 HDInsight 叢集 R 伺服器的資料儲存體選項詳細資訊，請參閱[適用於 HDInsight 叢集 R 伺服器的儲存體選項](hdinsight-hadoop-r-server-storage.md)。
  
## 存取叢集上的 R 伺服器

建立含 R 伺服器的叢集之後，即可透過 SSH/Putty 連線到叢集邊緣節點的 R 主控台，或者，如果您要選擇性地將 RStudio 伺服器 IDE 安裝在邊緣節點上，可透過瀏覽器進行連線。如需安裝 RStudio 伺服器的詳細資訊，請參閱[在 HDInsight 叢集上安裝 RStudio 伺服器](hdinsight-hadoop-r-server-install-r-studio.md)。

## 開發及執行 R 指令碼

您所建立與執行的 R 指令碼，可以任意運用 8000 多種開放原始碼封裝，以及 ScaleR 程式庫中的平行與分散式常式。在邊緣節點的 R 伺服器中執行指令碼時，會使用 R 解譯器在該位置執行，但以 Hadoop Map Reduce (RxHadoopMR) 或 Spark (RxSpark) 設定的計算內容呼叫其中一個 ScaleR 數式的步驟除外。在這種情況下，函數會以分散方式，跨已與參考資料相關聯之叢集的資料 (工作) 節點執行。如需不同計算內容選項的詳細資訊，請參閱[適用於 HDInsight Premium R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)。

## 模型運作

完成資料模型化時，可以在 Azure 中與內部部署中運作模型，以為新的資料進行預測 (也稱為評分)。以下提供一些範例：

### 在 HDInsight 中評分

若要在 HDInsight 中評分，可以針對已載入 Azure 儲存體帳戶的新資料檔案，撰寫呼叫模型的 R 函數以進行預測，並將預測儲存回您的儲存體帳戶。您可以視需要在叢集的邊緣節點上執行常式，或使用排程工作來進行。

### 在 Azure Machine Learning 中評分 

若要使用 Azure Machine Learning Web 服務進行評分，可以使用[開放原始碼 Azure Machine Learning R 封裝](http://www.inside-r.org/blogs/2015/11/18/enhancements-azureml-package-connect-r-azureml-studio)以[將模型發佈為 Azure Web 服務](http://www.r-bloggers.com/deploying-a-car-price-model-using-r-and-azureml/)，再使用 Azure Machine Learning 中的設施，建立 Web 服務的使用者介面，然後視需要呼叫 Web 服務以進行評分。如果您選擇這個選項，就必須將所有 ScaleR 模型物件轉換成對等的開放原始碼模型物件，才可搭配 Web 服務使用。這可以透過使用 ScaleR 強制型轉函數完成，例如 `as.randomForest()`，其適用於集成模型。
  
### 內部部署評分

若要在建立模型之後進行內部部署評分，可以在 R 中序列化模型，將其下載至內部部署，再還原序列化，然後使用它進行新資料的評分。您可以使用上述[在 HDInsight 中評分](#scoring-in-hdinsight)所列的方法，或使用 [DeployR](https://deployr.revolutionanalytics.com/) 進行此作業。

## 維護叢集 

### 安裝及維護 R 封裝

由於邊緣節點是執行 R 指令碼的主要位置，因此在邊緣節點上需要有大多數您所使用的 R 封裝。若要在邊緣節點上安裝其他 R 封裝，可以在 R 中使用一般 `install.packages()` 方法。
  
在大部分的情況下，如果您只要從 ScaleR 程式庫使用常式以跨叢集執行，就不需要在資料節點上安裝其他 R 封裝。但您可能需要其他封裝才可支援在資料節點上使用 **rxExec** 或 **RxDataStep** 執行。在這種情況下，必須在建立叢集之後，使用指令碼動作來指定其他封裝。如需詳細資訊，請參閱[建立含 R 伺服器的 HDInsight 叢集](hdinsight-hadoop-r-server-get-started.md)。
  
### 變更 Hadoop Map Reduce 記憶體設定 

您可以在執行 Map Reduce 工作時，修改叢集以變更 R 伺服器的可用記憶體數目。您可透過叢集的 Azure 入口網站刀鋒視窗使用 Ambari UI，完成這項作業。如需如何存取叢集的 Ambari UI 的指示，請參閱[使用 Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

您也可以在 **RxHadoopMR** 的呼叫中使用 Hadoop 參數，變更 R 伺服器可用的記憶體數目，如下所示。
 
	hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### 調整叢集規模

您可透過 Azure 入口網站，調高或調降現有叢集的規模。如此可用以取得較大型處理所需的額外容量，或於叢集閒置時調回容量。如需如何調整叢集規模的指示，請參閱[管理 HDInsight 叢集](hdinsight-administer-use-portal-linux.md)。

### 系統維護 

離峰期間，系統會在 HDInsight 叢集的基礎 Linux VM 上執行維護，以套用 OS 修補程式等等。一般而言，維護作業會在每週的星期一和星期四上午 3:30 (以 VM 的本地時間為準) 完成。執行更新時，系統會以一次僅影響四分之一以內叢集的方式進行。由於前端節點是備援節點，且並非所有資料節點皆會受到影響，因此在這段期間執行的任何工作雖然可能會變慢，但應該都可執行完成。除非發生需要重建叢集的嚴重失敗，否則任何您已經安裝的自訂軟體或本機資料，在這些維護事件中皆會保留。

## 適用於 HDInsight 叢集中 R 伺服器的 IDE 選項

HDInsight Premium 叢集的 Linux 邊緣節點，是 R 型分析的登陸區域。連線到叢集之後，即可在 Linux 命令提示字元處輸入 'R'，啟動 R 伺服器的主控台介面。如果您在另一個視窗中執行 R 指令碼開發的文字編輯器，可視需要將您的指令碼區段剪下並貼入 R 主控台，以使用增強的主控台介面。
  
用於 R 指令碼開發的簡易型文字編輯器也有升級用法，亦即在桌面上使用 R 型 IDE，例如，Microsoft 最近推出的 [R Tools for Visual Studio](https://www.visualstudio.com/zh-TW/features/rtvs-vs.aspx) (RTVS)、[RStudio](https://www.rstudio.com/products/rstudio-server/) 的桌面與伺服器工具系列，或 Walware 的 Eclipse 型 [StatET](http://www.walware.de/goto/statet)。
  
另一個選項是在 Linux 邊緣節點上安裝 IDE。在此情況下，比較常用的選擇是 [RStudio 伺服器](https://www.rstudio.com/products/rstudio-server/)，其提供可讓遠端用戶端使用的瀏覽器型 IDE。在 HDInsight Premium 叢集的邊緣節點上安裝 RStudio 伺服器時，可提供完整的 IDE 體驗供透過叢集上的 R 伺服器來開發及執行 R 指令碼；和預設使用 R 主控台相比，可以大幅提高生產力。如果您想要使用 RStudio 伺服器，請參閱[在 HDInsight 叢集上安裝 RStudio 伺服器](hdinsight-hadoop-r-server-install-r-studio.md)。

## 定價
 
含 R 伺服器的 HDInsight Premium 叢集，其相關聯的費用結構與標準 HDInsight 叢集類似，以各種名稱、資料和邊緣節點的基礎 VM 大小為基準，再加上 Premium 的彈性工時加費。如需 HDInsight Premium 定價，包括公開預覽期間定價及 30 天免費試用的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## 後續步驟

瀏覽以下連結可深入了解如何搭配使用 HDInsight 叢集與 R 伺服器。

- [開始使用 HDInsight 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)

- [將 RStudio 伺服器加入 HDInsight Premium 中](hdinsight-hadoop-r-server-install-r-studio.md)

- [適用於 HDInsight Premium R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)

- [適用於 HDInsight Premium R 伺服器的 Azure 儲存體選項](hdinsight-hadoop-r-server-storage.md)

 

<!---HONumber=AcomDC_0330_2016-->