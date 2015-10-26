<properties 
	pageTitle="當您使用 Visual Studio 連線服務將行動服務加入 Javascript App 時會發生什麼狀況" 
	description="說明您在 Visual Studio 中的 Azure 行動服務專案的情形 " 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="patshea"/>

# 當我使用連線的 Visual Studio 服務加入 Azure 行動服務時，我的 Javascript 專案會發生什麼狀況？

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###我的專案發生什麼情形？

#####已加入 NuGet 封裝

已安裝 **WindowsAzure.MobileServices.WinJS** NuGet 封裝，包括 `js\MobileServices.js` 檔案中的 Azure 行動服務程式庫。
  
#####行動服務的連接字串值 

在 `services\mobileServices\settings` 資料夾中，已產生一個含有 **MobileServiceClient** 的新的 JavaScript (.js) 檔案，其中包含所選行動服務的應用程式 URL 和應用程式金鑰。


#####已加入 default.html 的參考

`MobileServices.js` 的參考和設定檔案已加入至 `default.html`。


#####已加入連接的服務檔案

在 services 資料夾中，已加入連接的服務組態檔。



 

<!---HONumber=Oct15_HO3-->