<properties
    pageTitle="iOS 應用程式的分析 | Microsoft Azure"
    description="分析 iOS 應用程式的使用情況和效能。"
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/15/2015"
    ms.author="awills"/>

# iOS 應用程式的分析

Visual Studio Application Insights 可讓您監視行動應用程式的使用量、事件及當機。


> [AZURE.NOTE]我們建議使用 [HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios) 來獲得當機報告、分析、散發和意見反應管理。

## 需求

您需要：

* [Microsoft Azure](http://azure.com) 訂用帳戶。您使用 Microsoft 帳戶登入，可能是針對 Windows、XBox Live 或其他 Microsoft 雲端服務具備的帳戶。
* Xcode 6 或更新版本。
* SDK 會在具有 iOS 6.0 或更新版本的裝置上執行。

## 建立 Application Insights 資源

在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。挑選 iOS 選項。

![按一下 [新增]、[開發人員服務]、[Application Insights]](./media/app-insights-ios/11-new.png)

開啟的刀鋒視窗是您要查看您的應用程式效能和使用量資料的位置。若要在下次登入 Azure 時回到此位置，您應該會在開始畫面上發現它的磚。或者按一下 [瀏覽] 以尋找它。

## 設定

請遵循[設定指南](https://github.com/Microsoft/ApplicationInsights-iOS#setup)在您的應用程式中安裝 SDK。

## 在 Application Insights 中檢視資料

執行您的應用程式來產生一些遙測。

返回 http://portal.azure.com 並且瀏覽至您的 Application Insights 資源。

按一下 [搜尋] 以開啟[診斷搜尋][diagnostic] - 這是前幾個事件顯示的位置。如果您看不到任何資料，請稍做等待後再按一下 [重新整理]。

![按一下 [診斷搜尋]](./media/app-insights-ios/21-search.png)

使用您的應用程式時，[概觀] 刀鋒視窗上會顯示資料。

![[概觀] 刀鋒視窗](./media/app-insights-ios/22-oview.png)

按一下任何圖表以查看詳細資料。例如，當機：

![按一下當機圖表](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>後續步驟

[追蹤應用程式的使用量][track]

[診斷搜尋][diagnostic]

[計量瀏覽器][metrics]

[疑難排解][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->