<properties 
	pageTitle="開始使用驗證 (Android) | 行動開發人員中心" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 Windows 市集應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="ricksal,mahender"/>

# 將驗證加入至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：



本教學課程以行動服務快速入門為基礎。您也必須先完成教學課程[開始使用行動服務]。 

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>限制只有經驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

3. 開啟您完成教學課程[開始使用行動服務]時建立的專案。 

4. 從 [**執行**] 功能表，按一下 [**執行應用程式**] 來啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。 

	 這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增到應用程式

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>在用戶端快取驗證權杖

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>重新整理權杖快取

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 

##<a name="next-steps"></a>後續步驟

在下一個[行動服務使用者的伺服器端授權][以指令碼授權使用者]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。 


<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證新增到應用程式]: #add-authentication
[將驗證權杖儲存在用戶端]: #cache-tokens
[重新整理過期的權杖]: #refresh-tokens
[後續步驟]:#next-steps

<!-- URLs. -->
[開始使用行動服務]: mobile-services-dotnet-backend-android-get-started.md
[開始使用資料]: mobile-services-dotnet-backend-android-get-started-data.md
[開始使用驗證]: mobile-services-dotnet-backend-android-get-started-users.md
[開始使用推播通知]: mobile-services-dotnet-backend-android-get-started-push.md
[使用指令碼授權使用者]: mobile-services-dotnet-backend-android-authorize-users-in-scripts.md

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 做法概念性參考]: /develop/mobile/how-to-guides/work-with-net-client-library
[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->