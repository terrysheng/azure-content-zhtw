<properties 
	pageTitle="從 Azure 端點移轉到 Application Insights 可用性測試" 
	description="我們已將您的傳統 Azure 端點監視測試移動到新的 Application Insights 可用性測試。我們會在 2016 年 4 月 4 日進行切換。"
	services="application-insights" 
    documentationCenter=""
	authors="soubhagyadash" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2016" 
	ms.author="awills"/>
 
# 從 Azure 端點監視移動到 Application Insights 可用性測試

您的 Azure Web 應用程式使用[端點監視](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/)嗎？ 我們將會在 2016 年 4 月 4 日將它們取代為功能更強大的新[可用性測試](app-insights-monitor-web-app-availability.md)。我們已建立新的測試，不過它們在 4 月 4 日以前將會處於停用狀態。

如果您願意的話，可以編輯新測試並自行切換。它們位在 [Azure 入口網站](https://portal.azure.com)中的 Default-ApplicationInsights-CentralUS 資源群組中。


## 什麼是可用性測試？

可用性測試是 Azure 的功能，它能從全世界最多 16 個位置將 HTTP 要求傳送給任何網站或服務 (單一 Ping 測試或 Visual Studio Web 測試)，藉此持續檢查它們是否啟動且正在執行。

在[傳統的 Azure 入口網站](https://manage.windowsazure.com)中，這些測試稱為端點監視。它們範圍限制比較嚴謹。新的可用性測試是一項重大的改良功能︰

* 每個 Application Insights 資源最多 10 個 Visual Studio Web 測試或 Ping 測試。 
* 可從全世界最多 16 個位置將測試要求傳送給 Web 應用程式。改善對測試成功準則的控制。 
* 可測試任何網站或服務，不受限於 Azure Web 應用程式。
* 測試重試：減少因暫時性網路問題而產生的誤判警示。 
* Webhook 可接收警示的 HTTP POST 通知。

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

深入了解[可用性測試](app-insights-monitor-web-app-availability.md)。

可用性測試隸屬於 [Visual Studio Application Insights](app-insights-overview.md)，這是一項適用於任何 Web 應用程式的可延伸分析服務。



## 我的端點測試該如何處置？

* 我們已將您的端點監視測試複製到新的 Application Insights 可用性測試。我們是在 2016 年 3 月 4 日複製的，所以我們不會複製您在該日期之後建立的任何端點測試。
* 新的可用性測試目前處於停用狀態，而舊有的端點測試仍在運作中。
* 警示規則並未移轉。我們設定的新測試一開始均採用預設規則︰
 * 當 5 分鐘之內有 1 個以上的位置報告失敗時觸發。
 * 傳送電子郵件給訂用帳戶管理員。

您可以在 [Azure 入口網站](https://portal.azure.com)的 Default-ApplicationInsights-CentralUS 資源群組中找到移轉後的測試。測試名稱附有 "Migrated-" 前置詞。

## 我需要做什麼事嗎？

* 如果您在 2016 年 3 月 4 日之後建立舊版測試 (或我們在移轉時不知道為什麼遺漏了您的測試)，新的可用性測試很[容易設定](app-insights-monitor-web-app-availability.md)。

### 選項 A︰什麼事都不做。交給我們處理。

我們將會在 **4 月 4 日**︰

* 停用舊的端點測試。
* 啟用移轉後的可用性測試。

### 選項 B︰您自行管理和/或啟用新測試。

* 請在新 [Azure 入口網站](https://portal.azure.com)中檢閱及編輯新的可用性測試。 
 * 檢閱觸發準則
 * 檢閱電子郵件收件者
* 啟用新測試
* [在傳統入口網站](https://manage.windowsazure.com)中刪除舊的端點測試。我們建議您採用這種做法，以避免警示重複及降低網站上的測試流量負載。如果您未刪除，我們將會在 2016 年 4 月 4 日予以刪除。


### 選項 C︰選擇推出

如果您不打算使用可用性測試，可以在 [Azure 入口網站](https://portal.azure.com)中刪除它們。通知電子郵件底部也有取消訂閱連結。

我們依然會在 4 月 4 日刪除舊的端點測試。

## 我該如何編輯新測試？

登入 [Azure 入口網站](https://portal.azure.com)並找出 'Migrated-' Web 測試︰

![選擇 [資源群組]、[Default-ApplicationInsights-CentralUS]，然後開啟 'migrated' 測試。](./media/app-insights-migrate-azure-endpoint-tests/20.png)

編輯和/或啟用測試︰

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)


## 為什麼會發生這種情形？

為了提供更好的服務。舊端點服務的規模太過狹窄。您只可以提供兩個 URL，並從 3 個地理位置針對 Azure VM 或 Web 應用程式進行簡單的 Ping 測試 。新測試可以從最多 16 個位置執行多重步驟 Web 測試，而且您可以為一個應用程式指定高達 10 個測試。您可以測試任何 URL，不一定要是 Azure 網站。

新測試的設定能與您測試中的 Web 應用程式或 VM 分開。

我們移轉測試的目的，在於確保您可以在使用新入口網站時繼續控制它們。

## 什麼是 Application Insights？

新的可用性測試隸屬於 [Visual Studio Application Insights](app-insights-overview.md)。以下是 [2 分鐘影片](http://go.microsoft.com/fwlink/?LinkID=733921)。

## 新測試需要付費嗎？

我們採用預設的免費方案將移轉後的測試設定於 Application Insights 資源中。您可以收集最多 5 百萬個資料點。如此將能輕易地涵蓋測試目前使用的資料量。

當然，如果您喜歡 Application Insights，進而建立更多可用性測試或採用更多效能監視和診斷功能，定會產生更多資料點。不過，這樣做的結果可能只是達到免費方案的配額。除非您選擇標準或進階方案，否則將不會收到帳單。

[深入了解 Application Insights 價格和配額監視](app-insights-pricing.md)。

## 哪些項目會移轉，哪些不會？

舊端點測試的保留項目︰

* 要測試的端點 URL。
* 送出要求的地理位置。
* 測試頻率維持 5 分鐘。
* 測試逾時維持 30 秒。 

不移轉的項目︰

* 警示觸發規則。當 1 個位置在 5 分鐘內報告失敗時，我們設定的規則將會觸發。
* 警示收件者。通知電子郵件將會傳送給訂用帳戶擁有者和共同擁有者。 

## 我該去哪裡尋找新測試？

如果您願意的話，現在可以編輯任何新測試。登入 [Azure 入口網站](https://portal.azure.com)，開啟 [資源群組]，然後選取 [Default-ApplicationInsights-CentralUS]。在該群組中，您會發現新的 Web 測試。[深入了解新的可用性測試](app-insights-monitor-web-app-availability.md)。

請注意，新的警示電子郵件將會從 App Insights 警示 (ai-noreply@microsoft.com) 這個位址傳送。

## 如果我不做任何事，會發生什麼事嗎？

我們將會套用選項 A。我們會啟用移轉後的測試，並會設定前文所述的預設警示規則。您必須加入任何自訂警示規則和前文所述的收件者。我們將會停用舊版的端點監視測試。

## 我可以在哪裡提供這項功能的意見反應嗎？ 

歡迎提供意見反應。請[傳送電子郵件給我們](mailto:vsai@microsoft.com)。

<!---HONumber=AcomDC_0316_2016-->