<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

開始使用行動服務中的資料
========================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

[.NET 後端](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ ".NET 後端") | [JavaScript 後端](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "JavaScript 後端")

本主題將示範如何使用 Azure 行動服務，以在 Windows 市集應用程式中運用資料。在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。

**注意**

本教學課程需要 Visual Studio 2013，它可讓您更輕鬆地將您的 Windows 市集應用程式與行動服務連接。若要使用 Visual Studio 2012 完成相同的基本程序，請依照主題[使用 Visual Studio 2012 開始使用行動服務中的資料](/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012/)中的步驟進行。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案](#download-app)
2.  [建立行動服務](#create-service)
3.  [新增用於儲存的資料表](#add-table)
4.  [更新應用程式以使用行動服務](#update-app)
5.  [針對行動服務進行應用程式測試](#test-app)

**注意**

若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F)。

下載專案下載 GetStartedWithData 專案
------------------------------------

本教學課程是採用 [GetStartedWithMobileServices 應用程式](http://go.microsoft.com/fwlink/p/?LinkId=328660)所建立，此應用程式是 Visual Studio 2013 中的 Windows 市集應用程式專案。除了會將新增項目儲存在本機記憶體中之外，此應用程式的 UI 與行動服務快速入門所產生的應用程式完全相同。

1.  從[開發人員程式碼範例網站](http://go.microsoft.com/fwlink/p/?LinkId=328660) (英文) 下載 JavaScript 版本的 GetStartedWithData 範例應用程式。

	![](./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png)

2.  在 Visual Studio 2012 Express for Windows 8 中，開啟下載專案，並展開 **js** 資料夾檢查 default.js 檔案。

	Notice that added **TodoItem** objects are stored in an in-memory **List** object.

3.  按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式的 **[Insert a TodoItem]** 中鍵入一些文字，然後按一下 **[儲存]**。

	![](./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png)

	Notice that the saved text is displayed in the second column under **Query and update data**.

建立行動服務從 Visual Studio 建立行動服務
-----------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  在 [方案總管] 中展開 **services**、**mobile services**、**&lt;your\_service\>** 資料夾，然後開啟 service.js 指令碼檔案，並注意新的全域變數看起來會如下列範例所示：

    ``` {}
    var todolistClient = new WindowsAzure.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
    ```

    本程式碼透過使用全域變數，提供對應用程式中新行動服務的存取權。用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。由於此指令碼的參考已新增到 default.html 檔案，因此此變數可供所有也從此頁面受參考的指令碼檔案使用。

新增資料表將新資料表新增至行動服務並更新應用程式
------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  在 default.js 指令碼檔案中，將定義了現有項目集合的那一行加上註解，然後取消註解或新增下列程式碼行，並使用將專案連線到行動服務時新增到 service.js 檔案的變數取代 `<yourClient>;`：

        var todoTable = <yourClient>.getTable('TodoItem');

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

    **注意**

    建立的新的資料表僅含 Id 資料行。啟用動態結構描述時，行動服務會根據插入或更新要求中的 JSON 物件自動產生新資料行。如需詳細資訊，請參閱[動態結構描述](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj193175.aspx)。

3.  使用下列程式碼來取代 **RefreshTodoItems** 函數：

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

	This sets the binding to the collection of items in the todoTable, which contains all of the **TodoItem** objects returned from the mobile service. 

4.  使用下列程式碼來取代 **UpdateCheckedTodoItem** 函數：

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

	![](./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png)
          
	Notice that the **TodoItem** table now contains data, with id values generated by Mobile Services, and that columns have been automatically added to the table to match the TodoItem class in the app.

5.  在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 **[重新整理]**。

	請注意，完成值已從 **[false]** 變更為 **[true]**。

6.  在 default.js 專案檔案中，使用下列可篩選出已完成項目的程式碼，來取代現有的 **RefreshTodoItems** 函數：

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete:false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  在應用程式中檢查清單中的其中一個項目，然後按一下 **[重新整理]** 按鈕。

	請注意，檢查的項目現在會從清單中消失。每次的重新整理均會造成對行動服務做一次往返，現在會傳回篩選的資料。

這將結束**開始使用資料**教學課程。

後續步驟
--------

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js)
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-js)
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-js)
    了解如何將非常基本的推播通知傳送到應用程式。

-   [行動服務 HTML/JavaScript 作法概念參考](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)
    深入了解如何將行動服務搭配 HTML 及 JavaScript 使用。


