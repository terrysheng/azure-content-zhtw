<properties 
	pageTitle="Application Insights 的版本資訊" 
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
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# 適用於 Windows Phone 和市集的 Application Insights 版本資訊

[Application Insights SDK](app-insights-windows-get-started.md) 傳送有關您作用中的應用程式遙測到 [Application Insights](http://azure.microsoft.com/services/application-insights/)，您可以在此分析它的使用情況與效能。


#### 若要在應用程式中安裝 SDK

請參閱[開始使用適用於 Windows Phone 和市集應用程式的 Application Insights](app-insights-windows-get-started.md)。

#### 若要升級至最新的 SDK 

* 請取得一份 ApplicationInsights.config 的複本，以保留您所做的任何自訂。
* 在 [方案總管] 中，以滑鼠右鍵按一下您的專案並選擇 [管理 NuGet 封裝]。
* 設定篩選器來顯示已安裝的封裝。 
* 選取已安裝的 Application Insights 封裝並選擇升級。
* 比較舊版和新版的 ApplicationInsights.config。將任何您在舊版中所做的自訂合併回來。
* 重建您的方案。

## 1.0.0 版

### Windows 應用程式 SDK

- Windows 應用程式的新初始化。`InitializeAsync()` 方法的新 `WindowsAppInitializer` 類別允許啟動 SDK 集合的初始化。這項變更透過先前的 ApplicationInsights.config 技術，允許更精確的控制和重要的應用程式初始化效能改進。
- 不再自動設定 DeveloperMode。若要變更 DeveloperMode 行為，您必須在程式碼中指定。
- NuGet 封裝不會再插入 ApplicationInsights.config。手動加入 NuGet 封裝時，建議使用新的 WindowsAppInitializer。
- ApplicationInsights.config 只會讀取 `<InstrumentationKey>`，略過所有其他設定，而採用 WindowsAppInitializer 設定。
- SDK 將會自動收集市集市場。
- 許多錯誤修正、穩定性改良和效能增強功能。

### 核心 SDK

- ApplicationInsights.config 檔案不再是必要。並且不會由 NuGet 封裝加入。可以在程式碼中指定完整的組態。
- NuGet 封裝將不再加入目標檔案到您的方案。這會移除在偵錯組建期間 DeveloperMode 的自動設定。DeveloperMode 應該在程式碼中手動設定。

## 0.17 版

### Windows 應用程式 SDK

- Windows 應用程式 SDK 現在支援針對 Windows 10 技術預覽版且具備 VS 2015 RC 建立的通用 Windows 應用程式。

### 核心 SDK

- TelemetryClient 會預設為使用 InMemoryChannel 初始化。
- 已加入新的 API，`TelemetryClient.Flush()`。這個排清方法會觸發立即封鎖記錄到該用戶端的所有遙測上傳。這可讓您在程序關閉之前手動觸發上傳。
- NuGet 封裝加入了 .Net 4.5 目標。此目標沒有外部相依性 (移除了 BCL 和 EventSource 相依性)。

## 0.16 版 

2015-04-28 預覽

- SDK 現在支援 DNX 目標平台，以監視 [.NET 核心架構](http://www.dotnetfoundation.org/NETCore5) 應用程式。
- ```TelemetryClient``` 的執行個體不再快取檢測金鑰。現在如果未明確在 ```TelemetryClient``` 中設定檢測金鑰，則 ```InstrumentationKey``` 會傳回 Null。它可以在您於已收集一些遙測之後設定 ```TelemetryConfiguration.Active.InstrumentationKey``` 時修正問題，像是相依性收集器的遙測模組、Web 要求資料收集和效能計數器收集器會使用新的檢測金鑰。

## 0.15 版

- 新屬性 ```Operation.SyntheticSource``` 現在可用於 ```TelemetryContext```。現在您可以將您的遙測項目標示為「非真實使用者流量」，並指定如何產生此流量。做為範例，藉由設定這個屬性您可以區分來自您的測試自動化的流量和來自負載測試的流量。
- 通道邏輯已移至稱為 Microsoft.ApplicationInsights.PersistenceChannel 的個別 NuGet。預設通道現在稱為 InMemoryChannel
- 新方法 ```TelemetryClient.Flush``` 讓使用者能夠以同步方式從緩衝區排清遙測項目

## 0.13 版

沒有可用於較舊版本的版本資訊。

<!---HONumber=July15_HO4-->