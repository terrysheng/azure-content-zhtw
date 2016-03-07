
<properties
   pageTitle="執行自動化的 Elasticsearch 效能測試 | Microsoft Azure"
   description="說明如何在您自己的環境中執行效能測試。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# 執行自動化的 Elasticsearch 效能測試

本文是[系列文章的一部分](guidance-elasticsearch.md)。

[Tuning Data Ingestion Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料擷取效能)] 和 [Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料彙總和查詢效能)] 說明一些針對範例 Elasticsearch 叢集執行的效能測試。

已為這些測試編寫指令碼，讓它們能夠自動執行。本文件說明您如何在自己的環境中重複執行測試。

## 必要條件

自動化測試需要下列項目：

-  Elasticsearch 叢集。

- 如 [Creating a Performance Testing Environment for Elasticsearch on Azure (在 Azure 上建立適用於 Elasticsearch 的效能測試環境)] 一文所述的 JMeter 環境設定。

- 只在 JMeter 主要 VM 上安裝下列軟體。

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## 測試的運作方式
使用 JMeter 執行測試。JMeter 主要伺服器會載入測試計劃，並將它傳遞給一組實際執行測試的 JMeter 下層伺服器。JMeter 主要伺服器會協調 JMeter 下層伺服器，並彙總結果。

已提供下列測試計劃：

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx)。此測試計劃會在 3 個節點的叢集上執行擷取測試。

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx)。此測試計劃會在 6 個節點的叢集上執行擷取測試。

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx)。此測試計劃會在 6 個節點的叢集上執行擷取和查詢測試。

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx)。此測試計劃只會在 6 個節點的叢集上執行查詢測試。


如果您需要少或更多的節點，您可以使用這些測試計劃做為您自己案例的基礎。

測試計劃會使用 JUnit 要求取樣器，來產生測試資料並加以上傳。JMeter 測試計劃都會建立和執行此取樣器，並監視每個 Elasticsearch 節點以取得效能資料。如需詳細資訊，請參閱 [Tuning Data Ingestion Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料擷取效能)] 和 [Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料彙總和查詢效能)] 等文件的附錄。

## 建置和部署 JUnit JAR 和相依性
執行恢復能力測試之前，您應該下載、編譯和部署位於 [performance/junitcode] 資料夾下方的 JUnit 測試。JMeter 測試計劃會參考這些測試。如需詳細資訊，請參閱 [Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance (部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能)] 一文中的＜將現有的 JUnit 測試專案匯入至 Eclipse＞程序。

JUnit 測試有兩個版本：- [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73)。請使用此程式碼來執行擷取測試。這些測試會使用 Elasticsearch 1.73 - [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2)。請使用此程式碼來執行查詢測試。這些測試會使用 Elasticsearch 2.1 和更新版本。

將適當的 JAR 檔案以及其他相依性一併複製到您的 JMeter 電腦。此程序將會在 [Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance (部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能)][] 中加以說明。

> **重要** 部署 JUnit 測試之後，請使用 JMeter 來載入和設定測試計劃以參考此 JUnit 測試，並確定 *BulkInsertLarge* 執行緒群組會參考正確的 JAR 檔案、JUnit 類別名稱及測試方法：
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> 執行測試之前，請先儲存更新的測試計劃。

## 建立測試索引
每個測試都會根據測試執行時所指定的單一索引來執行擷取和 (或) 查詢。您應該使用 [Tuning Data Ingestion Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料擷取效能)] 和 [Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料彙總和查詢效能)] 等文件的附錄中所述的結構描述來建立索引，並根據您的測試案例 (已啟用/已停用文件值、多個複本等) 來設定它們。 請注意，測試計劃假設索引是由名為 *ctip* 的單一類型所組成。

## 設定測試指令碼參數
將下列測試指令碼參數檔案複製到 JMeter 伺服器電腦：

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties)。這個檔案會指定要使用的 JMeter 測試執行緒數目、測試持續期間 (以秒為單位)、Elasticsearch 叢集中節點 (或負載平衡器) 的 IP 位址，以及叢集的名稱：

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  編輯此檔案，並為測試和叢集指定適當的值。

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) 和 [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini)。這兩個檔案包含相同資訊；*win* 檔案的格式設定適用於 Windows 檔案名稱和路徑，而 *nix* 檔案的格式設定適用於 Linux 檔案名稱和路徑：

  ```ini [DEFAULT] debug=true #如果是 true，即會顯示主控台記錄。

  [RUN] pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #儲存測試結果的路徑。 jmx=C:\\Users\\administrator1\\testplan.jmx #JMeter 測試計劃的路徑。 machines=10.0.0.1,10.0.02,10.0.0.3 #Elasticsearch 資料節點的 IP，以逗號分隔。 reports=aggr,err,tps,waitio,cpu,network,disk,response,view #報告的名稱，以逗號分隔。 tests=idx1 #要測試的 Elasticsearch 索引名稱。 properties=run.properties #屬性檔案的名稱。 ```

  編輯這個檔案，以指定測試結果的位置、要執行的 JMeter 測試計劃名稱、Elasticsearch 資料節點的 IP 位址、包含將產生且未經處理的效能資料的報告，以及一或多個位於測試下方的索引名稱。如果 *run.properties* 檔案位於不同的資料夾或目錄中，請為此檔案指定完整的路徑。

## 執行測試

* 在與 *run.properties* 和 *query-config-win.ini* (*query-config-nix.ini*) 檔案相同的資料夾中，將檔案 [query-test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) 複製到 JMeter 伺服器電腦。

* 確認 *jmeter.bat* (Windows) 或 *jmeter.sh* (Linux) 位於您環境中的可執行檔路徑。

* 從命令列執行 *query-test.py* 指令碼以執行測試：

  ```cmd
  py query-test.py
  ```

* 當測試完成時，結果會儲存為 *query-config-win.ini* (*query-config-nix.ini*) 檔案中指定的 CSV 檔案組。您可以使用 Excel 來分析及繪製此資料。


[Tuning Data Ingestion Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料擷取效能)]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (在 Azure 上調整 Elasticsearch 的資料彙總和查詢效能)]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Creating a Performance Testing Environment for Elasticsearch on Azure (在 Azure 上建立適用於 Elasticsearch 的效能測試環境)]: guidance-elasticsearch-creating-performance-testing-environment.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance (部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能)]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->