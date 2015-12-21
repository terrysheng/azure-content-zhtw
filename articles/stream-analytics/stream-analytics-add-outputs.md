<properties 
	pageTitle="如何設定串流分析工作的資料輸出 | Microsoft Azure" 
	description="設定串流分析工作的輸出 | 學習路徑區段。"
	keywords="資料輸出, 資料移動"
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
	ms.date="12/04/2015" 
	ms.author="jeffstok"/>

# 如何設定串流分析工作的資料輸出

Azure 串流分析工作可以連線至一或多個輸出，且每個輸出都定義了一個與現有資料接收器之間的連線。當您的資料流分析工作處理並轉換傳入的資料時，系統會將資料輸出事件串流寫入工作的輸出中。

串流分析資料輸出可用來獲得即時的儀表板或警示資訊、觸發資料移動流程，或是單純保存資料以供稍後進行批次處理。串流分析與數個 Azure 服務具有第一級的整合，將在此詳述。

若要將輸入加入至串流分析工作：

1. 在 Azure 入口網站中，按一下 [**輸出**]，然後按一下串流分析工作的 [**加入輸出**]。

    ![加入輸出](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

    在 Azure Preview 入口網站中，按一下串流分析工作的 [**輸出**] 圖格。

    ![Azure 入口網站加入輸出](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. 指定輸出的類型：

    ![選擇資料移動類型](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

    ![在 Azure Preview 入口網站中選擇資料移動類型](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. 在 [**輸出別名**] 方塊中，替此輸出取一個好記的名稱。此名稱稍後可在作業查詢中用作指稱輸出。
    
    填寫其餘必要的連接屬性，以連接到輸出。這些欄位會因輸出類型而有所不同，其詳細定義在這裡。

    ![新增資料輸出屬性](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. 取決於輸出類型，您可能需要指定資料序列化或格式化的方式。每個輸出類型的特定序列化設定值記載於此。

    填寫其餘必要的連接屬性，以連接到資料來源。這些欄位會因輸入類型和來源類型而有所不同，詳細定義請見[此處](stream-analytics-create-a-job.md)。

    ![將資料輸出新增至事件中樞](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

    ![在 Azure 預覽入口網站中將資料輸出至事件中樞](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)

> [Azure.Note]任何新增至工作的輸出元素，都必須在工作開始之前，以及在事件開始運作之前就已經存在。例如，如果您把 Blob 儲存體當做輸出來使用，工作就不會自動建立儲存體帳戶。使用者必須在 Azure 串流分析工作開始之前建立儲存體帳戶。

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1210_2015-->