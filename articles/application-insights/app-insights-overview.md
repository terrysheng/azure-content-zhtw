<properties 
	pageTitle="什麼是 Application Insights？" 
	description="追蹤即時 Web 或裝置應用程式的使用情况和效能。偵測、分級和診斷問題。持續監視並改善您的使用者的成功。" 
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
	ms.date="11/23/2015" 
	ms.author="awills"/>
 
# 什麼是 Application Insights？

Application Insights 是一項可延伸分析服務，會監視您的即時應用程式。該服務可以協助您偵測並診斷效能問題，並了解實際上使用者如何運用您的應用程式。它是針對開發人員設計，以協助您持續改善應用程式的效能和可用性。

![製作使用者活動統計資料的圖表，或深入特定事件。](./media/app-insights-overview/00-sample.png)

它可在各種不同的平台上搭配 Web 和獨立式應用程式使用：裝載在內部部署或雲端的 .NET 或 J2EE。

Application Insights 是以開發團隊為目標。使用它，您可以：

* [分析使用量模式][knowUsers]以更了解您的使用者，並不斷改善您的應用程式。 
 * 頁面檢視計數、新的和返回的使用者、地理位置、平台以及其他核心使用量分析
 * 追蹤使用量路徑來評估每個功能的成功。
* [偵測、分級及診斷][detect]效能問題，並且在大部分的使用者感知之前加以修正。
 *  效能變更或當機的警示。
 *  度量，用以協助診斷效能問題，例如回應時間、CPU 使用量、相依性追蹤。
 *  Web 應用程式的可用性測試。
 *  例外狀況報告和警示
 *  強大的診斷記錄搜尋 (包括從您最愛的記錄架構的記錄追蹤)。

每個平台的 SDK 包含一系列的模組，可直接監視應用程式。此外，您可以撰寫自己的遙測，以取得更詳細且量身訂作的分析。

從您的應用程式收集而來的遙測資料會儲存並在 Azure 入口網站中分析，其中有直覺式的檢視，以及用於快速診斷和分析的功能強大的工具。



想要進一步分析嗎？ 將您的資料[匯出](app-insights-export-telemetry.md)到 [SQL](app-insights-code-sample-export-telemetry-sql-database.md)、[Power BI](app-insights-export-power-bi.md)，或是您自己的工具。

![在 Power BI 中檢視資料](./media/app-insights-overview/210.png)

## 平台和語言

針對範圍日益成長的平台提供了 SDK。目前此清單包括：

 * Azure 或您 IIS 伺服器上的 [ASP.NET 伺服器][greenbrown]
 * [Azure 雲端服務](app-insights-cloudservices.md)
 * [J2EE 伺服器][java]
 * [網頁][client]：HTML+JavaScript
 * [Windows 桌面版和伺服器的應用程式][desktop]
 * [其他平台][platforms] - Node.js、PHP、Python、Ruby、Joomla、SharePoint、WordPress

Application Insights 也可以從現有 ASP.NET Web 應用程式取得遙測，而不需重建它們。

如果您的應用程式有用戶端、伺服器和其他元件，您可以將它們全部檢測。資料將在 Application Insights 入口網站中整合，使得您可以將用戶端的事件與伺服器的事件相互關聯。


## 運作方式

您在應用程式中安裝小型 SDK，並且在 Application Insights 入口網站設定帳戶。SDK 會監視您的應用程式，並將遙測資料傳送至入口網站。入口網站會顯示統計圖表，並提供強大的搜尋工具以協助您診斷任何問題。

![您的應用程式中的 Application Insights SDK 會將遙測傳送到 Azure 入口網站中的 Application Insights 資源。](./media/app-insights-overview/01-scheme.png)

SDK 有數個模組收集遙測，例如，計算使用者、工作階段和效能。您也可以撰寫自己的自訂程式碼，將遙測資料傳送至入口網站。自訂遙測在追蹤使用者劇本時特別有用：您可以計算事件，例如按鈕點擊、達成特定目標或使用者錯誤。

對於 ASP.NET 伺服器與 Azure Web 應用程式，您也可以安裝[狀態監視器][redfield]，有兩種用法。它可讓您：

* 監視 Web 應用程式，而不需要重新建立或重新安裝它。
* 追蹤對相依模組的呼叫。



### 負荷為何？

對您的效能的影響很小。追蹤會呼叫非封鎖性，並且批次處理要求並在另一個執行緒中傳送。



## 若要開始使用

1. 您將需要 [Microsoft Azure](http://azure.com) 訂用帳戶。您可以免費註冊，並選擇 Application Insights 的免費[定價層](https://azure.microsoft.com/pricing/details/application-insights/)。

2. 登入 [Azure 入口網站](http://portal.azure.com)
3. 建立 Application Insights 資源。這是您會看到來自您的應用程式資料的位置。

    ![[加入]、[開發人員服務]、[Application Insights]。](./media/app-insights-overview/11-new.png)

    選擇您的應用程式類型。

4. 開啟新資源，然後開啟快速啟動指南。
    
    ![瀏覽，](./media/app-insights-overview/quickstart.png)

    本節說明如何在您的應用程式中安裝 SDK。如果是 Web 應用程式，您也將了解如何將 SDK 加入至 Web 網頁，以及設定可用性測試。


如需詳細資訊，請在此頁面左側導覽列中的 [開始使用] 下選擇您的應用程式類型。

## 代碼


[範例和逐步解說](app-insights-code-samples.md)

[SDK 實驗室](https://www.myget.org/gallery/applicationinsights-sdk-labs) - 您可以將 NuGet 封裝作為新增項目安裝 (以及解除安裝) 到 Application Insights SDK。親身體驗並提供意見反應！


## 支援與意見反應

* 疑難排解與問題：
 * [疑難排解][qna]
 * [MSDN 論壇](https://social.msdn.microsoft.com/Forums/vstudio/zh-TW/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* 錯誤：
 * [連線](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* 建議：
 * [使用者心聲 (英文)](http://visualstudio.uservoice.com/forums/121579-visual-studio/category/77108-application-insights)


## 影片


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]


<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->