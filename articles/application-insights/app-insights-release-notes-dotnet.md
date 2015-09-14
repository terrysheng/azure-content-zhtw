<properties 
	pageTitle=".NET 適用的 Application Insights 版本資訊"
	description="最新的更新。"
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
	ms.date="08/06/2015"
	ms.author="sergkanz"/>
 
# .NET 適用的 Application Insights SDK 的版本資訊

[Application Insights SDK for .NET](app-insights-start-monitoring-app-health-usage.md) 傳送有關您作用中的應用程式遙測到 [Application Insights](http://azure.microsoft.com/services/application-insights/)，您可以在此分析它的使用情況與效能。


#### 若要在應用程式中安裝 SDK

See [Get started with Application Insights for .NET](app-insights-start-monitoring-app-health-usage.md).

#### 若要升級至最新的 SDK 

* 升級之後，必須將您對 ApplicationInsights.config 所做的任何自訂合併回來。如果您不確定是否自訂過，請建立新專案，將 Application Insights 新增到其中，然後以新專案中的 .config 檔案與您自己的比較。請記下任何的差異。
* 在 [方案總管] 中，以滑鼠右鍵按一下您的專案並選擇 [管理 NuGet 封裝]。
* 設定篩選器來顯示已安裝的封裝。 
* 選取 **Microsoft.ApplicationInsights.Web** 然後選擇 [升級]。(這也會升級所有相依的封裝。)
* 與舊的複本比較 ApplicationInsights.config。您看到的變更大部分是因為我們移除了部分模組，並讓其他的模組可參數化。恢復您對舊檔案做的任何自訂。
* 重建您的方案。

## 1\.2 版

- 在 ASP.NET 程式庫沒有相依性的遙測初始設定式會從 `Microsoft.ApplicationInsights.Web` 移動至新的相依性 NuGet `Microsoft.ApplicationInsights.WindowsServer`
- 在 `Microsoft.AI.Web.dll` 上的 `Microsoft.ApplicationInsights.Web.dll` 已重新命名
- 在 `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel` 上的 `Microsoft.ApplicationInsights.Web.TelemetryChannel` NuGet 已重新命名。在 `Microsoft.AI.ServerTelemetryChannel.dll` 上的 `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` 組件已重新命名。在 `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` 上的 `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` 類別已重新命名。
- 所有屬於 Web SDK 一部分的命名空間已變更為排除 `Extensibility` 組件。其中包含所有 ApplicationInsights.config 中的遙測初始設定式和 web.config 中的 `ApplicationInsightsWebTracking` 模組。
- 如果執行緒上沒有 HttpContext.Current，則使用執行階段檢測代理程式 (透過狀態監視器或 Azure 網站的延伸模組來啟用) 所收集的相依性將不會標示為非同步。
- 屬性 `DependencyTrackingTelemetryModule` 的 `SamplingRatio` 不會執行任何動作且會標記為過時。
- 在 `Microsoft.AI.PerfCounterCollector` 上的 `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` 組件已重新命名
- Web 角色和裝置 SDK 中的數個輕微錯誤修正


## 1\.1 版

- 加入了新遙測類型 `DependencyTelemetry`，它可以用來傳送來自應用程式的相依性呼叫 (如 SQL、HTTP 呼叫等) 的相關資訊。
- 加入了新多載方法 `TelemetryClient.TrackDependency`，可讓您傳送呼叫相依性的相關資訊。
- 修正了使用 TelemetryConfiguration.CreateDefault 時診斷模組擲回的 NullReferenceException 模組。

## 1\.0 版

- 將遙測初始設定式和遙測模組從不同子命名空間移至根 `Microsoft.ApplicationInsights.Extensibility.Web` 命名空間。
- 從遙測初始設定式和遙測模組的名稱移除 "Web" 前置詞，因為它已經包含在 `Microsoft.ApplicationInsights.Extensibility.Web` 命名空間名稱中。
- 從 `Microsoft.ApplicationInsights` 組件將 `DeviceContextInitializer` 移動到 `Microsoft.ApplicationInsights.Extensibility.Web` 組件，並轉換為 `ITelemetryInitializer`。
- 將命名空間和組件的名稱從 `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` 變更為 `Microsoft.ApplicationInsights.Extensibility.DependencyCollector`，以獲得與 NuGet 封裝名稱的一致性。
- 將 `RemoteDependencyModule` 重新命名為 `DependencyTrackingTelemetryModule`。
- 將 `CustomPerformanceCounterCollectionRequest` 重新命名為 `PerformanceCounterCollectionRequest`。

## 0\.17 版
- 已移除 Framework 4.5 的應用程式對 EventSource NuGet 的相依性。
- 將不會在伺服器端產生匿名使用者和工作階段 Cookie 。若要實作 Web 應用程式的使用者和工作階段追蹤，JS SDK 的檢測現在為必要 – 仍採用來自 JavaScript SDK 的 Cookie。不再支援遙測模組 ```WebSessionTrackingTelemetryModule``` 和 ```WebUserTrackingTelemetryModule```，並且已經從 ApplicationInsights.config 檔案中移除。請注意，這項變更可能會造成使用者和工作階段計數的明顯重新結算，因為現在只需計算使用者產生的工作階段。
- OSVersion 不再預設由 SDK 填入。當它是空的時，Application Insights 管線會根據使用者代理程式來計算 OS 和 OSVersion。 
- 高負載案例的持續性通道最佳化會用於 Web SDK。修正「死亡螺旋」問題。死亡螺旋這種狀況是，當遙測項目計數中的尖峰，遠超過端點上的節流限制時，將會導致在特定的時間後重試，並且會於再次重試時遭到節流。
- 針對生產最佳化開發人員模式。若意外地離開，開發人員模式不會像之前般嘗試輸出額外的資訊導致大量負荷。
- 根據預設，當應用程式於偵錯工具下才會啟用開發人員模式。您可以使用 ```ITelemetryChannel``` 介面的 ```DeveloperMode``` 屬性覆寫它。

## 0\.16 版 

2015-04-28 預覽

- SDK 現在支援 DNX 目標平台，以監視 [.NET 核心架構](http://www.dotnetfoundation.org/NETCore5) 應用程式。
- ```TelemetryClient``` 的執行個體不再快取檢測金鑰。現在如果未明確在 ```TelemetryClient``` 中設定檢測金鑰，則 ```InstrumentationKey``` 會傳回 Null。它可以在您於已收集一些遙測之後設定 ```TelemetryConfiguration.Active.InstrumentationKey``` 時修正問題，像是相依性收集器的遙測模組、Web 要求資料收集和效能計數器收集器會使用新的檢測金鑰。

## 0\.15 版

- 新屬性 ```Operation.SyntheticSource``` 現在可用於 ```TelemetryContext```。現在您可以將您的遙測項目標示為「非真實使用者流量」，並指定如何產生此流量。做為範例，藉由設定這個屬性您可以區分來自您的測試自動化的流量和來自負載測試的流量。
- 通道邏輯已移至稱為 Microsoft.ApplicationInsights.PersistenceChannel 的個別 NuGet。預設通道現在稱為 InMemoryChannel
- 新方法 ```TelemetryClient.Flush``` 讓使用者能夠以同步方式從緩衝區排清遙測項目

## 0\.13 版

沒有可用於較舊版本的版本資訊。

 

<!---HONumber=September15_HO1-->