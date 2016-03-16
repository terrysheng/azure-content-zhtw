<properties 
	pageTitle="Application Insights 的相依性追蹤" 
	description="使用 Application Insights 分析內部部署或 Microsoft Azure Web 應用程式的使用情況、可用性和效能。" 
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
	ms.date="02/10/2016" 
	ms.author="awills"/>


# 設定 Application Insights：追蹤相依性


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



「相依性」是由應用程式呼叫的外部元件。這通常是使用 HTTP 呼叫的服務，或資料庫，或檔案系統。在 Visual Studio Application Insights 中，您很容易看到應用程式等待相依性所用的時間，以及相依性呼叫失敗的頻率。

![範例圖表](./media/app-insights-asp-net-dependencies/10-intro.png)

預設的相依性監視目前會回報這些相依性類型的呼叫：

* ASP.NET
 * SQL DATABASE
 * 使用 HTTP 式繫結的 ASP.NET Web 和 WCF 服務
 * 本機或遠端 HTTP 呼叫
 * Azure DocumentDb、資料表、Blob 儲存體和佇列
* Java
 * 透過 [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) 驅動程式，例如 MySQL、SQL Server、PostgreSQL 或 SQLite 呼叫資料庫。
* 網頁中的 JavaScript - [網頁 SDK](app-insights-javascript.md) 會將 Ajax 呼叫自動記錄為相依性。

您可以使用 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)，撰寫自己的 SDK 呼叫來監視其他相依性。


## 設定相依性監視

您需要 [Microsoft Azure](http://azure.com) 訂用帳戶。

### 如果您的應用程式是在您的 IIS 伺服器上執行

1. 在 IIS Web 伺服器中，以系統管理員認證登入。
2. 下載並執行[狀態監視器安裝程式](http://go.microsoft.com/fwlink/?LinkId=506648)。
4. 在安裝精靈中，登入 Microsoft Azure。

    ![使用 Microsoft 帳戶認證登入 Azure](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *連接錯誤？ 請參閱[疑難排解](#troubleshooting)。*

5. 挑選您想要監視的已安裝 Web 應用程式或網站，然後設定您在 Application Insights 入口網站中查看結果時想要使用的資源。

    ![選擇應用程式和資源。](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    一般來說，您可以選擇設定新的資源和[資源群組][roles]。

    否則，如果您已經為網站設定 [Web 測試][availability]，或設定 [Web 用戶端監視][client]，請使用現有的資源。

6. 重新啟動 IIS。

    ![選擇對話方塊頂端的 [重新啟動]。](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    您的 Web 服務將會中斷一小段時間。

6. 請注意，ApplicationInsights.config 已插入至您想要監視的 Web 應用程式。

    ![在 Web 應用程式的程式碼檔案旁找到 .config 檔案。](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   web.config 也有一些變動。

#### 稍後再 (重新) 設定嗎？

完成精靈之後，您隨時都可以重新設定代理程式。如果已安裝代理程式，但初始設定有一些問題，則您也可以這樣做。

![按一下工作列上的 [Application Insights] 圖示](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### 如果您的應用程式是以 Azure Web 應用程式執行

在您的 Azure Web 應用程式的控制台中，加入 Application Insights 延伸模組。

![在您的 Web 應用程式中，依序按一下 [設定]、[延伸模組]、[加入]、[Application Insights]](./media/app-insights-asp-net-dependencies/05-extend.png)


### 如果是 Azure 雲端服務專案

[將指令碼加入至 Web 和背景工作角色](app-insights-cloudservices.md)。

## <a name="diagnosis"></a> 診斷相依性效能問題

若要評估伺服器上的要求效能：

![在 Application Insights 的應用程式 [概觀] 頁面中，按一下 [效能] 磚](./media/app-insights-asp-net-dependencies/01-performance.png)

向下捲動以查看要求方格：

![含有平均和計數的要求清單](./media/app-insights-asp-net-dependencies/02-reqs.png)

最上方的要求花了很長的時間。來看看我們是否可以查明時間花費在何處。

按一下該列，以查看個別的要求事件：


![要求發生次數的清單](./media/app-insights-asp-net-dependencies/03-instances.png)

按一下任一個長時間執行的執行個體，來進一步檢查。

> [AZURE.NOTE] 稍微向下捲動，以選擇執行個體。管線中的延遲可能表示最上方的執行個體資料不完整。

向下捲動至與此要求相關的遠端相依性呼叫：

![尋找遠端相依性的呼叫，識別不尋常的持續時間](./media/app-insights-asp-net-dependencies/04-dependencies.png)

看起來此要求的大部分時間似乎都花費在呼叫本機服務上。

選取該列，以取得詳細資訊：


![點選該遠端相依性來找出問題原因](./media/app-insights-asp-net-dependencies/05-detail.png)

詳細資料含有足以診斷問題的資訊。



## 失敗

如果有失敗的要求，請按一下圖表。

![按一下失敗要求的圖表](./media/app-insights-asp-net-dependencies/06-fail.png)

點選要求類型和要求執行個體，以尋找失敗的遠端相依性呼叫。


![按一下要求類型，按一下執行個體以取得同一個執行個體的不同檢視，按一下執行個體以取得例外狀況的詳細資料。](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## 自訂相依性追蹤

標準的相依性追蹤模組會自動探索外部相依性，例如資料庫和 REST API。但是您可能想以相同的方式對待一些其他元件。

您可以使用標準模組所使用的相同 [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency) 來撰寫傳送相依性資訊的程式碼。

例如，如果您建置程式碼的組件不是您自己撰寫的，您可以計算對組件的所有呼叫，以找出它佔回應時間的比例。若要在 Application Insights 中的相依性圖表中顯示此資料，請使用 `TrackDependency` 傳送。

```C#

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

## 後續步驟

- [例外狀況](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [使用者和頁面資料](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md

 

<!---HONumber=AcomDC_0211_2016-->