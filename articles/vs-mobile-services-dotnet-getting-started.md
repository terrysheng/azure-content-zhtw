<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 發生什麼情形？

###### 加入參考

Azure 行動服務 NuGet 封裝加入至專案。因此，下列 .NET 參考已加入至專案：Microsoft.WindowsAzure.Mobile, Microsoft.WindowsAzure.Mobile.Ext, Newtonsoft.Json, System.Net.Http.Extensions, System.Net.Http.Primitives

###### 行動服務的連接字串值

在 App.xaml.cs 檔案中，已使用所選行動服務的應用程式 URL 和應用程式金鑰建立 MobileServiceClient 物件。

### 開始使用行動服務

###### 取得資料表的參考

下列程式碼可取得資料表的參考，而該資料表包含 TodoItem 的資料，可在後續操作中用來讀取和更新資料表。您需要 TodoItem 類別，並設定屬性來解譯行動服務為了回應查詢而傳送的 JSON。

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

資料表的權限必須設為「具有應用程式金鑰的任何人」，此程式碼才能運作。如果您變更權限來保護行動服務，則需要加入使用者驗證支援。請參閱[開始使用驗證][開始使用驗證] (英文)。

###### 加入項目

將新的項目插入至資料表。

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

###### 讀取/查詢資料表

下列程式碼會查詢資料表的所有項目。請注意，只會傳回第一頁的資料，依預設有 50 個項目。您可以傳遞想要的頁面大小 (這是選用參數)。

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

###### 更新項目

更新資料表中的資料列。參數項目是要更新的 TodoItem 物件。

    await todoTable.UpdateAsync(item);

###### 刪除項目

刪除資料庫中的資料列。參數項目是要刪除的 TodoItem 物件。

    await todoTable.DeleteAsync(item);

  [開始使用驗證]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
