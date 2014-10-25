<properties title="Application Insights" pageTitle="Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Application Insights - 開始使用

*Application Insights 處於預覽階段。*

Application Insights 可讓您監視即時應用程式的可用性、效能和使用情形(不見得一定是 Microsoft Azure 應用程式)。設定很簡單，一下子就能看到結果。

-   **可用性** - 確定 Web 應用程式可用且可回應。我們每隔幾分鐘就會從全球各地測試您的 URL，讓您知道是否有問題。
-   **效能** - 診斷 Web 服務中的任何效能問題或例外狀況。了解回應時間如何隨著要求計數而變化、查明 CPU 或其他資源是否遭到過度使用、從例外狀況取得堆疊追蹤，以及輕鬆地搜尋記錄追蹤。
-   **使用情形** - 了解使用者如何使用您的應用程式，讓您將開發工作投注在最實用的部分。目前，您可以監視 Web 應用程式、Windows 市集和 Windows Phone 應用程式。

## 開始使用

有兩種方式可開始使用：

-   [在 Visual Studio 中將 Application Insights 加入至專案][]

    將 Application Insights 加入至專案來追蹤使用情形、效能和可用性，以及分析診斷記錄。您在偵錯模式下幾分鐘內就可以看到資料，然後部署專案來取得即時資料。

    如果是更新或建立專案，請使用此選項。

    [將 Application Insights 加入至專案來開始使用。][在 Visual Studio 中將 Application Insights 加入至專案]

-   [立即診斷即時 Web 服務的問題][]

    在 IIS 伺服器上安裝 Application Insights 代理程式，幾分鐘之內就能看到效能資料。查看要求計數、回應時間、資源負載，並取得例外狀況追蹤。

    如果您需要立即了解 Web 伺服器的運作情形，請使用此選項。不需要重新部署您的程式碼。但您需要有伺服器的管理存取權，以及 Microsoft Azure 帳戶。

    您可以隨時加入可用性監視。

    稍後，您可以使用其他選項，將 Application Insights 加入至專案來分析診斷記錄和追蹤使用情形。

    [在 Web 伺服器上安裝 Application Insights 來開始使用。][立即診斷即時 Web 服務的問題]

> [WACOM.NOTE] Visual Studio Online 中有[舊版的 Application Insights][]。我們正從頭開始將它重建為 Microsoft Azure 的一部分，而這也是您正在此處閱讀的新版本。

![Example application monitor in Application Insights][]

## 詳細資訊

-   [Application Insights][]
-   [將 Application Insights 加入至專案][在 Visual Studio 中將 Application Insights 加入至專案]
-   [立即監視即時 Web 伺服器][立即診斷即時 Web 服務的問題]
-   [在 Application Insights 中探索度量][]
-   [診斷記錄搜尋][]
-   [使用 Web 測試來追蹤可用性][]
-   [使用事件和度量來追蹤使用量][]
-   [問與答及疑難排解][]

<!--Link references-->

  [在 Visual Studio 中將 Application Insights 加入至專案]: ../app-insights-monitor-application-health-usage/
  [立即診斷即時 Web 服務的問題]: ../app-insights-monitor-performance-live-website-now/
  [舊版的 Application Insights]: http://msdn.microsoft.com/en-us/library/dn481095.aspx
  [Example application monitor in Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Application Insights]: ../app-insights-get-started/
  [在 Application Insights 中探索度量]: ../app-insights-explore-metrics/
  [診斷記錄搜尋]: ../app-insights-search-diagnostic-logs/
  [使用 Web 測試來追蹤可用性]: ../app-insights-monitor-web-app-availability/
  [使用事件和度量來追蹤使用量]: ../app-insights-web-track-usage-custom-events-metrics/
  [問與答及疑難排解]: ../app-insights-troubleshoot-faq/
