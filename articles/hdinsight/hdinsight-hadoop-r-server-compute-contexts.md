<properties
   pageTitle="適用於 HDInsight Premium 上的 R 伺服器的計算內容選項 | Azure"
   description="了解 HDInsight Premium 上的 R 伺服器的使用者可用的不同計算內容選項"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# 適用於 HDInsight Premium 上的 R 伺服器的計算內容選項

HDI Spark 上的 R 伺服器提供最新功能，讓以 R 為基礎的分析使用您的 [Azure Blob](../storage/storage-introduction.md "Azure Blob 儲存體") 儲存體帳戶或本機 Linux 檔案系統上容器中的 HDFS 中儲存的資料。因為 R 伺服器的建置基礎為開放原始碼 R，因此您所建置的 R 應用程式可以任意運用 8000 多種開放原始碼 R 封裝，以及 [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR") 中的常式與 R 伺服器隨附的 Microsoft 巨量資料分析封裝。Premium 叢集的邊緣節點提供便利的登陸區域，可連接到叢集並執行 R 指令碼。使用邊緣節點時，您可以透過使用 ScaleR 的 Hadoop Map Reduce 或 Spark 計算內容，跨邊緣節點伺服器核心或叢集節點，執行 ScaleR 的平行化分散式函式。

## 邊緣節點的計算內容

一般而言，邊緣節點上的 R 伺服器中執行的 R 指令碼，會在該節點上的 R 解譯器內執行，除了呼叫 ScaleR 函式的這些步驟除外。ScaleR 呼叫會在計算環境中執行，該環境是由您如何設定 ScaleR 計算內容決定的。從邊緣節點執行您的 R 指令碼時的計算內容的可能值為本機循序 (‘local’)、本機平行 (‘localpar’)、Map Reduce 和 Spark，其中：

| 計算內容 | 設定方式 | 執行內容 |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| 本機循序 | rxSetComputeContext(‘local’) | 邊緣節點伺服器上的循序 (非平行) 執行 |
| 本機平行 | rxSetComputeContext(‘localpar’) | 跨邊緣節點伺服器的核心進行平行處理 |
| Spark | RxSpark() | 跨 HDI 叢集的節點透過 Spark 平行處理分散式執行 |
| Map Reduce | RxHadoopMR() | 跨 HDI 叢集的節點透過 Map Reduce 平行處理分散式執行 |


假設您想要針對效能目的進行平行處理執行，則有三個選項。您的選擇將取決於您的分析工作的本質，和資料的大小和位置。

## 決定計算內容

目前沒有公式可以填入，告訴您要使用哪個計算內容，而是有一些指導原則，會為您指向正確的選擇，或者如果需要最佳選擇，至少能夠在執行基準測試之前，協助您縮小範圍。這些指導原則包括︰

1.	本機 Linux 檔案系統比 HDFS 還快
2.	重複分析將會比較快，如果資料位於本機，且是 XDF 
3.	串流，來自小型資料的文字資料來源，否則在分析之前轉換為 XDF 
4.	對於非常大型的資料，將資料複製/串流至邊緣節點以進行分析的額外負荷會變得不可行 
5.	在 Hadoop 中分析時 Spark 比 Map Reduce 還快，直到資料變得非常大且不再適合於分散式記憶體

對於這些原則，用來選取計算內容的一般規則是︰

### 本機平行

- 如果要分析的資料很小，而且不需要重複分析，則直接串流至分析常式並且使用 'localpar'。 
- 如果要分析的資料很小並且需要重複分析，或者不是太大，則將它複製到本機檔案系統、匯入至 XDF，然後透過 'localpar' 分析。 

### Hadoop Spark

- 如果要分析的資料很大，則匯入至 HDFS 中的 XDF (除非儲存體有問題)，然後透過 'Spark' 分析。 

### Hadoop Map Reduce

- 如果要分析的資料非常大，且 Spark 效能開始下降，則嘗試透過 ‘Map Reduce’ 分析。

## rxSetComputeContext 的內嵌說明

如需 ScaleR 計算內容的詳細資訊和範例，請參閱 rxSetComputeContext 方法上 R 中的內嵌說明，例如︰

    > ?rxSetComputeContext 

請參閱《ScaleR 分散式計算指南》，可以從 [R 伺服器 MSDN](https://msdn.microsoft.com/library/mt674634.aspx "MSDN 上的 R 伺服器") 程式庫取得。


## 後續步驟

現在您已了解如何建立包含 R 伺服器的新 HDInsight 叢集，以及透過 SSH 工作階段使用 R 主控台的基本概念，請參閱下列內容以探索在 HDInsight 上使用 R 伺服器的其他方式。

- [Hadoop 上的 R 伺服器的概觀](hdinsight-hadoop-r-server-overview.md)
- [開始使用 Hadoop 中的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)
- [將 RStudio 伺服器新增至 HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [適用於 HDInsight Premium 上的 R 伺服器的 Azure 儲存體選項](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0330_2016-->