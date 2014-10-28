<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# 監視應用程式的健全狀況和使用情形

*Application Insights 處於預覽階段。*

確保您的應用程式運作正常且滿足使用者的需求。Application Insights 會向您警告任何效能問題，並協助找出和診斷根本原因。它可讓您追蹤使用者的活動，以便您調整應用程式來符合他們的需求。

Application Insights 可以監視託管於內部部署或虛擬機器上的 Windows Phone 應用程式、Windows 市集應用程式和 Web 應用程式，以及 Microsoft Azure 網站。

您需要有 [Visual Studio Update 3][Visual Studio Update 3] 和 [Microsoft Azure][Microsoft Azure] 中的帳戶 (有免費試用期間)。

1.  [加入 Application Insights][加入 Application Insights]

-   [執行專案][執行專案]
-   [查看監視資料][查看監視資料]
-   [部署應用程式][部署應用程式]
-   [後續步驟][後續步驟]

*此外，如果您要監視現有的 Web 服務，但不要重新部署它或使用 Visual Studio，您可以[在伺服器上安裝代理程式][在伺服器上安裝代理程式]。*

## <a name="add"></a>1. 加入 Application Insights

### 如果是新專案...

在 Visual Studio 2013 中建立新專案時，請確定已選取 Application Insights。

![Create an ASP.NET project][Create an ASP.NET project]

如果這是第一次，則會要求您提供 Microsoft Azure 預覽的登入認證，或要求您註冊(這與您的 Visual Studio Online 帳戶不同)。

> 沒有 Application Insights 選項？確定您是使用 Visual Studio 2013 Update 3，且正在建立 Web、Windows 市集或 Windows Phone 專案。

### ... 或者，如果是現有的專案

若要將 Application Insights 加入至新專案，請在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [加入 Application Insights]。

![Choose Add Application Insights][Choose Add Application Insights]

## <a name="run"></a>2. 執行專案

按 F5 執行應用程式並試驗一下 - 開啟不同頁面。

在 Visual Studio 中，您會看到已收到的事件計數。

![][]

## <a name="monitor"></a>3. 查看監視資料

從專案中開啟 Application Insights。

![Right-click your project and open the Azure portal][Right-click your project and open the Azure portal]

在 [應用程式健全狀況] 和 [流量分析] 磚中尋找資料。例如：

![Click through to more data][Click through to more data]

按一下任何磚以查看更多詳細資料。[深入了解磚和報告。][深入了解磚和報告。]

> [WACOM.NOTE] 許多磚在預覽版本中顯示的詳細資料有限。

## <a name="deploy"></a>4. 部署應用程式

部署應用程式並觀察資料不斷累積。

## <a name="next"></a>後續步驟

[深入了解磚和報告][深入了解磚和報告。]

[Web 測試][Web 測試]

[擷取和搜尋診斷記錄][擷取和搜尋診斷記錄]

[疑難排解][疑難排解]

## 詳細資訊

-   [Application Insights][Application Insights]
-   [將 Application Insights 加入至專案][將 Application Insights 加入至專案]
-   [立即監視即時 Web 伺服器][在伺服器上安裝代理程式]
-   [在 Application Insights 中探索度量][深入了解磚和報告。]
-   [診斷記錄搜尋][擷取和搜尋診斷記錄]
-   [使用 Web 測試來追蹤可用性][Web 測試]
-   [使用事件和度量來追蹤使用量][使用事件和度量來追蹤使用量]
-   [問與答及疑難排解][疑難排解]

<!--Link references-->

  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Microsoft Azure]: http://azure.com
  [加入 Application Insights]: #add
  [執行專案]: #run
  [查看監視資料]: #monitor
  [部署應用程式]: #deploy
  [後續步驟]: #next
  [在伺服器上安裝代理程式]: ../app-insights-monitor-performance-live-website-now/
  [Create an ASP.NET project]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Choose Add Application Insights]: ./media/appinsights/appinsights-03-addExisting.png
  []: ./media/appinsights/appinsights-09eventcount.png
  [Right-click your project and open the Azure portal]: ./media/appinsights/appinsights-04-openPortal.png
  [Click through to more data]: ./media/appinsights/appinsights-05-usageTiles.png
  [深入了解磚和報告。]: ../app-insights-explore-metrics/
  [Web 測試]: ../app-insights-monitor-web-app-availability/
  [擷取和搜尋診斷記錄]: ../app-insights-search-diagnostic-logs/
  [疑難排解]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [將 Application Insights 加入至專案]: ../app-insights-monitor-application-health-usage/
  [使用事件和度量來追蹤使用量]: ../app-insights-track-usage-custom-events-metrics/
