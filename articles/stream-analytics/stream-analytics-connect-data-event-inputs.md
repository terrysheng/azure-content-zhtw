<properties 
	pageTitle="連接串流分析輸入 | Microsoft Azure" 
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
	ms.date="11/23/2015" 
	ms.author="jeffstok"/>

# 連接串流分析輸入來源

## 了解串流分析輸入
---
串流分析的輸入定義為資料來源的連接。在工作訂用帳戶內外，串流分析具有與 Azure 來源事件中樞和 Blob 儲存體的第一級整合。當資料傳送到該資料來源時，它會由串流分析工作所取用並即時處理。輸入可分為兩個不同類型：資料流輸入和參考資料輸入。

## 資料流輸入
---
串流分析工作必須包含至少一個要由工作取用和轉換的資料流輸入。支援將 Azure Blob 儲存體和 Azure 事件中樞當成資料流輸入來源。Azure 事件中樞用來從多個裝置和服務收集事件資料流，這些裝置和服務包括例如社交媒體活動摘要、股票交易資訊或來自感應器的資料。或者，Azure Blob 儲存體可當做擷取大量資料的輸入來源。

## 參考資料輸入
---
串流分析也支援第二種輸入來源類型，稱為參考資料。這是通常用來執行相互關聯與查閱的輔助資料，此處通常是靜態或不常變更的資料。在預覽版本中，Azure Blob 儲存體是目前唯一支援當成參考資料的輸入來源。參考資料來源 Blob 的大小以 50MB 為限。

## 建立事件中樞資料輸入資料流
---
### 事件中樞概觀
事件中樞可以擷取大量的事件，是資料擷取至串流分析工作最常見的方法。事件中樞搭配 Stream Analytics 之後，可以為客戶提供一種端對端的解決方案來分析即時資料 -- 用戶可以透過事件中樞即時將事件傳送到 Azure，然後 Stream Analytics 工作就會即時處理事件。例如，用戶可以將網頁點選次數、感應器數據、線上記錄事件傳送到事件中樞，然後建立各種 Azure 串流分析工作，讓這些工作將事件中樞當做輸入資料流來進行資料的即時篩選、彙總以及聯結。事件中樞也可以當做資料出口。如需事件中樞的詳細資訊，請參閱[事件中樞](https://azure.microsoft.com/services/event-hubs/ "事件中樞")文件。

### 用戶群組
每一個串流分析事件中樞輸入都應該設定為有自己的用戶群組。當工作包含自我聯結或多個輸入時，某些輸入就有可能供多個讀取器下游使用，這樣會影響單個用戶群組中的讀取器數目。若要避免超出每個資料分割的每個用戶群組 5 個讀取器的事件中樞限制，最好指定每個串流分析工作的用戶群組。請注意，每一個事件中樞另外還有 20 個用戶群組的限制。如需詳細資訊，請參閱[事件中樞程式設計指南](https://msdn.microsoft.com/library/azure/dn789972.aspx "事件中樞程式設計指南")。

## 建立事件中樞資料輸入資料流
---
### 加入事件中樞做為輸入資料流  ###

1. 在串流分析工作的 [輸入] 索引標籤上，按一下 [加入輸入]，然後選取預設選項 [資料流]，再按一下滑鼠右鍵。

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. 接著選取 [事件中樞]。

    ![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

3. 輸入或選取以下欄位並按一下滑鼠右鍵：

    - **輸入別名**：工作查詢中將用來參考這個輸入的易記名稱  
    - **服務匯流排命名空間**：服務匯流排命名空間是一個容器，包含一組訊息實體。建立新的事件中樞時，也會建立服務匯流排命名空間。  
    - **事件中樞**：事件中樞輸入的名稱  
    - **事件中樞原則名稱**：共用的存取原則，可以在事件中樞的 [設定] 索引標籤上建立。每一個共用存取原則會有名稱、權限 (由您設定) 和存取金鑰。  
    - **事件中樞用戶群組** (選擇性)：負責從事件中樞擷取資料的用戶群組。如果未指定，Stream Analytics 工作使用預設用戶群組，然後從事件中樞擷取資料。建議您為每一個串流分析工作使用不同的取用者群組。  

    ![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

4. 指定下列設定：

    - **事件序列化格式**：為了確定您的查詢運作如預期，串流分析需要知道您的內送資料流使用哪一種序列化格式 (JSON、CSV 或 Avro)。  
    - **編碼**：UTF-8 是目前唯一支援的編碼格式。  

    ![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

5. 按一下核取按鈕以完成精靈，並確認串流分析可成功連接到事件中樞。

## 建立 Blob 儲存體的資料流輸入
---
在要於雲端中儲存大量非結構化資料的案例中，Blob 儲存體提供具有成本效益且可擴充的解決方案。Blob 儲存體中的資料通常會被視為「靜止」的資料，但它可由串流分析當做資料流來處理。串流分析的 Blob 儲存體輸入常見案例是記錄檔處理，這時會從系統擷取遙測，並且需要剖析和處理，以擷取有意義的資料。請務必注意，串流分析中的 Blob 儲存體事件預設時間戳記，是建立 blob 當時的時間戳記。若要使用事件裝載中的時間戳記，將資料當做資料流處理，必須使用 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 關鍵字。如需 Blob 儲存體的詳細資訊，請參閱 [Blob 儲存體](http://azure.microsoft.com/services/storage/blobs/)文件。

### 加入 Blob 儲存體做為資料流輸入  ###

1. 在串流分析工作的 [輸入] 索引標籤上，按一下 [加入輸入]，然後選取預設選項 [資料流]，再按一下滑鼠右鍵。

    ![image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. 選取 [BLOB 儲存體]，然後按一下滑鼠右鍵。

    ![image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

3. 輸入或選取下列欄位：

    - **輸入別名**：工作查詢中將用來參考這個輸入的易記名稱  
    - **儲存體帳戶**：如果儲存體帳戶位於串流工作以外的不同訂用帳戶，則將需要儲存體帳戶名稱和儲存體帳戶金鑰。  
    - **儲存體容器**：容器提供儲存在 Microsoft Azure Blob 服務中的 blob 的邏輯分組。當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。  

    ![image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

4. 按一下 [設定進階設定] 方塊，可使用在自訂路徑中設定讀取 blob 路徑前置詞模式的選項。如果未指定此欄位，則串流分析會讀取該容器中的所有 Blob。

    ![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

5. 選擇下列設定：

    - **事件序列化格式**：為了確定您的查詢運作如預期，串流分析需要知道您的內送資料流使用哪一種序列化格式 (JSON、CSV 或 Avro)。  
    - **編碼**：UTF-8 是目前唯一支援的編碼格式。  


    ![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

6. 按一下核取按鈕以完成精靈，並確認串流分析可成功連接到 Blob 儲存體帳戶。

## 建立 Blob 儲存體的參考資料
---
Blob 儲存體可以用於定義串流分析工作的參考資料。這是靜態或緩慢變更的資料，用於執行查詢或使資料相互關聯。可以啟用重新整理參考資料的支援，方法是使用 {date} 與 {time} 權杖指定輸入設定內的路徑模式。串流分析會根據此路徑模式更新參考資料定義。例如，日期格式為 “YYYY-MM-DD” 且時間格式為 “HH:mm” 的模式 `"/sample/{date}/{time}/products.csv"`，會告知串流分析在 UTC 時區 2015 年 4 月 16 日的下午 5:30 擷取更新的 blob `"/sample/2015-04-16/17:30/products.csv"`

> [AZURE.NOTE]僅當該時間與 Blob 名稱中編碼的時間一致時，目前串流分析工作才會尋找參考 Blob 重新整理資料：例如尋找 UTC 時區 2015 年 4 月 16 日下午 5:30 到 5:30:59.999999999 的 /sample/2015-04-16/17:30/products.csv 工作。當時間為下午 5:31 時，就會停止尋找 /sample/2015-04-16/17:30/products.csv，並開始尋找 /sample/2015-04-16/17:31/products.csv。

先前參考資料 Blob 唯一考量的時間就是該工作啟動的時間。在這個時間，此工作正在尋找其名稱含有以工作開始時間之前的值編碼的最近一個日期/時間 (該工作開始時間前最新的參考資料 Blob)。這是為了確保工作的開頭有非空白參考資料集。如果無法找到一個，工作就會失敗，並向使用者顯示診斷注意事項：

### 加入 Blob 儲存體做為參考資料  ###

1. 在串流分析工作的 [輸入] 索引標籤上，按一下 [加入輸入]，然後選取 [參考資料]，再按一下滑鼠右鍵。

    ![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

2.	輸入或選取下列欄位：

    - **輸入別名**：工作查詢中將用來參考這個輸入的易記名稱  
    - **儲存體帳戶**：如果儲存體帳戶位於串流工作以外的不同訂用帳戶，則將需要儲存體帳戶名稱和儲存體帳戶金鑰。  
    - **儲存體容器**：容器提供儲存在 Microsoft Azure Blob 服務中的 blob 的邏輯分組。當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。  
    - **路徑模式**：用來在指定容器中找出 Blob 的檔案路徑。在該路徑內，您也可以指定下列 2 個變數的一個或多個執行個體：{date} 和 {time}。  

    ![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

3. 選擇下列設定：

    - **事件序列化格式**：為了確定您的查詢運作如預期，串流分析需要知道您的內送資料流使用哪一種序列化格式 (JSON、CSV 或 Avro)。  
    - **編碼**：UTF-8 是目前唯一支援的編碼格式。  

    ![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

4.	按一下核取按鈕以完成精靈，並確認串流分析可成功連接到 Blob 儲存體帳戶。

    ![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)


## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics 管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1125_2015-->