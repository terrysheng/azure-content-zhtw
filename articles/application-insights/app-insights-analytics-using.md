<properties 
	pageTitle="使用分析 - 強大的 Application Insights 搜尋工具" 
	description="使用分析，這是強大的 Application Insights 診斷搜尋工具。" 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>




# 使用 Application Insights 中的分析


[分析](app-insights-analytics.md) 是 [Application Insights](app-insights-overview.md) 的強大搜尋功能。這些頁面說明 Analytics 查詢語言。

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

## 開啟分析

在 Application Insights 中，從您的應用程式的首頁資源，按一下 [分析]。

![開啟 portal.azure.com，開啟您的 Application Insights 資源，然後按一下 [分析]。](./media/app-insights-analytics/001.png)

內嵌教學課程將提供您一些可執行作業的概念。

[這裡有更廣泛的教學課程](app-insights-analytics-tour.md)。

## 撰寫查詢

撰寫以任何列在左側的資料表名稱開頭的查詢。使用 `|` 建立[運算子](app-insights-analytics-queries.md)的管線。


![](./media/app-insights-analytics-using/150.png)

* 請勿在查詢中放置空白行。
* 您可以在查詢中使用單一分行符號。
* 您可以在視窗中顯示數個查詢 (以空白行分隔)。
* 若要執行查詢，**將游標放在查詢內部或結尾**，然後按一下 [執行]。


![](./media/app-insights-analytics-using/130.png)

* 您可以儲存並回收查詢視窗的內容。

![](./media/app-insights-analytics-using/140.png)

## 排列結果

您可以挑選您要查看的資料行。展開任何項目以查看所有傳回的資料行值。

![](./media/app-insights-analytics-using/030.png)

> [AZURE.NOTE] 按一下資料行的標頭，即可快速重新排列網頁瀏覽器中可用的結果。但請注意，對大型結果集而言，下載至瀏覽器的資料列數目有限。所以，以這種方式排序並不一定會顯示實際的最高或最低項目。為此，您應該使用[top](app-insights-analytics-queries.md#top-operator) 或 [sort](app-insights-analytics-queries.md#sort-operator) 運算子。

但使用 [take](app-insights-analytics-queries.md#take-operator)、[top](app-insights-analytics-queries.md#top-operator) 或 [summarize](app-insights-analytics-queries.md#summarize-operator) 運算子，避免從伺服器下載大型資料表是相當好的做法。無論如何都自動限制每個查詢 10000 個資料列。


## 圖表

選取您想要的圖表類型︰

![](./media/app-insights-analytics-using/230.png)

如果您有數個正確類型的資料行，您可以選擇 x 和 y 軸，以及一個資料行的維度來據以分割結果︰

![](./media/app-insights-analytics-using/100.png)

根據預設，結果一開始會顯示為資料表，而您會手動選取圖表。但您可以在查詢結尾使用 [Render 指示詞](app-insights-analytics-queries.md#render-directive)以選取圖表。

## 匯出至 Excel

執行查詢之後，您可以下載 .csv 檔案。按一下 [匯出至 Excel]。

## 匯出至 Power BI

1. 將游標放在查詢中，然後選擇 [匯出至 Power BI]。

    ![](./media/app-insights-analytics-using/240.png)

    這會下載 M 指令碼檔案。

3. 將 M 語言指令碼複製到 Power BI Desktop 進階查詢編輯器中。
 * 開啟匯出的檔案。
 * 在 Power BI Desktop 中選取︰[取得資料]、[空白查詢]、[進階編輯器]，並貼上 M 語言指令碼。

    ![](./media/app-insights-analytics-using/250.png)

4. 視需要編輯認證，您現在可以建置您的報告。

    ![](./media/app-insights-analytics-using/260.png)



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->