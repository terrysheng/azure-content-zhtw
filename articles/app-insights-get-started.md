<properties title="Application Insights" pageTitle="Application Insights - 開始監視應用程式的健康情況和流量" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights - 開始監視應用程式的健康情況和流量

*Application Insights 目前為預覽階段。*

Application Insights 可讓您監視即時應用程式在以下各方面的情況：

* **可用性** - 我們會每隔幾分鐘從世界各地測試一下 URL。
* **效能** - 偵測及診斷效能問題和例外狀況。
* **流量** - 了解哪些使用者將應用程式運用在哪些用途，以利改善應用程式。

組態作業非常簡單，幾分鐘之內就能看到結果。我們目前支援 ASP.NET Web 應用程式 (位於您自己的伺服器上或 Azure 上)。


## 開始使用

從這個圖表左邊的進入點依照任何組合、任何順序開始。挑選適合您的途徑。如果您正在開發 ASP.NET Web 應用程式，首先請將 Application Insights 加入至您的 Web 專案 - 稍後可輕鬆加入其他項目。

您在 [Microsoft Azure](http://azure.com) 中需要帳戶 (除非使用 VSO 版本)。

<table >
<tr valign="top"><th>需求</th><th colspan="2">作法</th><th>What you get</th></tr>
<tr valign="top"><td>取得 ASP.NET 應用程式的效能和流量分析</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">將 Application Insights 加入至 Web 專案</a></td><td>效能度量：載入計數、回應時間、...</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">從伺服器程式碼傳送事件和度量</a></td><td>自訂商務分析</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">從伺服器傳送追蹤和例外狀況遙測，或擷取協力廠商記錄資料。</td><td>伺服器應用程式診斷。搜尋和篩選記錄資料。</a></td></tr>
<tr valign="top"><td>從網頁 (在任何平台上) 取得流量分析</td><td colspan="2"><a href="../app-insights-web-track-usage/">在網頁中插入 AI 指令碼</a></td><td>流量分析：頁面檢視、回訪使用者、工作階段計數</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">在網頁指令碼中撰寫事件和度量呼叫</a></td><td>自訂使用者體驗分析</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">在網頁指令碼中撰寫追蹤和診斷呼叫</a></td><td>搜尋和篩選記錄資料。</td></tr>
<tr valign="top"><td>在已執行於 Web 伺服器的 ASP.NET 應用程式中診斷問題</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">在 Web 伺服器上安裝狀態監視器</a></td><td>相依性呼叫持續時間和計數；CPU、記憶體和網路計數器；載入計數、回應時間</td></tr>
<tr valign="top"><td>監視任何網頁的可用性</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">在 Application Insights 上設定 Web 測試</a></td><td>可用性監視和警示</td></tr>
<tr valign="top"><td>取得 Windows Phone 應用程式、Windows 市集應用程式或 Java 網站的效能及流量分析</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">現在，請使用 VSO 舊版的 Application Insights</a></td><td>流量和效能分析。<a href="http://msdn.microsoft.com/library/dn793604.aspx">我們正逐步打造 Azure 版本的功能。</a></td></tr>
</table>


## <a name="video"></a>視訊

#### 簡介

> [AZURE.VIDEO application-insights-introduction]

#### 開始使用

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


