<properties 
	pageTitle="" 
	description="在 Cordova 專案中使用行動服務" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# 在 Visual Studio 2015 預覽版中搭配使用行動服務與 Cordova 專案

若要在 Visual Studio 2015 預覽版的 Cordova 專案中使用 Azure 行動服務，您需要套用下列因應措施。

1. 在您 Visual Studio 2015 預覽版的  Cordova 專案中開啟 Config.xml，並且在 [**外掛程式**] 索引標籤下啟用 **Azure 行動服務**外掛程式。<br/>![][1]

2. 在 index.html 中，刪除提及**MobileServices.Web-1.2.2.min.js** 的每一行。<br/>

<PRE style="prettyprint">
&lt;!DOCTYPE html&gt; &lt;html&gt; &lt;head&gt; &lt;meta charset="utf-8" /&gt; &lt;title&gt;MyCordovaApp&gt;/title&gt;

    &lt;!--MyCordovaApp references --&gt;
    &lt;link href="css/index.css" rel="stylesheet" /&gt;
&lt;/head&gt; &lt;body&gt; &lt;p&gt;Hello world!&lt/p&gt;

    &lt;!--Cordova reference, this is added to your app when it's build. --&gt;
    &lt;script src="cordova.js"&gt;&lt;/script&gt;
    &lt;script src="scripts/platformOverrides.js"&gt;&lt;/script&gt;

    &lt;script src="scripts/index.js"&gt;&lt;/script&gt;

    &lt;!-- yourservicename references --&gt;
    <span style="background-color:yellow">&lt;script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js"&gt;&lt/script&gt;</span>
    &lt;script src="/services/mobileservices/settings/yourservicename.js"&gt;&lt;/script&gt;
&lt;/body&gt; &lt;/html&gt; </PRE>

3. 在 [服務] -> [mobileServices] -> [設定] 資料夾開啟 {yourservicename}.js，並以下列內容取代現有的程式碼片段：

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png
<!--HONumber=54-->