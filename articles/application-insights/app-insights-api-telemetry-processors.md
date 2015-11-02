<properties 
	pageTitle="自訂事件和度量的 Application Insights API" 
	description="在您的裝置或桌面應用程式、網頁或服務中插入幾行程式碼，來追蹤使用狀況及診斷問題。" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="awills"/>

# 在 Application Insights SDK 中處理遙測

*Application Insights 目前僅供預覽。*

您可以針對 Application Insights SDK 撰寫與設定外掛程式，以自訂在遙測傳送至 Application Insights 服務之前，擷取與處理它的方式。

* [取樣](#sampling)可減少遙測的量而不會曲解統計資料。它可將相關資料點保持在一起以輕鬆搜尋。
* [遙測處理器](#telemetry-processors)會在 SDK 中初始化、篩選並取樣資料，之後才將資料傳送到伺服器。比方說，您可以將計算出的屬性加入至某些遙測，或可以從傀儡程式中排除要求來減少遙測量。 
* [遙測初始設定式](#telemetry-initializers)會將預設屬性加入至傳送的所有遙測，而且可以覆寫標準遙測模組的一些行為。屬性是對每個追蹤呼叫所評估，因此您可以變更這些值。
* [內容初始設定式](#context-initializers)也可設定全域屬性。屬性會在啟動時設定一次，並且之後無法變更。雖然這會節省一些 CPU 時間 (相較於使用遙測初始設定式)，它們也非常有彈性。
* [SDK API](app-insights-api-custom-events-metrics.md) 是用來傳送自訂事件和度量。

這些不同的機制之間有一些備援性，而且很可能是初始設定式會在未來變更。

開始之前：

* 在應用程式中安裝 [Application Insights SDK](app-insights-asp-net.md)。遙測處理器需要 
* 請嘗試 [Application Insights API](app-insights-api-custom-events-metrics.md)。 


## 取樣

*這項功能處於 Beta 版。*

減少流量同時保留準確的統計資料所建議的方式。篩選器會選取相關的項目，使得您可以瀏覽診斷中的項目。事件計數會在計量瀏覽器中調整，以補償所篩選的項目。

針對 ASP.NET 伺服器，在適合的初始化位置例如 Application\_Start 中插入它：

*C#*

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```

針對網頁，在您插入 (通常是在主頁面如 \_Layout.cshtml 中) 的 [Application Insights 程式碼片段](app-insights-javascript.md)中放置額外的程式行：

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* 設定等於 100/N 的百分比，其中 N 是整數 - 例如 50 (=1/2)、33.33 (=1/3)、25 (=1/4)、10 (=1/5) (在這些範例中為 10)。
* 如果您同時在網頁和伺服器中設定取樣，請確定在兩端中設定相同的取樣百分比。
* 用戶端和伺服器端會協調以選取相關的項目。

[深入了解取樣](app-insights-sampling.md)。

## 遙測處理器

撰寫遙測處理器來篩選和前置處 理Application Insights SDK 產生的遙測，之後才傳送至後端入口網站。這包括來自標準模組如 HTTP 要求收集器和相依性收集器的遙測。比方說，您可以篩選出有關來自傀儡程式要求或成功的相依性呼叫的遙測。

> [AZURE.WARNING]篩選傳送自使用處理器的 SDK 的遙測可能會曲解您在入口網站中看到的統計資料，並且難以追蹤相關的項目。
> 
> 請考慮改用[取樣](#sampling)。

### 建立遙測處理器

1. 若要建立篩選器，請實作 ITelemetryProcessor。這是遙測模組、遙測初始設定式和遙測通道之類的另一個擴充點。 

    請注意，遙測處理器建構一連串的處理。當您具現化遙測處理器時，您會傳遞連結至鏈結中的下一個處理器。遙測資料點傳遞至處理序方法時，它會完成其工作並接著呼叫鏈結中的下一個遙測處理器。

    ``` C#

    namespace FilteringTelemetryProcessor
    {
      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.DataContracts;

      class UnauthorizedRequestFilteringProcessor : ITelemetryProcessor
      {
        public UnauthorizedRequestFilteringProcessor(ITelemetryProcessor next)
		//Initialization will fail without this constructor. Link processors to each other
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }      private ITelemetryProcessor Next { get; set; }
      }
    }

    ```
2. 在適當的初始化類別中 - 例如在 Global.asax.cs 中的 AppStart - 插入您的處理器至鏈結：

    ```C#

    var builder = new TelemetryChannelBuilder();
    builder.Use((next) => new UnauthorizedRequestFilteringProcessor(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    TelemetryConfiguration.Active.TelemetryChannel = builder.Build();

    ```

    在這個點之後建立的 TelemetryClients 會使用您的處理器。

### 範例篩選器

#### 綜合要求

篩選出 bot 和 Web 測試。雖然計量瀏覽器可讓您篩選出綜合來源，此選項會藉由在 SDK 篩選它們以降低流量。

``` C#

public void Process(ITelemetry item)
{
    if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource))
    { return; }

    this.Next.Process(item);
}

```

#### 驗證失敗

篩選出具有 "401" 回應的要求。

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### 篩選出快速遠端相依性呼叫

如果您只想要診斷速度很慢的呼叫，請篩選出快的項目。

> [AZURE.NOTE]這樣會曲解您在入口網站上看到的統計資料。相依性圖表將看起來好像相依性呼叫均失敗。

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```


## 遙測初始設定式

使用遙測初始設定式來定義與所有遙測一起傳送的全域屬性；並覆寫選取的標準遙測模組行為。

例如，Web 封裝的 Application Insights 會收集有關 HTTP 要求的遙測。根據預設，它會將所有含 >= 400 回應碼的要求標記為失敗。但如果您想將 400 視為成功，您可以提供設定 Success 屬性的遙測初始設定式。

如果您提供遙測初始設定式，會在呼叫任何的 Track*() 方法時呼叫它。這包括由標準遙測模組呼叫的方法。依照慣例，這些模組不會設定任何已由初始設定式設定的屬性。

**定義您的初始設定式**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**載入您的初始設定式**

在 ApplicationInsights.config 中：

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*或者*，您也可以在程式碼 (如 Global.aspx.cs) 中具現化初始設定式：


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[詳細查看此範例。](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### JavaScript 遙測初始設定式

*JavaScript*

在您從入口網站取得的初始化程式碼之後立即插入遙測初始設定式：

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

如需 telemetryItem 上可用的非自訂屬性摘要，請參閱[資料模型](app-insights-export-data-model.md/#lttelemetrytypegt)。

您可以依需要加入多個初始設定式。






## <a name="default-properties"></a>內容初始設定式 - 設定所有遙測的預設屬性

您可以設定全域初始設定式，使得所有新 TelemetryClients 會自動使用您的內容。這包括平台特定遙測模組傳送的標準遙測，例如 Web 伺服器要求追蹤。

典型的用途是識別來自不同版本或您的應用程式元件的遙測。在入口網站中，您可以依據「應用程式版本」屬性篩選或群組結果。

一般而言，[建議使用遙測初始設定式，而非內容初始設定式](http://apmtips.com/blog/2015/06/09/do-not-use-context-initializers/)。

#### 定義內容初始設定式


*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MyNamespace
    {
      // Context initializer class
      public class MyContextInitializer : IContextInitializer
      {
        public void Initialize (TelemetryContext context)
        {
            if (context == null) return;

            context.Component.Version = "v2.1";
        }
      }
    }
```

*Java*

```Java

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyContextInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.Component.Version = "2.1";
      }
    }
```

#### 載入內容初始設定式

在 ApplicationInsights.config 中：

    <ApplicationInsights>
      <ContextInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MyNamespace.MyContextInitializer, MyAssemblyName"/> 
        ...
      </ContextInitializers>
    </ApplicationInsights>

*或者*，您也可以在程式碼中具現化初始設定式：

*C#*

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyContextInitializer());
    }
```

*Java*

```Java

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyContextInitializer());
```



## 參考文件

* [API 概觀](app-insights-api-custom-events-metrics.md)

* [ASP.NET 參考](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 參考](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript 參考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## SDK 程式碼

* [ASP.NET 核心 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)
* [所有平台](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## 問題

* *Track\_() 呼叫會擲回什麼例外狀況？*
    
    無。您不需要將它們包裝在 try-catch 子句中。如果 SDK 發生問題，它會記錄訊息，您可以在偵錯主控台輸出中查看。若訊息得以留存，也可在診斷搜尋中查看。



* *是否有 REST API？*

    是，但我們尚未發佈。

## <a name="next"></a>接續步驟


[搜尋事件和記錄檔][diagnostic]

[範例和逐步解說](app-insights-code-samples.md)

[疑難排解][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO4-->