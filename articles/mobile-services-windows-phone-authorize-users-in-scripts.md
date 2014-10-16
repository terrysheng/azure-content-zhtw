<properties pageTitle="Service-side authorization (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

行動服務使用者的服務端驗證
==========================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Windows 市集 C#")[Windows 市集 JavaScript](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Windows 市集 JavaScript")[Windows Phone](/zh-tw/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/zh-tw/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS")[Android](/zh-tw/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android")[HTML](/zh-tw/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML")[Xamarin.iOS](/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS")[Xamarin.Android](/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android")

[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/ ".NET 後端") | [JavaScript 後端](/zh-tw/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/ "JavaScript 後端")

本主題將示範如何使用伺服器指令碼，授權經驗證的使用者從 Windows Phone 8 應用程式存取 Azure 行動服務中的資料。在本教學課程中，您將向行動服務註冊指令碼，以根據經驗證的使用者本身的 userId 篩選查詢，確保每位使用者只能看見自己的資料。

本教學課程將根據行動服務快速入門，並以上一堂教學課程[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)為基礎。在開始本教學課程之前，您必須首先完成[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)。

[觀看教學指南](http://go.microsoft.com/fwlink/?LinkId=298630) [播放視訊](http://go.microsoft.com/fwlink/?LinkId=298630) 15:00

註冊指令碼
----------

由於快速入門應用程式會讀取和插入資料，因此您需要對 TodoItem 資料表註冊這些操作所用的指令碼。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

    ![](./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png)

2.  按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

    ![](./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png)

3.  按一下 **[指令碼]**，然後選取 **Insert** 操作。

    ![](./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png)

4.  使用下列函數來取代現有的指令碼，然後按一下 **[儲存]**：

        function insert(item, user, request) {
        item.userId = user.userId;    
        request.execute();
        }

    此指令碼會將 userId 值新增至項目 (也就是經驗證的使用者本身的使用者識別碼)，然後再將項目插入 TodoItem 資料表。

    **注意**

    第一次執行此 insert 指令碼時，必須啟用動態結構描述。在啟用動態結構描述的情況下，行動服務會在首次執行時自動將 **userId** 欄新增至 **TodoItem** 資料表。依預設，新行動服務的動態結構描述為已啟用，您應先將它停用，再將應用程式發佈至 Windows Phone 市集。

5.  重複步驟 3 和 4，以下列函數取代現有 **Read** 操作：

        function read(query, user, request) {
        query.where({ userId:user.userId});    
        request.execute();
        }

    This script filters the returned TodoItem objects so that each user only receives the items that they inserted.

測試應用程式
------------

1.  在 Visual Studio 2012 Express for Windows Phone 中，開啟您在完成教學課程[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)時所修改的專案。

2.  按 F5 鍵執行應用程式，然後以您選擇的身分識別提供者登入。

    Notice that this time, although there are items already in the TodoItem table from previous tutorials, no items are returned.This happens because previous items were inserted without the userId column and now have null values.

3.  在應用程式中，於文字方塊輸入文字，然後按一下 **[儲存]**。

    ![](./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png)

    This inserts both the text and the userId in the TodoItem table in the mobile service.Because the new item has the correct userId value, it is returned by the mobile service.

4.  返回[管理入口網站](https://manage.windowsazure.com/)中的 **todoitem** 資料表，按一下 **[瀏覽]** 並確認每個新增的項目現在都有相關聯的 userId 值。

後續步驟
--------

這將結束示範使用驗證基本概念的教學課程。考慮搜尋有關下列行動服務主題的更多資訊：

-   [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)
    深入了解使用行動服務來排序和查詢資料。

-   [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)
    了解如何將非常基本的推播通知傳送到應用程式。

-   [行動服務伺服器指令碼參照](http://go.microsoft.com/fwlink/?LinkId=262293)
    深入了解註冊與使用伺服器指令碼。


