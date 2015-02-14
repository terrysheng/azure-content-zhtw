為了支援行動服務的離線功能，我們使用了  `IMobileServiceSyncTable` 介面，並對本機存放區初始化  `MobileServiceClient.SyncContext`。在此案例中，本機存放區是 SQLite 資料庫。

正常情況下，在行動服務的 CRUD 作業執行時，應用程式會如同仍處於連線狀態，但所有的作業都會對本機存放區執行。

當我們要同步本機存放區與伺服器時，我們使用了  `IMobileServiceSyncTable.PullAsync` 與  `MobileServiceClient.SyncContext.PushAsync` 方法。

*  為了將變更推送至伺服器，我們呼叫了  `IMobileServiceSyncContext.PushAsync()`。此方法是  `IMobileServicesSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更。

    只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。
   
* 為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了  `IMobileServiceSyncTable.PullAsync`。

    提取一律會先發出推送動作。這是為了確保本機存放區中的所有資料表與關聯性都能維持一致。

    此外，還有  `PullAsync()` 的多載，可以讓查詢依序指定，以篩選儲存在用戶端上的資料。若未傳遞查詢， `PullAsync()` 將會提取對應之資料表 (或查詢) 中的所有資料列。您可以傳遞查詢，只篩選應用程式需要同步的變更。

* 若要啟用增量同步，請將查詢識別碼傳遞到  `PullAsync()`。查詢識別碼是用來儲存來自上次提取操作的上次更新時間戳記。查詢識別碼應該是描述性字串，而且在您應用程式的每個邏輯查詢中必須是唯一的。若查詢具有參數，則相同的參數值必須是查詢識別碼的一部分。

    例如，若您篩選 userid ，它必須是查詢識別碼的一部分：

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    若要停止使用增量同步，請傳遞  `null` 做為查詢識別碼。在此案例中，將在對  `PullAsync` 的每個呼叫上抓取所有記錄，這可能沒有效率。

* 將記錄從您的行動服務資料庫刪除之後，若要將其從裝置本機存放區移除，您應該啟用[虛刪除]。否則，您的應用程式應該定期呼叫  `IMobileServiceSyncTable.PurgeAsync()` 以清除本機存放區。
<!--HONumber=42-->
