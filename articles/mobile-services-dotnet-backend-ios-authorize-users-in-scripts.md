<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

行動服務使用者的伺服器端授權
============================

[Windows 市集 C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Windows 市集 JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS")

[.NET 後端](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/ ".NET 後端") | [JavaScript 後端](/en-us/documentation/articles/mobile-services-ios-authorize-users-in-scripts/ "JavaScript 後端")

本主題說明如何為已驗證的使用者授權，從 iOS 應用程式存取 Azure 行動服務中的資料。在本教學課程中，您會將程式碼新增至控制器中的資料存取方法，而根據經驗證使用者的 userId 篩選查詢，確保每位使用者只能看到他們自己的資料。

本教學課程會以行動服務快速入門為基礎，並依據上一個[開始使用驗證](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users)教學課程建立。開始此教學課程之前，您必須先完成[開始使用驗證](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users)。

修改資料存取方法
----------------

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

測試應用程式
------------

1.  在 Xcode 中，開啟您完成教學課程[開始使用驗證](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users)時所修改的專案。

2.  按 **[執行]** 按鈕以建置專案，並在 iPhone 模擬器中啟動應用程式，然後使用您選擇的身分識別提供者進行登入。

	請注意，雖然已有先前教學課程中在 TodoItem 資料表中輸入的項目，依然不會傳回任何項目。因為之前的項目插入時沒有 userID 資料欄，而現在有 Null 值。

3.  在應用程式的 **[Insert a TodoItem]** 中輸入文字，然後按一下 **[儲存]**。

	![][3]

	如此會在行動服務中的 TodoItem 資料表中插入文字及 userId。因為新的項目具有正確的 userID 值，所以會由行動服務傳回並顯示在第二個資料欄中。

4.  返回 [管理入口網站][Azure 管理入口網站] 的 **todoitem** 資料表中，按一下 **[瀏覽]**，並驗證每個新增項目現在是否具有相關聯的 userId 值。

5.  (選用) 如果您有其他登入帳戶，您可以驗證使用者是否只能看到自己的資料，方法是關閉此應用程式然後重新執行。顯示登入認證對話方塊時，請輸入不同的登入，然後驗證是否不會顯示在上一個帳戶下所輸入的項目。


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/