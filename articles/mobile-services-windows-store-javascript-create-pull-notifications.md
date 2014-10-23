<properties linkid="develop-mobile-tutorials-create-pull-notifications-js" urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Define a custom API that supports pull notifications - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 定義支援定期通知的自訂 API

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/en-us/develop/mobile/tutorials/create-pull-notifications-dotnet" title="Windows 市集 C#">Windows 市集 C#</a><a href="/en-us/develop/mobile/tutorials/create-pull-notifications-js" title="Windows 市集 JavaScript" class="current">Windows 市集 JavaScript</a>
</div>

本主題說明如何使用自訂 API 來支援 Windows 市集應用程式中的定期通知。在啟用定期通知的情況下，Windows 將定期存取您的自訂 API 端點，並使用傳回的 XML (採用磚的特定格式) 來更新開始功能表上的應用程式磚。如需詳細資訊，請參閱[定期通知][]。

將此功能新增至您在完成[開始使用行動服務][]或[開始使用資料][]教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1.  [定義自訂 API][]
2.  [更新應用程式以開啟定期通知][]
3.  [測試應用程式][]

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須先完成[開始使用行動服務][]或[開始使用資料][]。

## <a name="define-custom-api"></a>定義自訂 API

1.  登入 [Azure 管理入口網站][]，按一下 [行動服務]，然後按一下您的應用程式。

    ![][]

2.  按一下 [API] 索引標籤，然後按一下 [Create a custom API]。

    ![][1]

    這樣做會顯示 [Create a new custom API] 對話方塊。

3.  將 [Get permission] 變更為 [Everyone]，在 [API 名稱] 中輸入 *tiles*，然後按一下核取按鈕。

    ![][2]

    這會建立具有公用 GET 存取權的新 API。

4.  按一下 API 資料表中新的磚項目。

    ![][3]

5.  按一下 [指令碼] 索引標籤，並使用下列程式碼來取代現有程式碼：

        exports.get = function(request, response) {
            var wns = require('wns');
            var todoItems = request.service.tables.getTable('TodoItem');
            todoItems.where({
                complete: false
            }).read({
                success: sendResponse
            });

            function sendResponse(results) {
                var tileText = {
                    text1: "My todo list"
                };
                var i = 0;
                console.log(results)
                results.forEach(function(item) {
                    tileText["text" + (i + 2)] = item.text;
                    i++;
                });
                var xml = wns.createTileSquareText01(tileText);
                response.set('content-type', 'application/xml');
                response.send(200, xml);
            }
        };

    此程式碼會從 TodoItem 資料表中傳回前 3 個未完成的項目，然後將其載入傳至 **wns**.**createTileSquareText01** 函數的 JSON 物件中。此函數會傳回下列磚範本 XML：

        <tile>
            <visual>
                <binding template="TileSquareText01">
                    <text id="1">My todo list</text>
                    <text id="2">Task 1</text>
                    <text id="3">Task 2</text>
                    <text id="4">Task 3</text>
                </binding>
            </visual>
        </tile>

    由於用戶端將會傳送 GET 要求以存取磚範本，因此會使用 **exports.get** 函數。

    <div class="dev-callout"><b>注意</b>
<p>此自訂 API 指令碼會使用 Node.js <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">wns 模組</a>，而此模組可透過 <strong>require</strong> 函數來參考。此模組與可用來從伺服器指令碼傳送推播通知的 <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">push 物件</a>所傳回的 <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj554217.aspx">wns 物件</a>不同。</p>
</div>

接著，您將修改快速入門應用程式，以藉由要求新的自訂 API 啟動會更新動態磚的定期通知。

## <a name="update-app"></a><span class="short-header">更新應用程式</span>更新應用程式以開啟定期通知

1.  在 Visual Studio 中，按 F5 鍵以執行上一個教學課程中的快速入門應用程式。

2.  確定至少顯示了一個項目。如果沒有任何項目，請在 [Insert a TodoItem] 中輸入文字，然後按一下 [儲存]。

3.  在 Visual Studio 中，展開 [方案總管] 中的 `\js` 資料夾，開啟 default.js 專案，然後在定義 **client** 變數的程式碼後面新增下列程式碼行：

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

    此程式碼會開啟定期通知，以向新的**磚**自訂 API 要求磚範本資料。選取與您的資料更新頻率最相符的 [PeriodicUpdateRecurrance] 值。

## <a name="test-app"></a>測試應用程式

1.  在 Visual Studio 中，按 F5 鍵以再次執行此應用程式。

    這會開啟定期通知。

2.  導覽至 [開始] 畫面，找出應用程式的動態磚，並留意到項目資料現在已顯示在磚中。

    ![][4]

## 後續步驟

現在您已建立定期通知，建議您進一步了解關於下列行動服務主題的詳細資訊：

-   [開始使用推播通知][]
    定期通知會受到 Windows 的管理，且只會依據預先定義的排程執行。推播通知可由行動服務隨需傳送，且可以是快顯、磚和原始通知。

-   [行動服務伺服器指令碼參照][]
     (英文) 深入了解建立自訂 API。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 市集 C#]: /en-us/develop/mobile/tutorials/create-pull-notifications-dotnet "Windows 市集 C#"
  [Windows 市集 JavaScript]: /en-us/develop/mobile/tutorials/create-pull-notifications-js "Windows 市集 JavaScript"
  [定期通知]: http://msdn.microsoft.com/en-us/library/windows/apps/jj150587.aspx
  [開始使用行動服務]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
  [開始使用資料]: /en-us/develop/mobile/tutorials/started-with-data-js
  [定義自訂 API]: #define-custom-api
  [更新應用程式以開啟定期通知]: #update-app
  [測試應用程式]: #test-app
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
  [2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
  [3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
  [wns 模組]: http://go.microsoft.com/fwlink/p/?LinkId=306750
  [push 物件]: http://go.microsoft.com/fwlink/p/?LinkId=260591
  [wns 物件]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554217.aspx
  [4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png
  [開始使用推播通知]: /en-us/develop/mobile/tutorials/get-started-with-push-js
  [行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/?LinkId=262293
