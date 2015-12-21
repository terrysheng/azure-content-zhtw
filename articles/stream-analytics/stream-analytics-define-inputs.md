<properties
	pageTitle="資料連線：來自事件資料流的資料流輸入 | Microsoft Azure"
	description="了解如何設定通往串流分析的資料連線 (也就是「輸入」)。輸入包括來自事件的資料流，以及參考資料。"
	keywords="資料流, 資料連線, 事件資料流"
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
	ms.date="11/23/2015"
	ms.author="jeffstok"/>

# 資料連線：了解資料從事件到串流分析的資料流輸入

通往串流分析的資料連線，就是源自於資料來源的事件資料流，也就是「輸入」。 串流分析與 Azure 資料流來源事件中樞、IoT 中樞及 Blob 儲存體的整合性極佳，而它們可以來自您分析工作所用的 Azure 訂用帳戶，或是其他不同的帳戶。

## 資料輸入類型：資料流和參考資料
當資料推送到資料來源時，串流分析工作會即時取用並處理該資料。輸入可分為兩個不同類型：資料流輸入和參考資料輸入。

### 資料流輸入
資料流是一段時間內發生的無限制事件順序。串流分析工作必須包含至少一個要由工作取用和轉換的資料流輸入。支援將 Blob 儲存體、事件中樞，以及 IoT 中心當成資料流輸入來源。事件中樞是用來從多個裝置和服務收集事件資料流，這些資料流包括社交媒體活動摘要、股票交易資訊，或來自感應器的資料。IoT 中心經過最佳化，最適合從物聯網 (IoT) 案例中相互連接的裝置收集資料。Blob 儲存體可用於擷取大量資料，做為資料流的輸入來源。

### 參考資料
串流分析也支援第二種輸入來源類型，稱為參考資料。這是輔助資料，也就是通常用來執行相互關聯與查閱的靜態或不常變更的資料。在預覽版本中，Azure Blob 儲存體是目前唯一支援當成參考資料的輸入來源。參考資料來源 Blob 的大小以 50MB 為限。若要了解如何建立參考資料輸入，請參閱[使用參考資料](stream-analytics-use-reference-data.md)。

## 使用事件中樞來建立資料流輸入

[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)是具高延展性的發佈-訂閱事件擷取器。事件中樞每秒可擷取數百萬個事件，可讓您處理和分析連接的裝置和應用程式所產生的大量資料。它是串流分析最常使用的輸入之一。事件中樞和串流分析會一起提供客戶即時分析的端對端解決方案。事件中樞可讓客戶即時將事件摘要處理至 Azure，而串流分析工作可以即時處理它們。例如，用戶可以將網頁點選次數、感應器數據、線上記錄事件傳送到事件中樞，然後建立各種 Azure 串流分析工作，讓這些工作將事件中樞當做輸入資料流來進行資料的即時篩選、彙總以及相互關聯。

請務必注意，來自資料流分析事件中樞的事件預設時間戳記，即為事件抵達事件中樞的時間戳記，也就是 EventEnqueuedUtcTime。若要使用事件承載中的時間戳記，將資料當作資料流處理，必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

### 用戶群組

每一個串流分析事件中樞輸入都應該設定為有自己的用戶群組。當工作包含自我聯結或多個輸入時，某些輸入就有可能供多個讀取器下游使用，這樣會影響單個用戶群組中的讀取器數目。若要避免超出每個資料分割的每個用戶群組 5 個讀取器的事件中樞限制，最好指定每個串流分析工作的用戶群組。請注意，每一個事件中樞另外還有 20 個用戶群組的限制。如需詳細資訊，請參閱[事件中樞程式設計指南](./event-hubs/event-hubs-programming-guide.md)。

## 將事件中樞設定為輸入資料流

下表說明事件中樞輸入索引標籤中的每一個屬性及其描述：

| 屬性名稱 | 描述 |
|------|------|
| 輸入別名 | 在工作查詢中將用來參考這個輸入的易記名稱 |
| 服務匯流排 命名空間 | 服務匯流排命名空間是一個容器，包含一組訊息實體。建立新的事件中樞時，也會建立服務匯流排命名空間。 |
| 事件中心 | 事件中樞輸入的名稱。 |
| 事件中樞原則名稱 | 共用的存取原則，可以在事件中樞的 [設定] 索引標籤上建立。每一個共用存取原則會有名稱、權限 (由您設定) 和存取金鑰。 |
| 事件中樞原則金鑰 | 用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件中樞取用者群組 (選用) | 負責從事件中樞擷取資料的用戶群組。如果未指定，Stream Analytics 工作使用預設用戶群組，然後從事件中樞擷取資料。建議您為每一個串流分析工作使用不同的取用者群組。 |
| 事件序列化格式 | 為了確定您的查詢運作如預期，串流分析需要知道您的內送資料流使用哪一種序列化格式 (JSON、CSV 或 Avro)。 |
| 編碼 | UTF-8 是目前唯一支援的編碼格式。 |

當您的資料來自事件中樞來源時，您可以在串流分析查詢中存取一些中繼資料欄位。下表列出這些欄位及其描述。

| 屬性 | 描述 |
|------|------|
| EventProcessedUtcTime | 資料流分析處理事件的日期與時間。 |
| EventEnqueuedUtcTime | 事件中樞收到事件的日期與時間。 |
| PartitionId | 輸入配接器以零起始的資料分割識別碼。 |

例如，您可以撰寫類似下列的查詢：

````
SELECT
	EventProcessedUtcTime,
	EventEnqueuedUtcTime,
	PartitionId
FROM Input
````

## 建立 IoT 中樞資料流輸入

Azure IoT 中樞是已針對 IoT 案例最佳化的高延展性發佈/訂閱事件擷取器。請務必注意，來自資料流分析之 IoT 中心的事件預設時間戳記，即為事件抵達 IoT 中心的時間戳記，也就是 EventEnqueuedUtcTime。若要使用事件承載中的時間戳記，將資料當作資料流處理，必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

### 用戶群組

每一個資料流分析 IoT 中心輸入都應該設定為有自己的取用者群組。當工作包含自我聯結或多個輸入時，某些輸入就有可能供多個讀取器下游使用，這樣會影響單個用戶群組中的讀取器數目。若要避免超出每個資料分割的每個取用者群組 5 個讀取器的 IoT 中心限制，最好為每個資料流分析工作指定取用者群組。

## 將 IoT 中樞設定為輸入資料流

下表說明 IoT 中心輸入索引標籤中的每一個屬性及其描述：

| 屬性名稱 | 描述 |
|------|------|
| 輸入別名 | 在工作查詢中將用來參考這個輸入的易記名稱。 |
| IoT 中心 | IoT 中心是一組訊息實體的容器。 |
| 端點 | IoT 中心端點的名稱。 |
| 共用存取原則名稱 | 提供 IoT 中心存取權的共用存取原則。每一個共用存取原則會有名稱、權限 (由您設定) 和存取金鑰。 |
| 共用存取原則金鑰 | 用來驗證 IoT 中心存取權的共用存取金鑰。 |
| 取用者群組 (選用) | 負責從 IoT 中心擷取資料的取用者群組。如果未指定，資料流分析工作會使用預設取用者群組，從 IoT 中心擷取資料。建議您為每一個串流分析工作使用不同的取用者群組。 |
| 事件序列化格式 | 為了確定您的查詢運作如預期，串流分析需要知道您的內送資料流使用哪一種序列化格式 (JSON、CSV 或 Avro)。 |
| 編碼 | UTF-8 是目前唯一支援的編碼格式。 |

當您的資料來自 IoT 中心來源時，您可以在資料流分析查詢中存取一些中繼資料欄位。下表列出這些欄位及其描述。

| 屬性 | 描述 |
|------|------|
| EventProcessedUtcTime | 處理事件的日期與時間。 |
| EventEnqueuedUtcTime | IoT 中心收到事件的日期與時間。 |
| PartitionId | 輸入配接器以零起始的資料分割識別碼。 |
| IoTHub.MessageId | 用來關聯 IoT 中心內的雙向通訊。 |
| IoTHub.CorrelationId | 用於 IoT 中心內的訊息回應和意見反應。 |
| IoTHub.ConnectionDeviceId | 用來傳送此訊息的已驗證識別碼，IoT 中心傳送給服務的訊息都會加上戳記。 |
| IoTHub.ConnectionDeviceGenerationId | 用來傳送此訊息的已驗證裝置 generationId，IoT 中心傳送給服務的訊息都會加上戳記。 |
| IoTHub.EnqueuedTime | IoT 中樞收到訊息的時間。 |
| IoTHub.StreamId | 傳送裝置所加入的自訂事件屬性。 |

## 建立 Blob 儲存體資料流輸入

在要於雲端中儲存大量非結構化資料的案例中，Blob 儲存體提供具有成本效益且可擴充的解決方案。[Blob 儲存體](http://azure.microsoft.com/services/storage/blobs/)中的資料通常會被視為「靜止」的資料，但資料流分析可將其視為資料流來處理。串流分析的 Blob 儲存體輸入常見案例是記錄檔處理，這時會從系統擷取遙測，並且需要剖析和處理，以擷取有意義的資料。

請務必注意，資料流分析中的 Blob 儲存體事件預設時間戳記，是上一次修改 Blob 時的時間戳記，也就是 *BlobLastModifiedUtcTime*。若要使用事件承載中的時間戳記，將資料當作資料流處理，必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。

下表說明 Blob 儲存體輸入索引標籤中的每一個屬性及其描述：

<table>
<tbody>
<tr>
<td>屬性名稱</td>
<td>描述</td>
</tr>
<tr>
<td>輸入別名</td>
<td>在工作查詢中將用來參考這個輸入的易記名稱。</td>
</tr>
<tr>
<td>儲存體帳戶</td>
<td>您的 blob 檔案所在的儲存體帳戶名稱。</td>
</tr>
<tr>
<td>儲存體帳戶金鑰</td>
<td>與儲存體帳戶相關聯的密碼金鑰。</td>
</tr>
<tr>
<td>儲存體容器
</td>
<td>容器提供邏輯分組給儲存在 Microsoft Azure Blob 服務中的 blob。當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。</td>
</tr>
<tr>
<td>路徑前置詞模式 [選用]</td>
<td>用來在指定容器中找出 Blob 的檔案路徑。在該路徑內，您也可以指定下列 3 個變數的一個或多個執行個體：<BR>{date}、{time}、<BR>{partition}<BR>範例 1：cluster1/logs/{date}/{time}/{partition}<BR>範例 2：cluster1/logs/{date}<P>請注意，"*"不是路徑前置詞允許的值。僅允許有效的 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob 字元</a>。</td>
</tr>
<tr>
<td>日期格式 [選用]</td>
<td>如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。範例：YYYY/MM/DD</td>
</tr>
<tr>
<td>時間格式 [選用]</td>
<td>如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。目前唯一支援的值為 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>為了確定您的查詢運作如預期，串流分析需要知道您的內送資料流使用哪一種序列化格式 (JSON、CSV 或 Avro)。</td>
</tr>
<tr>
<td>編碼</td>
<td>對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。</td>
</tr>
<tr>
<td>分隔符號</td>
<td>串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。支援的值是逗號、分號、空格、索引標籤和分隔號。</td>
</tr>
</tbody>
</table>

當您的資料來自 Blob 儲存體來源時，您可以在串流分析查詢中存取一些中繼資料欄位。下表列出這些欄位及其描述。

| 屬性 | 描述 |
|------|------|
| BlobName | 事件來源的輸入 Blob 名稱。 |
| EventProcessedUtcTime | 資料流分析處理事件的日期與時間。 |
| BlobLastModifiedUtcTime | 上次修改 Blob 的時間與日期。 |
| PartitionId | 輸入配接器以零起始的資料分割識別碼。 |

例如，您可以撰寫類似下列的查詢：

````
SELECT
	BlobName,
	EventProcessedUtcTime,
	BlobLastModifiedUtcTime
FROM Input
````


## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟
您已經了解自己串流分析工作的資料連線選項。若要深入了解串流分析，請參閱：

- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics 管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_1210_2015-->