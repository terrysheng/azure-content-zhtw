<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-javascript" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" writer="wesmc" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" />

處理資料庫寫入衝突
==================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/ "Windows 市集 C#") [Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-javascript/ "Windows 市集 JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-wp8/ "Windows Phone")

本教學課程可協助您深入了解如何處理在兩個或更多用戶端寫入至 Windows 市集應用程式中的相同資料庫記錄時所發生的衝突。在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。Windows Azure 行動服務可支援偵測及解決這些衝突的作業。本主題將逐步引導您完成下列步驟，讓您處理伺服器上與應用程式中的資料庫寫入衝突。

在本教學課程中，您會在快速入門應用程式中新增功能，以處理更新 TodoItem 資料庫時所發生的爭用情況。本教學課程將逐步引導您完成下列基本步驟：

1.  [更新應用程式以允許更新](#uiupdate)
2.  [在應用程式中啟用衝突偵測](#enableOC)
3.  [在應用程式中測試資料庫寫入衝突](#test-app)
4.  [以伺服器指令碼自動處理衝突的解決方式](#scriptsexample)

本教學課程需要下列各項

-   Microsoft Visual Studio 2013 Express for Windows 或更新版本。
-   本教學課程是以行動服務快速入門為基礎。在開始本教學課程之前，您必須先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)而下載起始專案的 JavaScript 語言版本。
-   [Windows Azure 帳戶](http://www.windowsazure.com/zh-tw/pricing/free-trial/)
-   Windows Azure 行動服務 NuGet 封裝 1.1.5 或更新版本。若要取得最新版本，請遵循下列步驟：
    1.  在 Visual Studio 中開啟專案，在 [方案總管] 中以滑鼠右鍵按一下專案，然後按一下 **[管理 NuGet 封裝]**。

    2.  展開 **[線上]**，然後按一下 **[Microsoft and .NET]**。在搜尋文字方塊中，輸入 **WindowsAzure.MobileServices.WinJS**。在 **Windows Azure Mobile Services for WinJS** NuGet 封裝上，按一下 **[安裝]**。

        ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

更新 UI更新應用程式以允許更新
-----------------------------

在本節中您將會更新使用者介面，使各個項目的文字能夠進行更新。繫結範本將包含資料庫資料表中各個項目的核取方塊和文字類別控制項。您將可更新 TodoItem 的文字欄位。應用程式會處理 `keydown` 事件，使項目可在您按 **Enter** 鍵後進行更新。

1.  在 Visual Studio 中，開啟您在[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程中下載之 TodoList 專案的 JavaScript 語言版本。
2.  在 Visual Studio 的 [方案總管] 中開啟 default.html，將 `TemplateItem` div 標籤定義取代為如下的 div 標籤，然後儲存變更。這會新增文字方塊控制項，讓您能夠編輯 TodoItem 的文字。

         <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
        <div style="display:-ms-grid; -ms-grid-columns:auto 1fr">
        <input class="itemCheckbox" type="checkbox" data-win-bind="checked:complete; dataContext:this" />
        <div style="-ms-grid-column:2; -ms-grid-row-align:center; margin-left:5px">
        <input id="modifytextbox" class="text win-interactive" data-win-bind="value:text; dataContext:this" />
        </div>
        </div>
        </div>

3.  在 Visual Studio 的 [方案總管] 中，展開 **js** 資料夾。開啟 default.js 檔案，然後將 `updateTodoItem` 函數取代為下列不會從使用者介面中移除更新項目的定義。

         var updateTodoItem = function (todoItem) {
        // This code takes a freshly completed TodoItem and updates the database. 
        todoTable.update(todoItem);
           };

4.  在 default.js 檔案中，為 `keydown` 事件新增下列事件處理常式，使項目可在您按 **Enter** 鍵後進行更新。

         listItems.onkeydown = function (eventArgs) {
        if (eventArgs.key == "Enter") {
        var todoItem = eventArgs.target.dataContext.backingData;
        todoItem.text = eventArgs.target.value;
        updateTodoItem(todoItem);
             }
           };

現在，在按下 **Enter** 鍵後，應用程式即會將每個項目的文字變更重新寫入至資料庫。

啟用開放式並行存取在應用程式中啟用衝突偵測
------------------------------------------

Windows Azure 行動服務支援開放式並行存取控制項，方法是使用新增至每個資料表的 `__version` 系統屬性資料欄來追蹤對每個項目的變更。在本節中，我們將使應用程式能夠透過 `__version` 系統屬性偵測這些寫入衝突。如果在 todoTable 上啟用了此系統屬性，在嘗試更新期間，如果記錄自前次查詢後有所變更，系統將會以 `MobileServicePreconditionFailedException` 通知應用程式。此時，應用程式將可選擇是要認可它對資料庫的變更，還是保留資料庫的最後變更。如需行動服務之系統屬性的詳細資訊，請參閱[系統屬性](http://go.microsoft.com/fwlink/?LinkId=331143)。

1.  在 default.js 檔案中的 `todoTable` 變數宣告下新增程式碼，以加入可用來支援寫入衝突偵測的 **\_\_version** 系統屬性。

         var todoTable = client.getTable('TodoItem');
        todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;

2.  將 `Version` 系統屬性新增至資料表的系統屬性後，如果在更新期間發現自前次查詢後有所變更的記錄，系統將會以 `MobileServicePreconditionFailedException` 例外狀況通知應用程式。在 JavaScript 中，此例外狀況會在出現錯誤函數時產生。此錯誤會包含伺服器中用來解決衝突的最新版項目。在 default.js 中，更新用以捕捉錯誤及呼叫 `resolveDatabaseConflict` 函數的 `updateTodoItem` 函數。

         var updateTodoItem = function (todoItem) {
        // This code takes a freshly completed TodoItem and updates the database. 
        // If the server version of the record has been updated, we get the updated
        // record from the Precondition Failed error in order to resolve the conflict.
        var serverItem = null;
        todoTable.update(todoItem).then(null, function (error) {
        if (error.message == "Precondition Failed") {
        serverItem = error.serverInstance;
             }
        else{
        var msgDialog =
        new Windows.UI.Popups.MessageDialog(error.request.responseText,"Update Failed");
        msgDialog.showAsync();
             }
        }).done(function () {
        if (serverItem != null)
        resolveDatabaseConflict(todoItem, serverItem);
           });
         };

3.  在 default.js 中，為 `updateTodoItem` 函數中參考的 `resolveDatabaseConflict()` 函數新增定義。請注意，若要解決衝突，您必須先將本機項目的版本設為來自伺服器的更新版本，再更新資料庫中的項目。否則將會持續發生衝突。

         var resolveDatabaseConflict = function (localItem, serverItem) {
        var content = "This record has been changed as follows on the server already..\n\n" +
        "id :" + serverItem.id + "\n" +
        "text :" + serverItem.text + "\n" +
        "complete :" + serverItem.complete + "\n\n" +
        "Do you want to overwrite the server instance with your data
         ";
        var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Resolve Database Conflict");
        msgDialog.commands.append(new Windows.UI.Popups.UICommand("Yes"));
        msgDialog.commands.append(new Windows.UI.Popups.UICommand("No"));
        msgDialog.showAsync().done(function (command) {
        if (command.label == "Yes") {
        localItem.__version = serverItem.__version;
        updateTodoItem(localItem);
               }
           });
         }

測試應用程式在應用程式中測試資料庫寫入衝突
------------------------------------------

在本節中您將會建置 Windows 市集應用程式封裝，以將應用程式安裝至第二部機器或虛擬機器。接著，您將同時在兩部機器上執行應用程式而產生寫入衝突，以測試程式碼。兩個應用程式執行個體將會嘗試更新相同項目的 `text` 屬性，而讓使用者必須解決衝突。

1.  建立 Windows 市集應用程式封裝，以安裝在第二部機器或虛擬機器上。若要執行此動作，請在 Visual Studio 中按一下 **[專案]**-\>**[市集]**-\>**[建立應用程式套件]**。

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png)

2.  在 [建立您的套件] 畫面上按一下 **[否]**，因為此封裝將不會上傳至 Windows 市集。然後按 **[下一步]**。

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png)

3.  在 [選取和設定套件] 畫面上接受預設值，然後按一下 **[建立]**。

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png)

4.  在 [套件建立完成] 畫面上按一下 **[輸出位置]** 連結，以開啟封裝位置。

	![][11]

5.  將封裝資料夾 "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test" 複製到第二部機器。在該機器上開啟封裝資料夾，以滑鼠右鍵按一下 **Add-AppDevPackage.ps1** PowerShell 指令碼，然後按一下 **[用 PowerShell 執行]**，如下所示。依照提示安裝應用程式。

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png)

6.  按一下 **[偵錯]**-\>**[開始偵錯]**，在 Visual Studio 中執行應用程式的執行個體 1。在第二部機器的 [開始] 畫面上按一下向下箭頭，以依名稱檢視應用程式。接著，按一下 **todolist** 應用程式，以執行應用程式的執行個體 2。

    應用程式執行個體 1

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

    應用程式執行個體 2 
	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

7.  在應用程式的執行個體 1 中，將最後一個項目的文字更新為「測試寫入 1」****，然後按 **Enter** 鍵以更新資料庫。其範例如下列螢幕擷取畫面所示。

    應用程式執行個體 1 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png)

    應用程式執行個體 2 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

8.  此時，應用程式執行個體 2 中的最後一個項目具有舊版的項目。在該應用程式執行個體中，輸入「測試寫入 2」**** 做為最後一個項目的 `text` 屬性，然後按 **Enter** 鍵，以舊的 `_version` 屬性更新資料庫。

    應用程式執行個體 1 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    應用程式執行個體 2 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

9.  由於用於更新嘗試的 `__version` 值不符合伺服器 `__version` 值，因此行動服務 SDK 會擲回 `MobileServicePreconditionFailedException` 做為 `updateTodoItem` 函數中的錯誤，讓應用程式能夠解決此衝突。若要解決衝突，您可以按一下 **[是]**，以認可執行個體 2 中的值。或者，您可以按一下 **[否]** 捨棄執行個體 2 中的值，而保留已認可的應用程式執行個體 1 中的值。

    應用程式執行個體 1 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    應用程式執行個體 2 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png)

以指令碼處理衝突以伺服器指令碼自動處理衝突的解決方式
----------------------------------------------------

您可以使用伺服器指令碼來偵測及解決寫入衝突。當您使用指令碼邏輯 (而非使用者互動) 來解決衝突時，這將是理想的作法。在本節中，您會在應用程式的 TodoItem 資料表中新增伺服器端指令碼。此指令碼將使用下列邏輯來解決衝突：

-   如果 TodoItem 的 complete` 欄位設為 true，則會被視為已完成，`且文字無法再變更。
-   如果 TodoItem 的 complete` 欄位仍為 false，則更新文字的嘗試`將會被認可。

下列步驟將引導您新增伺服器更新指令碼並加以測試。

1.  登入 [Windows Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

	![][7]

2.  按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

	![][8]

3.  按一下 **[指令碼]**，然後選取 **[更新]** 作業。

	![][9]

4.  以下列函數取代現有的指令碼，然後按一下 **[儲存]**。

         function update(item, user, request) { 
        request.execute({ 
        conflict:function (serverRecord) {
        // Only committing changes if the item is not completed.
        if (serverRecord.complete === false) {
        //write the updated item to the table
        request.execute();
                     }
        else
                     {
        request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                     }
                 }
             }); 
         }   

5.  在兩部機器上執行 **todolist** 應用程式。變更執行個體 2 中最後一個項目的 TodoItem `text`，然後按 **Enter** 鍵，使應用程式更新資料庫。

    應用程式執行個體 1 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    應用程式執行個體 2 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

6.  在應用程式的執行個體 1 中，為最後一個文字屬性輸入不同的值，然後按 **Enter** 鍵。應用程式會嘗試以不正確的 `__version` 屬性更新資料庫。

    應用程式執行個體 1 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png)

    應用程式執行個體 2 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png)

7.  請留意到，由於伺服器指令碼已解決衝突，而使更新得以進行 (因為項目未標示為完成)，因此應用程式中未發生例外狀況。若要確認更新已順利完成，請在執行個體 2 中按一下 **[重新整理]**，以重新查詢資料庫。

    應用程式執行個體 1 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

    應用程式執行個體 2 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

8.  在執行個體 1 中，按一下可完成最後一個待辦事項的核取方塊。

    應用程式執行個體 1 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png)

    應用程式執行個體 2 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

9.  在執行個體 2 中，嘗試更新最後一個 TodoItem 的文字，然後按 **Enter** 鍵；這將會導致衝突，因為「完成」欄位已設為 true 而產生更新。指令碼對衝突的因應方式是拒絕更新以解決衝突，因為項目已完成。指令碼在回應中提供了一則訊息。

    應用程式執行個體 1 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png)

    應用程式執行個體 2 

	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png)

後續步驟
--------

本教學課程示範了如何讓 Windows 市集應用程式處理在行動服務中使用資料時所發生的寫入衝突。您可以接著完成我們資料序列中的下列其中一個教學課程：

-   [使用指令碼驗證及修改資料](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/)
    深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送的資料。

-   [使用分頁縮小查詢範圍](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/)
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，您可以嘗試下列其中一個 Windows 市集教學課程：

-   [開始使用驗證](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
    了解如何驗證應用程式的使用者。

-   [開始使用推送通知](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push/)
    了解如何使用行動服務將非常基本的推送通知傳送到應用程式。


<!-- Anchors. -->
[Update the application to allow updates]: #uiupdate
[Enable Conflict Detection in your application]: #enableOC
[Test database write conflicts in the application]: #test-app
[Automatically handling conflict resolution in server scripts]: #scriptsexample
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Optimistic Concurrency Control]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Windows Azure Account]: http://www.windowsazure.com/zh-tw/pricing/free-trial/
[Validate and modify data with scripts]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Refine queries with paging]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with authentication]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Get started with push notifications]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[System Properties]: http://go.microsoft.com/fwlink/?LinkId=331143