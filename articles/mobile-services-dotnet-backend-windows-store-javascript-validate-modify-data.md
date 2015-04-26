<properties 
	pageTitle="使用 .Net 後端驗證與修改資料 (Windows 市集) | 行動開發人員中心" 
	description="了解如何使用 .Net 後端 Windows Azure 行動服務驗證、修改和增加 Javascript Windows 市集應用程式的資料。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="wesmc"/>

# 使用 .NET 後端在行動服務中驗證與修改資料

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

本主題將說明如何在 .Net 後端 Azure 行動服務中使用程式碼，以驗證及修改資料。.NET 後端服務是使用 Web API 架構建置的 HTTP 服務。如果您熟悉以 Web API 架構定義的  `ApiController` 類別，則行動服務提供的  `TableController` 類別是非常直覺性的。 `TableController` 衍生自  `ApiController` 類別，其提供額外的功能做為與資料庫資料表的介面。它可用來對要插入和更新的資料執行作業，包括本教學課程中示範的驗證和資料修改。 

本教學課程將逐步引導您完成下列基本步驟：

1. [新增字串長度驗證]
2. [更新用戶端以支援驗證]
3. [測試長度驗證]
4. [新增 CompleteDate 的時間戳記欄位]
5. [更新用戶端以顯示 CompleteDate]

本教學課程會以先前的教學課程[開始使用]或[開始使用資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/)中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須先完成[開始使用]或[開始使用資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/)教學課程。  

## <a name="string-length-validation"></a>將驗證程式碼新增至行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>更新用戶端

現在，行動服務已經過設定而會驗證資料，並針對無效的文字長度傳送錯誤回應，您必須更新應用程式以便處理驗證的錯誤回應。用戶端應用程式呼叫  `IMobileServiceTable<TodoItem].InsertAsync()` 時，將會收到錯誤。

1. 在 Visual Studio 的 [方案總管] 視窗中導覽至 JavaScript 用戶端專案，然後展開 **js** 資料夾。開啟 default.js 檔案。

2. 在 default.js 中，以下列函數定義取代現有的 **insertTodoItem** 函數：


        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoIte
	ms.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

   	此版本的函數包含錯誤處理，並且會顯示 `MessageDialog`，其中含有來自回應、狀態文字和狀態代碼的錯誤訊息。

## <a name="test-length-validation"></a>測試長度驗證

1. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下 JavaScript 用戶端應用程式專案，然後按一下 [**偵錯]**、[**開始新執行個體**]。

2. 為新的 todo 項目輸入長度超過 10 個字元的文字，然後按一下 [**儲存**]。

    ![][1]

3. 此時會出現如下的訊息對話方塊，以回應無效文字。

    ![][2]

## <a name="add-timestamp"></a>新增 CompleteDate 的時間戳記欄位 


[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>更新用戶端以顯示 CompleteDate

最後步驟是更新用戶端以顯示此新的 **completeDate** 資料。 


1. 在 Visual Studio 的 [方案總管] 中，在 JavaScript 用戶端專案中開啟 default.html 檔案。以下列定義來取代繫結範本  `div` 標籤元素。接著，請儲存檔案。這會新增 `div` 標籤，其中 innerText 屬性會繫結至 **completeDate**。
	      
        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: 3">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
              dataContext: this" />
            <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
              data-win-bind="innerText: text">
            </div>
            <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
              data-win-bind="innerText: completeDate">
            </div>
          </div>
        </div>



2. 在 default.js 中，移除現有 **refreshTodoItems** 函數中的 `.Where` 子句函數，使完成的 todoitem 納入結果中。

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listIte
	ms.winControl.itemDataSource = todoIte
	ms.dataSource;
                    });
            };


3. 在 default.js 中，依照下列方式更新 **updateCheckedTodoItem** 函數，使項目可在更新之後重新整理，並使完成的項目不會從清單中移除。接著，請儲存檔案。	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. 在 Visual Studio 的 [方案總管] 視窗中，以滑鼠右鍵按一下 [**方案**]，然後按一下 [**重建方案**]，以重新建置用戶端和 .NET 後端服務。驗證專案已順利建置，且未發生錯誤。

    ![][3]
	
5. 按 **F5** 鍵，在本機執行用戶端應用程式和服務。新增某些項目，並加以點選將其標示為「完成」，以檢視更新的 **CompleteDate** 時間戳記。

    ![][4]

6. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下 Todolist 服務專案，然後按一下 [**發佈**]。使用您從 Azure 入口網站下載的發佈設定檔案，將您的 .NET 後端服務發佈至 Microsoft Azure。

7. 將行動服務位址的連線取消註解，以更新用戶端專案的 default.js 檔案。對 Azure 帳戶中代管的 .NET 後端進行應用程式測試。




## <a name="next-steps"> </a>後續步驟

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍]。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

* [使用者的服務端授權]
  <br/>了解如何根據通過驗證的使用者識別碼篩選資料。

* [開始使用推播通知] 
  <br/>了解如何將極為基本的推播通知傳送到應用程式。

* [行動服務 .NET 做法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET

<!-- Anchors. -->
[新增字串長度驗證]: #string-length-validation
[更新用戶端以支援驗證]: #update-client-validation
[測試長度驗證]: #test-length-validation
[新增 CompleteDate 的時間戳記欄位]: #add-timestamp
[更新用戶端以顯示 CompleteDate]: #update-client-timestamp
[後續步驟]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started/#create-new-service
[使用者的服務端授權]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet
[開始使用]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript 和 HTML]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-js

[管理入口網站]: https://manage.windowsazure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 做法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library



<!--HONumber=42-->
