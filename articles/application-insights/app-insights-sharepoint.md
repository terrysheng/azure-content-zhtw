<properties 
	pageTitle="使用 Application Insights 監視 SharePoint 網站" 
	description="開始使用新的檢測金鑰監視新的應用程式" 
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
	ms.date="03/24/2016" 
	ms.author="awills"/>

# 使用 Application Insights 監視 SharePoint 網站


[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights 會監視應用程式的可用性、效能和使用情形。您將在這裡深入了解如何針對 SharePoint 網站進行設定。


## 建立 Application Insights 資源


在 [Azure 入口網站](https://portal.azure.com) 中，建立新的 Application Insights 資源。選擇 ASP.NET 做為應用程式類型。

![按一下 [屬性]，選取金鑰，然後按下 CTRL+C](./media/app-insights-sharepoint/01-new.png)


開啟的刀鋒視窗是您要查看您的應用程式效能和使用量資料的位置。若要在下次登入 Azure 時回到此位置，您應該會在開始畫面上發現它的磚。或者按一下 [瀏覽] 以尋找它。
    


## 將我們的指令碼加入至您的網頁

在快速入門中，取得網頁指令碼：

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

在您想要追蹤之每一個頁面的 &lt;/head&gt; 標記之前插入指令碼。如果您的網站有主版頁面，您可以那裡放入指令碼。例如，在 ASP.NET MVC 專案中，可放在 View\\Shared\_Layout.cshtml 中

指令碼包含檢測金鑰，會將遙測導向您的 Application Insights 資源。

### 將程式碼加入至網站頁面

#### 在主要頁面上

如果您可以編輯網站的主要頁面，將會提供網站中每一頁面的監視。

簽出主要頁面，並且使用 SharePoint Designer 或任何其他編輯器來編輯。

![](./media/app-insights-sharepoint/03-master.png)


在 </head> 標記之前加入程式碼。


![](./media/app-insights-sharepoint/04-code.png)

#### 或在個別的頁面上

若要監視一組有限的頁面，將指令碼個別加入至每個頁面。

插入網頁組件並在其中嵌入程式碼片段。


![](./media/app-insights-sharepoint/05-page.png)


## 檢視應用程式相關的資料

重新部署您的應用程式。

返回 [Azure 入口網站](https://portal.azure.com)中的應用程式刀鋒視窗。

前幾個事件將出現在搜尋中。

![](./media/app-insights-sharepoint/09-search.png)

如果您預期有更多資料，請在幾秒之後按一下 [重新整理]。

從 [概觀] 刀鋒視窗按一下 [使用情況分析] 以查看使用者、工作階段和頁面檢視次數的圖表：

![](./media/app-insights-sharepoint/06-usage.png)

按一下任何圖表以查看更多詳細資料 - 例如頁面檢視次數：

![](./media/app-insights-sharepoint/07-pages.png)

或使用者：


![](./media/app-insights-sharepoint/08-users.png)


## 擷取使用者識別碼


標準網頁程式碼片段不會從 SharePoint 擷取使用者識別碼，但只要稍做修改就能執行此作業。


1. 從 Application Insights 中的 [Essentials] 下拉式清單複製您應用程式的檢測金鑰。 


    ![](./media/app-insights-sharepoint/02-props.png)

2. 使用檢測金鑰替換下列程式碼片段中的 'XXXX'。
3. 在您的 SharePoint 應用程式中內嵌指令碼，而非您從入口網站取得的程式碼片段。



```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 
  
<script type="text/javascript"> 
var personProperties; 
  
// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 
  
function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 
     
    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 
    
    personProperties = peopleManager.getMyProperties(); 
  
    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 
     
// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 
  
// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## 後續步驟

* 可監視網站可用性的 [Web 測試](app-insights-monitor-web-app-availability.md)。

* 其他應用程式類型的 [Application Insights](app-insights-overview.md)。



<!--Link references-->


 

<!---HONumber=AcomDC_0330_2016-->