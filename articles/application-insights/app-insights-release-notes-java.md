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
	ms.author="awills"/>
 
# Java 適用的 Application Insights SDK 的版本資訊

[Application Insights SDK for Java](app-insights-java-get-started.md) 傳送有關您作用中的應用程式遙測到 [Application Insights](http://azure.microsoft.com/services/application-insights/)，您可以在此分析它的使用情況與效能。

#### 若要在應用程式中安裝 SDK

請參閱[開始使用 SDK for Java](app-insights-java-get-started.md)。

#### 若要升級至最新的 SDK 

升級之後，必須將您對 ApplicationInsights.xml 所做的任何自訂合併回來。請取得一份複本來與新的檔案比較。

如果您使用 Maven 或 Gradle

1. 如果您已在 pom.xml 或 build.gradle 中指定特定的版本號碼，請更新版本號碼。
2. 請重新整理專案的相依項目。

否則

* 請下載最新版的 [Azure Libraries for Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) 取代舊版本。 
 
比較舊的和新的 ApplicationInsights.xml。您看到的變更許多是因為我們新增與移除了模組。恢復您所做的任何自訂。

## 1\.0.0 版
- 加入 CollectD 適用的 Application Insights 寫入器外掛程式的支援。
- 加入 Application Insights Java 代理程式的支援。
- 支援 HttpClient 版本 4.2 版或更新版本的相容性問題的修正程式。

## 0\.9.6 版
- 讓 Java SDK 與 Servlet 2.5 版與 HttpClient 4.3 之前的版本相容。
- 加入 Java EE 攔截器的支援。
- 從 Logback 附加器移除多餘的相依性。

## 0\.9.5 版  

- 修正自訂事件因 Cookie 剖析錯誤而沒有與使用者/工作階段相互關聯的問題。  
- 改善解析 ApplicationInsights.xml 組態檔位置的邏輯。
- 將不會在伺服器端產生匿名使用者和工作階段 Cookie。若要實作 Web 應用程式的使用者和工作階段追蹤，JavaScript SDK 的檢測現在為必要 – 仍採用來自 JavaScript SDK 的 Cookie。請注意，這項變更可能會造成使用者和工作階段計數的明顯重新結算，因為現在只需計算使用者產生的工作階段。

## 0\.9.4 版

- 支援從 32 位元 Windows 電腦收集效能計數器。
- 支援使用新的 ```trackDependency``` 方法 API 手動追蹤相依性。
- 能夠藉由將 ```SyntheticSource``` 屬性加入至報告項目，將遙測項目標記為綜合。
 

<!---HONumber=July15_HO5-->