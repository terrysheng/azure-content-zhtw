<properties 
	pageTitle="適用於使用中 Java Web 應用程式的 Application Insights" 
	description="開始監視已在伺服器上執行的 Web 應用程式" 
	services="application-insights" 
    documentationCenter="java"
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
 
# 適用於使用中 Java Web 應用程式的 Application Insights

*Application Insights 目前僅供預覽。*

如果您有 Web 應用程式已在 J2EE 伺服器上執行，您可以使用 [Appliction Insights](app-insights-overview.md) 開始監視它，不需要變更程式碼或重新編譯您的專案。使用此選項可以取得傳送至您伺服器的 HTTP 要求、未處理的例外狀況和效能計數器的相關資訊。

您將需要 [Microsoft Azure](http://azure.com) 訂用帳戶。

> [AZURE.NOTE]此頁面上的程序會在執行階段將 SDK 加入您的 Web 應用程式。如果您不想要更新或重建您的原始程式碼，這會非常好用。不過，如果可以的話，我們建議您改為[將 SDK 加入原始程式碼](app-insights-java-get-started.md)。這樣可以提供您更多選項，包括撰寫程式碼來追蹤使用者活動。

## 1\.取得 Application Insights 檢測金鑰

1. 登入 [Microsoft Azure 入口網站](https://portal.azure.com)
2. 建立新 Application Insights 資源

    ![按一下 + 並選擇 [Application Insights]](./media/app-insights-java-get-started/01-create.png)
3. 將應用程式類型設定為 Java Web 應用程式。

    ![填寫名稱，選擇 [Java Web 應用程式]，然後按一下 [建立]](./media/app-insights-java-get-started/02-create.png)
4. 尋找新資源的檢測金鑰。您很快需要將此資訊貼上到程式碼專案中。

    ![在新資源概觀中，按一下 [屬性] 並複製檢測金鑰](./media/app-insights-java-get-started/03-key.png)

## 2\.下載 SDK

1. 下載 [Java 適用的 Application Insights SDK](http://dl.windowsazure.com/applicationinsights/javabin/sdk.zip)。 
2. 在您的伺服器上，將 SDK 內容解壓縮到載入您專案二進位檔的目錄。如果您使用 Tomcat，這通常會在 `webapps<your_app_name>\WEB-INF\lib` 下


## 3\.加入 Application Insights XML 檔案

您可以在加入 SDK 的資料夾中建立 ApplicationInsights.xml。將下列 XML 放入上述檔案。

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
* 事件相互關聯是 HTTP 要求元件的補充。它會指派識別碼給伺服器收到的每個要求，並將它加入為遙測的每個項目的屬性，作為 'Operation.Id' 屬性。它可讓您相互關聯與每個要求關聯的遙測，方法是在[診斷搜尋](app-insights-diagnostic-search.md)中設定篩選器。


## 4\.加入 HTTP 篩選器

在專案中找到並開啟 web.xml 檔案，並合併 web-app 節點下的下列程式碼片段，在其中您可以設定應用程式篩選器。

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

## 5\.重新啟動您的 Web 應用程式

## 6\.在 Application Insights 中檢視遙測

返回 [Microsoft Azure 入口網站](https://portal.azure.com) 中的 Application Insights 資源。

[概觀] 分頁上會顯示 HTTP 要求資料。(如果沒有出現，請稍等片刻，然後按一下 [重新整理]。)

![範例資料](./media/app-insights-java-get-started/5-results.png)
 

逐一點選任何圖表以查看更詳細的度量。

![](./media/app-insights-java-get-started/6-barchart.png)

 

而檢視要求的屬性時，您可以查看與它關聯的遙測事件，例如要求和例外狀況。
 
![](./media/app-insights-java-get-started/7-instance.png)


[深入了解度量。](app-insights-metrics-explorer.md)



## 後續步驟

* [將遙測加入至您的網頁](app-insights-web-track-usage.md)，即可監視頁面檢視和使用者度量。
* [設定 Web 測試](app-insights-monitor-web-app-availability.md)，以確認應用程式處於線上狀態且能夠回應。
* [擷取記錄追蹤](app-insights-java-trace-logs.md)
* [搜尋事件和記錄](app-insights-diagnostic-search.md)以協助診斷問題。


 

<!---HONumber=August15_HO6-->