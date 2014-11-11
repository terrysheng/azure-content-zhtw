<properties linkid="develop-mobile-tutorials-get-started-offline-data-android" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin Android application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="donnam,wesmc" editor="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam,wesmc" />

# 開始在行動服務中使用離線資料同步

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

本主題將說明如何使用 Azure 行動服務的離線功能。這些功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。離線功能可讓您在重新上線時同步處理本機變更與行動服務。

在本教學課程中，您將會更新[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]教學課程中的應用程式，以支援 Azure 行動服務的離線功能。接著，您會在中斷連線的離線狀態下新增資料、將這些項目同步處理至線上資料庫，然後登入 Azure 管理入口網站，以檢視執行應用程式時對資料所做的變更。

> [WACOM.NOTE] 本教學課程旨在協助您深入了解如何透過行動服務，使用 Azure 儲存並擷取 Windows 市集應用程式中的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務][開始使用行動服務]。

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][Azure 免費試用]。

本教學課程將逐步引導您完成下列基本步驟：

1.  [更新應用程式以支援離線功能][更新應用程式以支援離線功能]
2.  [測試連接到行動服務的應用程式][測試連接到行動服務的應用程式]

本教學課程需要下列各項：

-   附有 [Xamarin 延伸][Xamarin 延伸]**或** [Xamarin Studio][Xamarin Studio] 的 Visual Studio
-   完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]教學課程
-   [Azure 行動服務 SDK 1.3.0-alpha3 版][Azure 行動服務 SDK 1.3.0-alpha3 版]
-   [Azure 行動服務 SQLite Store 1.0.0-alpha2 版][Azure 行動服務 SQLite Store 1.0.0-alpha2 版]

> [WACOM.NOTE] 下列指示假設您使用附有 Xamarin 延伸的 Visual Studio 2012 或更新版本。如果您使用 Xamarin Studio，則指示大致上都相同，但您應同時安裝 [NuGet Addin for Xamarin][NuGet Addin for Xamarin]，以便能輕鬆地將搶鮮版行動服務 NuGet 封裝新增至您的專案。

## <a name="enable-offline-app"></a>更新應用程式以支援離線功能

Azure 行動服務的離線功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。若要在您的應用程式中使用這些功能，您必須將 `MobileServiceClient.SyncContext` 初始化至本機存放區。接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。

1.  在 Visual Studio 中，開啟您在[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]教學課程中完成的專案。在 [方案總管] 中，移除對 [元件] 中的 [Azure 行動服務 SDK] 的參考。

2.  在 [Package Manager 主控台] 中使用下列指令碼，安裝行動服務 SQLiteStore 的搶鮮版封裝：

        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    這也會安裝所有必要的相依性。

3.  在參考節點中，移除對 `System.IO`、`System.Runtime` 和 `System.Threading.Tasks` 的參考。

### 編輯 ToDoActivity.cs

-   新增宣告

        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using System.IO;

-   將成員 `ToDoActivity.toDoTable` 的類型從 `IMobileServiceTable<>` 變更為 `IMobileServiceSyncTable<>`

-   在 `OnCreate(Bundle)` 方法中，於初始化成員 `client` 的程式行後面新增下列程式碼：

        // existing initializer
        client = new MobileServiceClient (applicationURL, applicationKey, progressHandler);

        // new code to initialize the SQLite store
        string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "test1.db");

        if (!File.Exists(path))
        {
            File.Create(path).Dispose();
        }

        var store = new MobileServiceSQLiteStore(path);
        store.DefineTable<ToDoItem>();

        await client.SyncContext.InitializeAsync(store, new TodoSyncHandler(this));

-   以相同的方法變更初始化 `toDoTable` 的程式行，以使用 `GetSyncTable<>` 方法，而不使用 `GetTable<>`：

        toDoTable = client.GetSyncTable <ToDoItem> ();

-   修改 `OnRefreshItemsSelected` 方法，以新增對 `PushAsync` 和 `PullAsync` 的呼叫：

        async void OnRefreshItemsSelected ()
        {
            await client.SyncContext.PushAsync();
            await toDoTable.PullAsync();
            await RefreshItemsFromTableAsync();
        }

### 編輯 ToDoItem.cs

-   新增 using 陳述式：

        using Microsoft.WindowsAzure.MobileServices; 

-   在類別 `ToDoItem` 中新增下列成員：

        [Version]
        public string Version { get; set; }


        public override string ToString()
        {
            return "Text: " + Text + "\nComplete: " + Complete + "\n";
        }

## <a name="test-online-app"></a>測試應用程式

在本節中，您會測試將本機存放區與行動服務資料庫同步處理的 `SyncAsync` 方法。

1.  在 Visual Studio 中，按 [執行] 按鈕以建立專案，並在 iPhone 模擬器中啟動應用程式 (此專案的預設選項)。

2.  請注意，應用程式中的項目清單是空的。由於上一節中的程式碼變更，應用程式已不會再從行動服務中讀取項目，而會從本機存放區讀取。

3.  在 [待辦事項] 清單中新增項目。

    ![][0]

4.  登入 Microsoft Azure 管理入口網站，並檢視您的行動服務的資料庫。如果您的行動服務使用 JavaScript 後端，則您可以從行動服務的 [資料] 索引標籤瀏覽資料。如果您在行動服務中使用 .NET 後端，您可以在「SQL Azure 擴充功能」中對您的資料庫按一下 [管理] 按鈕，以對您的資料表執行查詢。

    請注意，資料並未與資料庫和本機存放區進行同步處理。

5.  在應用程式中，按 [重新整理] 按鈕。這會使應用程式依序呼叫 `MobileServiceClient.SyncContext.PushAsync` 和 `IMobileServiceSyncTable.PullAsync()`，然後呼叫 `RefreshTodoItems` 以使用本機存放區中的項目重新整理應用程式。

    此推送作業會使行動服務資料庫接收來自該存放區的資料。推送作業會從 `MobileServiceClient.SyncContext` 執行 (而非 `IMobileServicesSyncTable`)，並推送該同步內容所有相關資料表的變更。這是為了解說資料表之間有所關聯的情況。

    相對地，提取作業只會從指定的資料表中擷取記錄。如果此資料表在同步內容中有擱置的作業，則行動服務 SDK 會隱含地呼叫 `PushAsync` 作業。

    ![][1]

    ![][2]

## 摘要

為了支援行動服務的離線功能，我們使用了 `IMobileServiceSyncTable` 介面，並對本機存放區初始化 `MobileServiceClient.SyncContext`。在此案例中，我們以本機存放區做為 SQLite 資料庫。

正常情況下，在行動服務的 CRUD 作業執行時，應用程式會如同仍處於連接狀態，但所有的作業都會對本機存放區執行。

當我們要同步處理本機存放區與伺服器時，我們使用了 `IMobileServiceSyncTable.PullAsync` 和 `MobileServiceClient.SyncContext.PushAsync` 方法。

-   為了將變更推送至伺服器，我們呼叫了 `IMobileServiceSyncContext.PushAsync()`。此方法是 `IMobileServicesSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更：

    只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。

-   為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了 `IMobileServiceSyncTable.PullAsync`。

    提取一律會先發出推送動作。

    此外，也有 **PullAsync()** 的多載可允許指定查詢。請注意，在行動服務的預覽版離線支援中，**PullAsync** 將會讀取對應資料表 (或查詢) 中的所有資料列 -- 舉例來說，它不會嘗試僅讀取比前次同步還新的資料列。如果資料列已存在於本機同步資料表中，則將保持不變。

## 後續步驟

<!--* [Handling conflicts with offline support for Mobile Services] -->

-   [如何使用適用於 Azure 行動服務的 Xamarin 元件用戶端][如何使用適用於 Azure 行動服務的 Xamarin 元件用戶端]

<!-- Anchors. --> <!-- Images --> <!-- URLs. -->

  [開始使用行動服務]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started/
  [開始使用資料]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
  [Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28
  [更新應用程式以支援離線功能]: #enable-offline-app
  [測試連接到行動服務的應用程式]: #test-online-app
  [Xamarin 延伸]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Azure 行動服務 SDK 1.3.0-alpha3 版]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-alpha3
  [Azure 行動服務 SQLite Store 1.0.0-alpha2 版]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-alpha2
  [NuGet Addin for Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
  [0]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png
  [1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png
  [2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
  [如何使用適用於 Azure 行動服務的 Xamarin 元件用戶端]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/
