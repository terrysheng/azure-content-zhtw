<properties
   pageTitle="為 Elasticsearch 實作 JMeter 測試計畫 | Microsoft Azure"
   description="如何利用 JMeter 執行 Elasticsearch 的效能測試。"
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
   
# 為 Elasticsearch 實作 JMeter 測試計畫

本文章是[系列文章的一部分](guidance-elasticsearch.md)。

針對 Elasticsearch 進行的效能測試是使用 JMeter 測試計劃搭配 Java 程式碼來實作的，而此 Java 程式碼是為了執行將資料上傳至叢集而納入為 JUnit 測試。測試計畫和 JUnit 程式碼的說明位於[調整 Azure 上 Elasticsearch 資料擷取的效能][]，以及[在 Azure 上調整 Elasticsearch 的資料彙總和查詢效能][]。

本文件的目的在於總結從建構和執行這些測試計劃獲取的重要經驗。Apache JMeter 網站上的 [JMeter 最佳作法](http://jmeter.apache.org/usermanual/best-practices.html)頁面包含有效使用 JMeter 的更一般化建議。

## 實作 JMeter 測試計劃

下列清單總結您建立 JMeter 測試計劃時應該考量的項目：

- 針對每一個想要執行的測試建立個別的執行緒群組。測試可以包含多個步驟，其中包含邏輯控制器、計時器、前置和後置處理器、取樣器和接聽程式。

- 避免在執行緒群組中建立太多執行緒。執行緒數目過多會導致 JMeter 由於發生「記憶體區不足」例外狀況而失敗。最好是加入更多的 JMeter 下層伺服器，每一個伺服器執行少量的執行緒，而不要嘗試在單一 JMeter 伺服器上執行大量的執行緒。

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- 如果要評估叢集的效能，請將 [Perfmon 計量收集器](http://jmeter-plugins.org/wiki/PerfMon/)外掛程式加入測試計畫；Perfmon 計量收集器是一個作為標準 JMeter 標準外掛程式提供的 JMeter 接聽程式。以 CSV 格式將原始效能資料儲存至一組檔案，並在測試完成時加以處理。比起在擷取資料時處理，這樣做更有效率，而且加諸在 JMeter 的壓力更少。 

您可以使用 Excel 這類工具來匯入資料，並基於分析用途產生圖形的範圍。

![](./media/guidance-elasticsearch/jmeter-testing2.png)

您應該考慮擷取下列資訊：

- Elasticsearch 叢集中每個節點的 CPU 使用率。

- 每秒為每個節點從磁碟讀取的位元組數。

- 可能的話，等待 I/O 在每個節點上執行時所花費之 CPU 時間的百分比。對於 Windows VM，不一定可行，但對於 Linux，您可以建立一個可執行下列 shell 命令，以在節點上叫用 *vmstat* 的自訂計量 (EXEC 計量)：

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

*vmstat* 輸出內容中的欄位 16 包含等待 I/O 所花費的 CPU 時間。如需有關此陳述式如何運作的詳細資訊，請參閱 [vmstat 命令](http://linuxcommand.org/man_pages/vmstat8.html)。

- 跨網路到每個節點所傳送和接收的位元組數目。

- 使用個別的彙總報告接聽程式，來記錄成功和失敗作業的效能與頻率。將成功和失敗資料擷取到不同的檔案。

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- 保留每個 JMeter 測試案例越簡單越好，如此可讓您直接使效能與特定測試動作產生關聯。對於需要複雜邏輯的測試案例，請考慮將這個邏輯封裝在 JUnit 測試中，並使用 JMeter 中的 JUnit 要求取樣器來執行此測試。

- 使用 HTTP 要求取樣器來執行 HTTP 作業，例如 GET、POST、PUT 或 DELETE。例如，您可以使用 POST 查詢，並在 [*本文資料*] 方塊中提供查詢的詳細資料，來執行 Elasticsearch 搜尋：

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- 為了易於重複執行和重複使用，請將測試 JMeter 測試計劃參數化。然後，您可以使用指令碼來自動執行測試計劃。

## 實作 JUnit 測試

您可以建立一個或多個 JUnit 測試，將複雜程式碼納入 JMeter 測試計劃中。您可以使用 Java IDE (如 Eclipse) 撰寫 JUnit 測試。[部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能][]提供如何設定適當開發環境的相關資訊。

下列清單總結您撰寫 JUnit 測試的程式碼時所應遵循的一些最佳作法：

- 使用測試類別建構函式，將初始化參數傳遞至測試。JMeter 可以使用一個採用單一字串引數的建構函式。在建構函式中，將這個引數剖析為其個別項目，如下列程式碼範例所示：

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/\* JUnit test class constructor \*/
public ElasticsearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "\[ \]\*,\[ \]\*"; // comma surrounded by zero or more spaces
	String\[\] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- 在建構函式或設定測試類別中避免昂貴或 I/O 作業，因為每次 JUnit 測試執行，它們就會執行 (對於每個從 JMeter 執行的效能測試，相同的 JUnit 測試可能執行數千次)。

- 考慮對昂貴的測試案例初始化使用一次設定。

- 如果測試需要大量輸入參數，請將測試組態資訊儲存在個別組態檔，並將此檔案的位置傳遞至建構函式。

- 避免將檔案路徑固定寫在負載測試程式碼中。這些可能會由於作業系統 (例如 Windows 和 Linux) 之間的差異而導致失敗。

- 使用判斷提示來指出 JUnit 測試方法中的失敗，以便您可以利用 JMeter 追蹤它們，並使用它們做為商業度量。可能的話，傳回失敗原因的相關資訊，如下列程式碼範例中以粗體所示的資訊：

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[調整 Azure 上 Elasticsearch 資料擷取的效能]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[在 Azure 上調整 Elasticsearch 的資料彙總和查詢效能]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md

<!---HONumber=AcomDC_0224_2016-->