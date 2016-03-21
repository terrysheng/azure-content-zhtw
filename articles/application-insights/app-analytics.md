<properties 
	pageTitle="Application Analytics - 適用於 Application Insights 的強大搜尋工具" 
	description="Application Analytics 概觀，適用於 Application Insights 的強大搜尋工具。" 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>




# Application Insights Analytics：語言概觀


[Application Insights Analytics](app-analytics.md) 是強大的查詢引擎，適合用於 [Application Insights](app-insights-overview.md) 遙測。這些頁面說明 Application Insights 查詢語言，AIQL。

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
標準的 AIQL 查詢是「來源」資料表，後面接著一系列由 `|` 隔開的「運算子」。

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

此語言不只有許多 SQL 的功能。如同使用 SQL，您可以篩選資料、群組記錄、排序和聯結資料表。您也可以在欄位上執行計算。不同於 SQL，這些功能分成不同的作業，並取代巢狀查詢，改用非常直覺的方式讓您將資料從一項作業傳送到下一項。這可讓您輕鬆地撰寫複雜的查詢。


>[AZURE.NOTE] 我們建議您從[語言教學](app-analytics-tour.md)開始。


## 連接到您的 Application Insights 資料


在 Application Insights 中，從您應用程式的[概觀刀鋒視窗](app-insights-dashboards.md)開啟 [分析]：

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->