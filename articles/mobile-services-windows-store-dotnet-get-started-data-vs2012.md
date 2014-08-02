<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

開始以 Visual Studio 2012 使用行動服務中的資料
==============================================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012 "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

本主題將示範如何使用 Azure 行動服務，以在 Windows 市集應用程式中運用資料。在本教學課程中，您將下載 Visual Studio 2012 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。您可以按一下右方的短片，來檢視本教學課程的影片版。

[觀看教學指南](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services) [播放視訊](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services) 15:33

**注意**

本教學課程會將行動服務功能新增至 Visual Studio 2012 中建立的 Windows 市集應用程式。Visual Studio 2013 包含新功能，可讓您輕鬆地將您的 Windows 市集應用程式與行動服務連接。如需詳細資訊，請參閱[開始使用行動服務中的資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案](#download-app)
2.  [建立行動服務](#create-service)
3.  [新增用於儲存的資料表](#add-table)
4.  [更新應用程式以使用行動服務](#update-app)
5.  [針對行動服務進行應用程式測試](#test-app)

**注意**

若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28)。

下載專案下載 GetStartedWithData 專案
------------------------------------

本教學課程以 [GetStartedWithData 應用程式](http://go.microsoft.com/fwlink/?LinkId=262308) (英文) (Windows 市集應用程式) 為基礎而建立。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務快速入門所產生的應用程式兩者的 UI 完全相同。

1.  從[開發人員程式碼範例網站](http://go.microsoft.com/fwlink/?LinkId=262308) (英文) 中下載 C\# 版本的 GetStartedWithData 範例應用程式。

    ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png)

2.  在 Visual Studio 2012 Express for Windows 8 中，開啟下載專案並檢查 MainPage.xaml.cs 檔案。

       Notice that added **TodoItem** objects are stored in an in-memory **ObservableCollection<TodoItem>**.

3.  按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式的 **[Insert a TodoItem]** 中鍵入一些文字，然後按一下 **[儲存]**。

    ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png)

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

2.  在左側窗格中依序選取 **[線上]** 類別、**[Include Prerelease]**，搜尋 `WindowsAzure.MobileServices`，並按一下 **[Azure 行動服務]** 封裝上的 **[安裝]**，然後接受授權協定。

    ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png)

    這會將行動服務用戶端程式庫新增至專案。

3.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

4.  按一下 **[儀表板]** 索引標籤並記下 **[網站 URL]**，然後按一下 **[管理金鑰]** 並記下 **[應用程式金鑰]**。

    ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png)

    從應用程式程式碼存取行動服務時，您將會用到這些值。

5.  在 Visual Studio 中，開啟檔案 App.xaml.cs 並新增或取消註解下列 `using` 陳述式：

        using Microsoft.WindowsAzure.MobileServices;

6.  在此相同檔案中，依序取消註解定義 **MobileService** 變數的程式碼，並提供 **MobileServiceClient** 建構函式中行動服務的 URL 和應用程式金鑰。

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    這會建立用來存取行動服務的新 **MobileServiceClient** 執行個體。

7.  在檔案 MainPage.xaml.cs 中，新增或取消註解下列 `using`陳述式：

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  在相同檔案中，使用下列程式碼來取代 **TodoItem** 類別定義：

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  針對定義現有 **items** 集合的程式碼行進行註解，然後取消註解下列程式碼行：

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

       This code creates a mobile services-aware binding collection (**items**) and a proxy class for the SQL Database table **TodoItem** (**todoTable**). 

10.  在 **InsertTodoItem** 方法中，移除設定 **TodoItem**.**Id** 屬性的程式碼行，將 **async** 修正因子新增至方法，並取消註解下列程式碼行：

        await todoTable.InsertAsync(todoItem);

     此程式碼會將新的項目插入資料表中。

11.  在 **RefreshTodoItems** 方法中，將 **async** 修正因子新增至方法，然後取消註解下列程式碼行：

        items = await todoTable.ToCollectionAsync();

     This sets the binding to the collection of items in the todoTable, which contains all TodoItem objects returned from the mobile service. 

12.  在 **UpdateCheckedTodoItem** 方法中，將 **async** 修正因子新增至方法，並取消註解下列程式碼行：

        await todoTable.UpdateAsync(item);

     This sends an item update to the mobile service.

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

測試應用程式在您的新行動服務中測試應用程式
------------------------------------------

1.	在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.	和之前一樣，在 **[Insert a TodoItem]** 中鍵入文字，然後按一下 **[儲存]**。

    This sends a new item as an insert to the mobile service.

3.	在[管理入口網站](https://manage.windowsazure.com/)中按一下 **[行動服務]**，然後按一下您的行動服務。

4.	按一下 **[資料]** 索引標籤，然後按一下 **[瀏覽]**。

    ![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png)
          
    Notice that the **TodoItem** table now contains data, with id values generated by Mobile Services, and that columns have been automatically added to the table to match the TodoItem class in the app.

5.	在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 **[重新整理]**。

	請注意，完成值已從 **[false]** 變更為 **[true]**。

6.	在 MainPage.xaml.cs 專案檔案中，使用下列可篩選出已完成項目的程式碼，來取代現有的 **RefreshTodoItems** 方法：

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  在應用程式中檢查清單中的其中一個項目，然後按一下 **[重新整理]** 按鈕。

	請注意，檢查的項目現在會從清單中消失。每次的重新整理均會造成對行動服務做一次往返，現在會傳回篩選的資料。

這將結束**開始使用資料**教學課程。

後續步驟
--------

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)
    了解如何將非常基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念參考資料](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    深入了解如何使用搭配 .NET 的行動服務。


