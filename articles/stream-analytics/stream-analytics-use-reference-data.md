<properties 
	pageTitle="使用參考資料 | Microsoft Azure" 
	description="使用參考資料做為輸入資料流" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# 使用參考資料做為輸入

參考資料是靜態或本質上不常變更的有限資料集，可用來執行查閱或相互關聯資料流。若要使用 Azure 串流分析工作中的參考資料，您通常會使用查詢中的[參考資料聯結](https://msdn.microsoft.com/library/azure/dn949258.aspx)。串流分析會使用 Azure Blob 儲存體做為參考資料的儲存層，且可和 Azure Data Factory 參考資料一起轉換和/或複製到來自[任意數目的雲端架構和內部部署資料存放區](./articles/data-factory-data-movement-activities.md)的 Azure Blob 儲存體，做為參考資料。

## 設定參考資料

若要設定參考資料，您必須先建立屬於參考資料類型的輸入。下表說明您在建立參考資料輸入及其描述時必須提供的每個屬性：

<table>
<tbody>
<tr>
<td>屬性名稱</td>
<td>說明</td>
</tr>
<tr>
<td>輸入別名</td>
<td>在工作查詢中將用來參考這個輸入的易記名稱。</td>
</tr>
<tr>
<td>儲存體帳戶</td>
<td>您的 blob 檔案所在的儲存體帳戶名稱。如果它和您的串流分析工作位於相同的訂用帳戶中，您可以從下拉式清單加以選取。</td>
</tr>
<tr>
<td>儲存體帳戶金鑰</td>
<td>與儲存體帳戶相關聯的密碼金鑰。如果儲存體帳戶和您的串流分析工作位於相同的訂用帳戶中，則會自動填入。</td>
</tr>
<tr>
<td>儲存體容器</td>
<td>容器提供邏輯分組給儲存在 Microsoft Azure Blob 服務中的 blob。當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。</td>
</tr>
<tr>
<td>路徑前置詞模式 [選用]</td>
<td>用來在指定容器中找出 Blob 的檔案路徑。在該路徑內，您也可以指定下列 2 個變數的一個或多個執行個體：<BR>{date}、{time}<BR>範例 1：products/{date}/{time}/product-list.csv<BR>範例 2：products/{date}/product-list.csv
</tr>
<tr>
<td>日期格式 [選用]</td>
<td>如果您已經使用您指定之路徑模式內的 {date}，您可以從支援格式的下拉式清單選取組織檔案所使用的日期格式。範例：YYYY/MM/DD</td>
</tr>
<tr>
<td>時間格式 [選用]</td>
<td>如果您已經使用您指定之路徑模式內的 {time}，您可以從支援格式的下拉式清單選取組織檔案所使用的時間格式。範例：HH</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>為了確定您的查詢運作如預期，串流分析需要知道您的內送資料流使用哪一種序列化格式。參考資料的支援格式為 CSV 和 JSON。</td>
</tr>
<tr>
<td>編碼</td>
<td>UTF-8 是目前唯一支援的編碼格式</td>
</tr>
</tbody>
</table>

## 產生排程上的參考資料

如果您的參考資料是不常變更的資料集，可以啟用重新整理參考資料的支援，方法是使用 {date} 與 {time} 權杖指定輸入設定內的路徑模式。串流分析會根據此路徑模式採用更新的參考資料定義。例如，日期格式為 “YYYY-MM-DD” 且時間格式為 “HH:mm” 的模式 ````"/sample/{date}/{time}/products.csv"````，會告知串流分析在 UTC 時區 2015 年 4 月 16 日的下午 5:30 擷取更新的 blob ````"/sample/2015-04-16/17:30/products.csv"````。

> [AZURE.NOTE]目前串流分析工作只有在機器時間與 Blob 名稱中編碼的時間一致時，才會尋找 blob 重新整理。例如，工作會在 UTC 時區 2015 年 4 月 16 日的下午 5:30 到下午5:30:59.9 之間尋找 /sample/2015-04-16/17:30/products.csv。當機器時間為下午 5:31 時，就會停止尋找 /sample/2015-04-16/17:30/products.csv，並開始尋找 /sample/2015-04-16/17:31/products.csv。先前參考資料 Blob 唯一考量的時間就是該工作第一次啟動的時間。當時作業正在尋找指定的作業啟動時間之前所產生的最新 blob。這是為了確保工作啟動時有非空白參考資料集。如果無法找到，工作就會失敗，並向使用者顯示診斷注意事項。

[Azure Data Factory](http://azure.microsoft.com/documentation/services/data-factory/) 可以用來協調建立串流分析所需的更新 blob 工作，以更新參考資料定義。Data Factory 是雲端架構資料整合服務，用來協調以及自動移動和轉換資料。Data Factory 支援[連接到大量的雲端架構和內部部署資料存放區](./articles/data-factory-data-movement-activities.md)以及根據您指定的固定排程輕鬆地移動資料。如需詳細資訊以及有關如何設定 Data Factory 管線以產生在預先定義排程上重新整理之串流分析的參考資料的逐步指引，請參閱 [GitHub 範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)。

## 取得說明
如需進一步的協助，請參閱我們的 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟
以上就是串流分析 (物聯網資料串流分析專用的受管理服務) 的簡介。若要深入了解此服務，請參閱：

- [開始使用 Azure 串流分析](stream-analytics-get-started.md)
- [調整 Azure 串流分析工作](stream-analytics-scale-jobs.md)
- [Azure 串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=Sept15_HO2-->