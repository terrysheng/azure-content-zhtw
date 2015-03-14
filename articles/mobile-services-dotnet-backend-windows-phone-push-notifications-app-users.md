<properties 
	pageTitle="傳送推播通知給已驗證的使用者" 
	description="了解如何將推播通知傳送給特定" 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# 傳送推播通知給已驗證的使用者

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

本主題說明如何將推播通知傳送給任何已註冊之 iOS 裝置上的驗證使用者。不同於先前的[推播通知][開始使用推播通知]教學課程，本教學課程會變更您的行動服務，而要求使用者必須先完成驗證，用戶端才能向通知中樞註冊推播通知。此外也會修改註冊，以根據指派的使用者識別碼新增標記。最後會更新伺服器程式碼，而僅將通知傳送給已驗證的使用者，而不是所有的註冊。

本教學課程將逐步引導您完成下列程序:

+ [更新服務以要求註冊的驗證]
+ [更新應用程式以在註冊前先登入]
+ [測試應用程式]
 
本教學課程支援 Windows Phone 8.0 和 Windows Phone 8.1 ("Silverlight") 應用程式。如需 Windows Phone 8.1 市集應用程式，請參閱本主題的 [Windows 市集版本](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users)。

## 必要條件 

在開始本教學課程之前，您必須已完成下列行動服務教學課程:

+ [開始使用驗證]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [開始使用推播通知]<br/>使用通知中樞設定推播通知的 TodoList 範例應用程式。 

在您完成這兩個教學課程後，您即可防止未經授權的使用者從您的行動服務註冊推播通知。

## <a name="register"></a>更新服務以要求註冊所需的驗證

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

## <a name="update-app"></a>更新應用程式以在註冊前先登入

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)] 


## <a name="test"></a>測試應用程式

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>後續步驟

在下一個[行動服務使用者的伺服器端授權][以指令碼授權使用者]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。您可以在[行動服務 .NET 作法概念參考資料]中深入了解如何使用搭配 .NET 的行動服務。-->

<!-- Anchors. -->
[更新服務以要求註冊的驗證]: #register
[更新應用程式以在註冊前先登入]: #update-app
[測試應用程式]: #test
[後續步驟]:#next-steps


<!-- URLs. -->
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 做法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
