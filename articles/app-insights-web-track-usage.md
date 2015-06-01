<properties 
	pageTitle="追蹤 Web 應用程式中的使用情況" 
	description="記錄使用者活動。" 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>
 
# 追蹤 Web 應用程式的使用情況

了解 Web 應用程式的使用情況。設定流量分析，您將了解使用者查看的頁面、他們的回訪率，以及他們造訪您網站的頻率。只要加入幾個[自訂事件和度量][track]，您就可以詳細分析最受歡迎的功能、最常見的錯誤，並調整應用程式以滿足使用者的需求。

遙測是收集自用戶端和伺服器。用戶端資料是收集自所有新式網頁瀏覽器，而如果您的平台是 ASP.NET，則可以收集伺服器資料。(它不一定要在 Azure 上執行。) 

* [設定 Web 流量分析](#webclient)
* [流量分析](#usage)
* [單頁應用程式的自訂頁面計數](#spa)
* [檢查個別的頁面事件](#inspect)
* [使用自訂事件和度量來詳細追蹤](#custom)
* [影片](#video)

## <a name="webclient"></a> 設定 Web 用戶端分析

#### 在 Azure 中取得 Application Insights 資源

**如果您正在開發 ASP.NET 應用程式**，請[將 Application Insights 加入至 Web 專案][start] (如果尚未這麼做)。

**如果您的網站平台不是 ASP.NET：**請在 [Microsoft Azure](http://azure.com) 註冊，移至 [Preview 入口網站](https://portal.azure.com)，然後新增 Application Insights 資源。

![](./media/appinsights/appinsights-11newApp.png)

(稍後您可以使用 [瀏覽] 按鈕來返回。)



#### 將我們的指令碼加入至您的網頁

在快速入門中，取得網頁指令碼。

![](./media/appinsights/appinsights-06webcode.png)

在您想要追蹤之每一個頁面的 &lt;/head&gt; 標記之前插入指令碼。如果您的網站有主版頁面，您可以那裡放入指令碼。例如，在 ASP.NET MVC 專案中，可放在 View\Shared_Layout.cshtml 中

## <a name="usage"></a>流量分析

執行您的網站，稍微使用一下來產生遙測，並等候 1-2 分鐘。您可以在開發電腦上按 F5 執行它，或將它部署至伺服器。

在應用程式概觀分頁中，您可以看見以下流量磚：

![](./media/appinsights/appinsights-47usage.png)

還沒有資料嗎？請按一下頁面頂端的 **[重新整理]**。

* **每一瀏覽器的工作階段**

    「工作階段」是一段時間，開始於使用者開啟網站上的任何頁面時，結束於使用者未在 30 分鐘的逾時期限內傳送任何 Web 要求後。 

    逐一點選以將圖表放大顯示。

* **熱門頁面檢視**

    顯示過去 24 小時的總計數。

    按一下頁面檢視磚可取得詳細記錄。若要取得較長的歷程記錄，您可以變更報告的時間範圍。

![](./media/appinsights/appinsights-49usage.png)


按一下圖表以查看您可以顯示的其他度量，或加入新圖表並選取它要顯示的度量。

![](./media/appinsights/appinsights-63usermetrics.png)

> [AZURE.NOTE] 取消核取「所有」度量則可啟用所有度量。度量只能以某種組合來顯示。當您選取度量時，不相容的度量會停用。



## <a name="spa"></a> 單頁應用程式的自訂頁面計數

依預設，每當用戶端瀏覽器載入新頁面時，頁面計數便會發生。不過您也許會想要計算其他頁面檢視。例如，頁面可能將內容顯示在索引標籤中，而您想要在使用者切換索引標籤時計算一次頁面。或是，頁面中的 JavaScript 程式碼可能會在不變更瀏覽器 URL 的情況下載入新內容。 

請將與以下範例相似的 JavaScript 呼叫插入用戶端程式碼中的適當位置：

    appInsights.trackPageView(myPageName);

頁面名稱可能會含有與 URL 相同的字元，不過 "#" 或 "?" 之後的任何字元都將遭到忽略。


## <a name="inspect"></a> 檢查個別的頁面檢視事件

頁面檢視遙測資料一般是由 Application Insights 進行分析，而您只會看見以所有使用者為單位平均計算的累積報告。然而在偵錯時，您也可以查看個別的頁面檢視事件。

在 [Diagnostic Search] 分頁中，將 [篩選器] 設定為 [網頁檢視]。

![](./media/appinsights/appinsights-51searchpageviews.png)

選取任一事件以查看詳細資料。

> [AZURE.NOTE] 請注意，如果您使用[搜尋][diagnostic]，就必須比對整個單字："Abou" 和 "bout" 與 "About" 並不相符，不過 "Abou* " 則會相符。此外，您不能將萬用字元當做搜尋詞彙的開頭。例如，搜尋 "*bou" 將無法與 "About" 相符。 

> [深入了解診斷搜尋][diagnostic]

## <a name="custom"></a> 使用自訂事件和度量來詳細追蹤

想要了解使用者如何使用您的應用程式嗎？在您的用戶端和伺服器程式碼中插入呼叫，可以將您自己的遙測傳送至 Application Insights。例如，您可以了解雖然建立訂單但未完成的使用者數目、最常發生的驗證錯誤，或遊戲中的平均分數。

[深入了解自訂事件和度量 API][track]。

## <a name="video"></a> 影片：追蹤使用情況

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> 後續步驟

[使用事件和度量來追蹤使用情況][track]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=35.2-->

<!--HONumber=46--> 
