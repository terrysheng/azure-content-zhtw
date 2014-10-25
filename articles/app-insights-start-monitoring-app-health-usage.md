<properties title="Application Insights" pageTitle="Application Insights - start monitoring your app's health and usage" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Application Insights - 開始監視應用程式的健康情況和流量

*Application Insights 目前僅供預覽。*

Application Insights 可讓您監視即時應用程式在以下各方面的情況：

-   **可用性** - 我們會每隔幾分鐘從世界各地測試一下 URL。
-   **效能** - 偵測及診斷效能問題和例外狀況。
-   **流量** - 了解哪些使用者將應用程式運用在哪些用途，以利改善應用程式。

組態作業非常簡單，幾分鐘之內就能看到結果。我們目前支援 ASP.NET Web 應用程式 (位於您自己的伺服器上或 Azure 上)。

您需要 [Microsoft Azure](http://azure.com) 的帳戶 (有免費的試用期)。

開始使用 Application Insights 的方法有兩種：

-   (建議) [將 Application Insights 新增至 Visual Studio 專案](#add)，以便監視應用程式效能和流量。
-   [在不重新部署的情況下將代理程式安裝在伺服器上][redfield] - 不需要重新部署即時網站或修改原始程式碼即可監視即時網站。這種方法能讓您監視效能和例外狀況。您可以在之後新增流量監視功能。

> [WACOM.NOTE] Visual Studio Online 備有[舊版的 Application Insights](http://msdn.microsoft.com/zh-tw/library/dn481095.aspx)，其支援的應用程式類型更多。我們正著手將其重新建置並納入 Microsoft Azure 中，而您正在閱讀的內容即是與該新版本有關。

## <a name="add"></a>將 Application Insights 新增至專案中

您需要 [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (或更新版本)。

### 對於新專案

當您在 Visual Studio 2013 中建立新專案時，請務必選取 Application Insights。

![Create an ASP.NET project](./media/appinsights/appinsights-01-vsnewp1.png)

如果這是您第一次使用，系統會要求您登入或註冊 Microsoft Azure 預覽 (該帳戶與 Visual Studio Online 帳戶彼此獨立)。

如果您希望 Azure 資源的名稱與專案名稱不同，抑或是您希望資源以不同專案的形式出現在相同群組下方，請使用 [組態設定]。

*沒有 Application Insights 選項嗎？請檢查您是否正在使用 Visual Studio 2013 Update 3、是否已在 [擴充功能和更新] 中啟用 Application Insights Tools，以及是否正在建立 Web、Windows 市集或 Windows Phone 專案。*

### ... 對於現有專案

在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [Add Application Insights]。

![Choose Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*如果您想要設定 Web 流量分析，還需要再執行一個步驟，不過我們不妨先來看看一些資料...*

### <a name="run"></a>2. 執行專案

利用 F5 執行應用程式並立即試用 - 開啟不同的頁面。

在 Visual Studio 中，您可以看見已接收到的事件計數。

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. 查看監視資料

從專案開啟 Application Insights。

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)

在 [應用程式健全狀況] 磚內搜尋資料。剛開始的時候，您只會看見一或兩個資料點。例如：

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

按一下任一個磚以查看詳細資料。您可以變更報告顯示的內容。[深入了解如何設定應用程式健全狀況報告。(英文)][perf]

### <a name="webclient"></a>4. 設定 Web 流量分析

如果您將 Application Insights 新增至*現有的* Web 專案中，目前還無法在 [流量分析] 磚內看見任何內容。您還需要完成一個步驟。

在 Application Insights 中選擇 [快速啟動]、[Instrument your website]。

![In your project in Application Insights, click Quick start, Instrument your website, and copy the code](./media/appinsights/appinsights-06webcode.png)

將 JavaScript 程式碼複製到要監視的網頁中，放置在結尾 \</head\> 標記之前。在 ASP.NET 專案中，監視所有網頁最理想的方法是將程式碼放置在主要頁面 (通常稱為 `_SiteLayout` 或 `Views\Shared\_Layout`) 中。請注意，程式碼含有應用程式的 Application Insights 金鑰。

再次執行應用程式，這次您將可以在 [流量分析] 中看見資料。

![Click through to more data](./media/appinsights/appinsights-05-usageTiles.png)

[逐一點選圖表以查看詳細資料。(英文)][perf]

### <a name="deploy"></a>5. 部署應用程式

部署應用程式並觀看資料累積情形。

一旦您擁有即時應用程式後，請[設定 Web 測試][availability] (英文) 以確認應用程式處於即時狀態。

![Example application monitor in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### 想要在入口網站中變更應用程式的名稱嗎？

您可以變更專案傳送遙測資料的目標資源。

(「資源」意指顯示結果的具名 Application Insights 分頁。您可以將多個資源放置在一個群組中。例如，如果您擁有數個組成商務應用程式之組件的專案。)

在 [方案總管] 中以滑鼠右鍵按一下 ApplicationInsights.config，然後選擇 [Update Application Insights]。如此可開啟精靈，以供您選擇其他現有資源或建立新資源。

接著，請返回入口網站並刪除舊有的資源。

## <a name="video"></a>視訊

### 簡介

> [AZURE.VIDEO application-insights-introduction]

### 開始使用

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>後續步驟

[追蹤 Web 應用程式的流量 (英文)][usage]

[監視 Web 應用程式的效能 (英文)][perf]

[擷取及搜尋診斷記錄 (英文)][diagnostic]

[疑難排解][qna]

## 詳細資訊

-   [Application Insights - 開始使用 (英文)][start]
-   [立即監視即時網頁伺服器 (英文)][redfield]
-   [監視 Web 應用程式的效能 (英文)][perf]
-   [搜尋診斷記錄 (英文)][diagnostic]
-   [利用 Web 測試追蹤可用性 (英文)][availability]
-   [追蹤流量][usage]
-   [問答集和疑難排解 (英文)][qna]

<!--Link references-->



  [redfield]: ../app-insights-monitor-performance-live-website-now/
  [perf]: ../app-insights-web-monitor-performance/
  [availability]: ../app-insights-monitor-web-app-availability/
  [usage]: ../app-insights-web-track-usage/
  [qna]: ../app-insights-troubleshoot-faq/
  [diagnostic]: ../app-insights-search-diagnostic-logs/ 
  [start]: ../app-insights-start-monitoring-app-health-usage/

