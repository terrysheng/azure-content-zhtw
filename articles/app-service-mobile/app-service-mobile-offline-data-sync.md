<properties
	pageTitle="Azure 行動應用程式中的離線資料同步處理 | Microsoft Azure"
	description="Azure 行動應用程式離線資料同步處理功能的概念參考與概觀"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="wesmc"/>

# Azure 行動應用程式中的離線資料同步處理

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 什麼是離線資料同步處理？

離線資料同步處理是 Azure 行動應用程式的用戶端和伺服器 SDK 功能，可讓開發人員建立不需要網路連線就能運作的應用程式。

當您的應用程式處於離線模式時，使用者仍然可以建立和修改資料，而變更將會儲存至本機存放區。當應用程式回到線上時，即可將本機變更與您的 Azure 行動應用程式後端同步處理。此功能也包含偵測衝突的支援，即當同一筆記錄在用戶端和後端都發生變更。衝突可以在伺服器或用戶端處理。

離線同步處理有下列幾個優點：

* 在裝置上本機快取伺服器資料，以改善應用程式回應性
* 建立當網路有問題時仍可以使用的健全應用程式。
* 讓使用者即使在沒有網路存取的情況下仍能建立及修改資料，而支援連線微弱或無連線的情況
* 同步多個裝置之間的資料，並在兩個裝置修改相同的記錄時偵測衝突
* 高延遲或計量付費網路的網路使用限制。

下列教學課程說明如何使用 Azure 行動應用程式將離線同步處理新增至您的行動用戶端：

* [iOS：啟用離線同步處理]			
* [Xamarin iOS：啟用離線同步處理]	
* [Xamarin Android：啟用離線同步處理]
* [Windows 8.1：啟用離線同步處理]	

## 什麼是同步處理資料表？

若要存取 "/tables" 端點，Azure 行動用戶端 SDK 提供 `IMobileServiceTable` (.NET 用戶端 SDK) 或 `MSTable` (iOS 用戶端) 等介面。這些 API 直接連接至 Azure 行動應用程式後端，如果用戶端裝置沒有網路連線將會失敗。

若要支援離線使用，則您的應用程式應改為使用「同步處理資料表」 API，例如 `IMobileServiceSyncTable` (.NET 用戶端 SDK) 或 `MSSyncTable` (iOS 用戶端)。所有相同的 CRUD 作業 (Create、Read、Update、Delete) 都針對同步處理資料表 API 運作，不過它們現在會從「本機存放區」讀取或寫入。必須先初始化本機存放區，才能執行任何同步處理資料表作業。

## 什麼是本機存放區？

本機存放區是用戶端裝置上的資料持續層。Azure 行動應用程式用戶端 SDK 提供預設的本機存放區實作。在 Windows、Xamarin 和 Android 它是根據 SQLite，而在 iOS 則是根據 Core Data。

若要在 Windows Phone 或 Windows 市集 8.1 上使用 SQLite 為基礎的實作，您需要安裝 SQLite 擴充。如需詳細資訊，請參閱 [Windows 8.1：啟用離線同步處理]。Android 與 iOS 裝置的作業系統本身即包含 SQLite 版本，因此您不需要再參考自己的 SQLite 版本。

開發人員也可以實作自己的本機存放區。例如，如果您希望將資料以加密格式儲存在行動用戶端上，則您可以定義使用 SQLCipher 進行加密的本機存放區。

## 什麼是同步處理內容？

「同步處理內容」會與行動用戶端物件相關聯 (例如 `IMobileServiceClient` 或 `MSClient`)，並且追蹤對同步處理資料表所做的變更。同步處理內容會維護「作業佇列」，其中記錄 CUD 作業 (Create、Update、Delete) 的順序清單，該清單稍後會傳送至伺服器。

本機存放區會使用初始化方法 (例如 .NET 用戶端 SDK 中的 `IMobileServicesSyncContext.InitializeAsync(localstore)`)，來與同步處理內容相關聯。

<!-- TODO: link to client references -->


<!-- 
Client code will interact with the table using the `IMobileServiceSyncTable` interface to support offline buffering. This interface supports all the methods of `IMobileServiceTable` along with additional support for pulling data from a Mobile App backend table and merging it into a local store table. How the local table is synchronized with the backend database is mainly controlled by your logic in the client app.

The sync table uses the [System Properties](https://msdn.microsoft.com/library/azure/dn518225.aspx) on the table to implement change tracking for offline synchronization. 



* The data objects on the client should have some system properties, most are not required.
	* Managed
		* Write out the attributes
	* iOS
		*table for the entity
* Note: because the iOS local store is based on Core Data, the developer must define the following tables:
	* System tables  -->


## 離線同步處理如何運作

使用同步處理資料表的時候，您的用戶端程式碼可控制本機變更與 Azure 行動應用程式後端同步處理的時機。在有呼叫要「推送」(*push*) 變更之前不會傳送任何項目到後端。同樣地，只當有呼叫要「提取」(*pull*) 時才會將新資料填入本機存放區。

* **推送**：推送是同步處理內容的作業，會傳送自上一次推送之後的所有 CUD 變更。請注意，您無法只傳送個別資料表的變更，因為這樣作業傳送順序可能會發生錯誤。推送會對 Azure 行動應用程式後端執行一系列的 REST 呼叫，這將會修改伺服器資料庫。

* **提取**：提取會以各資料表為基礎執行，並且可以使用佇列來自訂，以抓取伺服器資料的特定子集。然後 Azure 行動用戶端 SDK 會將該結果資料插入本機存放區。

* **隱含推送**：如果提取是針對有擱置中本機更新的資料表執行，則提取會先在同步處理內容上執行推送。這有助於將已排入佇列的變更與來自伺服器的新資料之間的衝突最小化。

* **增量同步處理**：提取作業的第一個參數是「查詢名稱」，此參數只在用戶端使用。如果您使用非 null 的查詢名稱，Azure 行動 SDK 將會執行「增量同步處理」。每當提取作業傳回結果集，該結果集中最新的 `__updatedAt` 時間戳記就會儲存在 SDK 本機系統資料表。後續的提取作業只會擷取該時間戳記之後的記錄。

  若要使用增量同步處理，您的伺服器必須傳回有意義的 `__updatedAt` 值，也必須支援依據此欄位排序。不過，由於 SDK 會在 updatedAt 欄位上加入自己的排序，所以您不能使用本身具備 `$orderBy$` 子句的提取查詢。

  查詢名稱可以是您選擇的任何字串，但它在應用程式中的每個邏輯查詢都必須是唯一的。否則，不同的提取作業可能會覆寫相同的增量同步處理時間戳記，您的查詢可能因此傳回不正確的結果。

  如果查詢具有參數，一個建立唯一查詢名稱的方法是納入該參數值。例如，如果您要篩選 userid，您的查詢名稱可能如下：

		await todoTable.PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

  如果您想選擇不要增量同步處理，則傳遞 `null` 做為查詢識別碼。在此情況下，每次呼叫 `PullAsync` 時都會擷取所有的記錄，這可能會沒有效率。

 

<!--   mymobileservice-code.azurewebsites.net/tables/TodoItem?$filter=(__updatedAt ge datetimeoffset'1970-01-01T00:00:00.0000000%2B00:00')&$orderby=__updatedAt&$skip=0&$top=50&__includeDeleted=true&__systemproperties=__updatedAt%2C__deleted
 -->
* **清除**：您可以使用 `IMobileServiceSyncTable.PurgeAsync` 來清除本機存放區的內容。如果用戶端資料庫中有過時資料，或者您想要捨棄所有擱置的變更，可能就需要此作業。

  清除作業會從本機存放區清除資料表。如果有作業擱置與伺服器資料庫的同步處理，清除將會擲回例外狀況，除非設定 *force purge* 參數。

  舉例說明用戶端上過時資料，假設在 "todo list" 範例中，Device1 只會提取未完成的項目。接著，"Buy milk" todoitem 被其他裝置在伺服器上標記為已完成。不過，Device1 在本機存放區仍會有 "Buy milk" todoitem，因為它只提取未標記為已完成的項目。清除作業將會清除此過時項目。
 
## 後續步驟

* [iOS：啟用離線同步處理]			
* [Xamarin iOS：啟用離線同步處理]	
* [Xamarin Android：啟用離線同步處理]
* [Windows 8.1：啟用離線同步處理]	

<!-- Links -->

[iOS：啟用離線同步處理]: ../app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS：啟用離線同步處理]: ../app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android：啟用離線同步處理]: ../app-service-mobile-xamarin-ios-get-started-offline-data.md
[Windows 8.1：啟用離線同步處理]: ../app-service-mobile-windows-store-dotnet-get-started-offline-data.md

<!---HONumber=AcomDC_1203_2015-->