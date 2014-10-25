<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# 監視 Web 應用程式的效能

*Application Insights 目前僅供預覽。*

確認應用程式的運作狀況良好，以及迅速找出任何失敗。[Application Insights][start] 能指出任何效能問題和例外狀況，以及協助您尋找及診斷根本原因。

Application Insights 能監視託管於內部部署設施或虛擬機器上的 ASP.NET Web 應用程式和 Microsoft Azure 網站。

-   [設定效能監視](#setup)
-   [查看資料](#view)
-   [它們有哪些意義？](#metrics)
-   [診斷問題](#diagnosis)
-   [後續步驟](#next)

## <a name="setup"></a>設定效能監視

如果您尚未將 Application Insights 新增至專案中 (亦即專案沒有 ApplicationInsights.config)，請選擇以下任一種方法來開始使用：

-   [將 Application Insights 新增至 Visual Studio 中的應用程式專案][start] (英文) - 建議的方法。除了被動效能監視之外，您還可以插入診斷記錄及追蹤流量。
-   [立即監視即時網站的效能][redfield] (英文) - 如果您選用這個方法，便不需要更新應用程式專案或重新部署網站。

## <a name="view"></a>檢視報告

利用 F5 執行應用程式並立即試用 - 開啟不同的頁面。

在 Visual Studio 中，您可以看見已接收到的事件計數。

![](./media/appinsights/appinsights-09eventcount.png)

從專案開啟 Application Insights。

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)

在 [應用程式健全狀況] 磚內搜尋資料。剛開始的時候，您只會看見一或兩個資料點。例如：

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

以偵錯模式執行時，系統會透過管線迅速傳送遙測資料，因此您應該可以在幾秒內看見資料。在部署應用程式時，資料累積會較為緩慢。

如果您一開始看不到任何資料，請稍做等待後再按一下 [重新整理]。

### 探索度量

按一下任一個磚可查看詳細資料，以及查看較長期的結果。例如，按一下 [要求] 磚，然後選取時間範圍：

![Click through to more data and select a time range](./media/appinsights/appinsights-48metrics.png)

按一下圖形以選擇要顯示的度量：

![Click a graph to choose metrics](./media/appinsights/appinsights-61perfchoices.png)

> [AZURE.NOTE] **取消勾選所有度量**可查看所有可供選擇的項目。度量分為多個群組；當您選取群組的任何成員時，只有該群組的其他成員會出現。

## <a name="metrics"></a>它們有哪些意義？效能磚和報告

您可以取用多種效能度量。我們從預設顯示在應用程式分頁中的度量開始討論。

### 要求

在指定期間內接收到的 HTTP 要求數量。將此度量與其他報告中的結果比較，可了解應用程式在各種負載下的行為。

HTTP 要求包括頁面、資料及影像的所有 GET 或 POST 要求。

按一下磚可取得特定 URL 的計數。

### 平均回應時間

測量從 Web 要求進入應用程式到傳回回應之間的時間。

資料點會指出移動平均值。在有許多要求的情況下，可能會有一些要求偏離平均值，但在圖表中沒有顯著的高低起伏。

找尋異常的高峰。一般來說，當要求增加時，回應時間也會上升。如果出現不相稱的上升跡象，表示應用程式可能遭遇到資源限制 (例如，應用程式使用之服務的 CPU 或容量)。

按一下磚可取得特定 URL 的時間。

![](./media/appinsights/appinsights-42reqs.png)

### 最慢的要求

![](./media/appinsights/appinsights-44slowest.png)

顯示可能需要進行效能調整的要求。

### 失敗的要求

![](./media/appinsights/appinsights-46failed.png)

丟出無法攔截之例外狀況的要求計數。

按一下磚可查看特定失敗的詳細資料；選取個別要求可查看要求的詳細資料。

系統只會針對各項檢查保留一個具代表性的失敗範例。

### 其他度量

若要查看其他可顯示的度量，請按一下圖表，然後取消選取所有度量以查看可供使用的完整集合。按一下 (i) 可查看各項度量的定義。

![Deselect all metrics to see the whole set](./media/appinsights/appinsights-62allchoices.png)

選取任何度量都會隱藏其他無法顯示在同一圖表中的度量。

## <a name="diagnosis"></a>診斷問題

以下是幾個尋找及診斷效能問題的訣竅：

-   設定 [Web 測試][availability] (英文)，以在網站故障、回應異常或過於緩慢時收到警示。
-   比較要求計數與其他度量，了解失敗或回應過慢的情況是否與負載有關。
-   在程式碼中[插入及搜尋追蹤陳述式][diagnostic] (英文) 有助於找出問題所在。

## <a name="next"></a>後續步驟

[Web 測試][availability] (英文) - 定期從全世界傳送 Web 要求給應用程式。

[擷取及搜尋診斷追蹤][diagnostic] (英文) - 插入追蹤呼叫並詳查結果，以便找出問題所在。

[流量追蹤][usage] (英文) - 了解使用者使用應用程式的情況。

[疑難排解][qna] (英文) - 和問答集

## 詳細資訊

-   [Application Insights - 開始使用 (英文)][start]
-   [立即監視即時網頁伺服器 (英文)][redfield]
-   [監視 Web 應用程式的效能 (英文)][perf]
-   [搜尋診斷記錄 (英文)][diagnostic]
-   [利用 Web 測試追蹤可用性 (英文)][availability]
-   [追蹤流量][usage]
-   [問答集和疑難排解 (英文)][qna]

<!--Link references-->

[start]: ../app-insights-start-monitoring-app-health-usage/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[qna]: ../app-insights-troubleshoot-faq/
[webclient]: ../app-insights-start-monitoring-app-health-usage/#webclient

