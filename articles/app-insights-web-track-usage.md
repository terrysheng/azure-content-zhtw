<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# 追蹤流量

## <a name="webclient"></a>設定 Web 流量分析

如果您尚未這麼做，請[將 Application Insights 新增至 Web 專案][將 Application Insights 新增至 Web 專案] (英文)。

## <a name="usage"></a>流量分析

在應用程式概觀分頁中，您可以看見以下流量磚：

![][]

### 每個瀏覽器的工作階段

*工作階段*是一段時間，其始於當使用者開啟網站上的任何頁面時，終於當使用者未在 30 分鐘的逾時期限內傳送任何 Web 要求後。

逐一點選以將圖表放大顯示。

### 熱門頁面檢視

顯示過去 24 小時的總計數。

按一下頁面檢視磚可取得詳細記錄。

![][1]

按一下 [時間範圍] 可查看七天內的長期記錄。

按一下圖形可查看其他可供顯示的度量。

![][2]

## 自訂頁面計數

依預設，每當用戶端瀏覽器載入新頁面時，頁面計數便會發生。不過您也許會想要計算其他頁面檢視。例如，由於頁面可能會將內容顯示在索引標籤中，因此您想要在使用者切換索引標籤時計算一次頁面。抑或是頁面中的 JavaScript 程式碼可能會在未變更瀏覽器 URL 的情況下載入新內容。

請將與以下範例相似的 JavaScript 呼叫插入用戶端程式碼中的適當位置：

    appInsights.trackPageView(myPageName);

頁面名稱可能會含有與 URL 相同的字元，不過 "\#" 或 "?" 之後的任何字元都將遭到忽略。

## 檢查個別的頁面檢視事件

頁面檢視遙測資料一般是由 Application Insights 進行分析，而您只會看見以所有使用者為單位平均計算的累積報告。然而在偵錯時，您也可以查看個別的頁面檢視事件。

在 [Diagnostic Search] 分頁中，將 [篩選器] 設定為 [網頁檢視]。

![][3]

選取任一事件以查看詳細資料。

> [WACOM.NOTE] 請注意，如果您使用[搜尋][搜尋]，必須比對整個單字："Abou" 和 "bout" 與 "About" 並不相符，不過 "Abou\* " 則會相符。此外，您不能將萬用字元當做搜尋詞彙的開頭。例如，搜尋 "\*bou" 將無法與 "About" 相符。

> [深入了解診斷搜尋][搜尋]

## 追蹤流量

> [AZURE.VIDEO tracking-usage-with-application-insights]

## 詳細資訊

-   [Application Insights - 開始使用 (英文)][將 Application Insights 新增至 Web 專案]
-   [立即監視即時網頁伺服器 (英文)][立即監視即時網頁伺服器 (英文)]
-   [監視 Web 應用程式的效能 (英文)][監視 Web 應用程式的效能 (英文)]
-   [搜尋診斷記錄 (英文)][搜尋]
-   [利用 Web 測試追蹤可用性 (英文)][利用 Web 測試追蹤可用性 (英文)]
-   [追蹤流量][追蹤流量]
-   [問答集和疑難排解 (英文)][問答集和疑難排解 (英文)]

<!--Link references-->

  [將 Application Insights 新增至 Web 專案]: ../app-insights-start-monitoring-app-health-usage/
  []: ./media/appinsights/appinsights-47usage.png
  [1]: ./media/appinsights/appinsights-49usage.png
  [2]: ./media/appinsights/appinsights-63usermetrics.png
  [3]: ./media/appinsights/appinsights-51searchpageviews.png
  [搜尋]: ../app-insights-search-diagnostic-logs/
  [立即監視即時網頁伺服器 (英文)]: ../app-insights-monitor-performance-live-website-now/
  [監視 Web 應用程式的效能 (英文)]: ../app-insights-web-monitor-performance/
  [利用 Web 測試追蹤可用性 (英文)]: ../app-insights-monitor-web-app-availability/
  [追蹤流量]: ../app-insights-web-track-usage/
  [問答集和疑難排解 (英文)]: ../app-insights-troubleshoot-faq/
