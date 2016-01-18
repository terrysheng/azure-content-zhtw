<properties 
	pageTitle="我的 Cordova 專案 (Visual Studio 儲存體連接服務) 發生什麼狀況 | Microsoft Azure" 
	description="描述使用 Visual Studio 連接服務加入 Azure 行動服務之後，您的 Azure Cordova 專案發生什麼狀況" 
	services="mobile-services" 
	documentationCenter="na" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="tarcher"/>

# 使用 Visual Studio 連接服務加入 Azure 行動服務之後，我的 Azure Cordova 專案發生什麼狀況？

##加入參考

所有多重裝置混合應用程式隨附的 Azure 行動服務用戶端外掛程式已啟用。
  
##行動服務的連接字串值

在`services\mobileServices\settings`下，已產生一個含有 **MobileServiceClient** 的新的 JavaScript (.js) 檔案，內含所選行動服務的應用程式 URL 和應用程式金鑰。此檔案包含行動服務用戶端物件的初始化，類似於下列程式碼。

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[深入了解行動服務](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0107_2016-->