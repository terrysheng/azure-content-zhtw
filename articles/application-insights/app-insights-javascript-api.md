<properties 
	pageTitle="Application Insights SDK JavaScript API" 
	description="參考文件" 
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
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# Application Insights SDK JavaScript API

當您在 [Application Insights](https://azure.microsoft.com/services/application-insights/) 中設定[網頁追蹤](app-insights-javascript.md)時，JavaScript SDK 就會載入到您的網頁中。

[API 的概觀與範例](app-insights-api-custom-events-metrics.md)

## AppInsights 類別

傳送遙測到 Application Insights 的 SDK 門面。

在您[設定網頁追蹤](app-insights-javascript.md)的網頁上，您可以使用 `appInsights` 執行個體。例如：
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

記錄已對使用者顯示的頁面或類似容器。

 | | 
---|---|---
`name` | `? string` | 用來識別入口網站中頁面的名稱。預設為文件標題。
`url` | `? string` | 識別頁面或類似項目的相對或絕對的 URL。預設為視窗位置。
`properties` | `? {[string]:string}` | 用來篩選入口網站中頁面和度量的其他資料。預設為空白。
`measurements` | `? {[string]:number}` | 與此頁面相關聯的度量，顯示在入口網站的計量瀏覽器中。預設為空白。


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

記錄使用者動作或其他項目。

在入口網站中，您可以依名稱選取事件，以及[顯示計算事件數目的圖表或顯示相關聯的測量](app-insights-metrics-explorer.md)。

您也可以搜尋和[顯示個別事件](app-insights-diagnostic-search.md)。

 | | 
---|---|---
 `name` | `string` | 識別事件。具有相同名稱的事件會列入計算，而且可在[計量瀏覽器](app-insights-metrics-explorer.md)中繪製成圖表。
`properties` | `? {[string]:string}` | 用來篩選入口網站中頁面和度量的其他資料。預設為空白。
`measurements` | `? {[string]:number}` | 與此頁面相關聯的度量，顯示在入口網站的計量瀏覽器中。預設為空白。


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


記錄與特定事件無關的數值。通常用來傳送效能指標的一般報表。

在入口網站中，您可以依名稱選取度量[將值經過一段時間的變化繪製成圖表](app-insights-metrics-explorer.md)。您無法搜尋或檢視個別 trackMetric 呼叫。

若要傳送單一測量，請使用前兩個參數。如果您經常進行測量，您可以彙總多個測量，並且傳送所產生的間隔平均值，減少遙測頻寬。

 | | 
---|---|---
`name` | `string` | 識別度量的字串。在入口網站中，您可以依名稱選取要顯示的度量。
`average` | ` number` | 可以是單一測量，或數個測量的平均值。
`sampleCount` | `? number` | 測量計數，以平均值表示。預設值為 1。
`min` | `? number` | 範例中的最小測量。預設為平均值。
`max` | `? number` | 範例中的最大測量。預設為平均值。

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

記錄擷取到的例外狀況。(瀏覽器擷取到的例外狀況也會一併記錄。)

在入口網站中，您可以[搜尋例外狀況類型和檢視](app-insights-diagnostic-search.md)個別執行個體的類型、訊息和堆疊追蹤。

 | | 
---|---|---
`exception` | `Error` | 來自 catch 子句的錯誤。  
`handledAt` | `? string` | 預設為「未處理」。
`properties` | `? {[string]:string}` | 用來篩選入口網站中頁面和度量的其他資料。預設為空白。
`measurements` | `? {[string]:number}` | 與此頁面相關聯的度量，顯示在入口網站的計量瀏覽器中。預設為空白。

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

記錄診斷事件，例如進入或離開方法。

在入口網站中，您可以搜尋訊息內容，並且[顯示個別 trackTrace 事件](app-insights-diagnostic-search.md)。(不同於 `trackEvent`，您無法在入口網站中篩選訊息內容。)

 | | 
---|---|---
`message` | `string` | 診斷資料。可以遠比名稱還長。

### flush

    flush()

立即傳送所有已排入佇列的遙測。

在視窗關閉時使用此項。


### config

    config: IConfig

控制如何傳送遙測資料的值。

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

在您要插入網頁的[程式碼片段](app-insights-javascript-api.md)中設定這些值。尋找這一行，然後加入更多項目：

    })({
      instrumentationKey: "000...000"
    });

### context

    context: TelemetryContext

SDK 嘗試從環境擷取有關裝置、位置和使用者的資訊。


## TelemetryContext 類別




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## 開放原始程式碼

閱讀或參與 [SDK 的程式碼](https://github.com/Microsoft/ApplicationInsights-js)。

<!---HONumber=July15_HO4-->