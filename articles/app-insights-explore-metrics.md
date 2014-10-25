<properties title="Explore your metrics" pageTitle="Explore your metrics" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# 探索度量

如果您尚未[設定 Application Insights 的 Web 專案][]，請立即進行。

以下是您在 Application Insights 中的應用程式分頁上應該可看到的報告。若要從 Visual Studio 前往此頁面，請以滑鼠右鍵按一下 Web 專案，然後選擇 [Open Application Insights]。若要從 Microsoft Azure 預覽開始面板前往此頁面，請選擇 [瀏覽]、[Application Insights]，然後選取您的專案。

-   [應用程式健全狀況][]
-   [流量分析][]
-   [後續步驟][]

## <a name="health"></a>應用程式健全狀況

經由檢測應用程式的程式碼，可監視應用程式健全狀況。

![][]

### 平均回應時間

測量從 Web 要求進入應用程式到傳回回應為止所花費的時間。

圖點顯示移動平圴值。如果有許多要求，有些可能會偏離平均值，但圖表上沒有明顯的突起或下降。

尋找異常突起。一般而言，回應時間會隨著要求數目增加而上升。如果上升不成比例，表示應用程式可能達到資源限制，例如 CPU 或它所使用的服務容量。

### 要求

在指定期間收到的要求數。請比較此數據與其他圖表上的結果，以了解應用程式在不同負載下的運作情形。

### Web 測試

![][1]

[Web 測試][]會顯示從全球各地的 Application Insights 伺服器，定期將 Web 要求傳送至您伺服器的結果。

請檢查結果是否隨著要求計數而變化。

[如何設定 Web 測試][Web 測試]。

### 最慢的要求

![][2]

顯示哪些要求可能需要進行效能調整。

### 診斷搜尋

![][3]

如果您已[設定診斷記錄][]，請點選查看最新的事件。

### 失敗的要求

![][4]

擲出未攔截到之例外狀況的要求計數。

## <a name="usage"></a>流量分析

![][5]

使用量資料有一部分來自伺服器，也有一部分來自[網頁中的指令碼][設定 Application Insights 的 Web 專案]。

### 每個瀏覽器的工作階段數

「工作階段」是一段時間，從使用者在您網站上開啟任何頁面起算，並於使用者在 30 分鐘的逾時期間內未傳送任何 Web 要求時結束。

請點選來放大圖表。

### 熱門頁面檢視

顯示過去 24 小時的總計數。

請點選查看上週的頁面檢視圖表。

### 重新排列磚

![Choose settings, customize][]

## <a name="next"></a>後續步驟

[設定 Web 測試][Web 測試]

[擷取和搜尋診斷記錄][設定診斷記錄]

[疑難排解][]

## 詳細資訊

-   [Application Insights][]
-   [將 Application Insights 加入至專案][設定 Application Insights 的 Web 專案]
-   [立即監視即時 Web 伺服器][]
-   [在 Application Insights 中探索度量][]
-   [診斷記錄搜尋][設定診斷記錄]
-   [使用 Web 測試來追蹤可用性][Web 測試]
-   [使用事件和度量來追蹤使用量][]
-   [問與答及疑難排解][疑難排解]

<!--Link references-->

  [設定 Application Insights 的 Web 專案]: ../app-insights-monitor-application-health-usage/
  [應用程式健全狀況]: #health
  [流量分析]: #usage
  [後續步驟]: #next
  []: ./media/appinsights/appinsights-42reqs.png
  [1]: ./media/appinsights/appinsights-43webtests.png
  [Web 測試]: ../app-insights-monitor-web-app-availability/
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-45diagnostic.png
  [設定診斷記錄]: ../app-insights-search-diagnostic-logs/
  [4]: ./media/appinsights/appinsights-46failed.png
  [5]: ./media/appinsights/appinsights-47usage.png
  [Choose settings, customize]: ./media/appinsights/appinsights-21-customizeblade.png
  [疑難排解]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [立即監視即時 Web 伺服器]: ../app-insights-monitor-performance-live-website-now/
  [在 Application Insights 中探索度量]: ../app-insights-explore-metrics/
  [使用事件和度量來追蹤使用量]: ../app-insights-track-usage-custom-events-metrics/
