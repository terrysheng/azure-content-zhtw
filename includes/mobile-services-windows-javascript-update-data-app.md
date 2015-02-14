

1. 在 default.js 指令碼檔案中，於定義 todoItems 清單的程式碼文字行下方新增以下函式定義：
 
        // Add a filter that adds a header to prevent caching. This makes sure that the 
		// latest data is returned when the 'Refresh; button is clicked.        
        var noCachingFilter = function (request, next, callback) {
            if (request.type === 'GET' && !request.headers['If-Modified-Since']) {
                request.headers['If-Modified-Since'] = 'Mon, 27 Mar 1972 00:00:00 GMT';
            }
            next(request, callback);
        };

	如此能定義藉由新增  `If-Modified-Since` 標頭來避免用戶端快取的篩選函式。
 
2. 接著，取消註解或新增以下程式碼文字行，然後將 `<yourClient>` 取代為連接專案與行動服務時新增至 service.js 檔案中的變數：

		var todoTable = <yourClient>.withFilter(noCachingFilter).getTable('TodoItem');

   	此程式碼會使用快取篩選為新的資料庫資料表建立 Proxy 物件 (**todoTable**)。 

3. 使用下列程式碼來取代 **InsertTodoItem** 函數：

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	此程式碼會將新的項目插入資料表中。

3. 使用下列程式碼來取代 **RefreshTodoItems** 函式：

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	這會設定 todoTable 中項目集合的繫結，其中包含從行動服務中傳回的所有 **TodoItem** 物件。 

4. 使用下列程式碼來取代 **UpdateCheckedTodoItem** 函式：
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	這樣會將項目更新傳送到行動服務。

 應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。<!--HONumber=42-->
