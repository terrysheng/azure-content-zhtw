<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# 開始使用行動服務中的資料

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

本主題將示範如何使用 Azure 行動服務，以在 HTML 應用程式中運用資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。

> [WACOM.NOTE]本教學課程旨在協助您深入了解行動服務如何讓您能夠使用 Azure 來儲存及擷取 HTML 應用程式的資料。因此，本主題將逐步說明已在行動服務快速入門中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮首先完成教學課程[開始使用行動服務][]。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 HTML 應用程式專案][]
2.  [建立行動服務][]
3.  [新增用於儲存的資料表][]
4.  [更新應用程式以使用行動服務][]
5.  [針對行動服務進行應用程式測試][]

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Azure 免費試用</a>。</p></div>

### 其他需求

您可以在任何網頁伺服器上裝載 GetStartedWithData 應用程式。不過，為了方便起見，我們提供可讓您在 `http://localhost:8000` 上執行應用程式的指令碼。

-   若要使用 localhost，本教學課程需要您在本機電腦上執行下列其中一部網頁伺服器：

    -   **在 Windows 上**：IIS Express。IIS Express 是由 [Microsoft Web Platform Installer] 所安裝。
    -   **在 MacOS X 上**：Python (應該已安裝)。
    -   **在 Linux 上**：Python。您必須安裝 [最新版本的 Python]。

    您可以使用任何網頁伺服器來裝載應用程式，但是這些是已下載指令碼所支援的網頁伺服器。

-   支援 HTML5 的網頁瀏覽器。

## <a name="download-app"></a><span class="short-header">下載專案</span>下載 GetStartedWithData 專案

本教學課程是採用 [GetStartedWithData 應用程式][] (HTML5 應用程式) 所建立。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務快速入門所產生的應用程式兩者的 UI 完全相同。

1.  [下載 HTML 應用程式專案檔案][GetStartedWithData 應用程式]。

2.  在 HTML 編輯器中，開啟下載的專案並檢查 app.js 檔案。

    請注意，新增的項目會儲存在記憶體內部的 **Array** 物件 (**staticItems**) 中。重新整理此頁面，資料會隨即消失。

3.  從 **server** 子資料夾中啟動下列其中一個命令檔案。

    -   **launch-windows** (Windows 電腦)
    -   **launch-mac.command** (Mac OS X 電腦)
    -   **launch-linux.sh** (Linux 電腦)

    <div class="dev-callout"><b>注意</b>
<p>在 Windows 電腦上，PowerShell 要求您確認想要執行指令碼時，請輸入 'R'。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。</p>
</div>

    這樣會在本機電腦上啟動 Web 伺服器來裝載新的應用程式。

4.  在網頁瀏覽器中開啟 URL <http://localhost:8000/> 來啟動應用程式。

5.  在應用程式中，於 [Enter new task] 中輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下 [新增]。

    ![][]

    請注意，儲存的文字會新增至 **staticItems** 陣列，頁面會重新整理，以顯示新的項目。

## <a name="create-service"></a><span class="short-header">建立行動服務</span>在管理入口網站中建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">新增資料表</span>將資料表新增至行動服務

若要能夠在新行動服務中儲存應用程式資料，您必須先在相關聯的 SQL Database 執行個體中建立新的資料表。

1.  在管理入口網站中，按一下 **[行動服務]**，然後按一下您剛剛建立的行動服務。

2.  按一下 [資料] 索引標籤，然後按一下 [建立]。

    ![][1]

    這樣做會顯示 [建立新資料表] 對話方塊。

3.  在 [資料表名稱] 中輸入 *TodoItem*，然後按一下核取按鈕。

    ![][2]

    這會建立包含預設權限集的新儲存資料表 **TodoItem**。這表示擁有應用程式金鑰 (隨應用程式散佈) 的人都可以存取與變更資料表中的資料。

    <div class="dev-callout"> 
<b>注意</b> 
<p>行動服務快速入門中使用相同的資料表名稱。但每個資料表都會以給定行動服務特有的結構描述建立。目的是為了防止多個行動服務使用相同資料庫時產生資料衝突。</p> 
</div>

4.  按一下新的 **TodoItem** 資料表，並驗證其中不含資料列。

5.  按一下 [資料行] 索引標籤。驗證是否已為您自動建立下列預設資料行：

    | 資料行名稱    | 類型             | 索引                               |
    |---------------|------------------|------------------------------------|
    | id            | 字串             | 已編製索引                         |
    | \_\_createdAt | 日期             | 已編製索引                         |
    | \_\_updatedAt | 日期             | <font color="transparent">-</font> |
    | \_\_version   | 時間戳記 (MSSQL) | <font color="transparent">-</font> |

    此為行動服務資料表的最低需求。

    <div class="dev-callout"><b>注意</b>
<p>若您的行動服務啟用動態結構描述，當行動服務透過插入或更新操作收到 JSON 物件時，便會自動建立新資料行。</p>
</div>

6.  在 [設定] 索引標籤中，驗證 `localhost` 已列在 [Cross-Origin Resource Sharing (CORS)] 的 [Allow requests from host names] 清單中。否則，請在 [主機名稱] 欄位中輸入 `localhost`，然後按一下 [儲存]。

    ![][3]

    <div class="dev-callout"><b>注意</b>
<p>如果您將快速入門應用程式部署至 localhost 以外的 Web 伺服器，您必須將該 Web 伺服器的主機名稱新增至 [允許提出要求的主機名稱] 清單。如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dn155871.aspx" target="_blank">跨原始來源資源分享</a> (英文)。</p>
</div>

現在您已準備好將新的行動服務作為應用程式的資料儲存區使用。

## <a name="update-app"></a><span class="short-header">更新應用程式</span>更新應用程式以使用行動服務進行資料存取

您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1.  在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

2.  按一下 [儀表板] 索引標籤並記下 [網站 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

    ![][4]

    從應用程式程式碼存取行動服務時，您將會用到這些值。

3.  在您的 Web 編輯器中，開啟 index.html 專案檔案，並將下列內容新增至頁面的指令碼參考：

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

4.  在此編輯器中，開啟檔案 app.js，取消註解下列定義 **MobileServiceClient** 變數的程式碼，並在 **MobileServiceClient** 建構函式中依序提供行動服務的 URL 和應用程式金鑰：

        var MobileServiceClient = WindowsAzure.MobileServiceClient,
            client = new MobileServiceClient('AppUrl', 'AppKey'),               

    這會建立 MobileServiceClient 的新執行個體，可用來存取您的行動服務。

5.  註解化下列程式碼行：

        var itemCount = 0;
        var staticItems = [];

    資料會被儲存在行動服務，而非儲存在記憶體內部陣列中。

6.  取消註解下列程式碼行：

        todoItemTable = client.getTable('todoitem');

    此程式碼會為 SQL Database **TodoItem** 建立 Proxy 物件 (**todoItemTable**)。

7.  以下列程式碼取代 **$('\#add-item').submit** 事件處理常式：

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

8.  以下列程式碼取代 **refreshTodoItems** 方法：

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

9. 以下列程式碼取代 <strong>$(document.body).on('change', '.item-text')</strong> 和 <strong>$(document.body).on('change', '.item-complete')</strong> 事件處理常式：

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

10. 以下列程式碼取代 <strong>$(document.body).on('click', '.item-delete')</strong> 事件處理常式：

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	當按一下 [刪除]<strong></strong> 按鈕時，系統會傳送刪除到行動服務。

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

<h2><a name="test-app"></a><span class="short-header">測試應用程式</span>在您的新行動服務中測試應用程式</h2>

1.  在網頁瀏覽器中重新載入 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>，以啟動應用程式。

    <div class="dev-callout"><b>注意</b>
	<p>如果您需要重新啟動網頁伺服器，請重複第一部分中的步驟。</p>
    </div>

2.  和之前一樣，在 [Enter new task] 中輸入文字，然後按一下 [新增]。

    這會傳送新項目以插入至行動服務。

3.  在[管理入口網站][]中按一下 [行動服務]，然後按一下您的行動服務。

4.  按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

    ![][5]

    請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

5.  在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 [重新整理]。

    請注意，完成值已從 [false] 變更為 [true]。

6.  在 app.js 專案檔案中，尋找 **RefreshTodoItems** 方法，並以下列程式碼取代定義 `query` 的程式碼字行：

        var query = todoItemTable.where({ complete: false });

7.  再次載入此頁面，並勾選清單中的另一個項目。

    請注意，所有已核取的項目都會從清單中消失。現在在行動服務來回行程期間的更新結果中，會傳回篩選後的資料。

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 HTML 應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料][]
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍][]
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料數列之後，若要了解如何驗證應用程式的使用者，您可以完成[開始使用驗證][]來嘗試另一個教學課程。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-html
  [下載 HTML 應用程式專案]: #download-app
  [建立行動服務]: #create-service
  [新增用於儲存的資料表]: #add-table
  [更新應用程式以使用行動服務]: #update-app
  [針對行動服務進行應用程式測試]: #test-app
  [Azure 免費試用]: http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F
  [GetStartedWithData 應用程式]: http://go.microsoft.com/fwlink/?LinkID=286345
  []: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png
  [跨原始來源資源分享]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
  [管理入口網站]: https://manage.windowsazure.com/
  [5]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png
  [使用指令檔驗證與修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-html
  [使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-html
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-html
