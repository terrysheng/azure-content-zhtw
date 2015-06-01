<properties 
	pageTitle="Application Insights：平台" 
	description="我是否可以使用 Application Insights 搭配...？" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Application Insights：平台

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

#### 我是否可以使用 Application Insights 搭配...？


## 語言

+ [C#, VB](app-insights-start-monitoring-app-health-usage.md)
+ [JavaScript web pages](app-insights-web-track-usage.md)
+ [Windows Store JavaScript apps](#cordova)
+ [Java](app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

## 平台

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [Azure Web Apps 和 VM](insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Windows 桌面](app-insights-windows-desktop.md)


## 記錄架構

+	[Log4Net、NLog 或 System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java、Log4J 或 Logback](app-insights-java-trace-logs.md)


## 專案

亦請造訪 [GitHub 上的 Application Insights SDK 專案](https://github.com/Microsoft/AppInsights-Home)


### <a name="cordova"></a>Cordova 和 Windows 市集 JavaScript 應用程式

在 Visual Studio 中，以滑鼠右鍵按一下專案，然後選擇 [**管理 NuGet 封裝**]。

選取 [**線上**] 然後搜尋 Application Insights。

安裝 [**JavaScript 應用程式適用的 Application Insights API**]。

使用標準用戶端 [Web 應用程式指令碼](app-insights-web-track-usage.md)，但有一項變更。

從 Application Insights 入口網站取得指令碼時，請在檢測金鑰後插入一行：

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[使用 JavaScript 的 Windows 市集應用程式](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54-->