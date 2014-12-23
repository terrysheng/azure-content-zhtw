<properties title="Using Mobile Services with Cordova Projects" pageTitle="" metaKeywords="Azure, Cordova, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/11/2014" ms.author="ghogen" />

### 在 Visual Studio 2015 預覽版中搭配使用行動服務與 Cordova 專案

若要在 Visual Studio 2015 預覽版的 Cordova 專案中使用 Azure 行動服務，您需要套用下列因應措施。

1. 在 Visual Studio 2015 預覽版 Cordova 專案中，開啟 Config.xml，並在 [**外掛程式**] 索引標籤下，啟用 [**Windows Azure 行動服務**] 外掛程式。<br/>
![][1]

2. 在 index.html 中，刪除參考 **MobileServices.Web-1.2.2.min.js** 的行。<br/>

<PRE style="prettyprint">
&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;meta charset="utf-8" /&gt;
    &lt;title&gt;MyCordovaApp&gt;/title&gt;

    &lt;!--MyCordovaApp references --&gt;
    &lt;link href="css/index.css" rel="stylesheet" /&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;p&gt;Hello world!&lt/p&gt;

    &lt;!--Cordova reference, this is added to your app when it's build. --&gt;
    &lt;script src="cordova.js"&gt;&lt;/script&gt;
    &lt;script src="scripts/platformOverrides.js"&gt;&lt;/script&gt;

    &lt;script src="scripts/index.js"&gt;&lt;/script&gt;

    &lt;!-- yourservicename references --&gt;
    <span style="background-color:yellow">&lt;script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js"&gt;&lt/script&gt;</span>
    &lt;script src="/services/mobileservices/settings/yourservicename.js"&gt;&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
</PRE>

3. 在 [服務] -> [mobileServices] -> [設定] 資料夾開啟 {yourservicename}.js，並以下列內容取代現有的程式碼片段：

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png 
