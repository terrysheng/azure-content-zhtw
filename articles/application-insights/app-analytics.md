<properties 
	pageTitle="Analytics - 強大的 Application Insights 搜尋工具" 
	description="Analytics 概觀，強大的 Application Insights 診斷搜尋工具。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>




# Application Insights 的 Analytics


[Analytics](app-analytics.md) 是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。這些頁面說明 Analytics 查詢語言。

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
標準的查詢是來源資料表，後面接著一系列由 `|` 隔開的運算子。

例如，讓我們來了解海德拉巴的市民在一天當中的哪些時間試用我們的 Web 應用程式。同時我們要看看針對他們的 HTTP 要求傳回哪些結果碼。

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

我們會計算不同的用戶端 IP 位址，並以過去 30 天每天的小時為單位將它們群組。

使用橫條圖展示來顯示結果，選擇以不同的回應碼來堆疊結果：

![選擇橫條圖、X 和 Y 軸，然後分割](./media/app-analytics/020.png)

看來我們的應用程式在海德拉巴的午休時間及就寢時間最受歡迎。(而且我們應該調查那些 500 的代碼。)


也有強大的統計運算功能︰

![](./media/app-analytics/025.png)


這個語言具有許多吸引人的功能︰

* 依任何欄位[篩選](app-analytics-queries.md)未經處理的 app 遙測，包括您的自訂屬性和計量。
* [加入](app-analytics-queries.md#join-operator)多個資料表 – 將要求與頁面檢視、相依性呼叫、例外狀況和記錄追蹤相互關聯。
* 功能強大的統計[彙總](app-analytics-aggregations.md)。
* 功能如同 SQL 一般強大，但更容易用來進行複雜的查詢︰您可以使用管線將資料從某一個基本運算傳送到下一個運算，而不需使用巢串陳述式。
* 立即且功能強大的視覺效果。



>[AZURE.NOTE] 我們建議您從[語言教學](app-analytics-tour.md)開始。


## 連接到您的 Application Insights 資料


在 Application Insights 中，從 app 的[概觀刀鋒視窗](app-insights-dashboards.md)開啟 [分析]：

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->