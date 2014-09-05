<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

開始使用行動服務中的資料
========================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

本主題將示範如何使用 Azure 行動服務，以在 HTML 應用程式中運用資料。在本教學課程中，您將下載可在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。

**注意**

本教學課程旨在協助您深入了解行動服務可以如何讓您使用 Azure，以儲存與擷取 HTML 應用程式的資料。因此，本主題將逐步引導您完成在行動服務快速入門中已為您完成的許多步驟。如果這是您第一次使用行動服務，請考慮首先完成教學課程[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-html)。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 HTML 應用程式專案](#download-app)
2.  [建立行動服務](#create-service)
3.  [新增儲存體的資料表](#add-table)
4.  [更新應用程式以使用行動服務](#update-app)
5.  [對行動服務測試應用程式](#test-app)

**注意**

若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F)。

### 其他需求

您可以在任何網頁伺服器上裝載 GetStartedWithData 應用程式。不過，為了方便起見，我們提供可讓您在 `http://localhost:8000` 上執行應用程式的指令碼。

-   若要使用 localhost，本教學課程需要您在本機電腦上執行下列其中一部網頁伺服器：

    -   **在 Windows 上**：IIS Express。IIS Express 是透過 [Microsoft Web Platform Installer] 所安裝。
    -   **在 MacOS X 上**：Python (應該已安裝)。
    -   **在 Linux 上**：Python。您必須安裝 [最新版本的 Python]。

    您可以使用任何網頁伺服器來裝載應用程式，但是這些是已下載指令碼所支援的網頁伺服器。

-   支援 HTML5 的網頁瀏覽器。

下載專案下載 GetStartedWithData 專案
------------------------------------

本教學課程是採用 [GetStartedWithData 應用程式](http://go.microsoft.com/fwlink/?LinkID=286345) (HTML5 應用程式) 所建立。除了會將新增項目儲存在本機記憶體中之外，此應用程式的 UI 與行動服務快速入門所產生的應用程式完全相同。

1.  [下載 HTML 應用程式專案檔案](http://go.microsoft.com/fwlink/?LinkID=286345)。

2.  在 HTML 編輯器中，開啟下載的專案並檢查 app.js 檔案。

        Notice that added items are stored in an in-memory **Array** object (**staticItems**).Refresh the page, and the data disappears.此資料不會保留下來。

3.  從 **server** 子資料夾中啟動下列其中一個命令檔案。

    -   **launch-windows** (Windows 電腦)
    -   **launch-mac.command** (Mac OS X 電腦)
    -   **launch-linux.sh** (Linux 電腦)

    **注意**

    在 Windows 電腦上，PowerShell 要求您確認想要執行指令碼時，請輸入 'R'。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。

    這會在本機電腦上啟動網頁伺服器，以裝載新的應用程式。

4.  在網頁瀏覽器中開啟 URL <http://localhost:8000/>，以啟動應用程式。

5.  在應用程式中，於 **[Enter new task]** 中輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下 **[新增]**。

	![][0]  

	Notice that the saved text is added to the **staticItems** array, and the page is refreshed to display the new item.

建立行動服務在管理入口網站中建立新的行動服務
--------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

新增資料表將新資料表新增至行動服務
----------------------------------

若要能夠在新行動服務中儲存應用程式資料，您必須先在相關聯的 SQL Database 執行個體中建立新的資料表。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  按一下 **[資料]** 索引標籤，然後按一下 **[建立]**。

	![][5]

	This displays the **Create new table** dialog.

3.  在 **[資料表名稱]** 中輸入 *TodoItem*，然後按一下核取按鈕。

	![](./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png)

	這會建立包含預設權限集的新儲存資料表 **TodoItem**。這表示擁有應用程式金鑰 (隨應用程式散佈) 的人都可以存取與變更資料表中的資料。

    <div class="dev-callout"> 
    <b>Note</b> 
    <p>The same table name is used in Mobile Services quickstart.However, each table is created in a schema that is specific to a given mobile service.This is to prevent data collisions when multiple mobile services use the same database.</p> 
    </div>

1.  按一下新的 **TodoItem** 資料表，並驗證其中不含資料列。

2.  按一下 **[資料行]** 索引標籤。驗證是否已為您自動建立下列預設資料行：

    <table  border="1" cellpadding="10">
     	<tr>
    	<th>資料行名稱</th>
    
    	<th>類型</th>
    
    	<th>索引</th>
    
     	</tr>
    
     	<tr>
    	<td>id</td>
    
    	<td>字串</td>
    
    	<td>已編製索引</td>
    
     	</tr>
    
     	<tr>
    	<td>__createdAt</td>
    
    	<td>日期</td>
    
    	<td>已編製索引</td>
    
     	</tr>
    
     	<tr>
    	<td>__updatedAt</td>
    
    	<td>日期</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
    
     	<tr>
    	<td>__version</td>
    
    	<td>時間戳記 (MSSQL)</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
     	
     	</table>

	此為行動服務資料表的最低需求。

	<div class="dev-callout"><b>Note</b>
	<p>When dynamic schema is enabled on your mobile service, new columns are created automatically when JSON objects are sent to the mobile service by an insert or update operation.</p>
	</div>

1.  在 **[設定]** 索引標籤中，驗證 `localhost` 已列在 **[Cross-Origin Resource Sharing (CORS)]** 的 **[Allow requests from host names]** 清單中。否則，請在 **[主機名稱]** 欄位中輸入 `localhost`，然後按一下 **[儲存]**。

	![](./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png)

    <div class="dev-callout"><b>Note</b>
        <p>If you deploy the quickstart app to a web server other than localhost, you must add the host name of the web server to the <strong>Allow requests from host names</strong> list.For more information, see <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dn155871.aspx" target="_blank">Cross-origin resource sharing</a>.</p>
    </div>

現在您已準備好將新的行動服務作為應用程式的資料儲存區使用。

更新應用程式更新應用程式以使用行動服務進行資料存取
--------------------------------------------------

現在您的行動服務已準備就緒，您可以更新應用程式以便在行動服務 (而非本機集合) 中儲存項目。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  按一下 **[儀表板]** 索引標籤，並記下 **[網站 URL]**，然後按一下 **[管理金鑰]**，並記下 **[應用程式金鑰]**。

	![][8]

從應用程式程式碼中存取行動服務時，您將會用到這些值。

1.  在您的 Web 編輯器中，開啟 index.html 專案檔案，並將下列內容新增至頁面的指令碼參考：

         <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

2.  在此編輯器中，開啟檔案 app.js，取消註解下列定義 **MobileServiceClient** 變數的程式碼，並在 **MobileServiceClient** 建構函式中依序提供行動服務的 URL 和應用程式金鑰：

         var MobileServiceClient = WindowsAzure.MobileServiceClient,
        client = new MobileServiceClient('AppUrl', 'AppKey'),               

這會建立用來存取行動服務的新 MobileServiceClient 執行個體。

1.  註解化下列程式碼行：

         var itemCount = 0;
        var staticItems = [];

    資料會被儲存在行動服務，而非儲存在記憶體內部陣列中。

2.  取消註解下列程式碼行：

         todoItemTable = client.getTable('todoitem');

        This code creates a proxy object (**todoItemTable**) for the SQL Database **TodoItem**. 

3.  以下列程式碼取代 **\$('\#add-item').submit** 事件處理常式：

         $('#add-item').submit(function(evt) {
        var textbox = $('#new-item-text'),
        itemText = textbox.val();
        if (itemText !== '') {
        todoItemTable.insert({ text:itemText, complete:false })
        .then(refreshTodoItems);
             }
        textbox.val('').focus();
        evt.preventDefault();
         });

此程式碼會將新的項目插入資料表中。

1.  以下列程式碼取代 **refreshTodoItems** 方法：

         function refreshTodoItems() {

        var query = todoItemTable;

        query.read().then(function(todoItems) {
        listItems = $.map(todoItems, function(item) {
        return $('<li>')
        .attr('data-todoitem-id', item.id)
        .append($('<button class="item-delete">Delete</button>'))
        .append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
        .append($('<div>').append($('<input class="item-text">').val(item.text)));
                 });
                           
        $('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
        $('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
             });
         }

    這會傳送可傳回所有項目的查詢至行動服務。重複列舉結果並在頁面上顯示資料。

2.  以下列程式碼取代 **\$(document.body).on('change', '.item-text')** 和 **\$(document.body).on('change', '.item-complete')** 事件處理常式：

         $(document.body).on('change', '.item-text', function() {
        var newText = $(this).val();
        todoItemTable.update({ id:getTodoItemId(this), text:newText });
         });

        $(document.body).on('change', '.item-complete', function() {
        var isComplete = $(this).prop('checked');
        todoItemTable.update({ id:getTodoItemId(this), complete:isComplete })
        .then(refreshTodoItems);
         });
         
        在變更文字或勾選方塊時，這會將項目更新傳送到行動服務。

3.  以下列程式碼取代 **\$(document.body).on('click', '.item-delete')** 事件處理常式：

        $(document.body).on('click', '.item-delete', function () {
        todoItemTable.del({ id:getTodoItemId(this) }).then(refreshTodoItems);
        });

    當按一下 **[刪除]** 按鈕時，系統會傳送刪除到行動服務。

應用程式現已更新為使用行動服務進行後端儲存，現在可以開始對行動服務測試應用程式。

測試應用程式對您的新行動服務測試應用程式
----------------------------------------

1.  在網頁瀏覽器中重新載入 URL <http://localhost:8000/>，以啟動應用程式。

    **注意**

    如果您需要重新啟動網頁伺服器，請重複第一部分中的步驟。

2.  和之前一樣，在 **[Enter new task]** 中輸入文字，然後按一下 **[新增]**。

	This sends a new item as an insert to the mobile service.

3.  在[管理入口網站](https://manage.windowsazure.com/)中按一下 **[行動服務]**，然後按一下您的行動服務。

4.  按一下 **[資料]** 索引標籤，然後按一下 **[瀏覽]**。

	![][9]
          
	Notice that the **TodoItem** table now contains data, with id values generated by Mobile Services, and that columns have been automatically added to the table to match the TodoItem class in the app.

5.  在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 **[重新整理]**。

請注意，完成值已從 **[false]** 變更為 **[true]**。

1.  在 app.js 專案檔案中，尋找 **RefreshTodoItems** 方法，並以下列程式碼取代定義 `query` 的程式碼行：

         var query = todoItemTable.where({ complete:false });

2.  再次載入此頁面，並勾選清單中的另一個項目。

        Notice that the checked item now disappears from the list.Each update results in a round-trip to the mobile service, which now returns filtered data.

這將結束**開始使用資料**教學課程。

後續步驟
--------

本教學課程示範讓 HTML 應用程式能夠在行動服務中處理資料的基本概念。接著，考慮完成下列其中一個以您在此教學課程中建立的 GetStartedWithData 應用程式為基礎的教學課程。

-   [使用指令碼驗證及修改資料](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html)
    深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送的資料。

-   [使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-html)
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料數列之後，若要了解如何驗證應用程式的使用者，您可以完成[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-html)來嘗試另一個教學課程。

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-html
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-html

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[GetStartedWithData app]:  http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client

[Cross-origin resource sharing]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn155871.aspx