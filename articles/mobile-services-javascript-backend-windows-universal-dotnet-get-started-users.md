<properties 
	pageTitle="開始使用驗證 (Windows 市集) | 行動開發人員中心" 
	description="了解如何使用行動服務透過眾多識別提供者驗證 Windows 市集應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# 在您的行動服務應用程式中新增驗證

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)] 

本主題說明如何從您的通用 Windows 應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [將驗證新增至應用程式]
5. [將驗證權杖儲存在用戶端]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

>[AZURE.NOTE]本教學課程說明如何在 Windows 市集與 Windows Phone 市集 8.1 應用程式中驗證使用者。有關 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式，請檢視此版本的[開始使用行動服務中的驗證](mobile-services-windows-phone-get-started-users.md)。

>本教學課程示範行動服務如何管理不同的身分識別提供者的驗證流程。此方法設定起來很簡單，而且可支援多個提供者。若要改用 Live Connect 與用戶端管理的驗證方式，並且在您的 Windows Phone 應用程式中提供單一登入體驗，請參閱[使用 Microsoft 帳戶以用戶端管理的驗證來驗證 Windows 市集應用程式](mobile-services-windows-store-dotnet-single-sign-on.md)主題。藉由使用用戶端管理的驗證，您的應用程式即可存取身分識別提供者保留的其他使用者資料。您可藉由呼叫伺服器指令碼中的 **user.getIdentities()** 函數，在行動服務中取得相同的使用者資料。如需詳細資訊，請參閱 [這篇文章](http://go.microsoft.com/fwlink/p/?LinkId=506605)。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../includes/mobile-services-restrict-permissions-windows.md)] 
 
>[AZURE.NOTE]當您使用 Visual Studio 工具將您的應用程式連接到行動服務時，此工具會分別針對個別用戶端平台需要產生共兩組 **MobileServiceClient** 定義。這時您可以順勢將使用 `#if...#endif` 包裝的 **MobileServiceClient** 定義統一至單一未包裝的定義以供這兩個應用程式版本使用，藉此簡化產生的程式碼。如果您是從 Azure 管理入口網站下載快速入門應用程式，則不需要這麼做。

##<a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

現在，由您信任的識別提供者驗證的任何使用者都能存取 *TodoItem* 資料表。若要更善加保護使用者專屬的資料，您還必須實作授權。若要進行，您要取得指定使用者的使用者識別碼，然後用來判斷使用者對於指定資源具備何種層級的存取權。

##<a name="tokens"></a>將授權權杖儲存在用戶端

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>後續步驟

在下一個[行動服務使用者的伺服器端授權](mobile-services-javascript-backend-service-side-authorization.md)教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。您可以在[行動服務 .NET 作法概念參考資料]中深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[將驗證權杖儲存在用戶端]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[開始使用行動服務]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-javascript-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: mobile-services-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: mobile-services-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念參考資料]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=54-->