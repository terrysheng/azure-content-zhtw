<properties 
	pageTitle="Application Insights 中的遙測取樣" 
	description="如何讓遙測量保持在控制下。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="awills"/>

#  Application Insights 中的取樣

*Application Insights 目前僅供預覽。*


取樣是 Application Insights 中的選項，可讓您收集和儲存縮小的一組遙測，同時保有應用程式資料統計的正確分析。您通常會使用它來減少流量並避免[節流](app-insights-pricing.md#data-rate)。篩選資料的方式會允許相關的項目通過，使得您可以利用一組縮小的資料執行診斷調查。用戶端和伺服器端會自動協調以篩選相關的項目。在入口網站中呈現度量計數時，就會重新正規化以考慮取樣，以將對統計資料帶來的任何影響降至最低。


取樣目前為 Beta 版，因此在未來可能有所變更。

## 設定您的應用程式的取樣

取樣目前可供 ASP.NET SDK 或[任何網頁](#other-web-pages)使用。

### ASP.NET 伺服器
若要在應用程式中設定取樣，請插入下列程式碼片段至 Global.asax.cs 的 `Application_Start()` 方法：

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();
```

> [AZURE.NOTE]針對取樣百分比，選擇接近 100/N 的百分比，其中 N 是整數。舉例來說，有效的值包括的 50 (= 1/2)、33.33 (= 1/3)、25 (= 1/4)、20 (= 1/5)，依此類推。目前取樣並不支援其他值。

### 具有 JavaScript 的網頁

您可以從任何伺服器設定要取樣的網頁。針對 ASP.NET 伺服器，設定用戶端和伺服器端。

當您[設定 Application Insights 的網頁](app-insights-javascript.md)時，請修改您從 Application Insights 入口網站取得的程式碼片段。(在 ASP.NET 中，您會在 \_Layout.cshtml 中找到它。) 在檢測金鑰之前插入類似 `samplingPercentage: 10,` 的行：

    <script>
	var appInsights= ... 
	}({ 

	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

確定您在 JavaScript 中提供與伺服器端相同的取樣百分比。

[深入了解 API](app-insights-api-custom-events-metrics.md)


## 何時使用取樣？

對大多數小型和中型大小應用程式，您不需要取樣。最有用的診斷資訊和最準確的統計資料會是透過收集所有使用者活動的資料取得。

 
您會使用取樣的主要原因是：


* 當您的應用程式在短時間間隔傳送非常高比率的遙測時，Application Insights 服務會將資料點卸除 (「節流」)。 
* 您想要保持在您的定價層的資料點[配額](app-insights-pricing.md)。 
* 若要從收集的遙測降低網路流量。 

## 取樣運作方式？

從應用程式的觀點來看，取樣是 Application Insights SDK 的功能。您指定應傳送至 Application Insights 服務的所有資料資料點的百分比。從 Application Insights SDK 2.0.0 版起，您可以從程式碼控制取樣百分比。(未來版本的 SDK 會另外允許從 ApplicationInsights.config 檔案設定取樣百分比。)

SDK 會決定要卸除的遙測項目以及要保留哪些。取樣決策會根據數個規則，目標是要保留相關的資料點不變，在 Application Insights 中保有可採取動作而且即使有縮減資料集仍可靠的診斷經驗。比方說，如果是失敗的要求，您的應用程式會傳送其他遙測項目 (例如從此要求記錄的例外狀況和追蹤)，取樣將不會分割此要求和其他遙測。它會一起保留或卸除。如此一來，當您在 Application Insights 中查看要求詳細資料時，您一律可以看到要求和其相關聯的遙測項目。

針對定義「使用者」的應用程式 (也就是最常見的 Web 應用程式)，取樣決策是根據使用者識別碼的雜湊，這表示任何特定使用者的所有遙測可加以保留或卸除。針對沒有定義使用者的應用程式類型 (例如 Web 服務)，取樣決策係根據要求的作業識別碼。最後，對於未設定使用者或作業識別碼的遙測項目 (例如非同步執行緒報告、不具使用任何 http 內容的遙測項目)，取樣只會擷取每種類型的遙測項目的某個百分比。

呈現遙測回來給您時，Application Insights 服務會以收集時使用的相同取樣百分比調整度量，來彌補遺漏的資料點。因此，在 Application Insights 中查看遙測時，使用者會看到統計正確也非常接近實際數的近似值。

近似值的精確度絕大部分取決於設定的取樣百分比。此外，對於處理大量使用者的通常類似要求的應用程式，其精確度會增加。相反地，對於不處理大量負載的應用程式，就不需要取樣，因為這些應用程式通常可以傳送遙測同時保持在配額內，而不會因節流造成資料遺失。

請注意，Application Insights 不會對度量和工作階段遙測類型取樣，因為這些類型的有效位數減少可能高度讓人困擾。

## 取樣與 JavaScript SDK

用戶端 (JavaScript) SDK 參與取樣，連同伺服器端 SDK。已檢測的頁面只會從伺服器端決定「納入取樣」的相同使用者傳送用戶端遙測。此邏輯的設計是為了在用戶端和伺服器端之間保有使用者工作階段的完整性。如此一來，您可以從 Application Insights 中的任何特定遙測項目找到這個使用者或工作階段的所有其他遙測項目。

*我的用戶端和伺服器端遙測未顯示您上方描述的協調範例。*

* 確認您已在伺服器和用戶端上同時啟用取樣。
* 確定 SDK 版本為 2.0 或更新版本。
* 請檢查您的用戶端和伺服器中設定相同的取樣百分比。


## 常見問題集 

*為什麼不取樣簡單的「收集每個遙測類型百分之 X」？*

 *  雖然這個取樣方法會提供具有極高精確度的度量近似值，它會破壞根據每個使用者、工作階段和要求相互關聯資料的能力，而這對於診斷是非常重要。因此，對於「收集應用程式使用者百分之 X 的所有遙測項目」或「收集應用程式要求百分之 X 的所有遙測」邏輯，取樣的效果更佳。對於與要求無關聯的遙測項目 (例如背景非同步處理)，改為「收集每個遙測類型百分之 X 的所有項目」。 

*取樣百分比會隨著時間變更嗎？*

 * 在現今的實作中，在應用程式啟動設定之後，您通常不會變更取樣百分比。雖然您可以控制取樣百分比執行階段，沒有任何方式可判斷哪一個取樣百分比最佳且會在節流邏輯發動之前或在達到每月的資料量配額之前收集「僅適當的資料量」。未來版本的 Application Insights SDK 會包含自適性取樣，該取樣將根據目前觀察到的遙測量和其他因素快速上下調整取樣百分比。 

*如何知道哪個取樣百分比最適合我的應用程式？*

* 今天您必須猜測。分析 AI 中您目前的遙測使用量、觀察與節流相關的資料卸除，並估計所收集之遙測的量。這三項輸入與所選定價層，可對您可能想要減少收集的遙測量提出建議。不過，遙測量模式中的偏移可能會使以最佳方式設定的取樣百分比無效 (例如您的使用者數目增加)。實作時，自適性取樣將根據觀察的遙測量，自動控制取樣百分比至其最佳的層級。

*如果將取樣百分比設定成太低會發生什麼事？*

* 當 Application Insights 嘗試補償減少資料量縮減的資料視覺效果時，過度低的取樣百分比 (過度積極取樣) 會降低近似值的精確度。此外，診斷經驗可能會有負面影響，因為可能會出取樣出某些不常失敗或緩慢的要求。

*如果將取樣百分比設定成太高會發生什麼事？*

* 設定太高的取樣百分比 (不夠積極) 將會導致收集的遙測量減少不足。您可能仍會遇到與節流相關的遙測資料遺失，而使用 Application Insights 的成本由於超額費可能高於您的計劃。

*我可以在何種平台上使用取樣？*

* 目前取樣可供任何網頁使用，並且供 .NET Web 應用程式的用戶端和伺服器端使用。

*可以對裝置應用程式 (Windows Phone、iOS、Android 或桌面應用程式) 使用取樣嗎？*

* 否，目前不支援對裝置應用程式進行取樣。 

<!---HONumber=Oct15_HO4-->