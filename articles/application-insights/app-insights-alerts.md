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
	ms.date="11/23/2015" 
	ms.author="awills"/>
 
# 在 Application Insights 中設定警示

[Visual Studio Application Insights][start] 會警示您應用程式中效能或使用量度量的變更。

Application Insights 會在[多種平台][platforms]上監視即時應用程式，協助您診斷效能問題，以及了解使用模式。

有兩種警示：
 
* **Web 測試**會在您的網站無法在網際網路上使用或回應速度很慢時通知您。[深入了解][availability]。
* **度量警示**會在任何度量超出某些期間的臨界值 (例如失敗計數、記憶體或頁面檢視) 的時候通知您。 

有 [Web 測試的個別頁面][availability]，因此我們將著重於度量警示。

## 度量警示

如果您尚未設定應用程式的 Application Insights，請[立即進行][start]。

若要在度量超出臨界值時取得電子郵件，請從 [計量瀏覽器] 開始，或是從概觀刀鋒視窗上的 [警示] 規則磚開始。

![在 [警示規則] 刀鋒視窗中，按一下 [新增警示]。將您的應用程式設定為要測量的資源，提供警示的名稱，然後選擇度量。](./media/app-insights-alerts/01-set-metric.png)

設定其他屬性之前的資源。如果您想要設定效能或使用度量的相關警示，請**選擇 "(元件)" 資源**。

請小心注意系統要求您輸入臨界值時所使用的單位。

您提供的警示名稱必須為資源群組 (不只是您的應用程式) 中的唯一名稱。

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

* 警示有兩種狀態：「警示」和「良好」。 

* 警示狀態變更時，系統會傳送電子郵件。

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

## 使用 PowerShell 設定警示

針對大部分用途，僅須採取手動方式設定警示即可。但若您想要自動建立度量警示，則可使用 PowerShell。

#### 單次設定

若您未曾將 PowerShell 與 Azure 訂用帳戶搭配使用：

在您要執行指令碼的電腦上，安裝 Azure Powershell 模組。

 * 安裝 [Microsoft Web Platform Installer (v5 或更高版本)](http://www.microsoft.com/web/downloads/platform.aspx)。
 * 使用該程式安裝 Microsoft Azure PowerShell


#### 連接到 Azure

啟動 Azure PowerShell 並[連接至您的訂用帳戶](powershell-install-configure.md)：

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


#### 取得警示

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### Add alert


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



#### 範例 1

若伺服器針對 HTTP 要求的回應時間 (平均超過 5 分鐘) 慢於 1 秒，則傳送電子郵件給我。我的 Application Insights 資源稱為 IceCreamWebApp，其位於 Fabrikam 資源群組。我是 Azure 訂用帳戶的擁有者。

GUID 是該訂用帳戶的 ID (而非應用程式的檢測金鑰)。

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

#### 範例 2

我已有應用程式，並在其中使用 [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) 來報告名為 "salesPerHour" 的度量。 若 salesPerHour 超過 24 小時皆低於平均值 100，則傳送電子郵件給我的同事。

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

您亦可針對使用其他追蹤呼叫之[測量參數](app-insights-api-custom-events-metrics.md#properties) (例如 TrackEvent 或 trackPageView) 報告的度量，使用相同的規則。

#### 度量名稱

度量名稱 | 畫面名稱 | 說明
---|---|---
`basicExceptionBrowser.count`|瀏覽器例外狀況|在瀏覽器中擲回的未攔截例外狀況計數。
`basicExceptionServer.count`|伺服器例外狀況|應用程式擲回的未處理例外狀況計數
`clientPerformance.clientProcess.value`|用戶端處理時間|從接收上個文件位元組直至載入 DOM 的經過時間。系統可能仍在處理非同步要求。
`clientPerformance.networkConnection.value`|頁面載入網路連線時間| 瀏覽器連線至網路所需的時間。可為 0 (若已快取)。
`clientPerformance.receiveRequest.value`|接收回應時間| 瀏覽器傳送要求直至開始接收回應的經過時間。
`clientPerformance.sendRequest.value`|傳送要求時間| 瀏覽器傳送要求所耗費的時間。
`clientPerformance.total.value`|瀏覽器頁面載入時間|從使用者要求直至載入 DOM、樣式表、指令碼和影像的經過時間。
`performanceCounter.available_bytes.value`|可用的記憶體|針對處理程序或系統用途的立即可用實體記憶體。
`performanceCounter.io_data_bytes_per_sec.value`|處理程序 IO 速率|每秒讀取與寫入檔案、 網路和裝置的總位元組數。
`performanceCounter.number_of_exceps_thrown_per_sec`|例外狀況比率|每秒擲回的例外狀況。
`performanceCounter.percentage_processor_time.value`|處理程序 CPU|處理器針對應用程式處理程序執行指示，所用之全部處理程序執行緒的經過時間百分比。
`performanceCounter.percentage_processor_total.value`|處理器時間|處理器針對非閒置執行緒所耗費時間的百分比。
`performanceCounter.process_private_bytes.value`|處理程序私人位元組|以獨佔方式指派至監視應用程式處理程序的記憶體。
`performanceCounter.request_execution_time.value`|ASP.NET 要求執行時間|最近要求的執行時間。
`performanceCounter.requests_in_application_queue.value`|執行佇列中的 ASP.NET 要求|應用程式要求佇列的長度。
`performanceCounter.requests_per_sec`|ASP.NET 要求率|每秒從 ASP.NET 發出所有應用程式要求的速率。
`remoteDependencyFailed.durationMetric.count`|相依性失敗|伺服器應用程式針對外部資源的呼叫失敗計數。
`request.duration`|伺服器回應時間|從接收 HTTP 要求直至完成傳送回應的經過時間。
`request.rate`|要求率|每秒發出所有應用程式要求的速率。
`requestFailed.count`|失敗的要求|產生回應碼的 HTTP 要求計數 >= 400 
`view.count`|頁面檢視|網頁的用戶端使用者要求計數。系統會篩選掉綜合流量。
{您的自訂度量名稱}|{您的度量名稱}|由 [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) 或[追蹤呼叫之測量參數](app-insights-api-custom-events-metrics.md#properties)所報告的度量值。

此度量由不同遙測模組所傳送：

度量群組 | 收集器模組
---|---
basicExceptionBrowser、<br/>clientPerformance、<br/>view | [瀏覽器 JavaScript](app-insights-javascript.md)
performanceCounter | [效能](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [相依性](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request、<br/>requestFailed|[伺服器要求](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)


## 另請參閱


* [從範本建立 Application Insights 和 Web 測試資源](app-insights-powershell.md)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1125_2015-->