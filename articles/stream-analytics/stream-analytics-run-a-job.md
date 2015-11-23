<properties 
	pageTitle="執行工作 | Microsoft Azure" 
	description="執行工作學習路徑區段。"
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

# 執行工作

當已指定工作輸入、查詢及輸出時，即可開始串流分析工作。

開始您的工作：

1.	在 Azure 入口網站的工作儀表板，按一下頁面底部的 [開始]。

    ![開始按鈕](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

    在 Azure Preview 入口網站中，按一下工作頁面頂端的 [開始]。

    ![Azure Preview 入口網站 [開始] 按鈕](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)

2.	指定 [開始輸出] 值決定這項工作開始產生輸出的時間。先前從未開始之工作的預設設定為 [工作開始時間]，這表示此工作將立即開始處理資料。您也可以指定**自訂**的過去 (適用於使用歷程記錄的資料) 或未來 (延遲處理至未來時間) 時間。若作業先前曾開始並停止過，可選取 [上次停止時間] 選項，以便從上次輸出的時間點繼續工作並避免資料遺失。

    ![開始時間](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

    ![Azure Preview 入口網站開始時間](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)

3.	確認選擇。工作狀態會變更為 [開始]，且會在工作開始後的短時間內變為 [執行中]。您可以在 [通知中樞] 中監視**開始**作業的進度：

    ![進度](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

    ![Azure Preview 入口網站進度](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Nov15_HO3-->