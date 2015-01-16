<properties title="Track custom usage events and metrics in your web app with Application Insights" pageTitle="使用 Application Insights 追蹤 Web 應用程式中的使用量事件和度量" description="插入幾行程式碼以了解使用者對於您的網站所進行的動作。" metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani"  />
 
<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-10-01" ms.author="awills" />

# 追蹤 Web 應用程式中自訂使用量事件和度量

*Application Insights 目前僅供預覽。*

在 Web 應用程式中插入幾行程式碼以了解使用者的動作。您可以追蹤事件、度量和頁面檢視。您會看到將所有使用者彙總而得到的資料圖形和表格。 

> [AZURE.NOTE] 完整的使用者體驗目前尚未完備。您可以將自訂事件和度量傳送至 Application Insights，然後在「[診斷搜尋][diagnostic]」中搜尋原始遙測。但您還看不到摘要統計圖表 - 即將推出。

<!-- Sample pic -->

* [用戶端和伺服器追蹤](#clientServer)
* [開始之前](#prep)
* [追蹤度量](#metrics)
* [追蹤事件](#events)
* [追蹤頁面檢視](#pageViews)
* [使用屬性來篩選、搜尋和分割您的資料](#properties)
* [結合度量和事件](#measurements)
* [設定預設屬性值](#defaults)
* [定義多個內容](#contexts)
* [開啟和關閉遙測](#disable)
* [後續步驟](#next)



## <a name="clientServer"></a> 用戶端和伺服器追蹤

您可以從應用程式的用戶端 (網頁)、伺服器端或兩者傳送遙測。

用戶端和伺服器 API 很相似。您可以從使用者的網頁瀏覽器和從您的 Web 伺服器傳送相同類型的遙測。差別在於您可以傳送的資料範圍。

* 如果您有許多使用中的網頁，且其中含有大量的 JavaScript，則在 Web 用戶端追蹤特別有用。例如，您可以監視使用者按一下特定按鈕的頻率，或遇到驗證錯誤的頻率。
* 在 Web 伺服器追蹤很適合用來監控商務度量和事件，例如客戶購物籃中的金額或放棄的訂單計數。

在典型的 ASP.NET Web 應用程式中，您在主版網頁中會以預設 JavaScript 來呼叫 trackPageView()，並在伺服器程式碼中加入一些呼叫來追蹤事件和度量。如果您的用戶端程式碼相當多，則您也可能會在用戶端加入一些呼叫來追蹤事件和度量。


## <a name="prep"></a>開始之前

如果您尚未完成這些動作：

* 從 ASP.NET Web 應用程式取得遙測：
    [將 Application Insights 加入至專案][greenbrown]
    在 Web 伺服器程式碼中，加入：
    (C#) `using Microsoft.ApplicationInsights;`
	(VB) `Imports Microsoft.ApplicationInsights`
* [設定 Web 流量分析][usage]。JavaScript 初始化程式碼應該加入您要撰寫監視程式碼的每個網頁中，或主版頁面中。 
    如果正常運作，您應該會在 [流量分析] 下的 [概觀] 分頁上看到資料。

當您在開發電腦中以偵錯模式執行您的應用程式時，在數秒內結果就會出現在 Application Insights 中。當您部署應用程式時，資料會花費較長的時間通過伺服器至用戶端之間的管線。

<!--
## <a name="metrics"></a> 追蹤度量

您不需要執行其他任何動作，即可取得頁面檢視等基本的使用量資料。但是，您可以撰寫幾行程式碼，以進一步了解使用者如何使用您的應用程式。

例如，如果您的應用程式是遊戲，您可能想要知道使用者達到的平均分數，並在您發行新版本之後，了解他們是否覺得更簡單或更困難。

若要追蹤度量 (也就是，得分之類的數值)，請應用程式中的適當位置插入如下的一行指令碼：

用戶端的 JavaScript

    appInsights.trackMetric("Alerts", notifications.Count);

伺服器上的 C#

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

伺服器上的 VB

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

測試應用程式，並使用它以執行 trackMetric() 呼叫。


然後在 Application Insights 中移至您的應用程式，並點選 [[度量][metrics]] 磚。選取度量來查看最初的結果。


圖形會顯示從所有使用者記錄的最近平均值。 


(順便提及：度量並非最適合用來診斷問題。如果有此需要，請查看[診斷記錄][diagnostic]。) -->


## <a name="events"></a>追蹤事件

事件可指出某件事情發生的頻率 (以使用者的平均值而言)。例如，假設您想要知道使用者完成您的遊戲的頻率。在遊戲結束的程式碼中，插入如下這一行：

用戶端的 JavaScript

    appInsights.trackEvent("EndOfGame");

伺服器上的 C#
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

伺服器上的 VB


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

如果您從用戶端和伺服器傳送遙測，請務必為事件指定不同的名稱。


## <a name="pageViews"></a>頁面檢視 (僅限用戶端)

根據預設，網頁標頭中的初始化指令碼會記錄頁面檢視，並以頁面的相對 URL 來命名事件。這些呼叫提供基本的頁面使用統計資料。 

![Usage analytics on main app blade](./media/appinsights/appinsights-05-usageTiles.png)

### 自訂頁面資料

如果想要的話，您可以修改呼叫來變更名稱，或插入額外的呼叫。比方說，如果單一頁面的 Web 應用程式中顯示多個索引標籤，您可能想要在使用者切換到不同的索引標籤時記錄頁面檢視。例如：

用戶端的 JavaScript：

    appInsights.trackPageView("tab1");

如果您在不同的 HTML 網頁內有數個索引標籤，您也可以指定 URL：

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>使用屬性來篩選、搜尋和分割您的資料

您可以將屬性和測量附加至事件、頁面檢視和其他遙測資料。 

**屬性**是可在使用情況報告中用來篩選遙測的字串值。例如，如果您的應用程式提供數個遊戲，則您可以將遊戲的名稱附加至每個事件，以了解哪些遊戲較受歡迎。

**度量單位**是可讓您在使用情況報告中取得統計資料的數值。


用戶端的 JavaScript

    appInsights.trackEvent("EndOfGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

伺服器上的 C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements);


伺服器上的 VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


以相同的方式將屬性附加至頁面檢視：

用戶端的 JavaScript

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] 切勿在屬性中記錄個人識別資訊。


## 計時的頁面檢視和事件

您可以將計時資料附加至事件和頁面檢視。請使用下列呼叫，而不是呼叫 trackEvent 或 trackPageView：

用戶端的 JavaScript

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

在開始和停止呼叫中，使用相同的字串做為第一個參數。

## <a name="defaults"></a>設定預設屬性值 (不是在 Web 用戶端)

您可以在 TelemetryContext 中設定預設值。它們會附加至從內容傳送而來的每個度量和事件。 
    

伺服器上的 C#

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
伺服器上的 VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
個別的遙測可以覆寫預設值。

如果您想要在預設屬性值群組之間切換，請設定多個內容。



## <a name="next"></a>後續步驟


[搜尋事件和記錄檔][diagnostic]

[疑難排解][qna]


[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




