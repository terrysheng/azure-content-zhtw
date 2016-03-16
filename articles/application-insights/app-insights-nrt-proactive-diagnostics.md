<properties 
	pageTitle="Application Insights 中的「接近即時主動診斷」" 
	description="NRT 主動診斷會自動在伺服器回應時間出現異常行為時通知您。不需要設定。" 
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
	ms.date="02/16/2016" 
	ms.author="awills"/>
 
# 接近即時主動診斷

*這項功能為初期試用版。*

*請傳送意見反應至：*[ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)

如果您的 Web 應用程式要求的失敗率明顯增加，[Visual Studio Application Insights](app-insights-overview.md) 會以接近即時通知的方式自動通知您。為了協助您分級並診斷問題，通知中會提供失敗要求的特性分析與相關遙測。其中也有 Application Insights 入口網站的連結，以供進一步診斷。不需要設定該功能，因為它是使用機器學習演算法來預測一般失敗率基準。它需要有特定的最小流量才能運作。

以下是警示的範例：

![顯示失敗之相關叢集分析的智慧警示範例](./media/app-insights-nrt-proactive-diagnostics/010.png)

若要使用失敗警示，您必須[為您的 ASP.NET 設定 Application Insights](app-insights-asp-net.md) 或[為您的 Java Web 應用程式設定 Application Insights](app-insights-java-get-started.md)。

## 運作方式

接近即時主動診斷會監視從應用程式收到的遙測，特別是要求失敗率的遙測。此度量通常會指出傳回的回應碼等於或大於 400 (除非您已撰寫自訂程式碼來[篩選](app-insights-api-filtering-sampling.md#filtering)或產生您自己的 [TrackRequest](app-insights-api-custom-events-metrics.md#track-request) 呼叫) 的 HTTP 要求數。

服務會根據此度量先前的行為預測應預期的範圍值。如果實際值明顯較高，系統便會產生警示。

當發出警示時，服務會對要求的多個維度執行叢集分析，以嘗試識別描述失敗特徵之值的模式。在上述範例中，分析已發現大部分的失敗與特定的要求名稱有關，但也發現失敗與主機或服務執行個體無關。

分析器接著發現與已識別之叢集中的要求相關聯的例外狀況及相依性失敗，同時也包含與那些要求相關的任何追蹤記錄檔的範例。

產生的分析報告會以電子郵件寄送給您，除非您已設定不接收該報告。

如同[您手動設定的警示](app-insights-alerts.md)，您可以檢查警示的狀態，並在 Application Insights 資源的 [警示] 刀鋒視窗中進行設定。但不同於其他警示，您不需要設定「調適性失敗警示」。若有需要，您可以將它停用或變更其目標電子郵件地址。

## 如果您收到警示

發出警示表示系統偵測到異常的要求失敗率。原因可能是您的應用程式或其環境有問題。或許您剛上傳的版本運作不順暢，或者資料庫或外部資源等等相依性發生故障。

您可以根據要求的百分比及受影響的使用者數目來決定此問題的緊急程度。

在多數情況下，您可以根據要求名稱、例外狀況、相依性及其他提供的資料快速診斷問題。

但如果您需要進一步調查，每個區段中的連結將直接連結到已針對相關要求、例外狀況、相依性或追蹤篩選的[搜尋頁面](app-insights-diagnostic-search.md)。或者，您可以開啟 [Azure 入口網站](https://portal.azure.com)，瀏覽至您應用程式的 Application Insights 資源，並開啟 [失敗] 刀鋒視窗。

## 檢閱最近的警示

若要在入口網站中檢閱警示，請依序開啟 [設定]、[操作事件]。

![警示摘要](./media/app-insights-nrt-proactive-diagnostics/040.png)

點選任何一個警示來查看其完整詳細資料。


## 設定警示 

> **尚未提供設定 UX 功能。**
> 
> 相反地，請傳送電子郵件至 [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)。
>
> 其運作方式：

開啟 [警示] 頁面。「調適性失敗警示」已包含於任何您已手動設定的警示中，且您可以查看其目前是否處於警示狀態。

![在 [概觀] 頁面中，按一下 [警示] 磚。或在任何 [度量] 頁面上，按一下 [警示] 按鈕。](./media/app-insights-nrt-proactive-diagnostics/020.png)

按一下警示來進行設定。

![組態](./media/app-insights-nrt-proactive-diagnostics/030.png)

請注意，您可以停用「調適性失敗警示」，但無法將它刪除 (或建立另一個)。


## 不同之處在於...

NRT 主動診斷與其他相似但不同的 Application Insights 功能互補。

* 您可以設定[度量警示](app-insights-alerts.md)，且可以檢視各種度量，例如 CPU 使用量、要求率、頁面載入時間等等。您可以使用它們來向自己發出警告，例如，如果您需要增加更多資源時。相較之下，NRT 主動診斷只涵蓋小範圍的重要度量 (目前僅包含要求失敗率)，其作用是一旦 Web 應用程式的要求失敗率增加幅度明顯高於其一般行為時，可以以接近即時通知的方式通知您。
* [主動偵測](app-insights-proactive-detection.md)也會使用電腦智慧在您的度量中探索不尋常的模式，且您不需進行設定。但不同於 NRT 主動診斷，主動偵測的目的是要尋找各種使用方式中可能無法正常作用的片段，例如特定頁面在特定的瀏覽器上。此分析會每日執行，且如果發現任何結果，它可能不像警示那麼緊急。相較之下，NRT 主動診斷的分析是對傳入的遙測持續執行，且如果伺服器失敗率超出預期，您會在幾分鐘內收到通知。


## 懇請給予意見反應

*這項功能為初期試用版。我們非常期待收到您的意見反應。請將意見反應傳送至：*[ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)，*或按一下警示訊息中的 [意見反應] 連結。*

<!---HONumber=AcomDC_0218_2016-->