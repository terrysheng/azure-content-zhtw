<properties linkid="/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 傳送推播通知給已驗證的使用者

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Windows 市集 C#" class="current">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/" title=".NET 後端" class="current">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/"  title="JavaScript 後端">JavaScript 後端</a></div>

本主題說明如何將推播通知傳送給任何已註冊之 iOS 裝置上的驗證使用者。不同於先前的[推播通知][推播通知]教學課程，本教學課程會變更您的行動服務，而要求使用者必須先完成驗證，用戶端才能向通知中心註冊推播通知。此外也會修改註冊，以根據指派的使用者識別碼新增標記。最後會更新伺服器程式碼，而僅將通知傳送給已驗證的使用者，而不是所有的註冊。

本教學課程將逐步引導您完成下列程序：

-   [更新服務以要求註冊的驗證][更新服務以要求註冊的驗證]
-   [更新應用程式以在註冊前先登入][更新應用程式以在註冊前先登入]
-   [測試應用程式][測試應用程式]

本教學課程支援 Windows 市集和 Windows Phone 市集應用程式。

## 必要條件

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

-   [開始使用驗證][開始使用驗證]
    將登入需求新增至 TodoList 範例應用程式。

-   [開始使用推播通知][推播通知]
    使用通知中心，設定推播通知的 TodoList 範例應用程式。

在您完成這兩個教學課程後，您即可防止未經授權的使用者從您的行動服務註冊推播通知。

## <a name="register"></a>更新服務以要求註冊所需的驗證

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

## <a name="update-app"></a>更新應用程式以在註冊前先登入

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

## <a name="test"></a>測試應用程式

[WACOM.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)]

<!---## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->

<!-- Anchors. -->

<!-- URLs. -->

  [推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [更新服務以要求註冊的驗證]: #register
  [更新應用程式以在註冊前先登入]: #update-app
  [測試應用程式]: #test
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
  [mobile-services-windows-store-dotnet-push-notifications-app-users]: ../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md
  [mobile-services-windows-test-push-users]: ../includes/mobile-services-windows-test-push-users.md
