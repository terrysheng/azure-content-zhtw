<properties
    pageTitle="啟用 Azure 行動應用程式 (Xamarin Android) 的離線同步處理"
    description="了解如何在 Xamarin Android 應用程式中使用應用程式服務行動應用程式快取和同步處理離線資料"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="02/04/2016"
    ms.author="wesmc"/>

# 啟用您 Xamarin.Android 行動應用程式的離線同步處理

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## 概觀

此教學課程介紹適用於 Xamarin.Android 之Azure 行動應用程式的離線同步處理功能。離線同步處理可讓使用者與行動應用程式進行互動--檢視、新增或修改資料--即使沒有網路連線進也可行。變更會儲存在本機資料庫中︰裝置上線後，這些變更就會與遠端服務進行同步處理。

在本教學課程中，您將會更新[建立 Xamarin Android 應用程式]教學課程中的用戶端專案，來支援 Azure Mobile Apps 的離線功能。如果您不要使用下載的快速入門伺服器專案，必須將資料存取擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要深入了解離線同步處理功能，請參閱 [Azure Mobile Apps 中的離線資料同步處理]主題。

## 需求

* Visual Studio 2013
* Visual Studio [Xamarin 延伸]**或** [Xamarin Studio]
* 完成[建立 Xamarin Android 應用程式]教學課程。本教學課程使用該教學課程中涵蓋的已完成應用程式。

## 檢閱用戶端同步處理程式碼

您完成[建立 Xamarin Android 應用程式]教學課程時所下載的 Xamarin 用戶端專案，已經包含了使用本機 SQLite 資料庫支援離線同步處理的程式碼。以下是已經包含在教學課程程式碼中之內容的簡要概觀。如需此功能的概念性概觀，請參閱 [Azure Mobile Apps 中的離線資料同步處理]。

* 必須先初始化本機存放區，才可以執行資料表作業。當 `ToDoActivity.OnCreate()` 執行 `ToDoActivity.InitLocalStoreAsync()` 時會初始化本機存放區資料庫。這會使用 Azure 行動應用程式用戶端 SDK 所提供之 `MobileServiceSQLiteStore` 類別，建立新的本機 SQLite 資料庫。

	`DefineTable` 方法會在本機存放區中建立與給定類型中的欄位相符的資料表，在此案例中為 `ToDoItem`。該類型不必包含遠端資料庫中的所有資料行。可以只儲存資料行的子集。

		// ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
				.GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
			// For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* `ToDoActivity` 的 `toDoTable` 成員屬於 `IMobileServiceSyncTable` 類型而非 `IMobileServiceTable`。這會將所有建立、讀取、更新和刪除 (CRUD) 資料表作業導向至本機存放區資料庫。

	您可以透過使用該用戶端連線的同步處理內容呼叫 `IMobileServiceSyncContext.PushAsync()`，決定將這些變更推送到 Azure 行動應用程式後端的時機。當呼叫 `PushAsync` 時，透過追蹤和推送所有用戶端應用程式修改之資料表中的變更，同步處理內容可協助保存資料表關聯性。

	每當重新整理 todoitem 清單或是已新增或完成 todoitem 時，提供的程式碼會呼叫 `ToDoActivity.SyncAsync()` 來進行同步處理。因此，它會在每次本機變更執行同步處理內容推送和同步處理資料表提取之後同步處理。不過，請務必了解，如果針對具有內容追蹤之擱置中本機更新的資料表執行提取，該提取作業將會先自動觸發內容推送。因此，在這些情況下 (重新整理、加入和完成項目) 您可以省略明確 `PushAsync` 呼叫。它是多餘的。

    在提供的程式碼中，遠端 `TodoItem` 資料表中的所有記錄都會進行查詢，但是也可能透過將查詢識別碼與查詢傳遞至 `PushAsync` 來篩選記錄。如需詳細資訊，請參閱 [Azure Mobile Apps 中的離線資料同步處理]中的*增量同步處理*一節。

	<!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
-->	// ToDoActivity.cs
        private async Task SyncAsync()
        {
			try {
	            await client.SyncContext.PushAsync();
	            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
			} catch (Java.Net.MalformedURLException) {
				CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
			} catch (Exception e) {
				CreateAndShowDialog (e, "Error");
			}
        }


## 執行用戶端應用程式

請執行用戶端應用程式至少一次，以填入本機存放區資料庫。在下一節中，您將會模擬一個離線案例，並在應用程式離線時修改本機存放區中的資料。

## 更新用戶端應用程式的同步處理行為

在本節中，您將透過使用無效的後端應用程式 URL，修改用戶端應用程式來模擬離線案例。當您新增或變更資料項目時，這些變更會存放在本機存放區，但不會同步處理到後端資料存放區，直到重新建立連線為止。

1. 在 `ToDoActivity.cs` 的頂端，變更 `applicationURL` 的初始設定，以指向無效的 URL：

        const string applicationURL = @"https://your-service.azurewebsites.fail/";

	請注意，當應用程式也使用驗證時，這會導致登入失敗。您也可以透過停用裝置的 WiFi 和行動電話通訊網路，或使用飛航模式來示範離線行為。

2. 更新 `ToDoActivity.SyncAsync`，這樣一來會假設您已是離線狀態而攔截並忽略 `MobileServicePushFailedException`。

        private async Task SyncAsync()
        {
			try {
	            await client.SyncContext.PushAsync();
	            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
			} catch (Java.Net.MalformedURLException) {
				CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
			} catch (MobileServicePushFailedException)
            {
                // Not reporting this exception. Assuming the app is offline for now
            }
            catch (Exception e) {
				CreateAndShowDialog (e, "Error");
			}
        }


3. 建置並執行應用程式。如果您收到回報名稱解析例外狀況的例外狀況對話方塊，請關閉它。新增一些 todo 項目並注意應用程式的行為 (如同已連線的情況)，因為 `MobileServicePushFailedException` 會在沒有顯示對話方塊的情形下進行處理。

4. 這些新增項目在可推送至行動後端之前，都只會存留在本機存放區中。關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

5. (選擇性) 使用 Visual Studio 檢視您的 Azure SQL Database 資料表，以查看後端資料庫中的資料並無變更。

   在 Visual Studio 中，開啟 [伺服器總管]。瀏覽至 **Azure**->**SQL Database** 中您的資料庫。在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟]。現在您可以瀏覽至您的 SQL Database 資料表和其內容。

6. (選擇性) 使用 REST 工具 (例如 Fiddler 或 Postman) 來查詢您的行動後端 (使用表單 `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem` 中的 GET 查詢)。


## 更新用戶端應用程式，重新連接您的行動後端

在本節中，您會將應用程式重新連接到行動後端，而模擬回到線上狀態的應用程式。當您執行重新整理動作時，資料將會同步處理至您的行動後端中。

1. 開啟 `ToDoActivity.cs`。更正 `applicationURL` 以指向正確的 URL。

2. 重新建置並執行應用程式。應用程式嘗試於啟動後與 Azure 行動應用程式後端同步處理。請確認沒有出現任何例外狀況對話方塊。

3. (選擇性) 使用 SQL Server 物件總管或 REST 工具 (例如 Fiddler) 檢視已更新的資料。請注意，Azure 行動應用程式後端資料庫與本機存放區之間的資料已同步處理。

    請注意，資料庫與本機存放區之間的資料已同步處理，並包含離線狀態下您所新增的項目。

## 其他資源

* [Azure 行動應用程式中的離線資料同步處理]

* [雲端報導︰Azure 行動服務中的離線同步處理] (注意︰影片位於行動服務上，但離線同步處理的運作方式類似在 Azure Mobile Apps 中的方式)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[建立 Xamarin Android 應用程式]: ../app-service-mobile-xamarin-android-get-started.md
[Azure Mobile Apps 中的離線資料同步處理]: ../app-service-mobile-offline-data-sync.md
[Azure 行動應用程式中的離線資料同步處理]: ../app-service-mobile-offline-data-sync.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin 延伸]: http://xamarin.com/visual-studio

[雲端報導︰Azure 行動服務中的離線同步處理]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0211_2016-->