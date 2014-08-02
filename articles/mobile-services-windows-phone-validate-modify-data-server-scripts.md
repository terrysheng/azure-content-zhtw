<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-wp8" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Phone 8 app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

使用伺服器指令碼在行動服務中驗證與修改資料
==========================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML") [Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")
[.NET 後端](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ ".NET 後端") | [JavaScript 後端](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "JavaScript 後端")

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。因為伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 Windows Phone 8 應用程式，以充分利用這些新行為。

[觀賞教學課程](http://go.microsoft.com/fwlink/?LinkId=298629) [播放視訊](http://go.microsoft.com/fwlink/?LinkId=298629) 11:36

本教學課程將逐步引導您完成下列基本步驟：

1.  [新增字串長度驗證](#string-length-validation)
2.  [更新用戶端以支援驗證](#update-client-validation)
3.  [插入時新增時間戳記](#add-timestamp)
4.  [更新用戶端以顯示時間戳記](#update-client-timestamp)

本教學課程會以上一堂教學課程[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)。

新增驗證
--------

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  按一下 **[指令碼]**，然後選取 **[插入]** 作業。

    ![](./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  以下列函數取代現有的指令碼，然後按一下 **[儲存]**。

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

1.  在 Visual Studio 2012 Express for Windows Phone 中，開啟您在完成教學課程[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)時所修改的專案。

2.  按 **F5** 鍵以執行應用程式，在文字方塊中輸入超過 10 個字元的文字，然後按一下 **[儲存]**。

	請注意，應用程式會提出未處理的 **MobileServiceInvalidOperationException** 做為由行動服務傳回的 400 回應的結果 (不正確的要求)。

3.  開啟檔案 MainPage.xaml.cs，然後以下列程式碼取代現有的 **InsertTodoItem** 方法：

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
                MessageBox.Show(e.Message,
                    string.Format("{0} (HTTP {1})",
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode), 
					MessageBoxButton.OK);
            }
        }

	此版本的方法包含會在 MessageBox 中顯示錯誤回應的 **MobileServiceInvalidOperationException** 錯誤處理。

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

    首次執行插入指令碼時，必須啟用動態結構描述。在啟用動態結構描述的情況下，行動服務會在首次執行時自動將 **createdAt** 欄新增至 **TodoItem** 資料表。依預設會啟用新行動服務的動態結構描述，您應該先加以停用，再將應用程式發佈至 Windows Phone 市集。

2.  在 Visual Studio 中，按 **F5** 鍵以執行應用程式，在文字方塊中輸入少於 10 個字元的文字，然後按一下 **[儲存]**。

	請注意新的時間戳記不會出現在應用程式 UI 中。

3.  回到管理入口網站中，按一下 **[todoitem]** 資料表中的 **[瀏覽]** 索引標籤。

	請注意現在會有一個 **createdAt** 資料欄，且新插入的項目也具有時間戳記值。

接下來，您必須更新 Windows Phone 應用程式以顯示此新資料行。

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
            public DateTime? CreatedAt { get; set; }
        }

    此新類別定義將包括如可為 Null 之 DateTime 類型的新時間戳記屬性。

    **注意**

    **DataMemberAttribute** 會告訴用戶端，將應用程式中的新 **CreatedAt** 屬性對應到 TodoItem 資料表中所定義、且使用不同大小寫的 **createdAt** 欄。使用此屬性，您的應用程式便可擁有物件上的屬性名稱，且該名稱會與 SQL Database 中的資料欄名稱不同。若是沒有這個屬性，則會因大小寫差異而發生錯誤。

2.  在 MainPage.xaml 檔案中的 **CheckBoxComplete** 元素正下方新增下列 XAML 元素：

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

	如此會在文字方快中顯示近的 **CreatedAt** 屬性。

3.  按 **F5** 鍵以執行應用程式。

	“請注意，只有在您更新 insert 指令碼後插入的項目，才會顯示時間戳記。

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

	此方法會更新查詢，篩選出沒有時間戳記的項目。

5.  按 **F5** 鍵以執行應用程式。

	請注意，所有已建立但不含時間戳記值的項目，都會從 UI 中消失。

您已完成此使用資料教學課程。

後續步驟
--------

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：[使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8)。

-   [使用指令碼授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8)
    <br/>了解如何根據通過驗證使用者的 ID 來篩選資料。

-   [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)
    <br/>了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務伺服器指令碼參考](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>深入了解如何註冊和使用伺服器指令碼。


