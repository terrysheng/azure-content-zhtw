<properties 
	pageTitle="串流分析警示 | Microsoft Azure" 
	description="了解串流分析警示" 
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

# 設定警示

## 監視頁面

您可以設定規則，以便在計量達到您所指定的條件時觸發警示。

例如，「如果過去 15 分鐘內的輸出事件 <100，則將電子郵件通知傳送給電子郵件識別碼：xyz@company.com”。

可透過入口網站在計量上設定規則，或透過作業記錄檔資料以[程式設計方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)來設定規則。

## 透過 Azure 入口網站設定警示

有兩種方式可以設定警示：

1.	您串流分析工作的 [監視] 索引標籤  
2.	管理服務中的作業記錄檔  

## 透過入口網站中工作的 [監視器] 索引標籤的警示

1.	在 [監視] 索引標籤中選取計量，然後按一下儀表板底部的 [加入規則] 按鈕，並設定您的規則。  

    ![儀表板](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	定義此警示的名稱和說明

    ![建立規則](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	輸入臨界值、警示評估視窗與警示的動作

    ![定義狀況](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## 透過作業記錄設定警示

1.	移至 [Azure 入口網站](https://manage.windowsazure.com)中管理服務的 [警示] 索引標籤  
2.	按一下 [加入規則]  

    ![準則](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	定義此警示的名稱和說明。選取「串流分析」做為服務類型，並選取工作名稱以做為服務名稱。

    ![定義警示](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->