<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-wp8" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Phone 8) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Phone app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

使用 .Net 後端在行動服務中驗證與修改資料
========================================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows 市集 C#") [Windows 市集 JavaScript](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows 市集 JavaScript") [Windows Phone](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ ".NET 後端") | [JavaScript 後端](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "JavaScript 後端")

本主題將說明如何在您的 .Net 後端 Windows Azure 行動服務中使用程式碼，對資料進行驗證和修改。.Net 後端服務是使用 Web API 架構建置的 HTTP 服務。如果您熟悉以 Web API 架構定義的 `ApiController` 類別，您將會覺得行動服務所提供的 `TableController` 類別非常直接易懂。`TableController` 衍生自 `ApiController` 類別，可提供用來處理資料庫資料表的附加功能。它可用來對要插入和更新的資料執行作業，包括本教學課程中示範的驗證和資料修改。

本教學課程將逐步引導您完成下列基本步驟：

1.  [新增字串長度驗證](#string-length-validation)
2.  [更新用戶端以支援驗證](#update-client-validation)
3.  [測試長度驗證](#test-length-validation)
4.  [新增 CompleteDate 的時間戳記](#add-timestamp)
5.  [更新用戶端以顯示 CompleteDate](#update-client-timestamp)

本教學課程會以先前的教學課程[開始使用](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/)或[開始使用資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須先完成[開始使用](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/)或[開始使用資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)教學課程。

新增驗證
--------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

更新用戶端
----------

現在，行動服務已經過設定而會驗證資料，並針對無效的文字長度傳送錯誤回應，您必須更新應用程式以便處理驗證的錯誤回應。用戶端應用程式呼叫 `IMobileServiceTable<TodoItem].InsertAsync()` 時所產生的錯誤會顯示為 `MobileServiceInvalidOperationException`。

1.  在 Visual Studio 的 [方案總管] 視窗中導覽至用戶端專案，然後開啟 MainPage.xaml.cs 檔案。將下列 using 陳述式新增至檔案。

         using Newtonsoft.Json.Linq;

2.  在 MainPage.xaml.cs 中，以下列程式碼取代現有的 **InsertTodoItem** 方法：

         private async void InsertTodoItem(TodoItem todoItem)
         {
        // This code inserts a new TodoItem into the database.When the operation completes
        // and Mobile Services has assigned an Id, the item is added to the CollectionView
        MobileServiceInvalidOperationException invalidOpException = null;
        try
             {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
             }
        catch (MobileServiceInvalidOperationException e)
             {
        invalidOpException = e;
             }
        if (invalidOpException != null)
             {
        string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
        var responseContent = JObject.Parse(strJsonContent);
        MessageBox.Show(string.Format("{0} (HTTP {1})",
        (string)responseContent["message"], (int)invalidOpException.Response.StatusCode),
        invalidOpException.Message, MessageBoxButton.OK);
             }
         }

	此版本的方法包含會在訊息方塊中顯示錯誤回應的 **MobileServiceInvalidOperationException**。

測試長度驗證
------------

1.  在 Visual Studio 中，設定所需的 Windows Phone 部署目標。接著，在 [方案總管] 視窗中，以滑鼠右鍵按一下用戶端應用程式專案，然後按一下 **[偵錯]**、**[Start new instance]**。

2.  為新的 todo 項目輸入長度超過 10 個字元的文字，然後按一下 **[儲存]**。

    ![](./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length.png)

3.  此時會出現如下的訊息對話方塊，以回應無效文字。

    ![](./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png)

新增 CompleteDate 的時間戳記欄位
--------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

更新用戶端以顯示 CompleteDate
-----------------------------

最後的步驟是更新用戶端以顯示新的 **CompleteDate** 資料。

1.  在 Visual Studio 的 [方案總管] 中，在 Todolist 用戶端專案中開啟 MainPage.xaml 檔案，然後以下列定義取代 **StackPanel** 元素。接著，請儲存檔案。這會變更 **CheckBoxComplete** 的事件處理常式，因此我們必須處理 `click` 事件。此外，我們也會在核取方塊旁新增文字區塊，並將其繫結至完成日期時間戳記。

         <StackPanel Orientation="Horizontal">
        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}"
        Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" 
        VerticalAlignment="Center" />
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" 
        VerticalAlignment="Center" />
        </StackPanel>

2.  在 Visual Studio 的 [方案總管] 中，在 Todolist 用戶端專案中開啟 MainPage.xaml.cs 檔案，然後將 `CheckBoxComplete_Checked` 事件處理常式取代為 `CheckBoxComplete_Clicked` 事件處理常式，如下所示。這就是我們在項目完成後所看見的完成日期。

         private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
         {
        CheckBox cb = (CheckBox)sender;
        TodoItem item = cb.DataContext as TodoItem;
        UpdateCheckedTodoItem(item);
         }

3.  接著，在 MainPage.xaml.cs 檔案中，將現有的 **TodoItem** 類別取代下列以新的 **CompleteDate** 屬性做為可為 Null 之類型的定義。

         public class TodoItem
         {
        public string Id { get; set; }
        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }
        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }        
        [JsonProperty(PropertyName = "CompleteDate")]
        public DateTime
         CompleteDate { get; set; }
         }

    > [WACOM.NOTE] `DataMemberAttribute` 會指示用戶端，將應用程式中的新 `CompleteDate` 屬性對應至 TodoItem 資料表中定義的 `CompleteDate` 資料行。使用此屬性，您的應用程式便可擁有物件上的屬性名稱，且該名稱會與 SQL Database 中的資料欄名稱不同。

4.  在 MainPage.xaml.cs 中，移除或註解化現有 **RefreshTodoItems** 方法中的 `.Where` 子句函數，使完成的 todoitem 納入結果中。

             // This query filters out completed TodoItems and 
        // items without a timestamp. 
        items = await todoTable
        //.Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

5.  在 MainPage.xaml.cs 中，依照下列方式更新 **UpdateCheckedTodoItem** 方法，使項目可在更新之後重新整理，並使完成的項目不會從清單中移除。接著，請儲存檔案。

         private async void UpdateCheckedTodoItem(TodoItem item)
         {
        // This code takes a freshly completed TodoItem and updates the database.
        await todoTable.UpdateAsync(item);
        //items.Remove(item);
        RefreshTodoItems();
         }

6.  在 Visual Studio 的 [方案總管] 視窗中，以滑鼠右鍵按一下 **[方案]**，然後按一下 **[Rebuild Solution]**，以重新建置用戶端和 .NET 後端服務。驗證專案已順利建置，且未發生錯誤。

7.  按 **F5** 鍵，在本機執行用戶端應用程式和服務。新增某些項目，並加以點選將其標示為「完成」，以檢視更新的 **CompleteDate** 時間戳記。

8.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下 Todolist 服務專案，然後按一下 **[發佈]**。使用您從 Azure 入口網站下載的發佈設定檔案，將您的 .NET 後端服務發佈至 Microsoft Azure。

9.  將行動服務位址的連線取消註解，以更新用戶端專案的 App.xaml.cs 檔案。對 Azure 帳戶中代管的 .NET 後端進行應用程式測試。

後續步驟
--------

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

-   [使用者的服務端授權](zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/)
    了解如何根據已驗證的使用者 ID 來篩選資料。

-   [開始使用推播通知](zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/)
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念性參考](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    深入了解如何搭配使用行動服務與 .NET。


