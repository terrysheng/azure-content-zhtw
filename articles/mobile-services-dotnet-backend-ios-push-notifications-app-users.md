<properties pageTitle="傳送推播通知給已驗證的使用者" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 傳送推播通知給已驗證的使用者

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET backend" class="current">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript backend">JavaScript 後端</a></div>

本主題說明如何將推播通知傳送給任何已註冊之 iOS 裝置上的已驗證使用者。不同於先前的[推播通知][開始使用推播通知]教學課程，本教學課程會變更您的行動服務，而要求使用者必須先完成驗證，用戶端才能向通知中樞註冊推播通知。註冊也修改為根據指派的使用者識別碼來新增一個標籤。最後，更新伺服器程式碼，將通知只傳送給已驗證的使用者，而非所有註冊。

本教學課程將逐步引導您完成下列程序：

+ [更新服務以要求註冊的驗證]
+ [更新應用程式以在註冊前先登入]
+ [測試應用程式]

本教學課程支援 Windows 市集和 Windows Phone 市集應用程式。

##必要條件

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [開始使用驗證]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [開始使用推播通知]<br/>使用通知中樞，設定推播通知的 TodoList 範例應用程式。

在您完成這兩個教學課程後，您即可防止未經授權的使用者從您的行動服務註冊推播通知。

##<a name="register"></a>更新服務以要求註冊所需的驗證

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>更新應用程式以在註冊前先登入

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>測試應用程式

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[更新服務以要求註冊的驗證]: #register
[更新應用程式以在註冊前先登入]: #update-app
[測試應用程式]: #test
[後續步驟]:#next-steps


<!-- URLs. -->
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
