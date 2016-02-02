<properties
	pageTitle="開始在 Java Web 專案中使用 Application Insights | Microsoft Azure"
	description="使用 Application Insights 監視 Java 網站的效能和使用量"
	services="application-insights"
    documentationCenter="java"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/24/2016"
	ms.author="awills"/>

# 在 Java Web 專案中開始使用 Application Insights

*Application Insights 目前僅供預覽。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

[Application Insights](https://azure.microsoft.com/services/application-insights/) 是一項可延伸的分析服務，可幫助您了解即時應用程式的效能和使用情形。使用它可[偵測及診斷效能問題和例外狀況](app-insights-detect-triage-diagnose.md)，以及 [撰寫程式碼][api]來追蹤使用者對 app 執行的動作。

![範例資料](./media/app-insights-java-get-started/5-results.png)

Application Insights 支援 Linux、Unix 或 Windows 上執行的 Java 應用程式。

您需要：

* Oracle JRE 1.6 或更新版本，或 Zulu JRE 1.6 或更新版本
* [Microsoft Azure](https://azure.microsoft.com/) 訂用帳戶。(您可以從[免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。)

*如果您有使用中的 Web 應用程式，您可以依照替代的程序[在執行階段將 SDK 加入 Web 伺服器](app-insights-java-live.md)。替代方法可避免重建程式碼，但您沒有選項可撰寫程式碼來追蹤使用者活動。*


## 1\.取得 Application Insights 檢測金鑰

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)。
2. 建立新 Application Insights 資源。

    ![按一下 + 並選擇 [Application Insights]](./media/app-insights-java-get-started/01-create.png)
3. 將應用程式類型設定為 Java Web 應用程式。

    ![填寫名稱，選擇 [Java Web 應用程式]，然後按一下 [建立]](./media/app-insights-java-get-started/02-create.png)
4. 尋找新資源的檢測金鑰。您很快需要將此資訊貼上到程式碼專案中。

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/app-insights-java-get-started/03-key.png)

## 2\.將 Java 適用的 Application Insights SDK 加入至專案

*選擇適合您的專案的方式。*

#### 如果您要在 Eclipse 中建立動態網站專案...

使用 [Java 適用的 Application Insights SDK 外掛程式][eclipse]。

#### 如果您使用 Maven...

如果您的專案已設定為使用 Maven 來建置，請將下列程式碼合併至 pom.xml 檔案。

然後重新整理專案相依性，以下載程式庫。

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[1.0,)</version>
      </dependency>
    </dependencies>


* *建置或總和檢查碼驗證錯誤？* 嘗試使用特定版本，例如：`<version>1.0.n</version>`。您可以在 [SDK 版本資訊](app-insights-release-notes-java.md)或 [Maven 成品](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights)中找到最新版本。
* *需要更新為新的 SDK？* 請重新整理專案的相依項目。

#### 如果您使用 Gradle...

如果您的專案已設定為要使用 Gradle 建置，請將下列程式碼合併至 build.gradle 檔案。

然後重新整理專案相依性，以下載程式庫。

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '1.+'
      // or applicationinsights-core for bare API
    }

* *建置或總和檢查碼驗證錯誤？ 嘗試使用特定版本，例如：* `version:'1.0.n'`。*您可以在 [SDK 版本資訊](app-insights-release-notes-java.md)中找到最新版本。*
* *更新為新版 SDK*
 * 請重新整理專案的相依項目。

#### 否則...

手動加入 SDK：

1. 下載 [Application Insights SDK for Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html)。
2. 從 ZIP 檔案解壓縮二進位檔案，然後加入您的專案。

### 問題...

* *ZIP 中的 `-core` 和 `-web` 元件之間有何關係？*

 * `applicationinsights-core` 會提供裸機 API。您一律會需要它。
 * `applicationinsights-web` 提供追蹤 HTTP 要求計數和回應時間的度量。如果您不想自動收集此遙測，您可以忽略這個。例如，如果您想要自己撰寫。

* *在我們發佈變更時更新 SDK*
 * 下載最新的 [Application Insights SDK for Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.zip) 並取代舊的。
 * [SDK 版本資訊](app-insights-release-notes-java.md)中會說明變更內容。



## 3\.加入 Application Insights .XML 檔案

在專案中的資源資料夾中加入 ApplicationInsights.xml，或確定已將其加入專案部署類別路徑。將下列 XML 複製到其中。

替換為您從 Azure 入口網站取得的檢測金鑰。

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* 檢測金鑰會隨著遙測的每個項目傳送，並告知 Application Insights 在您的資源中顯示它。
* HTTP 要求元件是選用的。它會自動將要求和回應時間的遙測傳送到入口網站。
* 事件相互關聯是 HTTP 要求元件的補充。它會指派識別碼給伺服器收到的每個要求，並將它加入為遙測的每個項目的屬性，作為 'Operation.Id' 屬性。它可讓您相互關聯與每個要求關聯的遙測，方法是在[診斷搜尋][diagnostic]中設定篩選器。

## 4\.加入 HTTP 篩選器

上一個組態步驟可讓 HTTP 要求元件記錄每個 Web 要求。(如果您只需要單純的 API，則非必要。)

在您的專案中找到並開啟 web.xml 檔案，然後將下列程式碼合併至 Web 應用程式節點下，也就是應用程式篩選器設定的位置。

為獲得最準確的結果，應該在其他所有篩選器之前先對應此篩選器。

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

#### 如果您使用 MVC 3.1 或更新版本

編輯這些元素來併入 Application Insights 封裝：

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### 如果您使用 Struts 2

將此項目加入到 Struts 組態檔案 (通常名稱為 struts.xml 或 struts-default.xml)：

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(如果預設堆疊中定義了攔截器，可以將攔截器加入該堆疊。)



## 5\.執行您的應用程式

在您的開發電腦上以偵錯模式執行應用程式，或發佈至您的伺服器。

## 6\.在 Application Insights 中檢視遙測

返回 [Microsoft Azure 入口網站](https://portal.azure.com) 中的 Application Insights 資源。

[概觀] 分頁上會顯示 HTTP 要求資料。(如果沒有出現，請稍等片刻，然後按一下 [重新整理]。)

![範例資料](./media/app-insights-java-get-started/5-results.png)


逐一點選任何圖表以查看更詳細的度量。

![](./media/app-insights-java-get-started/6-barchart.png)



而檢視要求的屬性時，您可以查看與它關聯的遙測事件，例如要求和例外狀況。

![](./media/app-insights-java-get-started/7-instance.png)



[深入了解度量。][metrics]

#### 智慧型位址名稱計算

Application Insights 假設 MVC 應用程式的 HTTP 要求的格式為：`VERB controller/action`


例如，`GET Home/Product/f9anuh81`、`GET Home/Product/2dffwrf5` 和 `GET Home/Product/sdf96vws` 將會分組至 `GET Home/Product`。

這可提供要求有意義的彙總，例如要求數量和要求的平均執行時間。


## 5\.在伺服器上安裝您的 App

現在將您的應用程式發佈至伺服器供人使用，然後查看入口網站顯示的遙測。

* 請確定您的防火牆允許應用程式將遙測傳送至這些連接埠：

 * dc.services.visualstudio.com:443
 * dc.services.visualstudio.com:80
 * f5.services.visualstudio.com:443
 * f5.services.visualstudio.com:80


* 在 Windows 伺服器上，安裝：

 * [Microsoft Visual C++ 可轉散發套件](http://www.microsoft.com/download/details.aspx?id=40784)

    (這會啟用效能計數器。)

## 例外狀況與要求失敗

會自動收集未處理的例外狀況：

![向下捲動，並按一下 [失敗] 磚](./media/app-insights-java-get-started/21-exceptions.png)

若要收集其他例外狀況的資料，您有兩個選項：

* [在您的程式碼中插入 TrackException 的呼叫][apiexceptions]。 
* [在伺服器上安裝 Java 代理程式](app-insights-java-agent.md)。指定您想要觀看的方法。


## 監視方法呼叫和外部相依性

[安裝 Java 代理程式](app-insights-java-agent.md)以記錄指定的內部方法和透過 JDBC 發出的呼叫與計時資料。


## 效能計數器

按一下 [伺服器] 磚，您就會看到一些效能計數器。


![](./media/app-insights-java-get-started/11-perf-counters.png)

### 自訂效能計數器集合

若要停用效能計數器的一組標準集合，請將下列程式碼加入 ApplicationInsights.xml 檔案的根節點下：

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### 收集其他效能計數器

您可以指定要收集的其他效能計數器。

#### JMX 計數器 (由虛擬機器公開)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName` – Application Insights 入口網站中顯示的名稱。
*	`objectName` – JMX 物件名稱。
*	`attribute` – 要提取的 JMX 物件名稱的屬性
*	`type` (選用) - JMX 物件屬性的類型：
 *	預設值：簡易類型，例如 int 或 long。
 *	`composite`：效能計數器資料的格式為 'Attribute.Data'
 *	`tabular`：效能計數器資料的格式為資料表列



#### Windows 效能計數器

每個 [Windows 效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)是類別的成員 (以欄位是類別成員的相同方式)。類別可以是全域，或可以有一定數量或指定的執行個體。

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName – Application Insights 入口網站中顯示的名稱。
*	categoryName – 與此效能計數器有關聯的效能計數器類別 (效能物件)。
*	counterName – 效能計數器的名稱。
*	instanceName – 效能計數器類別執行個體的名稱，或如果類別包含單一執行個體，則為空白字串 ("")。如果 categoryName 為 Process，而您要收集的效能計數器來自應用程式執行所在的目前 JVM 處理程序，請指定 `"__SELF__"`。

您的效能計數器會在[計量瀏覽器][metrics]中以自訂度量的形式顯示。

![](./media/app-insights-java-get-started/12-custom-perfs.png)


### Unix 效能計數器

* [使用 Application Insights 外掛程式安裝 collectd](app-insights-java-collectd.md)，來取得各種不同的系統和網路資料。

## 取得使用者與工作階段資料

好了，您現在正在從 Web 服務傳送遙測資料。現在若要取得應用程式的完整 360 度檢視，可以加入多個監視：

* [將遙測加入至您的網頁][usage]，即可監視頁面檢視和使用者度量。
* [設定 Web 測試][availability]，以確認應用程式處於線上狀態且能夠回應。

## 擷取記錄追蹤

您可以使用 Application Insights 將來自 Log4J、Logback 或其他記錄架構的記錄分解。您可以將記錄與 HTTP 要求和其他遙測相互關聯。[了解作法][javalogs]。

## 傳送您自己的遙測

既然您已安裝 SDK，您可以使用 API 來傳送自己的遙測。

* [追蹤自訂事件和度量][api]，以了解使用者正對應用程式進行的動作。
* [搜尋事件和記錄][diagnostic]以協助診斷問題。


## 可用性 Web 測試

Application Insights 可讓您定期測試網站，以檢查網站運作中且正常回應。[若要設定][availability]，請向下捲動來按一下 [可用性]。

![向下捲動，並依序按一下 [可用性]、[加入 Web 測試]](./media/app-insights-java-get-started/31-config-web-test.png)

您將取得回應時間的圖表，以及若網站關閉還會取得電子郵件通知。

![Web 測試範例](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[深入了解 Web 測試的可用性。][availability]






## 有疑問嗎？ 有問題嗎？

[疑難排解 Java](app-insights-java-troubleshoot.md)

## 後續步驟

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0128_2016-->