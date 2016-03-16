<properties 
	pageTitle="在 Application Insights 中設定警示" 
	description="取得有關損毀、例外狀況、度量變更的電子郵件。" 
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
	ms.date="03/01/2016" 
	ms.author="awills"/>
 
# 在 Application Insights 中設定警示

[Visual Studio Application Insights][start] 會警示您應用程式中效能或使用量度量的變更。

Application Insights 會在[多種平台][platforms]上監視即時應用程式，協助您診斷效能問題，以及了解使用模式。

有兩種警示：
 
* **Web 測試**會在您的網站無法在網際網路上使用或回應速度很慢時通知您。[深入了解][availability]。
* **度量警示**會在任何度量超出某些期間的臨界值 (例如失敗計數、記憶體或頁面檢視) 的時候通知您。 

有 [Web 測試的個別頁面][availability]，因此我們將著重於度量警示。

> [AZURE.NOTE] 此外，您可能會收到來自[主動式偵測](app-insights-proactive-detection.md)的電子郵件，警告您關於您的應用程式效能的異常模式。不同於警示，這些通知會執行而不需要您進行設定。它們的目標是調整您的應用程式效能，而非引發關於立即問題的警示。

## 度量警示

如果您尚未設定應用程式的 Application Insights，請[立即進行][start]。

若要在度量超出臨界值時取得電子郵件，請從 [計量瀏覽器] 開始，或是從概觀刀鋒視窗上的 [警示] 規則磚開始。

![在 [警示規則] 刀鋒視窗中，按一下 [新增警示]。將您的應用程式設定為要測量的資源，提供警示的名稱，然後選擇度量。](./media/app-insights-alerts/01-set-metric.png)

* 設定其他屬性之前的資源。如果您想要設定效能或使用度量的相關警示，請**選擇 "(元件)" 資源**。
* 請小心注意系統要求您輸入臨界值時所使用的單位。
* 您提供的警示名稱必須為資源群組 (不只是您的應用程式) 中的唯一名稱。
* 如果您勾選 [電子郵件擁有者] 方塊，系統會透過電子郵件，將警示傳給每個可以存取該資源的人員。
* 如果您指定 [其他電子郵件]，系統會將警示傳送給這些人員或群組 (無論您是否核取 [電子郵件擁有者] 方塊)。 
* 如果您已設定回應通知的 Web 應用程式，請設定 [Webhook 位址](../azure-portal/insights-webhooks-alerts.md)，系統會在警示啟動 (即觸發) 和解決時加以呼叫。
* 您可以停用或啟用警示：請參閱位於刀鋒視窗頂端的按鈕。

*我沒看到 [新增警示] 按鈕。* - 您使用的是組織帳戶嗎？ 如果您有這個應用程式資源的擁有者或參與者存取權，您可以設定警示。查看 [設定] -> [使用者]。[深入了解存取控制][roles]。

## 查看您的警示

當警示在非作用中與作用中之間變更狀態時，您會收到電子郵件。

每個警示目前的狀態都顯示在警示規則刀鋒視窗中。

警示下拉式清單中有最近活動的摘要：

![](./media/app-insights-alerts/010-alert-drop.png)

狀態變更的歷程記錄位於作業事件記錄檔中：

![在 [概觀] 刀鋒視窗靠近底部的位置，按一下 [過去一週的事件]](./media/app-insights-alerts/09-alerts.png)

*這些「事件」是否與遙測事件或自訂事件相關？*

* 否。這些作業事件只是於此應用程式資源發生之事件的記錄。 


## 警示的運作方式

* 警示有三種狀態：「永遠不啟動」、「已啟動」和「已解決」。「已啟動」表示您指定的條件在上次評估時為 true。

* 警示狀態變更時，會產生通知。(如果警示條件在建立警示時已為 true，您可能在條件變為 false 之前都不會得到通知。)

* 如果您已勾選電子郵件方塊或提供電子郵件地址，則每個通知都會產生一封電子郵件。您也可以於 [通知] 查看下拉式清單。

* 每次度量抵達時都會評估警示，除此之外則無。

* 評估會彙總前一個期間的度量，然後將其與臨界值進行比較，以決定新的狀態。

* 您選擇的期間會指定彙總度量的間隔。它並不會影響評估警示的頻率：這會根據度量抵達的頻率而定。

* 如果經過一段時間後沒有特定度量的資料到達，此間距會在警示評估上，以及在度量總管的圖表上有不同的效果。在度量總管中，如果沒看到資料的時間超過圖表的取樣間隔時間，則圖表會顯示 0 值。但是以相同度量為基礎的警示將不會重新接受評估，而且警示的狀態會維持不變。

    當資料終於抵達時，圖表會跳回非零的值。警示的評估作業會依您指定的期間，並根據可用的資料來進行。如果新的資料點是期間內唯一可用的資料點，彙總將會據此進行。

* 即使您設定的期間較長，警示也可能會在警示和良好狀態之間經常變動。如果度量值徘徊在臨界值附近，就會發生這種情形。臨界值中沒有任何磁滯：轉換為警示時的值會與轉換為良好時的值相同。



## 可用性警示

您可以設定 Web 測試，從世界各個角落測試任何網站。[深入了解][availability]。

## 哪些是好的設定警示？

這取決於您的應用程式。開始時，最好不要設定太多度量。花點時間查看您的應用程式執行時的度量圖表，以了解正常運作時的情形。這會協助您找出改善其效能的方式。然後設定警示，在度量偏離正常區域時通知您。

熱門的警示包括：

* 如果您的應用程式是會顯示在公開的網際網路上的網站或 Web 服務，則 [Web 測試][availability]很重要。它們會告訴您您的網站是否當機，或者回應緩慢 - 即使這是電信業者的問題，而不是您的應用程式的問題。但是它們是綜合測試，因此不會測量使用者的實際經驗。
* [瀏覽器度量][client]，適合用於 Web 應用程式，尤其是瀏覽器**頁面載入時間**。如果您的網頁有很多指令碼，就必須留意**瀏覽器例外狀況**。若要取得這些度量和警示，您必須設定[網頁監視][client]。
* Web 應用程式伺服器端的**伺服器回應時間**和**失敗要求**。以及設定警示，注意這些度量，以查看高要求率時的差異是否不成比例：可能表示您的應用程式資源不足。
* **伺服器例外狀況** - 若要查看它們，您只需要進行一些[額外設定](app-insights-asp-net-exceptions.md)。

## 自動化

* [使用 PowerShell 自動設定警示](app-insights-powershell-alerts.md)
* [使用 Webhook 自動設定警示](../azure-portal/insights-webhooks-alerts.md)

## 另請參閱

* [可用性 Web 測試](app-insights-monitor-web-app-availability.md)
* [自動化設定警示](app-insights-powershell-alerts.md)
* [主動式偵測](app-insights-proactive-detection.md) 



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0302_2016-------->