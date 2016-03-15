<properties 
	pageTitle="使用 Application Insights 的 ASP.NET Web 應用程式分析" 
	description="裝載在內部部署環境或 Azure 之 ASP.NET 網站的效能、可用性及流量分析。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/06/2016" 
	ms.author="awills"/>


# 設定 ASP.NET 的 Application Insights


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Application Insights SDK 會從 Web 應用程式傳送分析遙測至 Azure 入口網站，您可以在此登入並查看應用程式效能和使用量的圖表。

![範例效能監視圖表](./media/app-insights-asp-net/10-perf.png)

您也可以調查特殊要求、例外狀況和記錄事件，並將其相互關聯。您可以使用 API 新增遙測以詳細監視效能和使用量。

#### 開始之前

您需要：

* [Microsoft Azure](http://azure.com) 訂用帳戶。如果您的小組或組織擁有 Azure 訂用帳戶，擁有者就可以使用您的 [Microsoft 帳戶](http://live.com)將您加入。
* Visual Studio 2013 Update 3 或更新版本。

## <a name="ide"></a> 在 Visual Studio 中將 Application Insights 加入專案

#### 對於新專案

當您在 Visual Studio 中建立新專案時，請務必選取 Application Insights。


![Create an ASP.NET project](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

選取具有 Azure 登入的帳戶。您可能會受邀重新輸入認證。(或者，如果您尚未登入，會新增 SDK 的程式碼，您稍後可以設定。)


#### ... 對於現有專案

在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [加入 Application Insights] 或 [設定 Application Insights]。

![Choose Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)


#### 設定選項

如果這是您第一次使用，系統會要求您登入或註冊 Microsoft Azure。

如果此應用程式是更大應用程式的一部分，您可以使用 [**組態設定**]，將它放在與其他元件相同的資源群組中。


####<a name="land"></a>「加入 Application Insights」執行了哪些動作？

命令執行了下列步驟 (如果想要的話，可以改為[手動執行](app-insights-start-monitoring-app-health-usage.md))：

1. 將 Application Insights Web SDK NuGet 封裝加入您的專案。若要在 Visual Studio 中看到它，請以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]。
2. 在 [Azure 入口網站][portal]中建立 Application Insights 資源。這是您會看到您的資料的位置。它會擷取可識別資源的「檢測金鑰」。
3. 在 `ApplicationInsights.config` 中插入檢測金鑰，讓 SDK 可以將遙測傳送至入口網站。

如果您一開始未登入 Azure，將會安裝 SDK，而不將它連接至資源。您可以在偵錯時，於 Visual Studio 搜尋視窗中查看及搜尋 Application Insights 遙測。您可以稍後再完成其他步驟。

## <a name="run"></a>執行專案

利用 F5 執行應用程式並立即試用：開啟不同的頁面來產生一些遙測。

在 Visual Studio 中，您可以看見已記錄的事件計數。

![在 Visual Studio 中，[Application Insights] 按鈕會在偵錯期間顯示。](./media/app-insights-asp-net/appinsights-09eventcount.png)

按一下此按鈕以開啟診斷搜尋。


### 診斷搜尋

[搜尋] 視窗會顯示已記錄的事件。(如果您在設定 Application Insights 時登入至 Azure，就能夠在入口網站搜尋相同的事件。)

![以滑鼠右鍵按一下專案，然後選擇 [Application Insights]、[搜尋]](./media/app-insights-asp-net/34.png)

任意文字搜尋適用於事件中的任何欄位。例如，搜尋頁面的 URL 的一部分；或者如用戶端城市的屬性值；或者追蹤記錄檔中的特定單字。


[深入了解搜尋](app-insights-diagnostic-search.md)

### 例外狀況

如果您已[設定例外狀況監視](app-insights-asp-net-exceptions.md)，例外狀況報告會顯示在 [搜尋] 視窗中。

按一下例外狀況以取得堆疊追蹤。如果應用程式的程式碼在 Visual Studio 中開啟，您可以從堆疊追蹤點選至程式碼的相關程式碼行。



## <a name="monitor"></a> 開啟 Application Insights

在 [Azure 入口網站][portal]中開啟 Application Insights 資源。

![Right-click your project and open the Azure portal](./media/app-insights-asp-net/appinsights-04-openPortal.png)

如果您沒有在將 Application Insights 加入此應用程式時登入 Azure，請立即登入。選取 [設定 Application Insights]。這樣做可讓您在部署應用程式後，繼續從上線的應用程式查看遙測。遙測會出現在 Application Insights 入口網站。

### 度量：彙總資料

在 [概觀] 圖表中尋找資料。剛開始的時候，您只會看見一或兩個資料點。例如：

![Click through to more data](./media/app-insights-asp-net/12-first-perf.png)

按一下任何圖表以查看詳細度量。[深入了解度量。][perf]

* *沒有使用者或頁面資料嗎？* - [新增使用者和頁面資料](app-insights-web-track-usage.md)

### 搜尋：個別事件

開啟 [搜尋] 來調查個別要求和其相關聯的事件。

![在 [搜尋] 刀鋒視窗中，搜尋頁面名稱或其他屬性。](./media/app-insights-asp-net/21-search.png)

[深入了解搜尋](app-insights-diagnostic-search.md)

* *沒有相關聯的事件嗎？* 設定[伺服器例外狀況](app-insights-asp-net-exceptions.md)和[相依性](app-insights-asp-net-dependencies.md)。


## 沒有資料？

* 在 Visual Studio 中，請確定您的應用程式正在傳送遙測。您應該會在 [輸出] 視窗和 [診斷中樞] 中看到追蹤。
* 請確定您在 Azure 中查看的是正確的項目。登入 [Azure 入口網站](https://portal.azure.com)，按一下 [瀏覽] > [Application Insights]，然後選取您的應用程式。
* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 開啟 [[搜尋][diagnostic]] 刀鋒視窗，查看個別事件。有時候，事件通過計量管線所需的時間較長。
* 請稍等片刻，然後按一下 [重新整理]。
* 請參閱[疑難排解][qna]。



## 發佈您的應用程式

現在部署應用程式並觀看資料累積情形。

以偵錯模式執行時，系統會透過管線迅速傳送遙測資料，因此您應該可以在幾秒內看見資料。在部署應用程式時，資料累積會較為緩慢。

#### 組建伺服器發生問題？

參閱[此疑難排解項目](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild)。

> [AZURE.NOTE] 如果您的應用程式會產生大量遙測 (且您使用的是 ASP.NET SDK 版本 2.0.0-beta3 或較新)，調適性取樣模型會自動藉由僅傳送事件代表性片段，以減少傳送到入口網站的量。不過，與同一個要求相關的事件會選取或取消選取為群組，讓您可以在相關事件之間瀏覽。[了解取樣](app-insights-sampling.md)。


## 偵錯遙測

### 診斷中樞

診斷中樞 (在 Visual Studio 2015 或更新版本) 會顯示其產生的 Application Insights 伺服器遙測。即使您選擇只安裝 SDK，而不連接至 Azure 入口網站中的資源，也會運作。

![開啟 [診斷工具] 視窗，並檢查 Application Insights 事件。](./media/app-insights-asp-net/31.png)

如果您在將遙測傳送至入口網站之前有一些[自訂遙測](app-insights-api-custom-events-metrics.md)想要偵錯，這特別有用。

* *首先，我完全設定 Application Insights 將遙測傳送至入口網站。但是現在我只想要查看在 Visual Studio 中的遙測。*

    從 ApplicationInsights.config 註解化 `<instrumentationkey>...` 這一行。當您準備再次將遙測傳送至入口網站時，請取消註解。



## 後續步驟

- [使用者和頁面資料](../article/application-insights/app-insights-javascript.md#selector1)
- [例外狀況](../article/application-insights/app-insights-asp-net-exceptions.md#selector1)
- [相依項目](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





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
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0309_2016-->