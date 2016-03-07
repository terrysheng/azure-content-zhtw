<properties 
	pageTitle="在 Application Insights 中探索 .NET 追蹤記錄" 
	description="搜尋使用 Trace、NLog 或 Log4Net 產生的記錄檔。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/22/2016" 
	ms.author="awills"/>
 
# 在 Application Insights 中探索 .NET 追蹤記錄  

如果您使用 NLog、log4Net 或 System.Diagnostics.Trace 在 ASP.NET 應用程式中進行診斷追蹤，您可以將記錄傳送至 [Visual Studio Application Insights][start]，其中您可以探索和搜尋它們。您的記錄檔會與來自應用程式的其他遙測合併，讓您可以識別與服務每個使用者要求相關聯的追蹤，並將它們與其他事件和例外狀況報告相互關聯。

> [AZURE.NOTE] 您需要記錄擷取模組嗎？ 對於第三方記錄器來說，它是一個有用的配接器，但是如果您還沒使用 NLog、log4Net 或 System.Diagnostics.Trace，請考慮直接呼叫 [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace)。


## 在您的 app 上安裝記錄

在專案中安裝您選擇的記錄架構。這應該會在 app.config 或 web.config 中產生一個項目。

> 如果您使用 System.Diagnostics.Trace，這應該會在 web.config 中加入一個項目。

## 設定 Application Insights 收集記錄

如果您尚未這麼做，請**[將 Application Insights 新增至 Web 專案](app-insights-asp-net.md)**。您將會看見包含記錄收集器的選項。

或者，在 [方案總管] 中以滑鼠右鍵按一下專案，來**設定 Application Insights**。選取包含記錄收集器的選項。

*沒有 Application Insights 功能表或記錄收集器選項嗎？* 請嘗試進行[疑難排解](#troubleshooting)。


## 手動安裝

如果 Application Insights 安裝程式不支援您的專案類型 (例如 Windows 傳統型專案)，請使用這個方法。

1. 如果您打算使用 log4Net 或 NLog，請將它安裝在您的專案。 
2. 在 [方案總管] 中，以滑鼠右鍵按一下您的專案並選擇 [**管理 NuGet 封裝**]。
3. 搜尋「Application Insights」

    ![Get the prerelease version of the appropriate adapter](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. 選取適當的套件 - 下列其中一個：
  + Microsoft.ApplicationInsights.TraceListener (擷取 System.Diagnostics.Trace 呼叫)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

NuGet 封裝會安裝必要的組件，並修改 web.config 或 app.config。

## 插入診斷記錄呼叫

如果您使用 System.Diagnostics.Trace，典型的呼叫如下：

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

如果您偏好 log4net 或 NLog：

    logger.Warn("Slow response - database01");


## 直接使用追蹤 API

您可以直接呼叫 Application Insights 追蹤 API。記錄配接器會使用此 API。

例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace 的優點在於您可以將較長的資料放在訊息中。例如，您可以在該處編碼 POST 資料。


## 探索記錄

在偵錯模式中執行您的 app 或即時部署它。

在 [Application Insights 入口網站][portal]中您的應用程式概觀刀鋒視窗，選擇[搜尋][diagnostic]。

![在 Application Insights 中，選擇 [搜尋]](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![診斷搜尋](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

例如，您可以：

* 篩選記錄追蹤，或具有特定屬性的項目
* 詳細檢查特定項目。
* 尋找與相同使用者要求相關的其他遙測 (也就是使用相同的 OperationId) 
* 將此頁面的組態儲存為我的最愛

> [AZURE.NOTE] **取樣** 如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則調適性取樣功能可能會運作，並只傳送一部分的遙測資料。[深入了解取樣。](app-insights-sampling.md)

## 後續步驟

[在 ASP.NET 中診斷失敗和例外狀況][exceptions]

[深入了解診斷搜尋][diagnostic]。



## 疑難排解

### 如果是 Java，我要怎麼做？

使用 [Java 記錄配接器](app-insights-java-trace-logs.md)。

### 專案內容功能表上沒有 Application Insights 選項

* 請檢查已在此開發電腦上安裝 Application Insights 工具。在 Visual Studio 功能表 [工具、擴充功能和更新] 中，尋找 Application Insights 工具。如果此工具不在 [已安裝] 索引標籤中，請開啟 [線上] 索引標籤並安裝它。
* 這可能是 Application Insights 工具不支援的一種專案類型。請使用[手動安裝](#manual-installation)。

### 組態工具中沒有記錄配接器選項

* 您必須先安裝記錄架構。
* 如果您使用 System.Diagnostics.Trace，請確定您[已在 `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx) 中設定它。
* 您已經擁有最新版的 Application Insights 工具嗎？ 在 Visual Studio 的 [工具] 功能表中，選擇 [擴充功能和更新]，然後開啟 [更新] 索引標籤。如果 Application Insights 工具存在於其中，請按一下該工具以進行更新。


### <a name="emptykey"></a>我收到「檢測金鑰不能是空白」的錯誤

您可能只安裝記錄配接器 Nuget 封裝，但未安裝 Application Insights。

在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config`，然後選擇 [**更新 Application Insights**]。將會出現對話方塊邀請您登入 Azure，並建立 Application Insights 資源或重複使用現有的資源。這樣應該可以解決。

### 我可以在診斷搜尋中看見追蹤，但是看不到其他事件

有時候可能需要一段時間，所有事件和要求才會通過管線。

### <a name="limits"></a>保留多少資料？

每個應用程式每秒最多 500 個事件。事件會保留七天。

### 我沒看到一些預期的記錄項目

如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則調適性取樣功能可能會運作，並只傳送一部分的遙測資料。[深入了解取樣。](app-insights-sampling.md)

## <a name="add"></a>接續步驟

* [設定可用性和回應性測試][availability]
* [疑難排解][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0224_2016-->