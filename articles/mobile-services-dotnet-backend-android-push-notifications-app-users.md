<properties linkid="/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# 傳送推播通知給已驗證的使用者

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/" title=".NET 後端" class="current">.NET 後端</a> | 
<a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/"  title="JavaScript 後端">JavaScript 後端</a>
</div>

本主題說明如何將推播通知傳送給任何已註冊的裝置上已驗證的使用者。與前一個[推播通知][]教學課程不同，本教學課程變更您的行動服務，要求使用者必須經過驗證，用戶端才能向通知中心註冊推播通知。註冊也修改為根據指派的使用者識別碼來新增一個標籤。最後，更新伺服器程式碼，將通知只傳送給已驗證的使用者，而非所有註冊。

本教學課程將逐步引導您完成下列程序：

-   [將服務更新為需要驗證才能註冊][]
-   [將應用程式更新為註冊之前需要登入][]
-   [測試應用程式][]

本教學課程支援 Android 應用程式。

## 必要條件

在開始本教學課程之前，您必須已完成這些行動服務教學課程：

-   [開始使用驗證][]
    在 TodoList 範例應用程式中增加登入需求。

-   [開始使用推播通知][推播通知]
    使用通知中心來設定 TodoList 範例應用程式的推播通知。

完成這兩個教學課程之後，您就可以防止未經驗證的使用者從您的行動服務註冊推播通知。

## <a name="register"></a>將服務更新為需要驗證才能註冊

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users][]]

## <a name="update-app"></a>將應用程式更新為註冊之前需要登入

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users][]]

## <a name="test"></a>測試應用程式

[WACOM.INCLUDE [mobile-services-android-test-push-users][]]

<!-- Anchors. --> 

<!-- URLs. -->

  [Windows 市集 C\#]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
  [Android]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/ ".NET 後端"
  [JavaScript 後端]: /zh-tw/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/ "JavaScript 後端"
  [推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/
  [將服務更新為需要驗證才能註冊]: #register
  [將應用程式更新為註冊之前需要登入]: #update-app
  [測試應用程式]: #test
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
  [mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
  [mobile-services-android-push-notifications-app-users]: ../includes/mobile-services-android-push-notifications-app-users.md
  [mobile-services-android-test-push-users]: ../includes/mobile-services-android-test-push-users.md
