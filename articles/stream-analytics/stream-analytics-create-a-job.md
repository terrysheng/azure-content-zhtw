<properties 
	pageTitle="建立工作 | Microsoft Azure" 
	description="建立工作學習路徑區段。"
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
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>

# 建立工作

Azure 串流分析中的最上層資源就是串流分析工作。其包含一或多個輸入資料來源、一個表示資料轉換的查詢，以及一或多個用來寫入結果的輸出目標。

若要開始使用串流分析，請建立新的串流分析工作。請注意直到開始工作之前，這個動作不會以任何方式計費。

1.  登入線上 [Microsoft Azure 入口網站](http://manage.windowsazure.com)或 Azure Preview 入口網站。
2.  在 Azure 入口網站中：按一下 [新增]，然後依序按一下 [資料服務]、[串流分析] 及 [快速建立]。

    ![快速建立精靈](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

    在 Azure Preview 入口網站：依序按一下 [新增]、[資料] 和 [分析]，然後按一下 [Azure 串流分析]。

    ![Azure 入口網站建立工作](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)

3.  為串流分析工作指定所需的組態。
	- 在 [工作名稱] 方塊中輸入名稱，以識別串流分析工作。[工作名稱] 經驗證後，[工作名稱] 方塊中便會出現綠色的核取記號。此 [工作名稱] 只能包含英數及 '-' 字元，且長度必須介於 3 到 63 個字元。
	- 使用 Azure 入口網站的 [區域] 或 Azure Preview 入口網站的 [位置] 來指定您想要執行工作的地理位置。
	- 如果使用 Azure 入口網站，請選取或建立儲存體帳戶，做為 [區域監視儲存體帳戶]。此儲存體帳戶會用來儲存在此區域內執行之所有串流分析工作的監視資料。
	- 如果使用 Azure Preview 入口網站、請指定新的或現有的**資源群組**來保存應用程式的相關資源。

4.  設定完新的串流分析工作選項後，請按一下 [建立串流分析工作]。建立串流分析工作可能需要數分鐘的時間。若要檢查狀態，您可以監視 [通知中樞] 中的進度。

    ![通知中樞](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

    ![Azure Preview 入口網站建立工作](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)

5.  新工作會以 [已建立] 的狀態列出。請注意，[開始] 按鈕已停用。您必須先設定工作輸入、查詢、輸出等項目，才能開始工作。

    ![工作狀態](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

    ![Azure Preview 入口網站工作狀態](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO3-->