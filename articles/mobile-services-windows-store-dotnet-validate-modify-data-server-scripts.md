<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="User server scripts to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use server scripts to validate, modify, and augment data for your Windows Store app with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

使用伺服器指令碼在行動服務中驗證與修改資料
==========================================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows 市集 C#") [Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows 市集 JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML") [Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ ".NET 後端") | [JavaScript 後端](zh-tw/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/ "JavaScript 後端")

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。因為伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 Windows 市集應用程式，以充分利用這些新行為。

[觀看教學課程](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services) [播放影片](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services) 09:54

本教學課程將逐步引導您完成下列基本步驟：

1.  [新增字串長度驗證](#string-length-validation)
2.  [更新用戶端以支援驗證](#update-client-validation)
3.  [插入時新增時間戳記](#add-timestamp)
4.  [更新用戶端以顯示時間戳記](#update-client-timestamp)

本教學課程會以上一堂教學課程[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)。

新增驗證
--------

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

      ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

     ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  按一下 **[指令碼]**，然後選取 **Insert** 操作。

     ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  使用下列函數來取代現有的指令碼，然後按一下 **[儲存]**：

        function insert(item, user, request) {
           if (item.text.length > 10) {
             request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
           } else {
             request.execute();
             }
         }

    本指令碼會檢查 **TodoItem.text** 屬性的長度，並在長度超過 10 個字元時傳送錯誤回應。否則，系統會呼叫 **execute** 方法以完成插入。

    **注意**

    您可以將位於 **[指令碼]** 索引標籤上的已註冊指令碼移除，方法是依序按一下 **[清除]**、**[儲存]**。

更新用戶端
----------

現在，行動服務正在驗證資料並傳送錯誤回應，您必須更新您的應用程式以便處理驗證的錯誤回應。

1.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成教學課程[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)時所修改的專案。

2.  按 **F5** 鍵以執行應用程式，然後在 **[Insert a TodoItem]** 中鍵入超過 10 個字元的文字，並按一下 **[儲存]**。

    Notice that the app raises an unhandled **MobileServiceInvalidOperationException** as a result of the 400 response (Bad Request) returned by the mobile service. 

3.  開啟檔案 MainPage.xaml.cs，然後新增下列 **using** 陳述式：

        using Windows.UI.Popups;

4.  使用下列內容來取代現有的 **InsertTodoItem** 方法：

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database.
            // When the operation completes and Mobile Services has
            // assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
                catch (MobileServiceInvalidOperationException e)
            {
                 MessageDialog errormsg = new MessageDialog(e.Message, 
                   string.Format("{0} (HTTP {1})",                     
                   e.Response.ReasonPhrase,
                   (int)e.Response.StatusCode));
                 var ignoreAsyncOpResult = errormsg.ShowAsync();
             }
         }

     This version of the method includes error handling for the **MobileServiceInvalidOperationException** that displays the error response in a popup.

新增時間戳記
------------

上一個工作驗證了插入操作，並已接受或拒絕此操作。現在，您將更新插入資料，方法是在插入物件之前，使用可將時間戳記屬性新增至物件的伺服器指令碼。

**注意**

此處所示範的 **createdAt** 時間戳記屬性現為備援屬性。行動服務會自動建立每個資料表的 **\_\_createdAt** 系統屬性。您只需將下列成員新增至 TodoItem 類別，便可在應用程式中使用此系統屬性

``` {}
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
```

1.  在[管理入口網站](https://manage.windowsazure.com/)的 **[指令碼]** 索引標籤中，使用下列函數來取代目前的 **Insert** 指令碼，然後按一下 **[儲存]**。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
             }
         }

    此函數可強化上一個 insert 指令碼，方法是在呼叫 **request**.**execute** 來插入物件之前，先將新的 **createdAt** 時間戳記屬性新增至物件。

    **注意**

    第一次執行此 insert 指令碼時，必須啟用動態結構描述。在啟用動態結構描述的情況下，行動服務會在首次執行時自動將 **createdAt** 欄新增至 **TodoItem** 資料表。依預設，新行動服務的動態結構描述為已啟用，並在將應用程式發佈至 Windows 市集之前將它停用。

2.  在 Visual Studio 中，按 **F5** 鍵以執行應用程式，然後在 **[Insert a TodoItem]** 中鍵入文字 (少於 10 個字元)，並按一下 **[儲存]**。

    Notice that the new timestamp does not appear in the app UI.

3.  回到管理入口網站中，按一下 **[todoitem]** 資料表中的 **[瀏覽]** 索引標籤。

     Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.

接下來，您必須更新 Windows 市集應用程式以便顯示此新資料欄。

重新更新用戶端
--------------

針對無法序列化成已定義類型屬性的回應，行動服務用戶端將忽略該回應中的任何資料。最後步驟是更新用戶端以顯示此新資料。

1.  在 Visual Studio 中開啟檔案 MainPage.xaml.cs，然後使用下列定義來取代現有的 **TodoItem** 類別：

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
            
            [JsonProperty(PropertyName = "createdAt")]
            public DateTime
            CreatedAt { get; set; }
         }

    此新類別定義將包括如可為 Null 之 DateTime 類型的新時間戳記屬性。

    **注意**

    `DataMemberAttribute` 會告訴用戶端，將應用程式中的新 `CreatedAt` 屬性對應到 TodoItem 資料表中所定義、且使用不同大小寫的 `createdAt` 欄。使用此屬性，您的應用程式便可擁有物件上的屬性名稱，且該名稱會與 SQL Database 中的資料欄名稱不同。若是沒有這個屬性，則會因大小寫差異而發生錯誤。

2.  在 MainPage.xaml 檔案中的 **CheckBoxComplete** 元素正下方新增下列 XAML 元素：

         <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

    This displays the new **CreatedAt** property in a text box. 

3.  按 **F5** 鍵以執行應用程式。

    Notice that the timestamp is only displayed for items inserted after you updated the insert script.

4.  使用下列程式碼來取代現有的 **RefreshTodoItems** 方法：

        private async void RefreshTodoItems()
        {
             // This query filters out completed TodoItems and 
             // items without a timestamp. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;
         }

    This method updates the query to also filter out items that do not have a timestamp value.

5.  按 **F5** 鍵以執行應用程式。

     Notice that all items created without timestamp value disappear from the UI.

您已完成此使用資料教學課程。

後續步驟
--------

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

-   [使用指令碼來授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    
    了解如何根據通過驗證使用者的 ID 來篩選資料。

-   [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)
    
    了解如何將非常基本的推播通知傳送到應用程式。

-   [行動服務伺服器指令碼參照](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    深入了解註冊與使用伺服器指令碼。

-   [行動服務 .NET 作法概念參考資料](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    
    深入了解如何使用搭配 .NET 的行動服務。


