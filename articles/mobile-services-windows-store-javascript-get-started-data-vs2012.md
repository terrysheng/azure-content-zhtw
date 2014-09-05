<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

開始以 Visual Studio 2012 使用行動服務中的資料
==============================================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012 "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

本主題將示範如何使用 Azure 行動服務，以在 Windows 市集應用程式中運用資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。

**注意**

本教學課程會將行動服務功能加入至 Visual Studio 2012 中建立的 Windows 市集應用程式。Visual Studio 2013 包含新功能，可讓您輕鬆地將您的 Windows 市集應用程式與行動服務連接。如需詳細資訊，請參閱[開始使用行動服務中的資料](/en-us/develop/mobile/tutorials/get-started-with-data-js/)。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案](#download-app)
2.  [建立行動服務](#create-service)
3.  [新增用於儲存的資料表](#add-table)
4.  [更新應用程式以使用行動服務](#update-app)
5.  [針對行動服務進行應用程式測試](#test-app)

**注意**

若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F)。

下載專案下載 GetStartedWithData 專案
------------------------------------

本教學課程以 [GetStartedWithData 應用程式](http://go.microsoft.com/fwlink/?LinkId=262308) (英文) (Windows 市集應用程式) 為基礎而建立。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務快速入門所產生的應用程式兩者的 UI 完全相同。

1.  從[開發人員程式碼範例網站](http://go.microsoft.com/fwlink/?LinkId=262308) (英文) 下載 JavaScript 版本的 GetStartedWithData 範例應用程式。

 	![][10]

2.  在 Visual Studio 2012 Express for Windows 8 中，開啟下載專案，並展開 **js** 資料夾檢查 default.js 檔案。

	Notice that added **TodoItem** objects are stored in an in-memory **List** object.

3.  按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式的 **[Insert a TodoItem]** 中鍵入一些文字，然後按一下 **[儲存]**。

	![][0]  

 	Notice that the saved text is displayed in the second column under **Query and update data**.

建立行動服務在管理入口網站中建立新的行動服務
--------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

新增資料表將資料表新增至行動服務
--------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

更新應用程式更新應用程式以使用行動服務進行資料存取
--------------------------------------------------

您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1.  在 Visual Studio 的 **[方案總管]** 中以滑鼠右鍵按一下專案名稱，然後選取 **[管理 NuGet 封裝]**。

2.  在左側窗格中選取 **[線上]** 類別、搜尋 `WindowsAzure.MobileServices.WinJS`、按一下 **[Azure Mobile Services for WinJS]** 封裝上的 **[安裝]**，然後接受授權合約。

	![](./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png)

	這會將行動服務用戶端程式庫新增至專案。

1.  在 default.html 專案檔中，將下列指令碼參考加入至頁面標頭中：

         <script type="text/javascript" src="/js/MobileServices.js"></script>

2.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

3.  按一下 **[儀表板]** 索引標籤並記下 **[網站 URL]**，然後按一下 **[管理金鑰]** 並記下 **[應用程式金鑰]**。

	![][8]

	從應用程式程式碼存取行動服務時，您將會用到這些值。

1.  在 Visual Studio 中，開啟檔案 default.js，取消註解下列定義 **client** 變數的程式碼，並在 **MobileServiceClient** 建構函式中依序提供行動服務的 URL 和應用程式金鑰：

             var client = new WindowsAzure.MobileServiceClient(
        "AppUrl",
        "appKey"
             );

	這會建立 MobileServiceClient 的新執行個體，可用來存取您的行動服務。

1.  取消註解下列程式碼行：

         var todoTable = client.getTable('TodoItem');

        This code creates a proxy object (**todoTable**) for the new database table. 

2.  使用下列程式碼來取代 **InsertTodoItem** 函數：

         var insertTodoItem = function (todoItem) {
        // Inserts a new row into the database.When the operation completes
        // and Mobile Services has assigned an id, the item is added to the binding list.
        todoTable.insert(todoItem).done(function (item) {
        todoItems.push(item);
             });
         };

	此程式碼會將新的項目插入資料表中。

1.  使用下列程式碼來取代 **RefreshTodoItems** 函數：

         var refreshTodoItems = function () {
        // This code refreshes the entries in the list by querying the TodoItems table. 
        todoTable.read().done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };      

        This sets the binding to the collection of items in the todoTable, which contains all completed items returned from the mobile service. 

2.  使用下列程式碼來取代 **UpdateCheckedTodoItem** 函數：

         var updateCheckedTodoItem = function (todoItem) {
        // This code takes a freshly completed TodoItem and updates the database. 
        todoTable.update(todoItem);
         };

        This sends an item update to the mobile service.

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

測試應用程式在您的新行動服務中測試應用程式
------------------------------------------

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  和之前一樣，在 **[Insert a TodoItem]** 中鍵入文字，然後按一下 **[儲存]**。

        This sends a new item as an insert to the mobile service.

3.  在[管理入口網站](https://manage.windowsazure.com/)中按一下 **[行動服務]**，然後按一下您的行動服務。

4.  按一下 **[資料]** 索引標籤，然後按一下 **[瀏覽]**。

	![][9]
          
	Notice that the **TodoItem** table now contains data, with id values generated by Mobile Services, and that columns have been automatically added to the table to match the TodoItem class in the app.

5.  在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 **[重新整理]**。

	請注意，完成值已從 **[false]** 變更為 **[true]**。

1.  在 default.js 專案檔案中，使用下列可篩選出已完成項目的程式碼，來取代現有的 **RefreshTodoItems** 函數：

         var refreshTodoItems = function () {                     
        // More advanced query that filters out completed items. 
        todoTable.where({ complete:false })
        .read()
        .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
                });            
         };

2.  在應用程式中檢查清單中的其中一個項目，然後按一下 **[重新整理]** 按鈕。

	Notice that the checked item now disappears from the list.Each refresh results in a round-trip to the mobile service, which now returns filtered data.

這將結束**開始使用資料**教學課程。

後續步驟
--------

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令碼驗證及修改資料](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js)
    深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送的資料。

-   [使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-js)
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012)
    了解如何將極為基本的推播通知傳送到應用程式。


<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png






[7]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
[8]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png


<!-- URLs. -->
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-js
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012
[C# and XAML]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/?LinkId=262308