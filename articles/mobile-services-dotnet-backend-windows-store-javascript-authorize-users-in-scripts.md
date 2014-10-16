<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

行動服務使用者的伺服器端授權
============================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Windows 市集 C#")[Windows 市集 JavaScript](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Windows 市集 JavaScript")[Windows Phone](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS")

[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/ ".NET 後端") | [JavaScript 後端](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/ "JavaScript 後端")

本主題說明如何授權經驗證的使用者，從 Windows 市集應用程式存取 Azure 行動服務中的資料。在本教學課程中，您會將程式碼新增至控制器中的資料存取方法，而根據經驗證使用者的 userId 篩選查詢，確保每位使用者只能看到他們自己的資料。

本教學課程會以行動服務快速入門為基礎，並依據上一個[開始使用驗證](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users)教學課程建立。開始此教學課程之前，您必須先完成[開始使用驗證](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users)。

修改資料存取方法
----------------

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

測試應用程式
------------

1.  在 Visual Studio 中，開啟您完成[開始使用驗證](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users)教學課程時所修改的專案。

2.  按 F5 鍵執行應用程式，然後以您選擇的識別提供者登入。

	請注意，雖然已有先前教學課程中在 TodoItem 資料表中輸入的項目，依然不會傳回任何項目。因為之前的項目插入時沒有 userID 資料欄，而現在有 Null 值。

3.  在應用程式的 **[Insert a TodoItem]** 中輸入文字，然後按一下 **[儲存]**。

	![][3]

	如此會在行動服務中的 TodoItem 資料表中插入文字及 userId。因為新的項目具有正確的 userID 值，所以會由行動服務傳回並顯示在第二個資料欄中。

4.  (選用) 如果您有其他登入帳戶，您可以驗證使用者是否只能看到自己的資料，方法是關閉此應用程式 (Alt+F4) 然後重新執行。顯示登入認證對話方塊時，請輸入不同的登入，然後驗證是否不會顯示在上一個帳戶下所輸入的項目。

後續步驟
--------

這將結束示範使用驗證基本概念的教學課程。考慮更深入了解下列行動服務主題：

-   [開始使用資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data)
    深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用推播通知](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push)
    了解如何將極為基本的推播通知傳送到應用程式。

-   [如何使用 Azure 行動服務的 HTML/JavaScript 用戶端](/zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library)
    深入了解行動服務如何與 JavaScript 和 HTML 搭配使用。


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Get started with Mobile Services]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Get started with data]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Get started with authentication]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Get started with push notifications]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push

[How to use an HTML/JavaScript client for Azure Mobile Services]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library