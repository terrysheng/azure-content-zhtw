<properties 
	pageTitle="管理 Application Insights 的定價和配額" 
	description="選擇您需要的價格方案" 
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
	ms.date="11/18/2015" 
	ms.author="awills"/>

# 管理 Application Insights 的定價和配額

*Application Insights 目前僅供預覽。*

[Visual Studio Application Insights][start] 的[定價][pricing]是根據每個應用程式的資料量而定。我們提供實質的免費層，讓您在該層次中使用大部分的功能，但會有一些限制。

每項 Application Insights 資源的收費方式採個別服務計價，並計入您 Azure 訂用帳戶的帳單。

[請參閱定價機制][pricing]。

## 檢視 Application Insights 資源的配額和價格方案

您可以在應用程式資源的 [設定] 中開啟 [配額 + 定價] 刀鋒視窗。

![依序選擇 [設定]、[配額 + 定價]。](./media/app-insights-pricing/01-pricing.png)

您選擇的定價結構描述會影響：

* [每月配額](#monthly-quota) - 您每個月可以分析的遙測數量。
* [資料速率](#data-rate) - 從您的應用程式處理資料的最高速率。
* [保留](#data-retention) - 資料可保留在 Application Insights 入口網站供您檢視的時間長度。
* [連續匯出](#continuous-export) - 您是否可以將資料匯出到其他工具和服務。

這些限制會分別為每個 Application Insights 資源加以設定。

### 免費試用高階

當您第一次建立新的 Application Insights 資源時，會先從免費層開始。

您可以隨時切換成免費試用 30 天的高階版。如此便可體驗高階層的優點。30 天之後，如果您未明確選擇另一個層次，它會自動還原到您之前所在的任一層次。您可以在試用期間隨時選取想要的層次，但在 30 天的期限結束前，您仍可以免費試用。


## 每月配額

* 您的應用程式每月最多可以將指定的遙測資料量上傳至 Application Insights。目前免費定價層的配額是每個月 5 百萬個資料點，而其他訂價配置會提供多出許多的配額。如果您的配額用完了，可以購買更多的配額。如需實際數字，請參閱[定價機制][pricing]。 
* 配額取決於您所選擇的定價層。
* 配額的計算是從每個月第一天的午夜起 (UTC)。
* 資料點圖表會顯示您這個月已使用多少配額。
* 配額是以資料點來進行測量。 不論您的程式碼，或其中一個標準遙測模組是否有明確呼叫，單一資料點都是其中一種追蹤方法的呼叫。 
* 產生資料點的項目：
 * 會自動收集資料的 [SDK 模組](app-insights-configuration-with-applicationinsights-config.md)，例如用來回報要求或損毀，或是測量效能。
 * 您編寫的 [API](app-insights-api-custom-events-metrics.md) `Track...` 呼叫，例如 `TrackEvent` 或 `trackPageView`。
 * 您已設定的[可用性 Web 測試](app-insights-monitor-web-app-availability.md)。
* 當您偵錯時，可以在 Visual Studio 的輸出視窗中看到從您應用程式所傳送的資料點。只要開啟您瀏覽器的偵錯窗格 (通常是 F12) 中的 [網路] 索引標籤，就能看到用戶端事件。
* 工作階段資料不會計入配額。這包括使用者、工作階段、環境和裝置資料的計數。
* 如果您要透過檢查來計算資料點的總數，可以在各種不同位置找到資料點：
 * 您在[診斷搜尋](app-insights-diagnostic-search.md)中看到的每個項目，包括 HTTP 要求、例外狀況、記錄追蹤、頁面檢視、相依性事件，及自訂事件。
 * [度量](app-insights-metrics-explorer.md) (例如效能計數器) 的每個原始測量資料。(您在圖表看到的點通常是多個原始資料點的彙總)。
 * Web 可用性圖表上的每個點，也都是幾個資料點的彙總。
* 您也可以在偵錯時，在來源檢查個別的資料點：
 * 如果您在 Visual Studio 的偵錯模式中執行應用程式，資料點會記錄在輸出視窗中。 
 * 若要查看用戶端資料點，請開啟瀏覽器的偵錯窗格 (通常是 F12)，然後開啟 [網路] 索引標籤。


### 超額部分

如果您的應用程式傳送的配額超過每月上限，您可以：

* 為額外的資料付費：如需詳細資料，請參閱[定價機制][pricing]。您可以預先選擇此選項。免費定價層中沒有此選項。
* 升級定價層。
* 不做任何動作。系統仍會繼續記錄工作階段資料，但診斷搜尋或計量瀏覽器中將不會顯示其他資料。


### 正在傳送多少資料？

[價格] 刀鋒視窗底部的圖表中，會依資料點數量分組，顯示您應用程式的資料點使用量。(您也可以在 [計量瀏覽器] 中建立此圖表。)

![位於 [定價] 刀鋒視窗底部](./media/app-insights-pricing/03-allocation.png)

按一下圖表可取得詳細資料，或在圖表上拖曳並按 (+) ，以取得某個時間範圍內的詳細資料。


## 資料速率

除了每月配額，還有資料速率的節流限制。對於免費[價格層][pricing]，此限制是平均每 5 分鐘 200 個資料點/秒；對於付費層，則為平均每 1 分鐘 500 個資料點/秒。

有三個個別計數的貯體：

* [TrackTrace 呼叫](app-insights-api-custom-events-metrics.md#track-trace)和[擷取的記錄檔](app-insights-asp-net-trace-logs.md)
* [例外狀況](app-insights-api-custom-events-metrics.md#track-exception)，限制為 50 點/秒。
* 所有其他遙測 (頁面檢視、工作階段、要求、相依性、度量、自訂事件、Web 測試結果)。





*如果應用程式超過每秒速率，會發生什麼事？*

* 系統會每分鐘評估應用程式傳送的資料量。如果每秒速率超過每分鐘平均值，伺服器會拒絕部分要求。接著，部分版本的 SDK 會嘗試每數分鐘重新傳送突波；其他像是 JavaScript SDK 則會放棄遭到拒絕的資料。

當節流發生時，您會看到警告您這種情況已發生的通知。

*如何知道應用程式正在傳送多少資料點？*

* 開啟 [設定/配額與定價] 查看 [資料數量] 圖表。
* 或在 [計量瀏覽器] 中，新增新的圖表，然後選取 [資料點數量] 做為其計量。切換群組，並依 [**資料類型**] 分組。

*如何減少我的應用程式傳送的資料量？*

* 使用[取樣](app-insights-sampling.md)。這項技術可減少資料率而不會曲解您的計量，且不會中斷在 [搜尋] 中於相關項目之間瀏覽的能力。自 ASP.NET SDK 2.0.0-beta3 起，系統預設會啟用自適性取樣。
* [關閉您不需要的遙測收集器](app-insights-configuration-with-applicationinsights-config.md)。


### 降低資料速率的秘訣

如果您遇到節流限制，以下是您可以執行的一些事項：

* 使用[取樣](app-insights-sampling.md)。這項技術可減少資料率而不會曲解您的計量，且不會中斷在 [搜尋] 中於相關項目之間瀏覽的能力。
* 藉由[編輯 ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 來關閉您不需要的集合模組。例如，您可能會決定效能計數器或相依性資料是不必要的。
* 預先彙總度量。如果您在應用程式中呼叫 TrackMetric，您可以使用接受批次測量之平均及標準差計算的多載來減少流量。您也可以使用[預先彙總套件](https://www.myget.org/gallery/applicationinsights-sdk-labs)。 


### 名稱限制

1.	您的應用程式具有最多 200 個唯一計量名稱和 200 個唯一屬性名稱。度量包括透過 TrackMetric 傳送的資料，以及其他資料類型 (例如事件) 的度量資料 。每個檢測金鑰的[計量和屬性名稱][api]是全域的，不只限於資料類型。
2.	只有在每個屬性具有少於 100 個唯一值時，[屬性][apiproperties]才能用於篩選和分組依據。唯一值超過 100 之後，屬性仍可用於搜尋與篩選，但無法用於篩選器。
3.	標準屬性，例如要求名稱和網頁 URL 會限制為每週 1000 個唯一值。超過 1000 個唯一值之後，額外值都會標示為「其他值」。 原始值仍然可以用於全文檢索搜尋和篩選。

## 資料保留

價格層會決定資料保存在入口網站的時間長度，並因此決定您可以設定的時間範圍。


* 未處理的資料點 (即您可以在 [診斷搜尋] 中檢查的執行個體)：7 至 30 天。
* 彙總的資料 (即您在計量瀏覽器中看到的計數、平均和其他統計資料) 在 1 分鐘的資料粒度中保存 30 天，而 1 小時或 1 天 (視類型而定) 則保存至少 13 個月。




## 檢視 Azure 訂用帳戶的帳單

Application Insights 費用會加到您的 Azure 帳單中。您可以在 Azure 入口網站中的 [帳務] 區段中，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。

![選擇側邊功能表中的 [帳務]。](./media/app-insights-pricing/02-billing.png)

## 限制摘要

[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=AcomDC_1223_2015-->