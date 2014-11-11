<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-js" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 使用伺服器指令碼在行動服務中驗證與修改資料



<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store C#">Windows 市集 C#</a>
<a href="/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript" class="current">Windows 市集 JavaScript</a>
<a href="/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a>
<a href="/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>


<div class="dev-center-tutorial-subselector">
	<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title=".NET backend">.NET 後端</a> | 
	<a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/"  title="JavaScript backend" class="current">JavaScript 後端</a>
</div>

本主題將示範如何在 Azure 行動服務中運用伺服器指令碼。您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。在本教學課程中，您將定義並註冊可驗證與修改資料的伺服器指令碼。因為伺服器端指令碼的行為常會影響用戶端，您也可以更新您的 Windows 市集應用程式，以充分利用這些新行為。

本教學課程將逐步引導您完成下列基本步驟：

1.  [新增字串長度驗證][新增字串長度驗證]
2.  [更新用戶端以支援驗證][更新用戶端以支援驗證]
3.  [插入時新增時間戳記][插入時新增時間戳記]
4.  [更新用戶端以顯示時間戳記][更新用戶端以顯示時間戳記]

本教學課程會以上一堂教學課程[開始使用資料][開始使用資料]中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須首先完成[開始使用資料][開始使用資料]。

## <a name="string-length-validation"></a>新增驗證

驗證使用者提交的資料長度一向是最佳做法。首先，註冊可驗證傳送至行動服務之字串資料長度的指令碼，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

    ![][0]

2.  按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

    ![][1]

3.  按一下 [指令碼]，然後選取 [插入] 作業。

    ![][2]

4.  以下列函數取代現有的指令碼，然後按一下 [儲存]。

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
	<p>您可以將位於 [指令碼] 索引標籤上的已註冊指令碼移除，方法是依序按一下 [清除]、[儲存]。</p>

    </div>

## <a name="update-client-validation"></a>更新用戶端

現在，行動服務正在驗證資料並傳送錯誤回應，您必須更新您的應用程式以便處理驗證的錯誤回應。

1.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成教學課程[開始使用資料][開始使用資料]時所修改的專案。

2.  按 **F5** 鍵以執行應用程式，然後在 [Insert a TodoItem] 中鍵入超過 10 個字元的文字，並按一下 [儲存]。

    請留意到，由於行動服務傳回了 400 回應 (不正確的要求)，因此應用程式發出未處理的錯誤。

3.  開啟檔案 default.js，然後以下列程式碼取代現有的 **InsertTodoItem** 方法：

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

<div class="dev-callout">

<b>注意</b>
<p>此處所示範的 <b>createdAt</b> 時間戳記屬性現為備援屬性。行動服務會自動建立每個資料表的 <b>__createdAt</b> 系統屬性。</p>

</div>

1.  在[管理入口網站][Azure 管理入口網站]的 [指令碼] 索引標籤中，使用下列函數來取代目前的 **Insert** 指令碼，然後按一下 [儲存]。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    此函數可強化上一個 insert 指令碼，方法是在呼叫 **request**.**execute** 來插入物件之前，先將新的 **createdAt** 時間戳記屬性新增至物件。

    <div class="dev-callout">

    <b>注意</b>
    <p>首次執行插入指令碼時，必須啟用動態結構描述。在啟用動態結構描述的情況下，行動服務會在首次執行時自動將 <b>createdAt</b> 欄新增至 <b>TodoItem</b> 資料表。依預設，新行動服務的動態結構描述為已啟用，您應該先將它停用之後再將應用程式發行至 Windows 市集。</p>

    </div>

2.  在 Visual Studio 中，按 **F5** 鍵以執行應用程式，然後在 [Insert a TodoItem] 中鍵入文字 (少於 10 個字元)，並按一下 [儲存]。

    請注意，新的時間戳記不會出現在應用程式 UI 中。

3.  回到管理入口網站中，按一下 [todoitem] 資料表中的 [瀏覽] 索引標籤。

    請注意，現在會有一個 **createdAt** 資料欄，且新插入的項目也具有時間戳記值。

接下來，您必須更新 Windows 市集應用程式以便顯示此新資料欄。

## <a name="update-client-timestamp"></a>重新更新用戶端

針對無法序列化成已定義類型屬性的回應，行動服務用戶端將忽略該回應中的任何資料。最後步驟是更新用戶端以顯示此新資料。

1.  在 Visual Studio 中開啟 default.html 檔案，然後在 TemplateItem 格線中新增下列 HTML 元素：

        <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
            data-win-bind="innerText: createdAt"></div>  

    如此會顯示新的 **createdAt** 屬性。

2.  按 **F5** 鍵以執行應用程式。

    請留意到，只有在您更新 insert 指令碼後插入的項目，才會顯示時間戳記。

3.  在 default.js 檔案中，以下列程式碼取代現有的 **RefreshTodoItems** 方法：

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

    此方法會更新查詢，篩選出沒有時間戳記的項目。

4.  按 **F5** 鍵以執行應用程式。

    請留意到，所有已建立但不含時間戳記值的項目，都會從 UI 中消失。

您已完成此使用資料教學課程。

## <a name="next-steps"> </a>後續步驟

現在，您已完成本教學課程，請考慮繼續進行資料數列中最後的教學課程：[使用分頁縮小查詢範圍][使用分頁縮小查詢範圍]。

您也可以在授權使用者及傳送推播通知時使用伺服器指令碼。如需詳細資訊，請參閱下列教學課程：

-   [使用指令碼授權使用者][使用指令碼授權使用者]
    了解如何根據通過驗證使用者的 ID 來篩選資料。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務伺服器指令碼參考][行動服務伺服器指令碼參考]
    深入了解如何註冊和使用伺服器指令碼。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [新增字串長度驗證]: #string-length-validation
  [更新用戶端以支援驗證]: #update-client-validation
  [插入時新增時間戳記]: #add-timestamp
  [更新用戶端以顯示時間戳記]: #update-client-timestamp
  [開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-js
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-js
  [使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-js
  [開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-js
  [行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
