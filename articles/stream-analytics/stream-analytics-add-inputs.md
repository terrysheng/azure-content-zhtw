<properties 
	pageTitle="將資料輸入新增至串流分析工作中 | Microsoft Azure" 
	description="將輸入新增至串流分析工作中 | 學習路徑區段。"
	keywords="資料輸入, 串流資料"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"
/>


# 將串流資料輸入新增至串流分析工作中

Azure 串流分析工作可以連線至一或多個輸入，且每個輸入都定義了一個與現有資料來源之間的連線。當資料傳送到該資料來源時，串流分析工作會即時取用該資料，並把它當做串流資料來處理。在工作訂用帳戶內外，串流分析具有與 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)和 [Azure Blob 儲存體](./storage/storage-dotnet-how-to-use-blobs.md)的第一級整合。串流分析有兩種不同的輸入類型：資料流和參考資料。

- **資料流**：串流分析工作必須至少包含一個由工作取用和轉換的資料流輸入。支援將 Azure Blob 儲存體和 Azure 事件中樞當成資料流輸入來源。Azure 事件中樞是用於從多個連接的裝置和服務收集事件資料流。Azure Blob 儲存體可用於擷取大量資料作為資料流的輸入來源。  
- **參考資料**：串流分析會支援稱為參考資料的第二類型輔助輸入。與動態資料相反，這種資料是靜態或變化緩慢的。其通常與資料流搭配使用來執行查閱和關聯，以建立更豐富的資料集。在預覽版本中，Azure Blob 儲存體是目前唯一支援當成參考資料的輸入來源。  

若要將輸入加入串流分析工作中：

1. 在 Azure 入口網站中按一下 [**輸入**]，然後按一下串流分析工作的 [**加入輸入**]。

    ![新增串流資料的資料輸入](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)

    在 Azure Preview 入口網站中，按一下串流分析工作的 [**輸入**] 圖格。

    ![新增串流資料的資料輸入](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)

2. 指定輸入類型：**資料流**或**參考資料**。

    ![新增正確的資料輸入, 串流或參考](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)

    ![新增正確的資料輸入, 串流或參考](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)

3. 如果要建立資料流輸入，請指定輸入的來源類型。由於目前僅支援 Blob 儲存體，因此在參考資料建立期間可以省略此步驟。

    ![加入資料流資料輸入](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)

    ![加入資料流資料輸入](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)

4. 在 [輸入別名] 方塊中，替這個輸出取一個易記的名稱。此名稱稍後將在作業查詢中用作指稱輸入。

    填寫其餘必要的連接屬性，以連接到資料來源。這些欄位會因輸入類型和來源類型而有所不同，詳細定義請見[此處](stream-analytics-create-a-job.md)。

    ![新增事件中樞資料輸入](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)

5. 指定輸入資料的序列化設定：
	- 若要確定查詢會依照您所預期的方式處理，請指定傳入資料的**事件序列化格式**。支援的序列化格式為 JSON、CSV 及 Avro。
	- 確認資料的**編碼**。UTF-8 是目前唯一支援的編碼格式。

    ![資料輸入的資料序列化設定](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)

    ![資料輸入的資料序列化設定](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)

6. 完成建立輸入之後，串流分析會確認其是否可以連接到輸入來源。您可以在 [通知中樞] 中檢視測試連接作業的狀態。

    ![測試串流資料輸入的連線](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)

    ![測試串流資料輸入的連線](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->