<properties 
	pageTitle="Application Insights 的疑難排解與問題" 
	description="Visual Studio 的 Application Insights 哪個部分不清楚或無法運作嗎？ 試試這裡。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="awills"/>
 
# 問題 - ASP.NET 的 Application Insights

## 組態問題

*我有設定下列項目的問題：*

* [.NET 應用程式](app-insights-asp-net-troubleshoot-no-data.md)
* [監視已在執行的應用程式](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Azure 診斷](app-insights-azure-diagnostics.md)
* [Java Web 應用程式](app-insights-java-troubleshoot.md)
* [其他平台](app-insights-platforms.md)


## 我是否可以使用 Application Insights 搭配...？

[請參閱「平台」][platforms]


## 它是免費的嗎？

* 是的，如果您選擇免費[定價層](app-insights-pricing.md)。您會獲得大部分的功能和大量資料配額。 
* 您必須提供信用卡資料以向 Microsoft Azure 註冊，但我們不會收取任何費用，除非您使用其他付費 Azure 服務，或明確地升級至付費層。
* 如果您的應用程式傳送的資料超過免費層的每月配額，它會停止記錄。如果發生這種情況，您可以選擇開始付費，或等到月底重設配額。
* 基本的使用情況和工作階段資料不受配額限制。
* 另外也有 30 天的免費試用，在這 30 天內您可以得到免費的高階功能。
* 每個應用程式資源都有個別的配額，其定價層的設定與其他任何資源無關。

#### 付費能得到什麼？

* 較大的[每月資料配額](https://azure.microsoft.com/pricing/details/application-insights/)。
* 能夠選擇針對超額部分付費，以繼續收集超過每月配額的資料。如果您的資料超過配額，我們將會以每 Mb 來收費。
* [連續匯出](app-insights-export-telemetry.md)


## <a name="q14"></a>Application Insights 在我的專案中修改什麼？

詳細資料視專案類型而定。若是 Web 應用程式：


+ 在您的專案中加入這些檔案：

 + ApplicationInsights.config。
 + ai.js


+ 安裝這些 NuGet 套件：

 -  *Application Insights API* - 核心 API

 -  *Application Insights API for Web Applications* - 用來從伺服器傳送遙測

 -  *Application Insights API for JavaScript Applications* - 用來從用戶端傳送遙測

    套件包含這些組件：

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ 將項目插入至：

 - Web.config

 - packages.config

+ (僅限新專案 - 如果您[將 Application Insights 加入至現有的專案][start]，則必須手動執行。) 將程式碼片段插入至用戶端和伺服器程式碼中，以使用 Application Insights 資源識別碼將這些片段初始化。例如，在 MVC 應用程式中，程式碼會插入至主版頁面 Views/Shared/\_Layout.cshtml


## 如何從舊版 SDK 升級？

請參閱您的應用程式類型所適用之 SDK 的[版本資訊](app-insights-release-notes.md)。



## <a name="update"></a>如何變更我的專案將資料傳送到哪一個 Azure 資源？

在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config`，然後選擇 [**更新 Application Insights**]。您可以將資料傳送至 Azure 中的現有資源或新資源。更新精靈會變更 ApplicationInsights.config 中的檢測機碼，這決定伺服器 SDK 將您的資料送往何處。除非您取消選取 [全部更新]，否則它也會變更機碼出現在您網頁中的位置。


## <a name="q06"></a>在 Microsoft Azure 預覽主畫面上，該對應會顯示我的應用程式狀態嗎？

不會！ 它是顯示 Azure 服務的狀態。若要查看 Web 測試結果，請選擇 [瀏覽] > [Application Insights] > (您的應用程式)，然後查看 Web 測試結果。



#### <a name="data"></a>資料會保留在入口網站多久？ 是否安全？

請參閱[資料保留和隱私權][data]。

## 記錄

#### <a name="post"></a>如何在診斷搜尋中查看 POST 資料？

我們不會自動記錄 POST 資料，但您可以使用 TrackTrace 呼叫：將資料放置到訊息參數中。相較於字串屬性的限制，此呼叫可容許較長的大小，不過您無法篩選。

## Security

#### 保留在入口網站上的資料安全嗎？ 會保留多久？

請參閱[資料保留和隱私權][data]。


## <a name="q17"></a>我是否已啟用 Application Insights 中的所有項目？

<table border="1">
<tr><th>您應該會看到</th><th>如何取得</th><th>取得原因</th></tr>
<tr><td>可用性圖表</td><td><a href="../app-insights-monitor-web-app-availability/">Web 測試</a></td><td>知道您的 Web 應用程式已啟動</td></tr>
<tr><td>伺服器應用程式效能：回應時間...
</td><td><a href="../app-insights-asp-net/">將 Application Insights 加入至專案</a><br/>或 <br/><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a> (或撰寫您自己的程式碼以<a href="../app-insights-api-custom-events-metrics/#track-dependency">追蹤相依性</a>)</td><td>偵測效能問題</td></tr>
<tr><td>相依性遙測</td><td><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a></td><td>診斷資料庫或其他外部元件的問題</td></tr>
<tr><td>取得例外狀況的堆疊追蹤</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">在程式碼中插入 TrackException 呼叫</a> (但部分會自動報告)</td><td>偵測並診斷例外狀況</td></tr>
<tr><td>搜尋記錄追蹤</td><td><a href="../app-insights-search-diagnostic-logs/">加入記錄配接器</a></td><td>診斷例外狀況、效能問題</td></tr>
<tr><td>用戶端使用基本概念：頁面檢視、工作階段...</td><td><a href="../app-insights-javascript/">網頁中的 JavaScript 初始設定式</a></td><td>流量分析</td></tr>
<tr><td>用戶端自訂度量</td><td><a href="../app-insights-api-custom-events-metrics/">追蹤網頁中的呼叫</a></td><td>增強使用者經驗</td></tr>
<tr><td>伺服器自訂度量</td><td><a href="../app-insights-api-custom-events-metrics/">追蹤伺服器程式碼中的呼叫</a></td><td>商業智慧</td></tr>
</table>

如果您的 Web 服務在 Azure VM 中執行，您也可以從該處[取得診斷][azurediagnostic]。

## 自動化

您可以[撰寫 PowerShell 指令碼](app-insights-powershell.md)來建立和更新 Application Insights 資源。

## 更多解答

* [Application Insights 論壇](https://social.msdn.microsoft.com/Forums/vstudio/zh-TW/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0302_2016-------->