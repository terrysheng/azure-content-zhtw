<properties 
	pageTitle="我如何在 Application Insights 中..." 
	description="Application Insights 中的常見問題集。" 
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
	ms.date="02/05/2016" 
	ms.author="awills"/>

# 我如何在 Application Insights 中...？

## ... 時收到電子郵件

### 我的網站當機時寄送電子郵件

設定[可用性 Web 測試](app-insights-monitor-web-app-availability.md)。

### 我的網站多載時寄送電子郵件

針對 [伺服器回應時間] 設定[警示](app-insights-alerts.md)。介於 1 到 2 秒之間的閾值應該會運作。

![](./media/app-insights-how-do-i/030-server.png)

您的 app 也可能會藉由傳回失敗碼顯示資源耗盡的徵兆。針對 [失敗的要求] 設定警示。

如果您想要針對 [伺服器例外狀況] 設定警示，可能必須進行[一些其他設定](app-insights-asp-net-exceptions.md)才能看到資料。

### 傳送電子郵件的例外狀況

1. [設定例外狀況監視](app-insights-asp-net-exceptions.md)
2. 針對例外狀況計數計量[設定警示](app-insights-alerts.md)


### 我的 app 發生事件時寄送電子郵件

我們假設您想要在特定事件發生時收到電子郵件。Application Insights 並未直接提供此功能，但它可[在計量超過某個閾值時傳送警示](app-insights-alerts.md)。

您可以針對[自訂計量](app-insights-api-custom-events-metrics.md#track-metric)設定警示，而不是自訂事件。撰寫一些程式碼以在事件發生時增加計量：

    telemetry.TrackMetric("Alarm", 10);

或：

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

因為警示有兩個狀態，所以當您考慮讓警示結束時必須傳送較低的值：

    telemetry.TrackMetric("Alarm", 0.5);

在[計量總管](app-insights-metrics-explorer.md)中建立圖表來查看您的警示：

![](./media/app-insights-how-do-i/010-alarm.png)

現在設定警示在計量一段短時間高於中間值時引發：


![](./media/app-insights-how-do-i/020-threshold.png)

將平均期間設定為最小值。

您就會在計量高於和低於閾值時收到電子郵件。

考慮事項：

* 警示有兩種狀態 (「警示」和「良好」)。只有在收到計量時才會評估狀態。
* 只有在狀態變更時才會傳送電子郵件。這是為什麼您必須同時傳送高值和低值的計量。 
* 若要評估警示，會採用收到的值除以先前的期間來獲得平均值。因為每次收到計量時都會進行計算，所以傳送電子郵件的頻率會比您設定的期間還要高。
* 由於「警示」和「良好」都會傳送電子郵件，所以您可能要以兩個狀態的條件來重新思考單次發生的事件。例如，不要採用「作業完成」事件，而採用「作業進行中」的條件，您就可以在工作開始和結束時收到電子郵件。

### 自動設定警示

[使用 PowerShell 建立新的警示](app-insights-alerts/#set-alerts-by-using-powershell)

## 使用 PowerShell 管理 Application Insights

* [建立新的資源](app-insights-powershell-script-create-resource.md)
* [建立新的警示](app-insights-alerts/#set-alerts-by-using-powershell)

## 應用程式版本和戳記

### 將開發、測試和生產的結果分開

* 針對不同的環境，設定不同的重點
* 針對不同的戳記 (開發、測試、生產)，使用不同的屬性值來標記遙測

[深入了解](app-insights-separate-resources.md)
 

### 篩選組建編號

當您發佈新的 App 版本時，希望能夠將不同組建的遙測分開。

您可以設定 [應用程式版本] 屬性，如此便能篩選[搜尋](app-insights-diagnostic-search.md)和[計量總管](app-insights-metrics-explorer.md)的結果。


![](./media/app-insights-how-do-i/050-filter.png)

設定 [應用程式版本] 屬性有幾種不同的方法。

* 直接設定：

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* 在[遙測初始設定式](app-insights-api-custom-events-metrics.md#telemetry-initializers)中將該行換行，以確保會以一致性方式設定所有 TelemetryClient 執行個體。

* [ASP.NET] 在 `BuildInfo.config` 中設定版本。Web 模組將會從 BuildLabel 節點取得版本。在您的專案中包含此檔案， 而且記得要在 [方案總管] 中設定 [永遠複製] 屬性。

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] 在 MSBuild 中自動產生 BuildInfo.config。若要這樣做，請先在 .csproj 檔案中加入幾行：

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
    ```

    這會產生一個稱為 *yourProjectName*.BuildInfo.config 的檔案。發佈程序會將這個檔案重新命名為 BuildInfo.config。

    當您使用 Visual Studio 建置時，組建標籤會包含預留位置 (AutoGen\_...)。但是當使用 MSBuild 建立時，則會填入正確的版本號碼。

    若要允許 MSBuild 產生版本號碼，請在 AssemblyReference.cs 中設定類似 `1.0.*` 的版本

## 監視後端伺服器與桌面應用程式

[使用 Windows Server SDK 模組](app-insights-windows-desktop.md)。


## 顯現資料

#### 具有來自多個 App 之計量的儀表板

* 在[計量總管](app-insights-metrics-explorer.md)中，自訂圖表並將它儲存為我的最愛。將它釘選到 Azure 儀表板。
* 

#### 資料來自其他來源和 Application Insights 的儀表板

* [將遙測匯出至 Power Bi](app-insights-export-power-bi.md)。 

或

* 使用 SharePoint 做為您的儀表板，在 SharePoint web 組件中顯示資料。[使用連續的匯出和資料流分析來匯出至 SQL](app-insights-code-sample-export-sql-stream-analytics.md)。使用 PowerView 來檢查資料庫，並建立適用於 PowerView 的 SharePoint web 組件。


### 複雜的篩選、分割和聯結

* [使用連續的匯出和資料流分析來匯出至 SQL](app-insights-code-sample-export-sql-stream-analytics.md)。使用 PowerView來檢查資料庫。

<a name="search-specific-users"></a>
### 篩選出匿名或已驗證的使用者

如果您的使用者登入，您可以設定[驗證使用者識別碼](app-insights-api-custom-events-metrics.md#authenticated-users)(它不會自動重新整理)。

接著，您可以：

* 搜尋特定的使用者識別碼

![](./media/app-insights-how-do-i/110-search.png)

* 篩選匿名或已驗證使用者的計量

![](./media/app-insights-how-do-i/115-metrics.png)

## 修改屬性名稱或值

建立[篩選器](app-insights-api-filtering-sampling.md#filtering)。這可讓您先修改或篩選遙測，然後再將它從您的應用程式傳送至 Application Insights。

## 列出特定使用者和其使用方式

如果您只想要[搜尋特定使用者](#search-specific-users)，就可以設定[驗證使用者識別碼](app-insights-api-custom-events-metrics.md#authenticated-users)。

如果您想要使用者清單以及像是他們查看過哪些頁面或登入頻率等資料，則有兩個選項：

* [設定驗證使用者識別碼](app-insights-api-custom-events-metrics.md#authenticated-users)、[匯出到資料庫](app-insights-code-sample-export-sql-stream-analytics.md)，然後使用適當的工具來分析使用者資料。
* 如果您只有少數的使用者，則可傳送自訂事件或計量、使用感興趣的資料做為計量值或事件名稱，然後設定使用者識別碼做為屬性。若要分析頁面檢視，可取代標準的 JavaScript trackPageView 呼叫。若要分析伺服器端遙測，可使用遙測初始設定式，將使用者識別碼新增至所有伺服器遙測。然後您可以篩選與分割關於使用者識別碼的計量資訊和搜尋。


## 降低從我的 App 到 Application Insights 的流量

* 在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中，停用任何您不需要的模組，例如效能計數器收集器。
* 使用 SDK 中的[取樣和篩選](app-insights-api-filtering-sampling.md)。
* 您在網頁中，限制針對每個頁面檢視回報的 Ajax 呼叫次數。在 `instrumentationKey:...` 之後的指令碼片段中，插入：`,maxAjaxCallsPerView:3` (或適當的數字)。
* 如果您使用的是 [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric)，請在傳送結果之前，先計算計量值批次的彙總。有一個 TrackMetric() 的多載是針對該動作所提供。


深入了解[價格和配額](app-insights-pricing.md)。

## 停用遙測

若要從伺服器**動態停止和開始**收集及傳輸遙測資料：

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



若要**停用選取的標準收集器** (例如效能計數器、HTTP 要求或相依性)，請刪除或註解化 [ApplicationInsights.config](app-insights-api-custom-events-metrics.md) 中的相關行。例如，如果您想要傳送自己的 TrackRequest 資料，可以這麼做。



## 檢視系統效能計數器

您可以在計量總管中顯示的計量資訊是一組系統效能計數器。有一個預先定義且標題為**伺服器**的刀鋒視窗會顯示它們其中幾個。

![開啟 Application Insights 資源並按一下伺服器](./media/app-insights-how-do-i/121-servers.png)

### 如果您看不到效能計數器資料

* 您自己的電腦或 VM 上的 **IIS 伺服器**。[安裝狀態監視器](app-insights-monitor-performance-live-website-now.md)。 
* **Azure 網站** - 我們尚未支援效能計數器。您可以取得數個計量來做為 Azure 網站控制台的標準部分。
* **Unix 伺服器** - [安裝 collectd](app-insights-java-collectd.md)

### 顯示更多效能計數器

* 首先，[新增圖表](app-insights-metrics-explorer.md)，並查看計數器是否位於我們提供的基本組合中。
* 如果沒有，請[將計數器加入效能計數器模組所收集的組合中](app-insights-web-monitor-performance.md#system-performance-counters)。

<!---HONumber=AcomDC_0211_2016-->