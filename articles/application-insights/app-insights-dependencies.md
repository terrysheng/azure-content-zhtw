<properties 
	pageTitle="在 Application Insights 中診斷相依性問題" 
	description="尋找失敗和相依性所造成的遲緩效能" 
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
	ms.date="02/09/2016" 
	ms.author="awills"/>
 
# 在 Application Insights 中診斷相依性問題


「相依性」是由應用程式呼叫的外部元件。這通常是使用 HTTP 呼叫的服務，或資料庫，或檔案系統。或是在您的網頁指令碼中，它可以是對伺服器進行的 AJAX 回呼。在 Visual Studio Application Insights 中，您很容易看到應用程式等待相依性所用的時間，以及相依性呼叫失敗的頻率。

## 可以使用的地方

預設的相依性監視目前適用於：

* 在 IIS 伺服器或在 Azure 上執行的 ASP.NET Web 應用程式和服務
* [Java Web 應用程式](app-insights-java-agent.md)
* [網頁](https://azure.microsoft.com/blog/ajax-collection-in-application-insights/)

若為其他類型，例如裝置應用程式，您可以使用 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) 撰寫自己的監視器。

預設的相依性監視目前會回報這些相依性類型的呼叫：

* ASP.NET
 * SQL DATABASE
 * 使用 HTTP 式繫結的 ASP.NET Web 和 WCF 服務
 * 本機或遠端 HTTP 呼叫
 * Azure DocumentDb、資料表、Blob 儲存體和佇列
* Java
 * 透過 [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) 驅動程式，例如 MySQL、SQL Server、PostgreSQL 或 SQLite 呼叫資料庫。
* 網頁
 * [AJAX 呼叫](app-insights-javascript.md)

同樣地，您可以撰寫自己的 SDK 呼叫來監視其他相依性。

## 設定相依性監視

為主機伺服器安裝適當的代理程式。

平台 | 安裝
---|---
IIS 伺服器 | [狀態監視器](app-insights-monitor-performance-live-website-now.md)
Azure Web 應用程式 | [Application Insights 延伸模組](../azure-portal/insights-perf-analytics.md)
Java Web 伺服器 | [Java Web 應用程式](app-insights-java-agent.md)
網頁 | [JavaScript 監視](app-insights-javascript.md) (沒有網頁監視以外的其他設定)

IIS 伺服器的狀態監視器不需要您使用 Application Insights SDK 重新建置來源專案。

## <a name="diagnosis"></a> 診斷 Web 伺服器中的相依性效能問題

若要評估伺服器上的要求效能：

![在 Application Insights 的應用程式 [概觀] 頁面中，按一下 [效能] 磚](./media/app-insights-dependencies/01-performance.png)

向下捲動以查看要求方格：

![含有平均和計數的要求清單](./media/app-insights-dependencies/02-reqs.png)

最上方的要求花了很長的時間。來看看我們是否可以查明時間花費在何處。

按一下該列，以查看個別的要求事件：


![要求發生次數的清單](./media/app-insights-dependencies/03-instances.png)

按一下任一個長時間執行的執行個體，來進一步檢查。

> [AZURE.NOTE] 稍微向下捲動，以選擇執行個體。管線中的延遲可能表示最上方的執行個體資料不完整。

向下捲動至與此要求相關的遠端相依性呼叫：

![尋找遠端相依性的呼叫，識別不尋常的持續時間](./media/app-insights-dependencies/04-dependencies.png)

看起來此要求的大部分時間似乎都花費在呼叫本機服務上。

選取該列，以取得詳細資訊：


![點選該遠端相依性來找出問題原因](./media/app-insights-dependencies/05-detail.png)

詳細資料含有足以診斷問題的資訊。



## 失敗

如果有失敗的要求，請按一下圖表。

![按一下失敗要求的圖表](./media/app-insights-dependencies/06-fail.png)

點選要求類型和要求執行個體，以尋找失敗的遠端相依性呼叫。


![按一下要求類型，按一下執行個體以取得同一個執行個體的不同檢視，按一下執行個體以取得例外狀況的詳細資料。](./media/app-insights-dependencies/07-faildetail.png)


## 自訂相依性追蹤

標準的相依性追蹤模組會自動探索外部相依性，例如資料庫和 REST API。但是您可能想以相同的方式對待一些其他元件。

您可以使用標準模組所使用的相同 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) 來撰寫傳送相依性資訊的程式碼。

例如，如果您建置程式碼的組件不是您自己撰寫的，您可以計算對組件的所有呼叫，以找出它佔回應時間的比例。若要在 Application Insights 中的相依性圖表中顯示此資料，請使用 `TrackDependency` 傳送。

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

如果您想要關閉標準的相依性追蹤模組，請移除 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中 DependencyTrackingTelemetryModule 的參考。


## Ajax

請參閱[網頁](app-insights-javascript.md)。


 

<!---HONumber=AcomDC_0211_2016-->