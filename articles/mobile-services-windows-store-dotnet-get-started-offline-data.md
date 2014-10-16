
<properties linkid="develop-mobile-tutorials-get-started-offline-data-dotnet" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use offline data in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="wesmc" />

開始在行動服務中使用離線資料
============================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows 市集 C#")

本主題將說明如何使用 Azure 行動服務的離線功能。Azure 行動服務的離線功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。離線功能可讓您在重新上線時同步處理本機變更與行動服務。

在本教學課程中，您將會更新[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-windows-store-get-started/)或[開始使用資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/)教學課程中的應用程式，以支援 Azure 行動服務的離線功能。接著，您會在中斷連線的離線狀態下新增資料、將這些項目同步處理至線上資料庫，然後登入 Windows Azure 管理入口網站，以檢視執行應用程式時對資料所做的變更。

> [WACOM.NOTE] 本教學課程旨在協助您深入了解如何透過行動服務，使用 Windows Azure 儲存並擷取 Windows 市集應用程式中的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-windows-store-get-started/)。

本教學課程將逐步引導您完成下列基本步驟：

1.  [更新應用程式以支援離線功能](#enable-offline-app)
2.  [在離線狀態下測試應用程式](#test-offline-app)
3.  [更新應用程式以重新連接您的行動服務](#update-online-app)
4.  [測試連接到行動服務的應用程式](#test-online-app)

本教學課程需要下列各項：

-   執行於 Windows 8.1 的 Visual Studio 2013。
-   完成[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-windows-store-get-started/)或[開始使用資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/)教學課程。
-   Windows Azure 行動服務 SDK NuGet 封裝 1.3.0 版 (alpha)
-   Windows Azure 行動服務 SQLite Store NuGet 封裝 0.1.0 (alpha)
-   SQLite for Windows 8.1

> [WACOM.NOTE] 若要完成此教學課程，您需要 Windows Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Windows Azure 免費試用](http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28)。

<a name="enable-offline-app"></a>更新應用程式以支援離線功能
--------------------------

Azure 行動服務的離線功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。若要在您的應用程式中使用這些功能，您必須將 `MobileServiceClient.SyncContext` 初始化至本機存放區。接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。

本節將以 SQLite 做為離線功能的本機存放區。

> [WACOM.NOTE] 您可以略過本節，直接下載某個已具有離線支援的「開始使用」專案。若要下載具有離線支援的專案，請參閱[開始使用離線範例](http://go.microsoft.com/fwlink/?LinkId=394777)。

1.  安裝 SQLite。您可以從連結 [SQLite for Windows 8.1](http://go.microsoft.com/fwlink/?LinkId=394776) 加以安裝。

    > [WACOM.NOTE] 如果您要使用 Internet Explorer，當您按一下安裝 SQLite 的連結時，系統會提示您下載 .zip 檔案格式的 .vsix。請以 .vsix 副檔名將此檔案儲存至您的硬碟，而不要使用 .zip。在 Windows 檔案總管中按兩下此 .vsix 檔案，以執行安裝。

2.  在 Visual Studio 中，開啟您在[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-windows-store-get-started/)或[開始使用資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/)教學課程中完成的專案。將 **[Windows 延伸]** 參考新增至 **[SQLite for Windows Runtime (Windows 8.1)]**。

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png)

    > [WACOM.NOTE] SQLite Runtime 會要求您變更要建置到 **x86**、**x64** 或 **ARM** 之專案的處理器架構。**[任何 CPU]** 不受支援。

3.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下您的用戶端應用程式專案，然後按一下 **[管理 NuGet 封裝]**，以執行 NuGet 封裝管理員。搜尋 **SQLiteStore**，以安裝 **WindowsAzure.MobileServices.SQLiteStore** 封裝。

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png)

4.  在 Visual Studio 的 [方案總管] 中，開啟 MainPage.xaml.cs 檔案。在檔案頂端新增下列 using 陳述式。

         using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

5.  在 Mainpage.xaml.cs 中，將 `todoTable` 的宣告取代為屬於 `IMobileServicesSyncTable` 類型、藉由呼叫 `MobileServicesClient.GetSyncTable()` 而初始化的宣告。

         //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

6.  在 MainPage.xaml.cs 中更新 `TodoItem` 類別，使該類別包含 **Version** 系統屬性，如下所示。

         public class TodoItem
         {
        public string Id { get; set; }
        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }
        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
        [Version]
        public string Version { get; set; }
         }

7.  在 MainPage.xaml.cs 中更新 `OnNavigatedTo` 事件處理常式，使其對 SQLite 存放區初始化用戶端同步內容。此 SQLite 存放區可使用與行動服務資料表的結構描述相符的資料表建立，但其中必須包含在上一個步驟中新增的 **Version** 系統屬性。

         protected async override void OnNavigatedTo(NavigationEventArgs e)
         {
        if (!App.MobileService.SyncContext.IsInitialized)
             {
        var store = new MobileServiceSQLiteStore("localsync12.db");
        store.DefineTable<TodoItem>();
        await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
             }
        RefreshTodoItems();
         }

8.  在 Visual Studio 的 [方案總管] 中，開啟 MainPage.xaml 檔案。找出哪個 Grid 元素包含標示為「查詢和更新資料」****的 StackPanel。新增下列 UI 程式碼，以將資料列定義中的元素取代為 ListView 起始標籤的元素。

    此程式碼會將資料列和資料行定義新增至格線，以配置元素。它也會為**推送**和**提取**作業新增兩個具有 click 事件處理常式的按鈕控制項。這些按鈕會定位在名為 ListItems 的 `ListView` 正上方。儲存檔案。

         <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <StackPanel Grid.Row="0" Grid.ColumnSpan="2">
        <local:QuickStartTask Number="2" Title="Query, Update, and Synchronize Data" 
        Description="Use the Pull and Push buttons to synchronize the local store with the server" />
        </StackPanel>
        <Button Grid.Row="1" Grid.Column="0" Margin="5,5,0,0" Name="ButtonPush"
        Click="ButtonPush_Click" Width="80" Height="34">â¬† Push</Button>
        <Button Grid.Row="1" Grid.Column="1" Margin="5,5,0,0"  Name="ButtonPull" 
        Click="ButtonPull_Click" Width="80" Height="34">â¬‡ Pull</Button>
        <ListView Name="ListItems" SelectionMode="None" Margin="0,10,0,0" Grid.ColumnSpan="2" Grid.Row="2">

9.  在 MainPage.xaml.cs 中，為 **[推送]** 和 **[提取]** 按鈕新增按鈕 click 事件處理常式，並儲存檔案。

         private async void ButtonPull_Click(object sender, RoutedEventArgs e)
         {
        Exception pullException = null;
        try
             {
        await todoTable.PullAsync();
        RefreshTodoItems();
             }
        catch (Exception ex)
             {
        pullException = ex;
             }
        if (pullException != null) {
        MessageDialog d = new MessageDialog("Pull failed:" + pullException.Message +
        "\n\nIf you are in an offline scenario, " + 
        "try your Pull again when connected with your Mobile Serice.");
        await d.ShowAsync();
             }
         }
        private async void ButtonPush_Click(object sender, RoutedEventArgs e)
         {
        string errorString = null;
        try
             {
        await App.MobileService.SyncContext.PushAsync();
        RefreshTodoItems();
             }
        catch (MobileServicePushFailedException ex)
             {
        errorString = "Push failed because of sync errors: " + 
        ex.PushResult.Errors.Count() + ", message:" + ex.Message;
             }
        catch (Exception ex)
             {
        errorString = "Push failed:" + ex.Message;
             }
        if (errorString != null) {
        MessageDialog d = new MessageDialog(errorString + 
        "\n\nIf you are in an offline scenario, " + 
        "try your Push again when connected with your Mobile Serice.");
        await d.ShowAsync();
             }
         }

10. 目前還不要執行應用程式。請按 **F7** 鍵，以重新建置專案。驗證未發生任何建置錯誤。

<a name="test-offline-app"></a>在離線狀態下測試應用程式
------------------------

在本節中，您將中斷行動服務的應用程式連線，以模擬離線狀態。接著，您會新增某些將保存在本機存放區中的資料項目。

請注意，在本節中，應用程式不應連接到任何行動服務。因此，如果您測試 **[推送]** 和 **[提取]** 按鈕，將會擲出例外狀況。在下一節中，您會將此用戶端應用程式重新連接到行動服務，以測試將存放區與行動服務資料庫同步處理的 **[推送]** 和 **[提取]** 作業。

1.  在 Visual Studio 的 [方案總管] 中，開啟 App.xaml.cs。將您 URL 的 "**azure-mobile.net**" 取代為 "**azure-mobile.xxx**"，以將 **MobileServiceClient** 的初始化變更為無效位址。接著，請儲存檔案。

          public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://your-mobile-service.azure-mobile.xxx/",
        "AppKey"
         );

2.  在 Visual Studio 中按 **F5**，以建置並執行此應用程式。輸入新的 todo 項目，然後按一下 **[儲存]**。新的 todo 項目在可推送至行動服務之前，都只會存留在本機存放區中。用戶端應用程式的行為，會如同它已連接到支援所有建立、讀取、更新、刪除 (CRUD) 作業的行動服務。

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png)

3.  關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

<a name="update-online-app"></a>更新應用程式以重新連接您的行動服務
----------------------------------

在本節中，您會將應用程式重新連接至行動服務。您將藉此模擬應用程式在行動服務中從離線狀態恢復為線上狀態的情境。

1.  在 Visual Studio 的 [方案總管] 中，開啟 App.xaml.cs。將您 URL 的 "**azure-mobile.xxx**" 取代為 "**azure-mobile.net**"，以將 **MobileServiceClient** 的初始化重新變更為正確的位址。接著，請儲存檔案。

          public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://your-mobile-service.azure-mobile.net/",
        "Your AppKey"
         );

<a name="test-online-app"></a>測試連接到行動服務的應用程式
----------------------------

在本節中，您會測試將本機存放區與行動服務資料庫同步處理的推送和提取作業。

1.  在 Visual Studio 中按 **F5** 鍵，以重新建置並執行此應用程式。請注意，雖然應用程式此時已連接到行動服務，但資料看起來仍會與離線狀態下相同。這是因為此應用程式一律會使用指向本機存放區的 `IMobileServiceSyncTable`。

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png)

2.  登入 Microsoft Azure 管理入口網站，並檢視您的行動服務的資料庫。如果您的行動服務使用 JavaScript 後端，則您可以從行動服務的 **[資料]** 索引標籤瀏覽資料。如果您在行動服務中使用 .NET 後端，您可以在「SQL Azure 擴充功能」中對您的資料庫按一下 **[管理]** 按鈕，以對您的資料表執行查詢。

    請注意，資料並未與資料庫和本機存放區進行同步處理。

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png)

3.  在應用程式中，按 **[推送]** 按鈕。這會使應用程式依序呼叫 `MobileServiceClient.SyncContext.PushAsync` 和 `RefreshTodoItems`，而使用本機存放區中的項目重新整理應用程式。此推送作業會使行動服務資料庫接收來自該存放區的資料。但本機存放區並不會接收行動服務資料庫中的項目。

    推送作業會從 `MobileServiceClient.SyncContext` 執行 (而非 `IMobileServicesSyncTable`)，並推送該同步內容所有相關資料表的變更。這是為了解說資料表之間有所關聯的情況。

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png)

4.  在應用程式中，新增若干新項目至本機存放區。

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png)

5.  這次請在應用程式中按 **[提取]** 按鈕。應用程式只會呼叫 `IMobileServiceSyncTable.PullAsync()` 和 `RefreshTodoItems`。請注意，行動服務資料庫中的所有資料都會提取至本機存放區，並顯示在應用程式中。但也請注意，本機存放區中的所有資料仍會推送至行動服務資料庫。這是因為**提取一律會先執行推送**。

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png)

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png)

摘要
----

為了支援行動服務的離線功能，我們使用了 `IMobileServiceSyncTable` 介面，並對本機存放區初始化 `MobileServiceClient.SyncContext`。在此案例中，我們以本機存放區做為 SQLite 資料庫。

正常情況下，在行動服務的 CRUD 作業執行時，應用程式會如同仍處於連接狀態，但所有的作業都會對本機存放區執行。

當我們要同步處理本機存放區與伺服器時，我們使用了 `IMobileServiceSyncTable.PullAsync` 和 `MobileServiceClient.SyncContext.PushAsync` 方法。

-   為了將變更推送至伺服器，我們呼叫了 `IMobileServiceSyncContext.PushAsync()`。此方法是 `IMobileServicesSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更：

    只有以某種方式在本機上修改過的記錄 (透過 CRUD 作業)，才會傳送至伺服器。

-   為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了 `IMobileServiceSyncTable.PullAsync`。

    提取一律會先發出推送動作。

    此外，也有 **PullAsync()** 的多載可允許指定查詢。請注意，在行動服務的預覽版離線支援中，**PullAsync** 將會讀取對應資料表 (或查詢) 中的所有資料列 -- 舉例來說，它不會嘗試僅讀取比前次同步還新的資料列。如果資料列已存在於本機同步資料表中，則將保持不變。

後續步驟
--------

-   [處理行動服務的離線支援衝突](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/)

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Test the app in an offline Scenario]: #test-offline-app
[Update the app to reconnect your mobile service]: #update-online-app
[Test the app connected to the Mobile Service]: #test-online-app
[Next Steps]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png


<!-- URLs. -->
[Handling conflicts with offline support for Mobile Services]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Getting Started Offline Sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Getting Started]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Get started with data]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Get started with Mobile Services]: /zh-tw/documentation/articles/mobile-services-windows-store-get-started/
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776


