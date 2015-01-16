<properties urlDisplayName="Validate Data" pageTitle="使用伺服器指令碼驗證與修改資料 (JavaScript) | 行動開發人員中心 " metaKeywords="" description="了解如何驗證和修改從 Windows 市集 JavaScript 應用程式使用伺服器指令碼傳送的資料。" metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# 使用伺服器指令碼在行動服務中驗證與修改資料 

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。因為伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 Windows 市集應用程式，以充分利用這些新行為。

本教學課程將逐步引導您完成下列基本步驟：

1. [新增字串長度驗證]
2. [更新用戶端以支援驗證]
3. [插入時新增時間戳記]
4. [更新用戶端以顯示時間戳記]

本教學課程會以先前的教學課程[開始使用資料]中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料]。  

## <a name="string-length-validation"></a>新增驗證

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1. 登入 [Azure 管理入口網站]，按一下 [**行動服務**]，然後按一下您的應用程式。 

   	![][0]

2. 按一下 [**資料**] 索引標籤，然後按一下 **TodoItem** 資料表。

   	![][1]

3. 按一下 [**指令碼**]，然後選取 [**Insert**] 作業。

   	![][2]

4. 以下列函數取代現有的指令碼，然後按一下 [**儲存**]。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    本指令碼會檢查 **TodoItem.text** 屬性的長度，並在長度超過 10 個字元時傳送錯誤回應。否則，系統會呼叫 **execute** 方法以完成插入。

    <div class="dev-callout"> 
	<b>注意</b> 
	<p>您可以移除 [<strong>指令碼</strong>] 索引標籤上已註冊的指令碼，方法是按一下 [<strong>清除</strong>]，然後按一下 [<strong>儲存</strong>]。</p></div>	

## <a name="update-client-validation"></a>更新用戶端

現在，行動服務正在驗證資料並傳送錯誤回應，您必須更新您的應用程式以便處理驗證的錯誤回應。

1. 在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成教學課程 [開始使用資料] 時所修改的專案。

2. 按 **F5** 鍵以執行應用程式，然後在 [**Insert a TodoItem**] 鍵入超過 10 個字元的文字，並按一下 [**儲存**]。

   	請留意到，由於行動服務傳回了 400 回應 (不正確的要求)，因此應用程式發出未處理的錯誤。

6. 	開啟檔案 default.js，然後以下列程式碼取代現有的 **InsertTodoItem** 方法：

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            }, function (error) {
                // Create the error message dialog and set its content to the error
                // message contained in the response.
                var msg = new Windows.UI.Popups.MessageDialog(
                    error.request.responseText);
                msg.showAsync();
            });
        };

   	此版本的方法包含會在對話方塊中顯示錯誤回應的錯誤處理。

## <a name="add-timestamp"></a>新增時間戳記

上一個工作驗證了插入操作，並已接受或拒絕此操作。現在，您將更新插入資料，方法是在插入物件之前，使用可將時間戳記屬性新增至物件的伺服器指令碼。

<div class="dev-callout"><b>注意</b>
<p>此處所示範的 <b>createdAt</b> 時間戳記屬性現為備援屬性。行動服務會自動建立每個資料表的 <b>__createdAt</b> 系統屬性。</p>
</div>

1. 在 [管理入口網站] 的 [**指令碼**] 索引標籤中，使用下列函數來取代目前的 **Insert** 指令碼，然後按一下 [**儲存**]。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    此函數可強化上一個 insert 指令碼，方法是在呼叫 **request**.**execute** 來插入物件之前，先將新的 **createdAt** 間戳記屬性新增至物件。 

    <div class="dev-callout"><b>注意</b>
	<p>首次執行插入指令碼時，必須啟用動態結構描述。在啟用動態結構描述的情況下，行動服務會在首次執行時自動將 <strong>createdAt</strong> 欄新增至 <strong>TodoItem</strong> 資料表。依預設，新行動服務的動態結構描述為已啟用，您應該先將它停用之後再將應用程式發行至 Windows 市集。</p>
    </div>

2. 在 Visual Studio 中按 **F5** 鍵以執行應用程式，然後在 [**Insert a TodoItem**] 輸入文字 (少於 10 個字元)，並按一下 [**儲存**]。

   	請注意新的時間戳記不會出現在應用程式 UI 中。

3. 回到管理入口網站中，按一下 **todoitem** 資料表中的 [**瀏覽**] 索引標籤。
   
   	請注意，現在會有一個 **createdAt** 資料行，且新插入的項目會具有時間戳記值。
  
接下來，您必須更新 Windows 市集應用程式以便顯示此新資料欄。

## <a name="update-client-timestamp"></a>重新更新用戶端

針對無法序列化成已定義類型屬性的回應，行動服務用戶端將忽略該回應中的任何資料。最後步驟是更新用戶端以顯示此新資料。

1. 在 Visual Studio 中開啟 default.html 檔案，然後在 TemplateItem 格線中新增下列 HTML 元素：
	      
        <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
            data-win-bind="innerText: createdAt"></div>  

   	如此會顯示新的 **createdAt** 屬性。 
	
6. 按 **F5** 鍵以執行應用程式。 

   	請留意到，只有在您更新 insert 指令碼後插入的項目，才會顯示時間戳記。

7. 在 default.js 檔案中，以下列程式碼取代現有的 **RefreshTodoItems** 方法：

        var refreshTodoItems = function () {
            // More advanced query that filters out completed items. 
            todoTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            })
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	This method updates the query to also filter out items that do not have a timestamp value.
	
8. 按 **F5** 鍵以執行應用程式。

   	請留意到，所有已建立但不含時間戳記值的項目，都會從 UI 中消失。

您已完成此使用資料教學課程。

## <a name="next-steps"> </a>後續步驟

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍]。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

* [使用指令碼授權使用者]
  <br/>了解如何根據通過驗證的使用者識別碼篩選資料。

* [開始使用推播通知] 
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

* [行動服務伺服器指令碼參考]
  <br/>進一步了解註冊和使用伺服器指令碼。

<!-- Anchors. -->
[新增字串長度驗證]: #string-length-validation
[更新用戶端以支援驗證]: #update-client-validation
[插入時新增時間戳記]: #add-timestamp
[更新用戶端以顯示時間戳記]: #update-client-timestamp
[後續步驟]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started/#create-new-service
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-js
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-js
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-js
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-js
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-js
[C# 和 XAML]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet

[管理入口網站]: https://manage.windowsazure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
