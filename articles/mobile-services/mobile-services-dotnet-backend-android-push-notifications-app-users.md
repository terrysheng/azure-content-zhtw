<properties 
	pageTitle="傳送推播通知給已驗證的使用者" 
	description="了解如何將推播通知傳送給特定" 
	services="mobile-services,notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# 傳送推播通知給已驗證的使用者

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##概觀

本主題說明如何將推播通知傳送給任何已註冊之 iOS 裝置上的驗證使用者。不同於先前的[推播通知][開始使用推播通知]教學課程，本教學課程會變更您的行動服務，而要求使用者必須先完成驗證，用戶端才能向通知中樞註冊推播通知。此外也會修改註冊，以根據指派的使用者識別碼新增標記。最後會更新伺服器程式碼，而僅將通知傳送給已驗證的使用者，而不是所有的註冊。


本教學課程支援 Android 應用程式。

##必要條件 

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [在您的行動服務應用程式中新增驗證]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [開始使用推播通知]<br/>使用通知中心，設定推播通知的 TodoList 範例應用程式。 

在您完成這兩個教學課程後，您即可防止未經授權的使用者從您的行動服務註冊推播通知。

##更新服務以要求註冊所需的驗證

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

##更新應用程式以在註冊前先登入

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](mobile-services-android-push-notifications-app-users.md)] 

##測試應用程式

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)] 


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[將驗證加入至行動服務應用程式]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[開始使用推播通知]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念性參考]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=47-->
 