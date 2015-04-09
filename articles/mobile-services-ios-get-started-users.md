<properties
	pageTitle="將驗證加入現有 Azure 行動服務應用程式 (iOS) | 行動開發人員中心"
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

在本教學課程中，您可以使用支援的身分識別提供者，將驗證加入快速入門專案。

本教學課程以[行動服務快速入門教學課程]為基礎，您必須先完成此教學課程。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>限制只有經驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

在 Xcode 中，開啟專案。按下 [**執行**] 按鈕以啟動應用程式。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的例外狀況。這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

##<a name="add-authentication"></a>將驗證加入應用程式

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>將驗證權杖儲存在應用程式中

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>後續步驟

在下一個教學課程[行動服務使用者的服務端授權]中，您將使用使用者識別碼值篩選傳回的資料。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證加入應用程式]: #add-authentication
[後續步驟]:#next-steps
[將驗證權杖儲存至您的應用程式]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[行動服務使用者的服務端授權]: mobile-services-javascript-backend-service-side-authorization.md
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[行動服務快速入門教學課程]: /develop/mobile/tutorials/get-started-ios
[開始使用資料]: /develop/mobile/tutorials/get-started-with-data-ios
[開始使用驗證]: /develop/mobile/tutorials/get-started-with-users-ios
[開始使用推播通知]: /develop/mobile/tutorials/get-started-with-push-ios
[使用指令碼授權使用者]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure 管理入口網站]: https://manage.windowsazure.com/

<!--HONumber=49-->