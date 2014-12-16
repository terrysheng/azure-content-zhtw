<properties urlDisplayName="Using Offline Data" pageTitle="在行動服務 (Xamarin Android) 中使用離線資料 | 行動開發人員中心 " metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin Android application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# 在行動服務中使用離線資料同步

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

本主題將說明如何使用 Azure 行動服務的離線功能。這些功能可讓您在行動服務處於離線狀態時，仍可與本機資料庫互動。離線功能可讓您在重新上線時同步處理本機變更與行動服務。 

在本教學課程中，您將會更新  [開始使用行動服務] 或 [開始使用資料] 教學課程中的應用程式，以支援 Azure 行動服務的離線功能。接著，您會在中斷連線的離線狀態下新增資料、將這些項目同步處理至線上資料庫，然後登入 Azure 管理入口網站，以檢視執行應用程式時對資料所做的變更。

>[WACOM.NOTE] 本教學課程旨在協助您深入了解如何透過行動服務，來使用 Azure 儲存並擷取 Windows 市集應用程式中的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務]。
>
> 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得多達 10 個免費的行動服務，即使在試用期結束之後仍可繼續使用這些服務。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。 

您可以在 [這裡](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android) 找到本教學課程為已完成狀態的專案。

本教學課程將逐步引導您完成下列基本步驟：

1. [更新應用程式以支援離線功能]
2. [測試連接到行動服務的應用程式]

本教學課程需要下列各項：

* 搭配 [Xamarin 擴充功能] **或** [Xamarin Studio]  的 Visual Studio
* 完成 [開始使用行動服務] 或 [開始使用資料] 教學課程
* [Azure 行動服務 SDK 1.3.0-beta2 版][Mobile Services SDK Nuget]
* [Azure 行動服務 SQLite Store 1.0.0-beta2 版][SQLite store nuget]

>[WACOM.NOTE] 下列指示假設您使用附有 Xamarin 擴充功能的 Visual Studio 2012 或更新版本。如果您打算使用 Xamarin Studio，請使用內建的 NuGet 封裝管理員支援。

## <a name="enable-offline-app"></a>更新應用程式以支援離線功能

Azure 行動服務離線同步處理可讓使用者在無法存取網路時，仍可與本機資料庫互動。若要在您的應用程式中使用這些功能，您必須將 `MobileServiceClient.SyncContext` 初始化至本機存放區。接著，請透過 `IMobileServiceSyncTable`  介面參考您的資料表。

1. 在 Visual Studio 中，開啟您在 [開始使用行動服務] 或 [開始使用資料] 教學課程中完成的專案。在 [方案總管] 中，移除 [**元件**] 中的 [**Azure 行動服務 SDK**] 參考。

2. 在 [Package Manager Console] 中使用下列指令碼，安裝行動服務 SQLiteStore 的搶鮮版封裝： 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    這也會安裝所有必要的相依性。
    
3. 在 [參考] 節點中，移除 `System.IO`、`System.Runtime` 和 `System.Threading.Tasks` 的參考。

### 編輯 ToDoActivity.cs

1. 新增宣告

		using Microsoft.WindowsAzure.MobileServices.Sync;
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
		using System.IO;

2. 將成員 `ToDoActivity.toDoTable` 的類型從 `IMobileServiceTable<>` 變更為 `IMobileServiceSyncTable<>`

3. 在 `OnCreate(Bundle)` 方法中，將下列程式碼新增至初始化成員 `client` 的程式行後面：

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

4. 在相同的方法中，將初始化 `toDoTable` 的程式行變更為改用 `GetSyncTable<>` 方法，而非 `GetTable<>`：

		toDoTable = client.GetSyncTable <ToDoItem> ();

5. 修改 `OnRefreshItemsSelected` 方法，以新增 `PushAsync`  和 `PullAsync` 的呼叫：

		async void OnRefreshItemsSelected ()
		{
		    await client.SyncContext.PushAsync();
			await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    await RefreshItemsFromTableAsync();
		}

### 編輯 ToDoItem.cs 

1. 新增 using 陳述式： 

        using Microsoft.WindowsAzure.MobileServices; 


2. 在類別 `ToDoItem` 中新增下列成員：
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

## <a name="test-online-app"></a>測試應用程式 

在本節中，您會測試將本機存放區與行動服務資料庫同步處理的 `SyncAsync` 方法。

1. 在 Visual Studio 中，按 [**執行**] 按鈕以建立專案，並在 iPhone 模擬器中啟動應用程式 (此專案的預設選項)。

2. 請注意，應用程式中的項目清單是空的。由於上一節中的程式碼變更，應用程式已不會再從行動服務中讀取項目，而會從本機存放區讀取。 

3. 在 [待辦事項] 清單中新增項目。

    ![][1]


4. 登入 Microsoft Azure 管理入口網站，並檢視您的行動服務的資料庫。如果您的行動服務使用 JavaScript 後端，則您可以從行動服務的 [**資料**] 索引標籤瀏覽資料。如果您在行動服務中使用 .NET 後端，您可以在「SQL Azure 擴充功能」中對您的資料庫按一下 [**管理**] 按鈕，以對您的資料表執行查詢。

    請注意，資料並未與資料庫和本機存放區進行同步處理。

5. 在應用程式中，按 [**重新整理**] 按鈕。這會使應用程式依序呼叫 `MobileServiceClient.SyncContext.PushAsync`、`IMobileServiceSyncTable.PullAsync()` 和 `RefreshTodoItems`，以使用本機存放區中的項目重新整理應用程式。 

    此推送作業會使行動服務資料庫接收來自該存放區的資料。推送作業會從 `MobileServiceClient.SyncContext` 執行 (而非 `IMobileServicesSyncTable`)，並推送該同步內容所有相關資料表的變更。這是為了解說資料表之間有所關聯的情況。
    
    相對地，提取作業只會從指定的資料表中擷取記錄。如果此資料表在同步內容中有擱置的作業，則行動服務 SDK 會隱含地呼叫 `PushAsync` 作業。
        
    ![][3] 



    ![][2]


  

##摘要

為了支援行動服務的離線功能，我們使用了 `IMobileServiceSyncTable` 介面，並初始化本機存放區的 `MobileServiceClient.SyncContext`。在此案例中，我們以本機存放區做為 SQLite 資料庫。

正常情況下，在行動服務的 CRUD 作業執行時，應用程式會如同仍處於連接狀態，但所有的作業都會對本機存放區執行。

當我們要同步處理本機存放區與伺服器時，我們使用了 `IMobileServiceSyncTable.PullAsync` 和 `MobileServiceClient.SyncContext.PushAsync` 方法。

*  為了將變更推送至伺服器，我們呼叫了 `IMobileServiceSyncContext.PushAsync()`。此方法是 `IMobileServicesSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更：

    只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。
   
* 為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了 `IMobileServiceSyncTable.PullAsync`。

    提取一律會先發出推送動作。  

    此範例會使用超載的 **PullAsync()**，可讓您指定查詢金鑰和查詢。查詢金鑰可用於增量同步處理。行動服務 SDK 會在每個成功提取作業之後追蹤上次更新時間戳記。在下一個提取中，只會擷取較新的記錄。如果未指定任何查詢金鑰，系統將會執行同步資料表的完整同步處理。

## 後續步驟

您可以在我們的 [GitHub 範例儲存機制](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android) 中下載完整版的教學課程。

<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [如何使用適用於 Azure 行動服務的 Xamarin 元件用戶端]

<!-- Anchors. -->
[更新應用程式以支援離線功能]: #enable-offline-app
[測試連接到行動服務的應用程式]: #test-online-app
[後續步驟]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png
[2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png



<!-- URLs. -->
[處理行動服務的離線支援衝突]: /zh-tw/documentation/articles/mobile-services-xamarin-android-handling-conflicts-offline-data/ 
[開始使用資料]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
[開始使用行動服務]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started/
[如何使用適用於 Azure 行動服務的 Xamarin 元件用戶端]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[行動服務 SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[SQLite 存放區 nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Xamarin 擴充功能]: http://xamarin.com/visual-studio
[Xamarin 的 NuGet 增益集]: https://github.com/mrward/monodevelop-nuget-addin
