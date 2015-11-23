<properties 
	pageTitle="加入輸出 | Microsoft Azure" 
	description="加入輸出學習路徑區段。"
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
	ms.date="11/10/2015" 
	ms.author="jeffstok"/>

# 加入輸出

Azure 串流分析工作可以連接到一或多個輸出，這會定義現有資料接收器的連接。當您的資料流分析工作處理和轉換傳入的資料時，輸出事件串流會寫入工作的輸出中。

串流分析輸出可用於溯源即時儀表板或警示中、觸發下游工作流程，或僅只是保存資料供稍後批次處理。串流分析與數個 Azure 服務具有第一級的整合，將在此詳述。

若要將輸入加入至串流分析工作：

1. 在 Azure 入口網站中，按一下 [輸出]，然後按一下串流分析工作的 [加入輸出]。

    ![加入輸出](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

    在 Azure Preview 入口網站中，按一下串流分析工作的 [輸出] 圖格。

    ![Azure 入口網站加入輸出](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. 指定輸出的類型：

    ![選擇資料類型](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

    ![Azure Preview 入口網站中選擇資料類型](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. 在 [輸出別名] 方塊中，替這個輸出取一個好記的名稱。此名稱稍後可在作業查詢中用作指稱輸出。
    
    填寫其餘必要的連接屬性，以連接到輸出。這些欄位會因輸出類型而有所不同，其詳細定義在這裡。

    ![加入屬性](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. 取決於輸出類型，您可能需要指定資料序列化或格式化的方式。每個輸出類型的特定序列化設定值記載於此。

    填寫其餘必要的連接屬性，以連接到資料來源。這些欄位會因輸入類型和來源類型而有所不同，其詳細定義在[這裡](stream-analytics-create-a-job.md)。

    ![加入事件中樞](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

    ![Azure Preview 入口網站加入事件中樞](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Nov15_HO3-->