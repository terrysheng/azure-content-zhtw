<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# 疑難排解及問與答 - Microsoft Azure 預覽的應用程式分析

-   [我在 Visual Studio 中沒有看到任何選項可將 Application Insights 加入至我的專案][]
-   [已建立新的 Web 專案，但加入 Application Insights 失敗。][]
-   [我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。][]
-   [我在流量分析下沒有看到資料][]
-   [我查看 Microsoft Azure 預覽開始面板。我如何在 Application Insights 中尋找我的資料？][]
-   [在 Microsoft Azure 預覽主畫面上，該對應會顯示我的應用程式狀態嗎？][]
-   [當我使用 將 Application Insights 加我至我的應用程式 並開啟 Application Insights 入口網站時，看起來與螢幕擷取畫面完全不同。][]
-   [我可以使用 Application Insights 來監視內部網路 Web 伺服器嗎？][]
-   [我如何取得 Windows Phone 或 Windows 市集的資料？][]
-   [我如何在程式碼中查看我記錄的事件和頁面檢視？][]
-   [為何會有兩個 Application Insights 版本？][]
-   [Azure 版本的 Application Insights 目前缺少什麼功能？][]
-   [我如何取回原本在 Visual Studio Online 版本的 Application Insights 中很棒的所有功能？][]
-   [Application Insights 在我的專案中修改什麼？][]
-   [我如何在 Application Insights 中尋找我的結果？][]
-   [後續步驟][]

## <a name="q01"></a>我在 Visual Studio 中沒有看到任何選項可將 Application Insights 加入至我的專案

-   請確定您有 [Visual Studio Update 3][]。它會預先安裝 Application Insights Tools，您應該可以在擴充管理員中看到。
-   Application Insights on Microsoft Azure Preview 目前僅適用於 C\# 或 Visual Basic 的 ASP.NET Web 專案。
-   如果您有現有的專案，請移至 [方案總管]，確定您按一下 Web 專案 (不是其他專案或方案)。您應該會看到功能表項目 [Add Application Insights Telemetry to Project]。
-   如果是建立新專案，請在 Visual Studio 中開啟 [檔案] \> [新增專案]，然後選取 {Visual C\#|Visual Basic} \> [Web] \> [ASP.NET Web 應用程式]。應該會有 [Add Application Insights to Project] 的選項。

## <a name="q02"></a>已建立新的 Web 專案，但加入 Application Insights 失敗。

如果與 Application Insights 入口網站通訊失敗，或您的帳戶有問題，就可能發生此情形。

-   請檢查您為正確的 Azure 帳戶提供登入認證。Microsoft Azure 認證 (在 [新增專案] 對話方塊中) 與 Visual Studio Online 認證 (在 Visual Studio 右上方) 可能不同。
-   請稍候，再[將 Application Insights 加入至現有的專案][]。
-   移至 Microsoft Azure 帳戶設定來檢查限制。檢查您是否可以手動加入 Application Insights 應用程式。

## <a name="q03"></a>我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。

-   您必須關閉再開啟您在等待資料的任何分頁。在目前版本中，分頁的內容不會自動重新整理。
-   在 Microsoft Azure 開始面板中，查看服務狀態對應。如果看到一些警示指示，請等待它們恢復 [正常]，然後關閉再重新開啟 Application Insights 應用程式分頁。
-   在 Web 伺服器的防火牆中，您可能必須為連出流量開啟連接埠 443。

## <a name="q04"></a>我在流量分析下沒有看到資料

-   資料來自網頁中的指令碼。如果您將 Application Insights 加入至現有的 Web 專案，則[您必須手動加入指令碼][將 Application Insights 加入至現有的專案]。

## <a name="q05"></a>我查看 Microsoft Azure 預覽開始面板。我如何在 Application Insights 中尋找我的資料？

下列其中一種方法：

-   選擇 [瀏覽]、[Application Insights]、您的專案名稱。如果您在其中沒有任何專案，則需要[在 Visual Studio 中將 Application Insights 加入至 Web 專案][將 Application Insights 加入至現有的專案]。

-   在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下 Web 專案，然後選擇 [Open Application Insights Portal]。

## <a name="q06"></a>在 Microsoft Azure 預覽主畫面上，該對應會顯示我的應用程式狀態嗎？

不會！它是顯示 Azure 服務的狀態。若要查看 Web 測試結果，請選擇 [瀏覽] \> [Application Insights] \> (您的應用程式)，然後查看 Web 測試結果。

## <a name="q07"></a>當我使用 [將 Application Insights 加我至我的應用程式] 並開啟 Application Insights 入口網站時，看起來與螢幕擷取畫面完全不同。

您可能是使用舊版的 Application Insights Tools，它會連接至 Visual Studio Online 版本。

您查看的說明頁面是參考 Application Insights for Microsoft Azure Preview，這在 Visual Studio Update 3 中已開啟。

## <a name="q08"></a>我可以使用 Application Insights 來監視內部網路 Web 伺服器嗎？

是，如果您的伺服器可以傳送資料至公用網際網路，您可以監視健全狀況和使用量。

但如果您想要對服務執行 Web 測試，則必須能夠從公用網際網路存取它。

## <a name="q10"></a>我如何在程式碼中查看我記錄的事件和頁面檢視？

我們在 Microsoft Azure 版本中還不支援此功能。敬請期待。目前，您可以嘗試使用[舊版本][]。

## <a name="q11"></a>為何會有兩個 Application Insights 版本？

舊版入口網站是 Visual Studio Online 的一部分。我們不再對此版本進行大幅變更。如果您有舊版的 Application Insights Tools for Visual Studio，它們會連接至 Visual Studio Online 入口網站。

Visual Studio Update 3 附隨預先安裝的新版 Application Insights Tools。它們會連接至新的 Application Insights 入口網站 (Microsoft Azure 預覽的一個元件)。我們正在將 Application Insights 移植到這個新環境。工作尚未完成，仍有許多限制。

## <a name="q12"></a>Azure 版本的 Application Insights 目前缺少什麼功能？

重要的工作都已展開。

但在目前，主要缺少的功能包括：對於 Windows Phone 和 Windows 市集等裝置應用程式的支援，以及 `trackEvent()` 和 `trackPageView()` 等自訂遙測的報告。

## <a name="q13"></a>我如何取回原本在 Visual Studio Online 版本的 Application Insights 中的所有功能？

1.  進入 Visual Studio 的擴充管理員。
2.  解除安裝 Application Insights Tools。
3.  執行[舊版工具的安裝程式][]，並閱讀其[入門指南][舊版本]。

## <a name="q14"></a>Application Insights 在我的專案中修改什麼？

-   在您的專案中加入這些檔案：

-   ApplicationInsights.config。
-   ai.js

-   安裝這些 NuGet 套件：

-   *Application Insights API* - 核心 API

-   *Application Insights API for Web Applications* - 用來從伺服器傳送遙測

-   *Application Insights API for JavaScript Applications* - 用來從用戶端傳送遙測

    套件包含這些組件：

-   Microsoft.ApplicationInsights

-   Microsoft.ApplicationInsights.Platform

-   (僅限新專案 - 如果您[將 Application Insights 加入至現有的專案][]，則必須手動執行。)將片段插入至用戶端和伺服器程式碼中，以使用 Application Insights 資源識別碼將它們初始化。例如，在 MVC 應用程式中，程式碼會插入至：

-   主版頁面 Views/Shared/\_Layout.cshtml

-   Web.config

-   packages.config

## <a name="q15"></a>我如何在 Application Insights 中尋找我的結果？

1.  開啟 Microsoft Azure：

  - 在 Visual Studio 中，以滑鼠右鍵按一下 Web 應用程式專案，然後選擇 [Open Azure Preview Portal]。
  - 或者，在網頁瀏覽器中，您可以在 Microsoft Azure 預覽中開啟您的帳戶。

1.  選擇 [瀏覽]、[Application Insights]，然後選取您的專案。

## <a name="next"></a>詳細資訊

-   [Application Insights][]
-   [將 Application Insights 加入至專案][將 Application Insights 加入至現有的專案]
-   [立即監視即時 Web 伺服器][]
-   [在 Application Insights 中探索度量][]
-   [診斷記錄搜尋][]
-   [使用 Web 測試來追蹤可用性][]
-   [使用事件和度量來追蹤使用量][]
-   [問與答及疑難排解][]

<!--Link references-->

  [我在 Visual Studio 中沒有看到任何選項可將 Application Insights 加入至我的專案]: #q01
  [已建立新的 Web 專案，但加入 Application Insights 失敗。]: #q02
  [我已成功加入 Application Insights 並執行我的應用程式，但在入口網站中從未看到資料。]: #q03
  [我在流量分析下沒有看到資料]: #q04
  [我查看 Microsoft Azure 預覽開始面板。我如何在 Application Insights 中尋找我的資料？]: #q05
  [在 Microsoft Azure 預覽主畫面上，該對應會顯示我的應用程式狀態嗎？]: #q06
  [當我使用 將 Application Insights 加我至我的應用程式 並開啟 Application Insights 入口網站時，看起來與螢幕擷取畫面完全不同。]: #q07
  [我可以使用 Application Insights 來監視內部網路 Web 伺服器嗎？]: #q08
  [我如何取得 Windows Phone 或 Windows 市集的資料？]: #q09
  [我如何在程式碼中查看我記錄的事件和頁面檢視？]: #q10
  [為何會有兩個 Application Insights 版本？]: #q11
  [Azure 版本的 Application Insights 目前缺少什麼功能？]: #q12
  [我如何取回原本在 Visual Studio Online 版本的 Application Insights 中很棒的所有功能？]: #q13
  [Application Insights 在我的專案中修改什麼？]: #q14
  [我如何在 Application Insights 中尋找我的結果？]: #q15
  [後續步驟]: #next
  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [將 Application Insights 加入至現有的專案]: ../app-insights-monitor-application-health-usage/
  [舊版本]: http://www.visualstudio.com/get-started/get-usage-data-vs
  [舊版工具的安裝程式]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
  [Application Insights]: ../app-insights-get-started/
  [立即監視即時 Web 伺服器]: ../app-insights-monitor-performance-live-website-now/
  [在 Application Insights 中探索度量]: ../app-insights-explore-metrics/
  [診斷記錄搜尋]: ../app-insights-search-diagnostic-logs/
  [使用 Web 測試來追蹤可用性]: ../app-insights-monitor-web-app-availability/
  [使用事件和度量來追蹤使用量]: ../app-insights-track-usage-custom-events-metrics/
  [問與答及疑難排解]: ../app-insights-troubleshoot-faq/
