<properties 
	pageTitle="使用 Application Insights 監視 Windows 市集和 Phone 應用程式中的使用量" 
	description="使用 Application Insights 分析 Windows 裝置應用程式的使用情况。" 
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
	ms.date="02/28/2015" 
	ms.author="awills"/>

#  使用 Application Insights 監視 Windows 市集和 Windows Phone 應用程式中的使用量

*Application Insights 目前僅供預覽。*

了解您有多少使用者，以及他們他們在查看您應用程式中的哪些頁面。Application Insights 提供現成的這些資料給您。只要在您的應用程式中插入幾行程式碼，就能深入了解使用者在您應用程式中行進的路徑，以及他們因此而達成什麼目的。

[將 Application Insights 加入至應用程式][windows]，並重新發行 (如果尚未這樣做)。


## <a name="usage"></a>追蹤使用量

從 [概觀] 時間表，按一下 [使用者] 和 [工作階段] 圖表，查看更詳細的分析。


![](./media/appinsights/appinsights-d018-oview.png)

* **使用者**依匿名方式追蹤，所以同一使用者在不同裝置上以兩次計算。
* **工作階段**於應用程式暫停時計算 (排除短暫間隔，以免將意外的擱置計算在內)。

#### 分割

分割圖表，依各種準則取得分解圖。例如，若要查看多少使用者在使用您應用程式的每個版本，請開啟 [使用者] 圖表，再依 [應用程式版本] 分割：

![在使用者圖表上，開啟 [分割]，選擇 [應用程式版本]](./media/appinsights/appinsights-d25-usage.png)


#### 頁面檢視

若要探索使用者在您應用程式中的行進路徑，請在程式碼中插入[頁面檢視遙測][api]：

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

查看頁面檢視圖表上的結果，並開啟詳細資料：

![](./media/appinsights/appinsights-d27-pages.png)

按一下任何頁面，查看特定發生次數的詳細資料。

#### 自訂事件

只要插入程式碼從您的應用程式傳送自訂事件，就能追蹤使用者的行為，以及特定功能和情節的使用量。

例如：

    telemetry.TrackEvent("GameOver");

資料會出現在 [自訂事件] 方格中。您可以在 [計量瀏覽器] 中查看彙總檢視，也可以按一下任何事件查看特定的發生次數。

![](./media/appinsights/appinsights-d28-events.png)


您可以將字串和數值屬性加入至任何事件。


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


按一下任何發生次數，查看詳細屬性，包括您已定義的屬性。


![](./media/appinsights/appinsights-d29-eventProps.png)

如需自訂事件的詳細資訊，請參閱 [API 參考][api]。



## <a name="debug"></a>偵測與發行模式

#### 偵錯

如果您在偵測模式下建置，則事件一旦產生，就會立即傳送。如果網際網路連線中斷，而且您在恢復連線之前結束應用程式，則會捨棄離線遙測。

#### 發行

如果您在發行配置中建置，則事件會先儲存在裝置中，等到應用程式恢復時才傳送。第一次使用應用程式時也會傳送資料。如果剛啟動時沒有網際網路連線，先前的遙測及目前生命週期的遙測會先儲存，等到下次恢復時才傳送。

## <a name="next"></a>接續步驟

[了解您的使用者][knowUsers]

[深入了解計量瀏覽器][metrics]


[疑難排解][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


<!--HONumber=54-->