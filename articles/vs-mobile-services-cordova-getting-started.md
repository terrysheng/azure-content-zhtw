<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 發生什麼情形？

### 加入參考

所有多重裝置混合應用程式隨附的 Azure 行動服務用戶端外掛程式已啟用。

### 行動服務的連接字串值

在 services\\mobileServices\\settings 下，已產生一個含有 MobileServiceClient 的新的 JavaScript (.js) 檔案，內含所選行動服務的應用程式 URL 和應用程式金鑰。此檔案包含行動服務用戶端物件的初始化，類似於下列程式碼。

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

### 開始使用行動服務

###### 取得資料表的參考

下列程式碼可取得資料表的參考，而該資料表包含 TodoItem 的資料，可在後續操作中用來讀取和更新資料表。建立行動服務時會自動建立 TodoItem 資料表。

    var todoTable = mobileServiceClient.getTable('TodoItem');

資料表的權限必須設為「具有應用程式金鑰的任何人」，這些範例才能運作。稍後您可以設定驗證。請參閱[開始使用驗證][開始使用驗證] (英文)。

###### 加入項目

將新的項目插入至資料表。自動會建立 id (字串類型的 GUID) 作為新資料列的主要索引鍵。在傳回的 [Promise][Promise] 物件上呼叫 [done][Promise] 方法，以取得所插入物件的複本，並處理任何錯誤。

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

###### 讀取/查詢資料表

下列程式碼會查詢資料表的所有項目，並依文字欄位排序。您可以在成功處理常式中新增程式碼來處理查詢結果。在此案例中是更新項目的本機陣列。

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

您可以使用 where 方法來修改查詢。以下範例是濾除已完成的項目。

    todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

有關更多您可以使用的查詢範例，請參閱 [query][query] 物件。

###### 更新項目

更新資料表中的資料列。在此程式碼中，當行動服務回應時，項目就從清單中移除。在傳回的 [Promise][Promise] 物件上呼叫 [done][Promise] 方法，以取得所插入物件的複本，並處理任何錯誤。

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

###### 刪除項目

使用 del 方法刪除資料表中的資料列。在傳回的 [Promise][Promise] 物件上呼叫 [done][Promise] 方法，以取得所插入物件的複本，並處理任何錯誤。

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

  [開始使用驗證]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-html-get-started-users/
  [Promise]: ()
  [query]: http://msdn.microsoft.com/library/azure/jj613353.aspx