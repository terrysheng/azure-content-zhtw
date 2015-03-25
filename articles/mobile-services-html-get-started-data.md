<properties 
	pageTitle="開始使用資料 (HTML 5) | 行動開發人員中心" 
	description="了解如何開始使用行動服務，在您的 HTML 應用程式中使用資料。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

本主題將示範如何使用 Azure 行動服務，以在 HTML 應用程式中運用資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。

>[AZURE.NOTE]本教學課程旨在協助您深入了解行動服務可以如何讓您使用 Azure，以儲存與擷取 HTML 應用程式的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮先完成<a href="/zh-tw/develop/mobile/tutorials/get-started-html">開始使用行動服務</a>教學課程。

本教學課程將逐步引導您完成下列基本步驟：

1. [下載 HTML 應用程式專案] 
2. [建立行動服務]
3. [新增用於儲存的資料表]
4. [更新應用程式以使用行動服務]
5. [針對行動服務進行應用程式測試]

> [AZURE.IMPORTANT] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F"%20target="_blank)。

###其他需求

您可以在任何網頁伺服器上裝載 GetStartedWithData 應用程式。不過，為了方便起見，我們提供可讓您在 `http://localhost:8000` 上執行應用程式的指令碼。
 
+ 若要使用 localhost，本教學課程需要您在本機電腦上執行下列其中一部網頁伺服器：

	+  **在 Windows 上**︰IIS Express。IIS Express 是透過 [Microsoft Web Platform Installer] 所安裝。   
	+  **在 MacOS X 上**︰Python (應該已安裝)。
	+  **在 Linux 上**︰Python。您必須安裝[最新版本的 Python]。 
	
	您可以使用任何網頁伺服器來裝載應用程式，但是這些是已下載指令碼所支援的網頁伺服器。  

+ 支援 HTML5 的網頁瀏覽器。

<h2><a name="download-app"></a>下載 GetStartedWithData 專案</h2>

本教學課程以 [GetStartedWithData 應用程式] (HTML5 應用程式) 為基礎。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務快速入門所產生的應用程式兩者的 UI 完全相同。 

1. [下載 HTML 應用程式專案檔案][GetStartedWithData 應用程式]。

2. 在 HTML 編輯器中，開啟下載的專案並檢查 app.js 檔案。

   	請注意，新增的項目會儲存在記憶體內部的 **Array** 物件 (**staticItems**) 中。Refresh the page, and the data disappears.此資料不會保留下來。

3. 從 **server** 子資料夾中啟動下列其中一個命令檔案。

	+ **launch-windows** (Windows 電腦) 
	+ **launch-mac.command** (Mac OS X 電腦)
	+ **launch-linux.sh** (Linux 電腦)

	> [AZURE.NOTE] 在 Windows 電腦上，PowerShell 要求您確認想要執行指令碼時，請輸入 'R'。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。
	
	這樣會在本機電腦上啟動 Web 伺服器來裝載新的應用程式。

4. 在網頁瀏覽器中開啟 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> 來啟動應用程式。

5. 在應用程式中，於 [**輸入新工作**] 中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下 [**新增**]。

   	![][0]  

   	請注意，儲存的文字會新增至 **staticItems** 陣列，頁面會重新整理，以顯示新的項目。

<h2><a name="create-service"></a>在管理入口網站中建立新的行動服務</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>將新資料表新增至行動服務</h2>

若要能夠在新行動服務中儲存應用程式資料，您必須先在相關聯的 SQL Database 執行個體中建立新的資料表。

1. 在管理入口網站中，**按一下 [行動服務]**，然後按一下您剛剛建立的行動服務。

2. 按一下 [資料] ****索引標籤，然後按一下 [建立]****。

   	![][5]

   	這樣做會顯示 [**建立新資料表**] 對話方塊。

3. 在 [**資料表名稱**] 中輸入 _TodoItem_，然後按一下核取按鈕。

  	![][6]

  	這會建立包含預設權限集的新儲存資料表 **TodoItem**。這表示擁有應用程式金鑰 (隨應用程式散佈) 的人都可以存取與變更資料表中的資料。

    > [AZURE.NOTE] 行動服務快速入門中使用相同的資料表名稱。不過，會在指定行動服務特定的結構描述中建立每個資料表.目的是為了防止多個行動服務使用相同資料庫時產生資料衝突。

4. 按一下新的 **TodoItem** 資料表，並驗證其中不含資料列。

5. 按一下 [資料行] ****索引標籤。驗證是否已為您自動建立下列預設資料行： 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>資料行名稱</th>
 	<th>類型</th>
 	<th>索引</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>string</td>
 	<td>Indexed</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>date</td>
 	<td>Indexed</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>date</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 

  	此為行動服務資料表的最低需求。 

    > [AZURE.NOTE] 若您的行動服務啟用動態結構描述，當行動服務透過插入或更新操作收到 JSON 物件時，便會自動建立新資料行。

6. 在 [**設定**] 索引標籤中，驗證  `localhost` 已列在 [**跨原始資源共用 (CORS)**] 的 [**允許來自主機名稱的要求**] 清單中。否則，請在 [**主機名稱**] 欄位中輸入  `localhost`，然後按一下 [**儲存**]。

  	![][11]

	> [AZURE.IMPORTANT] 如果您將快速入門應用程式部署至 localhost 以外的 Web 伺服器，您必須將該 Web 伺服器的主機名稱新增至 [**允許提出要求的主機名稱**] 清單。如需詳細資訊，請參閱[跨原始來源資源分享http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank)。

現在您已準備好將新的行動服務作為應用程式的資料儲存區使用。

<h2><a name="update-app"></a>更新應用程式以使用行動服務進行資料存取</h2>

您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機集合) 中儲存項目。 

3. 在「管理入口網站」中，按一下 [行動服務]****，然後按一下您剛剛建立的行動服務。

4. 按一下 [儀表板]**** 索引標籤並記下 [網站 URL]****，然後按一下 [管理金鑰]**** 並記下 [應用程式金鑰]****。

   	![][8]

  	從應用程式程式碼存取行動服務時，您將會用到這些值。

1. 在您的 Web 編輯器中，開啟 index.html 專案檔案，並將下列內容新增至頁面的指令碼參考：

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

5. 在此編輯器中，開啟檔案 app.js，取消註解下列定義 **MobileServiceClient** 變數的程式碼，並在 **MobileServiceClient** 建構函式中依序提供行動服務的 URL 和應用程式金鑰：

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	這會建立 MobileServiceClient 的新執行個體，可用來存取您的行動服務。

6. 註解化下列程式碼行：

		var itemCount = 0;
		var staticItems = [];

	資料會被儲存在行動服務，而非儲存在記憶體內部陣列中。

6. 取消註解下列程式碼行：

        todoItemTable = client.getTable('todoitem');

   	此程式碼會為 SQL Database **TodoItem** 建立 Proxy 物件 (**todoItemTable**)。 

7. 以下列程式碼取代 **$('#add-item').submit** 事件處理常式：

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	此程式碼會將新的項目插入資料表中。

8. 以下列程式碼取代 **refreshTodoItems** 方法：

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
					   
				$('#todo-items').empty().append(listItems).toggle(listIte
	ms.length > 0);
				$('#summary').html('<strong>' + todoIte
	ms.length + '</strong> item(s)');
			});
		}
	   

   這會傳送可傳回所有項目的查詢至行動服務。重複列舉結果並在頁面上顯示資料。 

9. 以下列程式碼取代 **$(document.body).on('change', '.item-text')** 和 **$(document.body).on('change', '.item-complete')** 事件處理常式：
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	在變更文字或勾選方塊時，這會將項目更新傳送到行動服務。

10. 以下列程式碼取代 **$(document.body).on('click', '.item-delete')** 事件處理常式：

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	按一下 [刪除]**** 按鈕時，系統會傳送刪除到行動服務。

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

<h2><a name="test-app"></a>對新的行動服務進行應用程式測試</h2>

4. 在網頁瀏覽器中重新載入 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>，以啟動應用程式。

    > [AZURE.NOTE] 如果您需要重新啟動網頁伺服器，請重複第一部分中的步驟。

2. 和之前一樣，在 [Enter new task]**** 中輸入文字，然後按一下 [新增]****。 

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中，按一下 [行動服務]****，然後按一下您的行動服務。

4. 按一下 [資料]**** 索引標籤，然後按一下 [瀏覽]****。

   	![][9]
  
   	請注意，**TodoItem** 資料表現在包含資料，其中識別碼值由行動服務產生，且資料欄已自動新增到資料表以符合應用程式中的 TodoItem 類別。

5. 在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 [重新整理]****。 

  	請注意，完成值已從 **false** 變更為 **true**。

6. 在 app.js 專案檔案中，尋找 **RefreshTodoItems** 方法，並以下列程式碼取代定義  `query` 的程式碼字行：

   		var query = todoItemTable.where({ complete: false });

7. 再次載入此頁面，並勾選清單中的另一個項目。

   	請注意，所有已核取的項目都會從清單中消失。每次更新都會往返行動服務一次，而現在會傳回篩選的資料。

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 HTML 應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

* [使用指令碼驗證及修改資料]
  <br/>深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍]
  <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。
 
完成資料數列之後，若要了解如何驗證應用程式的使用者，您可以完成[開始使用驗證]來嘗試另一個教學課程。

<!-- Anchors. -->
[下載 HTML 應用程式專案]: #download-app
[建立行動服務]: #create-service
[新增用於儲存的資料表]: #add-table
[更新應用程式以使用行動服務]: #update-app
[針對行動服務進行應用程式測試]: #test-app
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[使用指令碼驗證及修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-html
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-html
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-html

[Azure 管理入口網站]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[GetStartedWithData 應用程式]:  http://go.microsoft.com/fwlink/?LinkID=286345

[行動服務 HTML/JavaScript 做法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-html-js-client

[跨原始資源共用]: http://msdn.microsoft.com/library/windowsazure/dn155871.aspx



<!--HONumber=42-->
