<properties 
	pageTitle="建立串流分析輸入 |Microsoft Azure" 
	description="了解如何連接以及設定輸入來源，以制定串流分析解決方案。"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/05/2015" 
	ms.author="jeffstok"/>

# 建立串流分析輸入

## 了解串流分析輸入
---
串流分析的輸入定義為資料來源的連接。當資料傳送到該資料來源時，它會由串流分析工作所取用並即時處理。輸入可分為兩個不同群組：資料流輸入和參考資料輸入。在工作訂用帳戶內外，串流分析具有與輸入來源事件中樞和 Blob 儲存體的第一級整合。支援的資料格式為 Avro、CSV 和 JSON。

## 資料流輸入
---
在基本的層級中，資料流分析工作定義都必須包含至少一個要由工作取用和轉換的資料流輸入來源。支援將 Azure Blob 儲存體和 Azure 事件中樞當成資料流輸入來源。Azure 事件中樞輸入的來源用來從多個裝置和服務收集事件資料流。資料流的範例可能是社交媒體活動摘要、股票交易資訊或感應器資料等作業。

或者，Azure Blob 儲存體可當做擷取大量資料的輸入來源。請務必注意當使用 Azure Blob 時，資料須為靜止的，如此串流分析會解譯 Blob 中包含的所有資料所具有的時間戳記為 Blob 本身的建立時間戳記。也就是說，除非在 Blob 中的記錄包含時間戳記，否則會使用 TIMESTAMP BY 關鍵字。

## 參考資料輸入
---
串流分析也支援第二種輸入來源類型，又稱為參考資料。這是通常用來執行相互關聯與查閱的輔助資料，此處通常是靜態或不常變更的資料。在預覽版本中，Azure Blob 儲存體是目前唯一支援當成參考資料的輸入來源。參考資料來源 Blob 的大小以 50MB 為限。

可以啟用重新整理參考資料的支援，方法是使用 {date} 與 {time} 權杖指定輸入設定內的路徑模式。該工作將根據 Blob 名稱中使用 UTC 時區編碼的日期與時間載入對應的 Blob。要有這一系列具有編碼之日期和時間的參考資料 Blob 才能保證結果的一致性。例如，如果產生延遲處理，且我們必須重新載入 Blob 檔案，則我們預期在相同的位置存在未經修改的該檔案，否則我們可能會看到不同的輸出。唯一支援的案例是要加入新的 Blob，其具有以路徑模式編碼的未來日期和時間。

例如，若這項工作的參考輸入在入口網站設定使用的路徑模式如下：/sample/{date}/{time}/products.csv，其中日期格式為 “YYYY-MM-DD” 而時間格式為 “HH:mm”，那麼該工作將挑選 UTC 時區 2015 年 4 月 16 日下午 5:30 名為 /sample/2015-04-16/17:30/products.csv 的檔案。


> [AZURE.NOTE]僅當該時間與 Blob 名稱中編碼的時間一致時，目前串流分析工作才會尋找參考 Blob 重新整理資料：例如尋找 UTC 時區 2015 年 4 月 16 日下午 5:30 到 5:30:59.999999999 的 /sample/2015-04-16/17:30/products.csv 工作。當時間為下午 5:31 時，就會停止尋找 /sample/2015-04-16/17:30/products.csv，並開始尋找 /sample/2015-04-16/17:31/products.csv。

先前參考資料 Blob 唯一考量的時間就是該工作啟動的時間。在這個時間，此工作正在尋找其名稱含有以工作開始時間之前的值編碼的最近一個日期/時間 (該工作開始時間前最新的參考資料 Blob)。這是為了確保工作的開頭有非空白參考資料集。如果無法找到一個，工作就會失敗，並向使用者顯示診斷注意事項：

    “Initializing input without a valid reference data blob for UTC time <job start time>.”

 
## 建立資料流輸入
---
若要建立資料流輸入，請移至資料流分析工作的 [輸入] 索引標籤，在頁面的底部按一下 [加入輸入]。

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

 建立資料流輸入時將會顯示給使用者兩個選擇：[[事件中樞]](Creating-an-Event-hub-input-data-stream) 或 [Blob 儲存體] (建立 Blob 儲存體輸入資料流)。選取要處理資料流的適當類型。

![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

## 建立事件中樞資料輸入資料流
---
### 事件中樞概觀
事件中樞可以擷取大量的事件，是最常見的 Stream Analytics 資料入口。它們可以收集各種裝置和服務的事件資料流。事件中樞搭配 Stream Analytics 之後，可以為客戶提供一種端對端的解決方案來分析即時資料 -- 用戶可以透過事件中樞即時將事件傳送到 Azure，然後 Stream Analytics 工作就會即時處理事件。例如，用戶可以將網頁點選次數、感應器數據、線上記錄事件傳送到事件中樞，然後建立各種 Azure 串流分析工作，讓這些工作將事件中樞當做輸入資料流來進行資料的即時篩選、彙總以及聯結。事件中樞也可以當做資料出口。事件中樞當做輸出的一個可能用途，就是用於串流分析工作的輸出將成為另一個串流工作的輸入時。如需事件中樞的詳細資訊，請參閱[事件中樞](https://azure.microsoft.com/services/event-hubs/ "事件中心")文件。

### 用戶群組
每一個 Azure 串流分析工作都應該設定自己的事件中樞用戶群組。當工作包含自我聯結或多個輸入時，某些輸出就有可能供一個以上的讀取器下游使用，這樣會造成單個用戶群組中的讀取器總數超過事件中樞規定的每一個用戶群組最多 5 個讀取器的限制。在這種情況下，就需要將查詢細分成多個查詢並透過其他事件中樞，傳遞中繼結果。請注意，每一個事件中樞另外還有 20 個用戶群組的限制。如需詳細資訊，請參閱[事件中樞程式設計指南](https://msdn.microsoft.com/library/azure/dn789972.aspx "事件中心程式設計指南")。

## 在 Azure 入口網站建立事件中樞輸入的範例
---
以下是設定事件中樞做為輸入的逐步解說。若要開始使用事件中樞輸入，使用者應該收集有關事件中樞的下列相關資訊：

1. 輸入別名 - 易記的輸入別名，在該工作查詢中做為參考。
2. 服務匯流排命名空間的名稱。 
3. 事件中樞的名稱。
3. 事件中樞原則名稱。
4. 選擇性：事件中樞取用者群組名稱。
	- 負責從事件中樞擷取資料的用戶群組。如果未指定，Stream Analytics 工作使用預設用戶群組，然後從事件中樞擷取資料。建議您為每一個串流分析工作使用不同的取用者群組。
5. 該資料流會使用序列化格式 (Avro、CSV、JSON)。

先從串流分析工作的 [輸入] 頁面選取 [加入輸入]。

![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

然後選取該事件中樞做為輸入。

![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

接下來，針對該事件中樞，輸入資訊到此欄位中，如下所示。

![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

之後請驗證該事件序列化格式對於此資料流為正確。

![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

然後選取 [完成]，隨即建立事件中樞的資料流輸入。

## 建立 Blob 儲存體的輸入資料流
---
在要於雲端中儲存大量非結構化資料的案例中，Blob 儲存體提供具有成本效益且可擴充的解決方案。這項資料通常可視為是「靜止」的資料。對於資料流輸入，Blob 可能有用的一種案例是記錄分析，其中記錄檔已經從系統擷取，而且需要剖析和從中擷取有意義的資料。另一個可能的用途是分析資料倉儲的待用資料。如需 Blob 儲存體的詳細資訊，請參閱 [Blob 儲存體](http://azure.microsoft.com/services/storage/blobs/)文件。

以下是設定事件中樞做為輸入的逐步解說。若要開始使用 Azure Blob 儲存體輸入，需要以下資訊：

1. 輸入別名 - 易記的輸入別名，在該工作查詢中做為參考。
2. 如果儲存體帳戶位於串流工作以外的不同訂用帳戶，則將需要儲存體帳戶名稱和儲存體帳戶金鑰。
3. 容器名稱。
4. 檔案名稱前置詞。
5. 該資料使用的序列化格式 (Avro、CSV、JSON)。

在串流分析工作的 [輸入] 索引標籤上，按一下 [加入輸入]，然後選取預設選項 [資料流]。![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

接下來選取 [Blob 儲存體]

![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

然後如下所示針對儲存體帳戶，將資訊輸入欄位中。

![image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

> [AZURE.NOTE]按一下 [設定進階設定] 方塊，可啟用在自訂路徑中指定路徑前置詞模式用於讀取 Blob 的選項。如果未指定此欄位，則串流分析會讀取該容器中的所有 Blob。

![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

接下來請選擇該資料正確的序列化設定。選項包括 JSON、CSV 及 Avro。

![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

然後選取 [完成]，隨即建立 Blob 儲存體的資料流輸入。

## 建立 Blob 儲存體的參考資料流
---
若要使用參考資料功能，可使用 Blob 儲存體。

以下是設定事件中樞做為參考資料輸入的逐步解說。若要開始，會需要以下資訊：

1. 輸入別名 - 易記的輸入別名，在該工作查詢中做為參考。
2. 如果儲存體帳戶位於串流工作以外的不同訂用帳戶，則將需要儲存體帳戶名稱和儲存體帳戶金鑰。
3. 容器名稱。
4. 檔案名稱前置詞。
5. 該資料使用的序列化格式 (CSV、JSON)。
6. 路徑模式。用來在指定容器中找出 Blob 的檔案路徑。在該路徑內，您也可以指定下列 2 個變數的一個或多個執行個體：{date} 和 {time}。


在串流分析工作的 [輸入] 索引標籤上，按一下 [加入輸入]，然後選取預設選項 [參考資料]。

![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

然後如下所示針對 Blob 儲存體和儲存體帳戶，將資訊輸入欄位中。

![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

請務必向下捲動，以指定包含 Blob 以及日期和時間欄位的格式之路徑階層架構的前置詞模式。

![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

現在請選擇該資料正確的序列化設定。選項包括 JSON、CSV 及 Avro。

![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)

然後選取 [完成]，隨即建立 Blob 儲存體的參考資料流輸入。


## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-tw/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO6-->