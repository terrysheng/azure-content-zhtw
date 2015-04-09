<properties
	pageTitle="將驗證加入現有 Azure 行動服務應用程式 (iOS)  | 行動開發人員中心"
	description="了解如何使用行動服務透過眾多識別提供者驗證 iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# 將驗證加入現有的 Azure 行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

在本教學課程中，您可以使用支援的身分識別提供者，將驗證加入快速入門專案。本教學課程以[行動服務快速入門教學課程]為基礎，您必須先完成此教學課程。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>限制只有經驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

在 Xcode 中，開啟專案。按下 [**執行**] 按鈕以啟動應用程式。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的例外狀況。這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

##<a name="add-authentication"></a>將驗證加入應用程式

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>將驗證權杖儲存在應用程式中

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>後續步驟

在下一個教學課程[行動服務使用者的服務端授權]中，您將使用使用者識別碼值篩選傳回的資料。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證加入應用程式]: #add-authentication
[後續步驟]:#next-steps
[將驗證權杖儲存至您的應用程式]:#store-authentication

<!-- URLs. -->
[行動服務使用者的服務端授權]: mobile-services-dotnet-backend-service-side-authorization.md
[行動服務快速入門教學課程]: mobile-services-dotnet-backend-ios-get-started.md
[開始使用資料]: mobile-services-dotnet-backend-ios-get-started-data.md
[開始使用驗證]: mobile-services-dotnet-backend-ios-get-started-users.md
[開始使用推播通知]: mobile-services-dotnet-backend-ios-get-started-push.md
[使用指令碼授權使用者]: mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 做法概念性參考]: /develop/mobile/how-to-guides/work-with-net-client-library
[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->