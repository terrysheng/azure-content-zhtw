<properties
	pageTitle="在具有 Azure Mobile Apps 的 iOS 上新增驗證"
	description="了解如何使用 Azure Mobile Apps 透過眾多身分識別提供者驗證 iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/28/2015"
	ms.author="krisragh"/>

# 將驗證新增至您的 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

在本教學課程中，您可以使用支援的身分識別提供者，將驗證加入 [iOS 快速入門]專案。本教學課程以 [iOS 快速入門]教學課程為基礎，您必須先完成該教學課程。

##<a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Xcode 中，按下 [Run] 以啟動應用程式。因為應用程式嘗試以未驗證的使用者身分來存取後端，但 _TodoItem_ 資料表現在需要驗證，所以會引發例外狀況。

##<a name="add-authentication"></a>將驗證加入應用程式

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[iOS 快速入門]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
 

<!---HONumber=AcomDC_0107_2016--->