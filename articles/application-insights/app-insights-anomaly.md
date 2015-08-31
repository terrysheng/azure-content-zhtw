<properties 
	pageTitle="Application Insights：主動式異常偵測" 
	description="Application Insights 會深入分析您的 App 遙測，並且警告您有潛在的問題。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="awills"/>

#  Application Insights：主動式異常偵測

*Application Insights 目前僅供預覽。*


Application Insights 會深入分析您的 App 遙測，並且可以警告您有潛在的效能問題。您閱讀本文章可能是因為您收到我們透過電子郵件傳送的其中一個異常警示。

## 關於異常警示

* *為什麼會收到這個警示？*
 * Application Insights 會使用模式辨識規則定期分析您的資料，尋找您的應用程式中可能表示效能有問題的異常狀況。
* *收到通知代表一定是有問題嗎？*
 * 不一定。這只是一個建議，您可以仔細探究其中的問題。 
* *我該怎麼辦？*
 * [查看呈現的資料](#responding-to-an-alert)並考量這是否代表有問題。如果沒有，就沒關係。
* *你們會查看我的資料嗎？*
 * 不會。服務完全是自動的。只有您會收到通知。您的資料是[私密的](app-insights-data-retention-privacy.md)。


## 偵測程序

* *偵測哪些種類的異常？*
 * 由您自行檢查會很耗時的模式。例如，某種位置、時間與平台組合的效能不佳。
* *我可以建立自己的異常偵測規則嗎？*
 * 無法針對這類深入分析。(但是您可以[設定警示](app-insights-alerts.md)，在度量超出閾值時通知您)。
* *執行分析的頻率為何？*
 * 我們不會在 App 資源沒有取得夠多遙測時執行分析。您也不會收到有關偵錯工作階段的警告。


## 回應警示

您可以從電子郵件或從異常清單開啟異常報告。

![](./media/app-insights-anomaly/02.png)

注意事項：

* 說明
* 影響的敘述，告訴您影響使用者多久時間或頻率。

按一下圖表可開啟內含詳細資料的刀鋒視窗。

修改時間範圍和篩選器可探索遙測。




## 通知電子郵件

* *我必須訂閱這項服務才能收到通知嗎？*
 * 不用。我們的 bot 會定期調查所有 Application Insights 使用者的資料，如果偵測到問題，就會傳送通知。
* *我是否可以取消訂閱或改為傳送通知給我的同事？*
 * 按一下警示或電子郵件中的連結。開啟異常設定。
 
    ![](./media/app-insights-anomaly/01.png)

    這些通知目前是傳送給[對 Application Insights 資源擁有寫入存取權](app-insights-resources-roles-access-control.md)的人員。
* *我不想要被這些訊息淹沒。*
 * 訊息一天最多只有三封。您不會重複收到任何訊息。
* *如果我沒有做任何動作，會收到提醒嗎？*
 * 不會，每個問題您只會收到一次訊息。
* *我弄丟了電子郵件。在入口網站中哪裡可以找到通知？*
 * 在您 app 的 Application Insights 概觀中，按一下 [異常] 磚。 






 

<!---HONumber=August15_HO8-->