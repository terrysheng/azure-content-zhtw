<properties urlDisplayName="Using Offline Data" pageTitle="使用行動服務 (Windows 市集) 中的離線資料 | 行動開發人員中心" metaKeywords="" description="了解如何使用 Azure 行動服務在您的 Windows 市集應用程式中快取及同步處理離線資料" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# 使用行動服務中的離線資料同步

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本教學課程說明如何使用 Azure 行動服務在 Windows Universal Store 應用程式中新增離線支援。當您的應用程式處於離線狀態時，離線支援可讓您與本機資料庫互動。一旦您的應用程式與後端資料庫連線時，便可使用離線功能同步處理本機變更。 
</p>
<p>如果想要看影片，右邊片段播放的步驟與本教學課程相同。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">14:36</span></div>
</div>


在本教學課程中，您將會更新[開始使用行動服務]教學課程中的 Universal 應用程式，以支援 Azure 行動服務的離線功能。接著，您會在中斷連線的離線狀態下新增資料、將這些項目同步處理至線上資料庫，然後登入 Azure 管理入口網站，以檢視執行應用程式時對資料所做的變更。


>[WACOM.NOTE] 本教學課程旨在協助您深入了解如何透過行動服務，來使用 Azure 儲存並擷取 Windows 市集應用程式中的資料。如果這是您第一次接觸行動服務，請先完成[開始使用行動服務]教學課程。
>
>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動服務，即使在試用期結束之後仍可繼續使用這些服務。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。 
>
>您仍然可在[適用於 Visual Studio 2012 的 Windows Phone 8 教學課程]取得適用於 Visual Studio 2012 的舊 Windows Phone 8 課程。


本教學課程將逐步引導您完成下列基本步驟：

1. [更新應用程式以支援離線功能]
2. [在離線狀態下測試應用程式] 
3. [更新應用程式以重新連接您的行動服務]
4. [測試連接到行動服務的應用程式]

本教學課程需要下列項目：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 完成[開始使用行動服務]。
* [Azure Mobile Services SDK 1.3.0 版 (或更新版本)][行動服務 SDK Nuget]
* [Azure Mobile Services SQLite Store 1.0.0 版 (或更新版本)][SQLite Store Nuget]
* SQLite for Windows 8.1

>[AZURE.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。 

## <a name="enable-offline-app"></a>更新應用程式以支援離線功能

Azure 行動服務的離線功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。若要在您的應用程式中使用這些功能，您必須將 `MobileServiceClient.SyncContext` 初始化至本機存放區。接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。在本教學課程中，我們會在本機存放區使用 SQLite。

>[AZURE.NOTE] 您可以略過本節，從行動服務的 Github 範例存放庫便可取得已具有離線支援的範例專案。啟用離線支援的範例專案位於此處：[TodoList 離線範例]。


1. 安裝適用於 Windows 8.1 和 Windows Phone 8.1 的 SQLite 執行階段。 

    * **Windows 8.1 執行階段：**從這個連結安裝適用於 Windows 8.1 的 SQLite 執行階段：[適用於 Windows 8.1 的 SQLite]。
    * **Windows Phone 8.1：**從這個連結安裝適用於 Windows Phone 8.1 的 SQLite 執行階段：[適用於 Windows Phone 8.1 的 SQLite]。

    >[AZURE.NOTE] 如果您使用的是 Internet Explorer，當您按一下安裝 SQLite 的連結時，系統會提示您下載 .zip 檔案格式的 .vsix。請以 .vsix 副檔名將此檔案儲存至您的硬碟，而不要使用 .zip。在 Windows 檔案總管中按兩下此 .vsix 檔案，以執行安裝。

2. 在 Visual Studio 中，開啟您在[開始使用行動服務]教學課程中完成的專案。在 [方案總管] 中，以滑鼠右鍵按一下 Windows 8.1 執行階段和 Windows Phone 8.1 的平台專案的 [參考]****，並新增參考至 SQLite，其位於 [擴充功能]**** 區段。 

    ![][1]
    </br>**Windows 8.1 執行階段**

    ![][11]
    </br>**Windows Phone 8.1**

3. SQLite Runtime 會要求您變更要建置到 **x86**、**x64** 或 **ARM** 之專案的處理器架構。不支援「任何 CPU」****。在 Visual Studio 的 [方案總管] 中，按一下上方的 [方案]，將處理器架構下拉式方塊變更為您想測試的其中一項支援的設定。

    ![][13]

4. 安裝適用於 Windows 8.1 執行階段和 Windows Phone 8.1 的 **WindowsAzure.MobileServices.SQLiteStore** NuGet 封裝。

    * **Windows 8.1：**在 [方案總管] 中，以滑鼠右鍵按一下 Windows 8.1 專案，然後按一下 [管理 NuGet 封裝]****，以執行 NuGet 封裝管理員。搜尋 **SQLiteStore**，以安裝 WindowsAzure.MobileServices.SQLiteStore 封裝。
    * **Windows Phone 8.1：**以滑鼠右鍵按一下 Windows Phone 8.1 專案，然後按一下 [管理 NuGet 封裝]****，以執行 NuGet 封裝管理員。搜尋 **SQLiteStore**，以安裝 WindowsAzure.MobileServices.SQLiteStore 封裝。     

    >[WACOM.NOTE] 如果安裝建立了舊版 SQLite 的參考，您可以刪除該重複項。 

    ![][2]

5. 在 Visual Studio 的 [方案總管] 中，在共用專案中開啟 MainPage.cs 檔案。在檔案頂端新增下列 using 陳述式。

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. 在共用檔案 Mainpage.cs 中，將 `todoTable` 的宣告取代為屬於 `IMobileServicesSyncTable` 類型、藉由呼叫 `MobileServicesClient.GetSyncTable()` 而初始化的宣告。

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. 在共用檔案 MainPage.cs 中更新 `OnNavigatedTo` 事件處理常式，使其對 SQLite 存放區初始化用戶端同步內容。此 SQLite 存放區是使用與行動服務資料表的結構描述相符的資料表建立，其中必須包含您將在後續步驟中新增的 **Version** 系統屬性。

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
            await RefreshTodoItems();
        }


8. 在 Visual Studio 的 [方案總管] 中，展開共用專案中的 **DataModel** 並開啟 TodoItem.cs。為 MobileServices 命名空間新增 `using` 陳述式。需要此屬性才能解析 version 系統屬性的 version 屬性。

        using Microsoft.WindowsAzure.MobileServices;

      接著更新 `TodoItem` 類別，使該類別包含 **Version** 系統屬性，如下所示。資料表結構描述必須包含此處顯示的 **Version** 系統屬性，以支援離線功能。

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




9. 接著更新 Windows 8.1 與 Windows Phone 8.1 專案的使用者介面，以包含將支援本機離線資料庫和 Azure 中之行動服務資料庫間的離線同步處理的按鈕。 

    * **Windows 8.1：**在 Visual Studio 的 [方案總管] 中，開啟 Windows 8.1 專案下的 MainPage.xaml。尋找名稱為 **ButtonRefresh** 的按鈕。以下列的按鈕堆疊面板取代該按鈕元素。 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Download"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8.1：**在 Visual Studio 的 [方案總管] 中，開啟 Windows Phone 8.1 專案下的 MainPage.xaml。尋找名稱為 **ButtonRefresh** 的按鈕。以下列的按鈕堆疊面板取代該按鈕元素。 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Download"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. 在共用的 MainPage.cs 檔案中新增 [推送]**** 和 [提取]**** 按鈕的 click 事件處理常式。接著，請儲存檔案。 
        
    請注意，推送和提取作業可能會發生 `MobileServicePushFailedException`。對於提取作業，發生原因是因為提取作業會在內部執行推送，以確定所有資料表和任何關聯性均同步。在下一個教學課程中，[處理行動服務的離線支援衝突]會示範如何處理這些同步處理相關的例外狀況。

    若要使用離線資料同步來同步刪除的記錄，您應啟用[虛刪除](/zh-tw/documentation/articles/mobile-services-using-soft-delete/)。否則，您必須手動移除本機存放區的記錄，或呼叫 'IMobileServiceSyncTable::PurgeAsync()' 以清除本機存放區。

    在此範例中，我們將查詢傳遞至 `PullAsync` 方法呼叫，以支援增量同步處理。當您真的不想在同步期間提取整個資料表時，這會很有用。在這個案例中，我們不必擔心 todoitem 的文字變更，我們只是要提取完整項目來標示，以區隔我們的 todolist。

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // All items should be synced since other clients might mark an item as complete
                // The first parameter is a query ID that uniquely identifies the query.
                // This is used in incremental sync to get only newer items the next time PullAsync is called
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery());

                await RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Internal Push operation during pull request failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPull.IsEnabled = true; 
        }


        private async void ButtonPush_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Push is in progress
            // and visibly show the action is in progress. 
            ButtonPush.Focus(FocusState.Programmatic);
            ButtonPush.IsEnabled = false;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Push again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPush.IsEnabled = true;
        }

11. 建置方案，並確認在任一專案中未發生任何建置錯誤。


## <a name="test-offline-app"></a>在離線狀態下測試應用程式

在本節中，您將中斷行動服務的應用程式連線，以模擬離線狀態。接著，您會新增某些將保存在本機存放區中的資料項目。

請注意，在本節中，應用程式不應連接到任何行動服務。因此，如果您測試 [推送]**** 和 [提取]**** 按鈕，將會擲出例外狀況。在下一節中，您會將此用戶端應用程式重新連接到行動服務，以測試將存放區與行動服務資料庫同步處理的 [推送]**** 和 [提取]**** 作業。


1. 在 Visual Studio 的 [方案總管] 中，開啟共用專案中的 App.xaml.cs。將您 URL 的 "**azure-mobile.net**" 取代為 "**azure-mobile.xxx**"，以將 **MobileServiceClient** 的初始化變更為無效位址。接著，請儲存檔案。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. 在 Visual Studio 中按 **F5**，以建置並執行此應用程式。輸入新的 todo 項目，然後為每個項目按一下 [儲存]****。新的 todo 項目在可推送至行動服務之前，都只會存留在本機存放區中。用戶端應用程式的行為，會如同它已連接到支援所有建立、讀取、更新、刪除 (CRUD) 作業的行動服務。

    ![][4]

3. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

## <a name="update-online-app"></a>更新應用程式以重新連接您的行動服務

在本節中，您會將應用程式重新連接至行動服務。您將藉此模擬應用程式在行動服務中從離線狀態恢復為線上狀態的情境。


1. 在 Visual Studio 的 [方案總管] 中，開啟共用專案中的 App.xaml.cs。將您 URL 的 "**azure-mobile.xxx**" 取代為 "**azure-mobile.net**"，以將 **MobileServiceClient** 的初始化重新變更為正確的位址。接著，請儲存檔案。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>測試連接到行動服務的應用程式


在本節中，您會測試將本機存放區與行動服務資料庫同步處理的推送和提取作業。您可以測試 Windows Store 8.1 用戶端或 Windows Phone 8.1 用戶端。下列螢幕擷取畫面顯示 Windows Store 8.1 用戶端。 

1. 在 Visual Studio 中按 **F5** 鍵，以重新建置並執行此應用程式。請注意，雖然應用程式此時已連接到行動服務，但資料看起來仍會與離線狀態下相同。這是因為此應用程式一律會使用指向本機存放區的 `IMobileServiceSyncTable`。

    ![][4]

2. 登入 Microsoft Azure 管理入口網站，並檢視您的行動服務的資料庫。若您的服務針對行動服務使用 JavaScript 後端，則您可以從行動服務的 [資料]**** 索引標籤瀏覽資料。若您為您的行動服務使用 .NET 後端，您可以在「SQL Azure 擴充功能」中按一下您資料庫的 [管理]**** 按鈕，以對您的資料表執行查詢。

    請注意，資料並未與資料庫和本機存放區進行同步處理。

    ![][6]

3. 在應用程式中，按 [推送]**** 按鈕。這會使應用程式呼叫 `MobileServiceClient.SyncContext.PushAsync`。此推送作業會使行動服務資料庫接收來自該存放區的資料。但本機存放區並不會接收行動服務資料庫中的項目。

    推送作業會從 `MobileServiceClient.SyncContext` 執行 (而非 `IMobileServicesSyncTable`)，並推送該同步內容所有相關資料表的變更。這是為了解說資料表之間有所關聯的情況。

€

4. 在應用程式中，按一下幾個項目旁的核取方塊以在本機存放區中完成它們。 

    ![][8]

5. 這次請在應用程式中按 [提取] ****按鈕。應用程式會呼叫 `IMobileServiceSyncTable.PullAsync()` 和 `RefreshTodoItems`。請注意，行動服務資料庫中的所有資料都會提取至本機存放區，並顯示在應用程式中。但也請注意，本機存放區中的所有資料仍會推送至行動服務資料庫。這是因為「提取一律會先執行推送」****。這是為了確保本機存放區中的所有表格與關聯性都能維持同步。
 
    ![][9]

    ![][10] 
  

##摘要

為了支援行動服務的離線功能，我們使用了 `IMobileServiceSyncTable` 介面，並對本機存放區初始化 `MobileServiceClient.SyncContext`。在此案例中，本機存放區做是 SQLite 資料庫。

正常情況下，在行動服務的 CRUD 作業執行時，應用程式會如同仍處於連線狀態，但所有的作業都會對本機存放區執行。

當我們要將本機存放區與伺服器同步時，我們使用了 `IMobileServiceSyncTable.PullAsync` 與 `MobileServiceClient.SyncContext.PushAsync` 方法。

*  為了將變更推送至伺服器，我們呼叫了 `IMobileServiceSyncContext.PushAsync()`。此方法是 `IMobileServicesSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更：

    只有以某種方式在本機上修改過的記錄 (透過 CRUD 作業)，才會傳送至伺服器。
   
* 為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了 `IMobileServiceSyncTable.PullAsync`。

    提取一律會先發出推送動作。這是為了確保本機存放區中的所有表格與關聯性都能維持同步。

    此外，也有 `PullAsync()` 的多載可允許指定查詢以支援增量同步處理。如果不傳遞查詢，則 'Pullasync ()' 會提取對應的資料表 (或查詢) 中的所有資料列。您可以傳遞查詢，只篩選應用程式需要同步處理的變更。


## 後續步驟

* [處理行動服務的離線支援衝突]

<!-- Anchors. -->
[更新應用程式以支援離線功能]： #enable-offline-app
[在離線狀態下測試應用程式]： #test-offline-app
[更新應用程式以重新連接您的行動服務]： #update-online-app
[測試連接到行動服務的應用程式]： #test-online-app
[後續步驟]： #next-steps

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
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[處理行動服務的離線支援衝突]： /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[TodoList 離線範例]： http://go.microsoft.com/fwlink/?LinkId=394777
[開始使用行動服務]： /zh-tw/develop/mobile/tutorials/get-started/#create-new-service
[開始使用]： /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[開始使用資料]： /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[開始使用行動服務]： /zh-tw/documentation/articles/mobile-services-windows-store-get-started/
[適用於 Windows 8.1 的 SQLite]： http://go.microsoft.com/fwlink/?LinkId=394776
[適用於 Windows Phone 8.1 的 SQLite]： http://go.microsoft.com/fwlink/?LinkId=397953
[適用於 Visual Studio 2012 的 Windows Phone 8 教學課程]： /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[行動服務 SDK Nuget]： http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite 存放區 nuget]： http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0

<!--HONumber=35.2-->
