<properties 
	pageTitle="ASP.NET 的 Application Insights" 
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
	ms.date="07/14/2015" 
	ms.author="awills"/>


# ASP.NET 的 Application Insights

*Application Insights 目前僅供預覽。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


Visual Studio Application Insights 監視您的即時應用程式，協助您[偵測並診斷效能問題和例外狀況][detect]，同時[探索應用程式的使用情況][knowUsers]。它可以搭配各種不同的應用程式類型。這適用於裝載在專屬內部部署 IIS 伺服器或 Azure VM 上的應用程式，以及 Azure Web 應用程式。([也涵蓋裝置應用程式和 Java 伺服器][start]。)

![範例效能監視圖表](./media/app-insights-asp-net/10-perf.png)

對於許多應用程式類型，[Visual Studio 可以將 Application Insights 加入至您的應用程式](#ide)，而且您幾乎不會察覺。為了讓您清楚了解整個運作情形，本文將帶您手動完成這些步驟。

#### 開始之前

您需要：

* [Microsoft Azure](http://azure.com) 訂用帳戶。如果您的小組或組織擁有 Azure 訂用帳戶，擁有者就可以使用您的 [Microsoft 帳戶](http://live.com)將您加入。
* Visual Studio 2013 或更新版本。

## <a name="ide"></a> 在 Visual Studio 中將 Application Insights 加入至專案

#### 對於新專案

當您在 Visual Studio 中建立新專案時，請務必選取 Application Insights。


![Create an ASP.NET project](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ... 對於現有專案

在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [Add Application Insights]。

![Choose Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### 設定選項

如果這是您第一次使用，系統會要求您登入或註冊 Microsoft Azure 預覽(該帳戶與 Visual Studio Online 帳戶彼此獨立)。

如果此應用程式是更大應用程式的一部分，您可以使用 [**組態設定**]，將它放在與其他元件相同的資源群組中。


#### 「加入 Application Insights」的作用為何？

此命令會執行兩件事 (如果想要的話，您也可以手動執行)：

* 在 [Azure 入口網站][portal]中建立 Application Insights 資源。這是您會看到您的資料的位置。它會擷取可識別資源的*檢測金鑰*。
* 將 Application Insights Web SDK NuGet 封裝加入至您的專案，並將金鑰放在 `ApplicationInsights.config`。


## <a name="run"></a>執行專案

利用 F5 執行應用程式並立即試用：開啟不同的頁面來產生一些遙測。

在 Visual Studio 中，您可以看見已傳送到的事件計數。

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> 開啟 Application Insights

在 [Azure 入口網站][portal]中開啟 Application Insights 資源。

![Right-click your project and open the Azure portal](./media/app-insights-asp-net/appinsights-04-openPortal.png)


在 [概觀] 圖表中尋找資料。剛開始的時候，您只會看見一或兩個資料點。例如：

![Click through to more data](./media/app-insights-asp-net/12-first-perf.png)

按一下任何圖表以查看詳細度量。[深入了解度量。][perf]

現在部署應用程式並觀看資料累積情形。


以偵錯模式執行時，系統會透過管線迅速傳送遙測資料，因此您應該可以在幾秒內看見資料。在部署應用程式時，資料累積會較為緩慢。

#### 沒有資料？

* 開啟 [[搜尋][diagnostic]] 磚來查看個別事件。
* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 請稍等片刻，然後按一下 [重新整理]。
* 請參閱[疑難排解][qna]。

#### 組建伺服器發生問題？

請參閱[此疑難排解項目](app-insights-troubleshoot-faq.md#NuGetBuild)。


## 加入瀏覽器監視

瀏覽器或用戶端監視可提供有關在瀏覽器中發生的使用者、工作階段、頁面檢視和任何例外狀況或損毀的相關資料。

![選擇 [新增]、[開發人員服務]、[Application Insights]。](./media/app-insights-asp-net/16-page-views.png)

您也可以撰寫自己的程式碼，來追蹤您的使用者使用您的應用程式，徹底得知詳細的點按和按鍵層級。

#### 如果您的用戶端是 Web 瀏覽器

如果您的應用程式顯示網頁，請在每一頁加入 JavaScript 程式碼片段。從您的 Application Insights 資源取得程式碼：

![在您的 Web 應用程式中，開啟 [快速啟動]，然後按一下 [取得程式碼來監視我的網頁]](./media/app-insights-asp-net/02-monitor-web-page.png)

請注意，此程式碼包含可識別您的應用程式資源的檢測金鑰。

[進一步了解網頁追蹤。](app-insights-web-track-usage.md)

#### 如果您的用戶端是裝置應用程式

如果您的應用程式為用戶端 (例如電話或其他裝置) 提供服務，請加入[適當的 SDK](app-insights-platforms.md) 至您的裝置應用程式。

如果您使用與伺服器 SDK 相同的檢測金鑰來設定用戶端 SDK，將整合兩個資料流，讓您同時看到。

## 使用情況追蹤

送出新的使用者劇本後，您想知道有多少客戶正在使用它，以及他們是否達成其目標或遇到困難。透過同時在用戶端和伺服器程式碼中插入 TrackEvent() 及其他呼叫，取得使用者活動的詳細狀況。

[使用 API 來追蹤使用量][api]


## 診斷記錄

從您喜好的記錄架構[擷取記錄追蹤][netlogs]，以協助您診斷任何問題。您的記錄項目會隨著 Application Insights 遙測事件出現在[診斷搜尋][diagnostic]中。

## 發佈您的應用程式

如果您尚未發行您的應用程式 (因為您加入了 Application Insights)，請立即發行您的應用程式。隨著人們使用您的應用程式觀看圖表中的資料成長。

### 針對開發、測試和發行保持個別的資源

針對主要應用程式，建議您從偵錯、測試和生產傳送遙測資料到[不同資源](app-insights-separate-resources.md)。



## 加入相依性追蹤

[相依性度量](app-insights-dependencies.md)對於協助您診斷效能問題的價值難以衡量。它們會測量從您的應用程式對資料庫、REST API 和其他外部元件的呼叫。

![](./media/app-insights-asp-net/04-dependencies.png)

#### 如果您的應用程式是在您的 IIS 伺服器中執行

使用系統管理員權限登入您的伺服器，並安裝 [Application Insights 狀態監視器](http://go.microsoft.com/fwlink/?LinkId=506648)。

(您也可以使用狀態監視器來[檢測已在執行的應用程式](app-insights-monitor-performance-live-website-now.md)，即使它不是使用 SDK 建置。)

#### 如果您的應用程式是 Azure Web 應用程式

在您的 Azure Web 應用程式的控制台中，加入 Application Insights 延伸模組。

![在您的 Web 應用程式中，依序按一下 [設定]、[延伸模組]、[加入]、[Application Insights]](./media/app-insights-asp-net/05-extend.png)

(延伸模組只能協助使用 SDK 建置的應用程式。與狀態監視器不同，它無法檢測現有的應用程式。)

## 可用性 Web 測試

[設定 Web 測試][availability]以從外部測試您的應用程式處於線上且能夠回應。


![](./media/app-insights-asp-net/appinsights-10webtestresult.png)






## 若要升級至未來的 SDK 版本

若要升級至[新版的 SDK](app-insights-release-notes-dotnet.md)，請再次開啟 NuGet 封裝管理員，並篩選出已安裝的封裝。選取 Microsoft.ApplicationInsights.Web 然後選擇 [升級]。

如果您已對 ApplicationInsights.config 進行任何的自訂，請在升級前儲存複本，並在升級後合併您的變更到新版本中。



## <a name="video"></a>影片

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO3-->