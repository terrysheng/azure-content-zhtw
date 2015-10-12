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
	ms.date="09/23/2015" 
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


### 我的 app 發生事件時寄送電子郵件

我們假設您想要在特定事件發生時收到電子郵件。Application Insights 並不直接提供這項工具，但可以[在計量超過某個閾值時傳送警示](app-insights-alerts.md)。

您可以針對[自訂計量](app-insights-api-custom-events-metrics.md#track-metric)設定警示，而不是自訂事件。撰寫一些程式碼以在事件發生時增加計量：

    telemetry.TrackMetric("Alarm", 10);

或：

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

因為警示有兩個狀態，所以當您考慮讓警示結束時必須傳送較低的值：

    telemetry.TrackMetric("Alarm", 0.5);

在[計量瀏覽器](app-insights-metric-explorer.md)中建立圖表以查看您的警示：

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
 
## 應用程式版本篩選器

當您發佈新的 app 版本時，要能夠分開不同版本的遙測。

您可以設定 [應用程式版本] 屬性，以便篩選[搜尋](app-insights-diagnostic-search.md)和[計量瀏覽器](app-insights-metrics-explorer.md)的結果。


![](./media/app-insights-how-do-i/050-filter.png)

設定 [應用程式版本] 屬性有幾種不同的方法。

* 直接設定：

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* 在[遙測初始設定式](app-insights-api-custom-events-metrics.md#telemetry-initializers)中將該行換行，以確保一致設定所有 TelemetryClient 執行個體。

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

<!---HONumber=Oct15_HO1-->