<properties 
	pageTitle="開始使用驗證 (Appcelerator) | 行動開發人員中心" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="mahender"/>

# 在行動服務中開始使用驗證

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

本主題說明如何從您的應用程式在 Windows Azure 行動服務中驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊應用程式進行驗證，並設定行動服務]
2.  [限制只有經驗證的使用者具有資料表的權限]
3.  [將驗證加入應用程式]

本教學課程以行動服務快速入門為基礎。您也必須先完成教學課程[開始使用行動服務]。

若要完成本教學課程，您需要 Appcelerator Titanium Studio 3.2.1 或更新版本，及 iOS 7.0 和/或更新版本和 Android 4.3 或更新版本。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> 限制只有經驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

3.	在 Appcelerator Titanium Studio 中，開啟您完成[開始使用行動服務]教學課程時所建立的專案。

4.	按 [執行] 按鈕，以在 iPhone 模擬器中建置專案並啟動應用程式。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。
    
    這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 TodoItem 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增到應用程式

1.	開啟專案檔案 index.js，並在資料表事件接聽器方法中尋找 `case 2:`

    在您的應用程式中，您可以提示使用者可用的身分識別提供者或自動提供任何其中一個身分識別提供者。

2.	若要提供所有可用身分識別提供者，請使用下列程式碼：

        	var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        	var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        	var appName = 'appctest';
        	azureMobileServices.setAppName(appName);
        	var authenticationClients = ['Google', 'Facebook', 'Twitter', 'Microsoft Account', 'Active Directory', 'Cancel'];
       	 var dialog = Ti.UI.createOptionDialog({
            options : authenticationClients,
            title : 'Select a client'
       	 });
        	dialog.addEventListener('click', function(evt) {
            if (evt.index == 0 || evt.index == 1 || evt.index == 2 || evt.index == 3 || evt.index == 4) {
                var str = authenticationClients[evt.index];
                str = str.replace(/ /g, '');
                var authorizeClient = str.toLowerCase();
                if (authorizeClient == 'activedirectory') authorizeClient = 'aad';
                azureMobileServices.authorizeClient(authorizeClient, function(result) {
                    if (result == 'true') {
                        Alloy.createController('TableData');
                    }
                });
            } else {
                dialog.hide();
            }
        	});
        	dialog.show();

3.	若要提供特定身分識別提供者，請使用下列程式碼：

        	var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        	var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        	var authorizeClient = "Google"; //Replace "Google" with identity provider.
        	authorizeClient = authorizeClient.toLowerCase();
        	azureMobileServices.authorizeClient(authorizeClient, function(result) {
            if (result == 'true') {
                Alloy.createController('TableData');
            }
        	});

>[AZURE.NOTE] 如果您使用的身分識別提供者不是 Google，請將傳給 **authorizeClient** 的值變更為下列其中一個： *microsoftaccount*、 *facebook*、 *twitter*或 *windowsazureactivedirectory*。

4.	按 [執行] 按鈕以建置專案，並在 iPhone 模擬器或 Android 模擬器中啟動應用程式，然後按一下 [With Login] 選項以身分識別提供者的身分登入。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。


<!-- Anchors. -->

[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證加入應用程式]: #add-authentication

<!-- Images. -->

<!-- URLs. -->
[開始使用行動服務]: partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
<!--HONumber=49--> 

<!--HONumber=49-->