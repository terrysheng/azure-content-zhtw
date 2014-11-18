<properties pageTitle="伺服器端授權 (Android) | 行動開發人員中心" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="10/20/2014" ms.author="glenga" />

# 行動服務使用者的伺服器端授權

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

本主題說明如何為已驗證的使用者授權，從 Android 應用程式存取 Azure 行動服務中的資料。在本教學課程中，您會將程式碼新增至控制器中的資料存取方法，而根據經驗證使用者的 userId 篩選查詢，確保每位使用者只能看到他們自己的資料。

本教學課程會以行動服務快速入門為基礎，並依據上一個[開始使用驗證]教學課程建立。開始此教學課程之前，您必須先完成[開始使用驗證]。  

## <a name="register-scripts"></a>修改資料存取方法

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]


## 測試應用程式

1. 在 Eclipse 中，開啟您完成教學課程[開始使用驗證]時所修改的專案。

2. 從 [**執行**] 功能表中，按一下 [**執行**] 在 Android 模擬器中啟動專案。

   	Notice that this time, although there are items already in the TodoItem table from previous tutorials, no items are returned.因為之前的項目插入時沒有 userID 資料欄，而現在有 Null 值。

3. 在應用程式的 [**新增待辦事項**] 中輸入文字，然後按一下 [**儲存**]。

   	![][3]

   	This inserts both the text and the userId in the TodoItem table in the mobile service.因為新項目有正確的 userID 值，所以行動服務傳回該值並會顯示於第二個資料欄。

5. 返回[管理入口網站][Azure Management Portal] 的 **todoitem** 資料表中，按一下 [**瀏覽**]，並驗證每個新增項目現在是否具有相關聯的 userId 值。

6. (選用) 如果您有其他登入帳戶，您可以驗證使用者是否只能看到自己的資料，方法是關閉此應用程式然後重新執行。顯示登入認證對話方塊時，請輸入不同的登入，然後驗證是否不會顯示在上一個帳戶下所輸入的項目。

## 後續步驟

這將結束示範使用驗證基本概念的教學課程。考慮更深入了解下列行動服務主題：

* [開始使用資料]
  <br/>深入了解如何使用行動服務來儲存及查詢資料。

* [開始使用推播通知]
  <br/>了解如何將極為基本的推播通知傳送到應用程式。

* [行動服務 Android 作法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 Android。

<!-- Anchors. -->
[註冊伺服器指令碼]: #register-scripts
[後續步驟]：#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png

<!-- URLs. -->
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push

[行動服務 Android 作法概念參考]: /zh-tw/documentation/articles/mobile-services-android-how-to-use-client-library/
