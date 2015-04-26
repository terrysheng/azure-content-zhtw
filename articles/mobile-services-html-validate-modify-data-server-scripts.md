<properties 
	pageTitle="使用伺服器指令碼驗證與修改資料 (HTML 5) | 行動開發人員中心" 
	description="了解如何驗證和修改從 HTML 應用程式使用伺服器指令碼傳送的資料。" 
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
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# 使用伺服器指令碼在行動服務中驗證與修改資料 

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。由於伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 HTML 應用程式，以充分利用這些新行為。

本教學課程將逐步引導您完成下列基本步驟：

1. [新增字串長度驗證]
2. [更新用戶端以支援驗證]
3. [插入時新增時間戳記]
4. [更新用戶端以顯示時間戳記]

本教學課程會以上一堂[開始使用資料]教學課程中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料]。  

## <a name="string-length-validation"></a>新增驗證

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1. 登入「Azure 管理入口網站」[]，按一下 [行動服務]****，然後按一下您的應用程式。 

   	![][0]

2. 按一下 [**資料**] 索引標籤，然後按一下 **TodoItem** 資料表。

   	![][1]

3. 按一下 [指令碼]****，然後選取 [插入]**** 作業。

   	![][2]

4. 使用下列函式取代現有的指令碼，然後按一下 [**儲存**]。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: "Text cannot exceed 10 characters"
                });
            } else {
                request.execute();
            }
        }

    本指令碼會檢查 **TodoItem.text** 屬性的長度，並在長度超過 10 個字元時傳送錯誤回應。否則，系統會呼叫 **execute** 函數以完成插入。

    > [AZURE.TIP] 您可以將位於 [**指令碼**] 索引標籤上的已註冊指令碼移除，方法是依序按一下 [**清除**]、[**儲存**]。	

## <a name="update-client-validation"></a>更新用戶端

現在，行動服務正在驗證資料並傳送錯誤回應，您必須更新您的應用程式以便處理驗證的錯誤回應。

1. 從您完成[開始使用資料]教學課程時所修改之專案的 **server** 子資料夾，執行下列其中一個命令檔案。

	+ **launch-windows** (Windows 電腦) 
	+ **launch-mac.command** (Mac OS X 電腦)
	+ **launch-linux.sh** (Linux 電腦)

	> [AZURE.NOTE] 在 Windows 電腦上，PowerShell 要求您確認想要執行指令碼時，請輸入 `R`。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。

	這會在本機電腦上啟動 Web 伺服器，以代管應用程式。

1. 	開啟 app.js 檔案，然後以下列程式碼取代 **$('#add-item').submit()**  事件處理常式：

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems, function(error){
					alert(JSON.parse(error.request.responseText).error);
				});
			}
			textbox.val('').focus();
			evt.preventDefault();
		});

2. 在網頁瀏覽器中導覽至 <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>，在 [**新增工作**] 中輸入文字，然後按一下 [**新增**]。

   	請注意，作業失敗，而錯誤處理會在對話方塊中顯示錯誤回應。

## <a name="add-timestamp"></a>新增時間戳記

上一個工作驗證了插入操作，並已接受或拒絕此操作。現在，您將更新插入資料，方法是在插入物件之前，使用可將時間戳記屬性新增至物件的伺服器指令碼。

> [AZURE.NOTE] 此處所示範的 **createdAt** 時間戳記屬性現為備援屬性。行動服務會自動建立每個資料表的 **__createdAt** 系統屬性。

1. 在[管理入口網站]的 [**指令碼**] 索引標籤中，使用下列函數來取代目前的 **Insert** 指令碼，然後按一下 [**儲存**]。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: 'Text length must be under 10'
                });
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    此函數可強化上一個 insert 指令碼，方法是先將新的 **createdAt** 時間戳記屬性新增至物件，再呼叫 **request**.**execute**。 

    > [AZURE.IMPORTANT] 首次執行插入指令碼時，必須啟用動態結構描述。在已啟用動態結構描述的情況下，行動服務會在首次執行時將 **createdAt** 資料欄自動新增至 **TodoItem** 資料表。依預設會啟用新行動服務的動態結構描述，您應先加以停用，再發佈應用程式。

2. 在網頁瀏覽器中重新載入頁面，然後在 [**新增工作**] 中輸入文字 (少於 10 個字元)，然後按一下 [**新增**]。

   	請注意新的時間戳記不會出現在應用程式 UI 中。

3. 回到 [管理入口網站] 中，按一下 **todoitem** 資料表中的 [**Browse**] 索引標籤。
   
   	請注意，現在會有一個 **createdAt** 資料欄，且新插入的項目也具有時間戳記值。
  
接下來，您必須更新應用程式以顯示此新資料欄。

## <a name="update-client-timestamp"></a>重新更新用戶端

針對無法序列化成已定義類型屬性的回應，行動服務用戶端將忽略該回應中的任何資料。最後步驟是更新用戶端以顯示此新資料。

1. 在您的編輯器中開啟 app.js 檔案，然後以下列程式碼取代 **refreshTodoItems** 函數：

		function refreshTodoItems() {
			var query = todoItemTable.where(function () {
                return (this.complete === false);
            });

			query.read().then(function(todoItems) {
				var listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">')
							.prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text))
						.append($('<span class="timestamp">' 
							+ (item.createdAt && item.createdAt.toDateString() + ' '
							+ item.createdAt.toLocaleTimeString() || '') 
							+ '</span>')));

				});

				$('#todo-items').empty().append(listItems).toggle(listIte
	ms.length > 0);
				$('#summary').html('<strong>' + todoIte
	ms.length + '</strong> item(s)');
			});
		}

   	這會顯示新 **createdAt** 屬性的日期部分。 

2. 在您的編輯器中開啟 style.css 檔案，然後以下列程式碼取代  `item-text` 類別的樣式：

		.item-text { width: 70%; height: 26px; line-height: 24px; 
			border: 1px solid transparent; background-color: transparent; }
		.timestamp { width: 30%; height: 40px; font-size: .75em; }

	這會調整文字方塊的大小，並為新的時間戳記文字設定樣式。
	
6. 重新載入頁面。 	

   	請留意到，只有在您更新 insert 指令碼後插入的項目，才會顯示時間戳記。

7. 再次在 **refreshTodoItems** 函數中，以下列程式碼取代定義查詢的程式碼行：

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

   	This function updates the query to also filter out items that do not have a timestamp value.
	
8. 重新載入頁面。

   	請留意到，所有已建立但不含時間戳記值的項目，都會從 UI 中消失。

您已完成此使用資料教學課程。

## <a name="next-steps"> </a>後續步驟

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍]。

如需詳細資訊，請參閱[使用伺服器指令碼]和[行動服務 HTML/JavaScript 作法概念參考]


<!-- Anchors. -->
[新增字串長度驗證]: #string-length-validation
[更新用戶端以支援驗證]: #update-client-validation
[插入時新增時間戳記]: #add-timestamp
[更新用戶端以顯示時間戳記]: #update-client-timestamp
[後續步驟]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[使用伺服器指令碼]: /zh-tw/develop/mobile/how-to-guides/work-with-server-scripts
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-html
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-html
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-html
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-html
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-html

[管理入口網站]: https://manage.windowsazure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 HTML/JavaScript 做法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-html-js-client



<!--HONumber=42-->
