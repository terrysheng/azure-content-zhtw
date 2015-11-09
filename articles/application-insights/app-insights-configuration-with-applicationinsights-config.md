<properties 
	pageTitle="使用 ApplicationInsights.config 或 .xml 設定 Application Insights SDK" 
	description="啟用或停用資料收集模組，以及加入效能計數器和其他參數" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# 使用 ApplicationInsights.config 或 .xml 設定 Application Insights SDK

Application Insights .NET SDK 是由數個 NuGet 封裝所組成。[核心封裝](http://www.nuget.org/packages/Microsoft.ApplicationInsights)提供 API，用於傳送遙測至 Application Insights。[其他封裝](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights)提供遙測_模組_和_初始設定式_，用於自動從您的應用程式和其內容追蹤遙測。您可以藉由調整組態檔，來啟用或停用遙測模組和初始設定式，並為其設定一些參數。

組態檔的名稱為 `ApplicationInsights.config` 或 `ApplicationInsights.xml`，端視您的應用程式類型而定。當您[安裝某些版本的 SDK][start] 時，系統會自動將組態檔加入至您的專案。[IIS 伺服器上的狀態監視器][redfield]，或是當您[選取 Azure 網站或 VM 的 Application Insights 延伸模組][azure]時，也會將組態檔加入至 Web 應用程式。

沒有同等的檔案可以控制[網頁中的 SDK][client]。

組態檔中的每個模組都有一個節點。若要停用模組，請刪除節點或將其註解化。

## [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 封裝

`Microsoft.ApplicationInsights` NuGet 封裝在 `ApplicationInsights.config` 中提供下列遙測模組。

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
`DiagnosticsTelemetryModule` 報告 Application Insights 檢測程式碼本身中的錯誤。例如，如果程式碼無法存取效能計數器，或 `ITelemetryInitializer` 擲回例外狀況。此模組所追蹤的追蹤遙測會出現在[診斷搜尋][diagnostic]中。

## [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet 封裝

`Microsoft.ApplicationInsights.DependencyCollector` NuGet 封裝在 `ApplicationInsights.config` 中提供下列遙測模組。

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
`DependencyTrackingTelemetryModule` 會追蹤您的應用程式對外部相依性所做的呼叫 (例如 HTTP 要求和 SQL 查詢) 的相關遙測。若要允許此模組用於 IIS 伺服器，您必須[安裝狀態監視器][redfield]。若要在 Azure Web 應用程式或 VM 中使用此模組，[請選取 Application Insights 延伸模組][azure]。

您也可以使用 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) 撰寫您自己的相依性追蹤程式碼。

## [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 封裝

`Microsoft.ApplicationInsights.Web` NuGet 封裝在 `ApplicationInsights.config` 中提供下列遙測初始設定式和模組。

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**初始設定式**

* `SyntheticTelemetryInitializer` 會更新所有遙測項目在處理來自綜合來源 (例如可用性測試) 要求時追蹤的 `User`、`Session` 和 `Operation` 內容屬性。Application Insights 入口網站預設不會顯示綜合遙測。
* `ClientIpHeaderTelemetryInitializer` 會根據要求的 `X-Forwarded-For` HTTP 標頭來更新所有遙測項目的 `Location` 內容的 `Ip` 屬性。
* `UserAgentTelemetryInitializer` 會根據要求的 `User-Agent` HTTP 標頭來更新所有遙測項目的 `User` 內容的 `UserAgent` 屬性。
* `OperationNameTelemetryInitializer` 會根據 HTTP 方法，以及 ASP.NET MVC 控制器的名稱和叫用來處理要求的動作，更新所有遙測項目 `RequestTelemetry` 的 `Name` 屬性和 `Operation` 內容的 `Name` 屬性。
* `OperationNameTelemetryInitializer` 會更新 'Operation.Id` context property of all telemetry items tracked while 
handling a request with the automatically generated `RequestTelemetry.Id`。
* 針對具有從使用者瀏覽器中執行的 Application Insights JavaScript 檢測程式碼所產生的 `ai_user` Cookie 擷取的值的所有遙測項目，`UserTelemetryInitializer` 會更新 `User` 內容的 `Id` 和 `AcquisitionDate` 屬性。
* 針對具有從使用者瀏覽器中執行的 Application Insights JavaScript 檢測程式碼所產生的 `ai_session` Cookie 擷取的值的所有遙測項目，`SessionTelemetryInitializer` 會更新 `Session` 內容的 `Id` 屬性。 
* 針對具有從 Azure 執行階段環境擷取的資訊的所有遙測項目，`AzureRoleEnvironmentTelemetryInitializer` 會更新 `Device` 內容的 `RoleName` 和 `RoleInstance` 屬性。
* 針對具有 Web 應用程式執行所在電腦的網域名稱的所有遙測項目，`DomainNameRoleInstanceTelemetryInitializer` 會更新 `Device` 內容的 `RoleInstance` 屬性。
* 針對具有從 TFS 組建所產生的 `BuildInfo.config` 檔案擷取的值的所有遙測項目，`BuildInfoConfigComponentVersionTelemetryInitializer` 會更新 `Component` 內容 的 `Version` 屬性。
* `DeviceTelemetryInitializer` 會更新所有遙測項目 `Device` 內容的下列屬性。
 - `Type` 設定為 "PC"
 - `Id` 是設定為 Web 應用程式執行所在電腦的網域名稱。
 - `OemName` 是設定為使用 WMI 從 `Win32_ComputerSystem.Manufacturer` 欄位擷取的值。
 - `Model` 是設定為使用 WMI 從 `Win32_ComputerSystem.Model` 欄位擷取的值。
 - `NetworkType` 是設定為從 `NetworkInterface` 擷取的值。
 - `Language` 是設定為 `CurrentCulture` 的名稱。

**模組**

* `RequestTrackingTelemetryModule` 會追蹤您的 Web 應用程式所接收到的要求，並測量回應時間。
* `ExceptionTrackingTelemetryModule` 可計算 Web 應用程式中未處理的例外狀況數。請參閱[失敗和例外狀況][exceptions]。
* `DeveloperModeWithDebuggerAttachedTelemetryModule` 會強制 Application Insights `TelemetryChannel` 立即傳送資料，在偵錯工具附加至應用程式程序時一次一個遙測項目。這會減少當您的應用程式追蹤遙測，與當它出現在 Application Insights 入口網站但耗用大量 CPU 和網路頻寬負荷時的時間量。

## [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet 封裝

`Microsoft.ApplicationInsights.PerfCounterCollector` NuGet 封裝預設會對 `ApplicationInsights.config` 加入下列遙測模組。

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule` 會追蹤 Windows 效能計數器的數目。當您在計量瀏覽器中按一下圖表以開啟其 [詳細資料] 刀鋒視窗時，您可以看到這些計數器。

您可以監視其他效能計數器，包括標準 Windows 計數器，以及您加入的任何其他計數器。
      
使用以下語法來收集其他效能計數器：

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
`PerformanceCounter` 必須是 `\CategoryName(InstanceName)\CounterName` 或 `\CategoryName\CounterName`
      
如果您提供 `ReportAs` 屬性，則會將它當作 Application Insights 中顯示的名稱。

若要向 Application Insights 報告，計數器名稱只能包含：字母、圓角括號、斜線、連字號、底線、空格和點。

如果您想要監視的計數器含有任何無效的字元，例如 '#' 或數字，則您必須使用 ReportAs。
      
支援以下預留位置做為 `InstanceName`：

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## 通道參數 (Java)

這些參數會影響 Java SDK 應該儲存和排清它所收集之遙測資料的方式。

#### MaxTelemetryBufferCapacity

可以儲存在 SDK 記憶體內儲存空間中的遙測項目數。達到這個數目時，會排清遙測緩衝區，也就是將遙測項目傳送至 Application Insights 伺服器。

-	最小值：1
-	最大值：1000
-	預設值：500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

決定應該以何種頻率排清儲存在記憶體內儲存空間的資料 (並傳送至 Application Insights)。

-	最小值：1
-	最大值：300
-	預設值：5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

決定分配給本機磁碟上永續性存放裝置的大小上限 (MB)。此存放裝置會用於保存無法傳送至 Application Insights 端點的遙測項目。存放裝置大小達到上限時，會捨棄新的遙測項目。

-	最小值：1
-	最大值：100
-	預設值：10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

## 遙測初始設定式

您可以撰寫遙測初始設定式，以篩選及修改從應用程式收集的遙測。這些初始設定式可以連同標準模組一起從 .config 檔案進行初始化。[深入了解](app-insights-api-filtering-sampling.md)


## InstrumentationKey

這會決定顯示您資料的 Application Insights 資源。通常您會針對每個應用程式，用個別的金鑰建立個別資源。

如果想要以動態方式設定金鑰 (例如，想要將應用程式的結果傳送到不同的資源)，您可以在組態檔中省略金鑰，並將金鑰設定在程式碼中。

若要針對 TelemetryClient 的所有執行個體 (包括標準遙測模組) 設定金鑰，請在 TelemetryConfiguration.Active 中設定金鑰。請在初始化方法中這麼做，例如 ASP.NET 服務中的 global.aspx.cs：

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

如果您只想將特定的一組事件傳送至不同的資源，可以針對特定的 TelemetryClient 設定金鑰：

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[深入了解 API][api]。

若要取得新的金鑰，請[在 Application Insights 入口網站中建立新的資源][new]。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

<!---HONumber=Nov15_HO1-->