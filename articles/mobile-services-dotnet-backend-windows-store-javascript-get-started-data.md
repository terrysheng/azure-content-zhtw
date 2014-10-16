<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

開始使用行動服務中的資料
========================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Windows 市集 C#") [Windows 市集 JavaScript](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Windows 市集 JavaScript") [Windows Phone](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone")

[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ ".NET 後端") | [JavaScript 後端](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "JavaScript 後端")

本主題將說明如何以 Azure 行動服務作為 Windows 市集應用程式的後端資料來源。在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您將在本教學課程中建立的行動服務，將可在行動服務中支援 .NET 執行階段。如此，您即可在行動服務中使用 .NET 語言和 Visual Studio 來撰寫伺服器端商務邏輯。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 [JavaScript 後端版本](/en-us/develop/mobile/tutorials/get-started-with-data-js)。

**注意**

本教學課程需要 Visual Studio 2013。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案](#download-app)
2.  [建立新的行動服務](#create-service)
3.  [在本機下載行動服務](#download-the-service-locally)
4.  [更新 Windows 市集應用程式以使用行動服務](#update-app)
5.  [對本機代管的服務測試 Windows 市集應用程式](#test-locally-hosted)
6.  [將行動服務發佈至 Azure](#publish-mobile-service)
7.  [對 Azure 代管的服務測試 Windows 市集應用程式](#test-azure-hosted)

**注意**

若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F)。

下載專案下載 GetStartedWithData 專案
------------------------------------

本教學課程是採用 [GetStartedWithMobileServices 應用程式](http://go.microsoft.com/fwlink/p/?LinkId=328660)所建立，此應用程式是 Visual Studio 2013 中的 Windows 市集應用程式專案。除了會將新增項目儲存在本機記憶體中之外，此應用程式的 UI 與行動服務快速入門所產生的應用程式 UI 是類似的。

1.  從[開發人員程式碼範例網站](http://go.microsoft.com/fwlink/p/?LinkId=328660) (英文) 下載 JavaScript 版本的 GetStartedWithMobileServices 範例應用程式。

   	![][1]

2.  以滑鼠右鍵按一下 Visual Studio，然後按一下 **[以系統管理員身分執行]**，以管理權限執行 Visual Studio 2013。

3.  在 Visual Studio 2013 中，開啟下載的專案。在 [方案總管] 中展開 js 資料夾，並查看 default.js 檔案。

   	Notice that added **TodoItem** objects are stored in an in-memory in a `WinJS.Binding.List`.

4.  按 **F5** 鍵，以重建專案並啟動應用程式。

5.  在應用程式中，在 **[Insert a TodoItem]** 下的方塊中輸入文字，然後按一下 **[儲存]**。

   	![][0]  

   	Notice that the saved text is displayed in the second column under **Query and update data**.

建立新的行動服務建立新的行動服務
--------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

在本機下載服務下載行動服務專案並將其新增至方案
----------------------------------------------

1.  在 [Azure 管理入口網站](https://manage.windowsazure.com/)中，按一下新的行動服務或是其雲端圖示索引標籤，以移至概觀頁面。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png)

2.  按一下 **[Windows 市集]** 平台。在 **[開始使用]** 區段下展開 **[Connect an existing Windows Store app]**，然後按一下 **[下載]** 按鈕，以下載行動服務的個人化起始專案。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png)

3.  向下捲動至該 **[開始使用]** 區段的底部，找出標示為**「將您的服務發佈至雲端」**的步驟。按一下以下螢幕擷取畫面中顯示的連結，以下載您剛剛下載之行動服務的發佈設定檔。

    > [WACOM.NOTE] 請將此檔案存放在安全位置，因為其中包含與您的 Azure 帳戶有關的敏感資訊。在本教學課程中，您將在發佈行動服務後刪除此檔案。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png)

4.  解壓縮您所下載的個人化服務起始專案。將 zip 檔案中的資料夾，複製到「開始使用資料」方案檔案 (.sln) 所在的相同 **JavaScript** 目錄中。這樣可讓 NuGet Package Manager 易於將所有的封裝保持同步。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png)

5.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下「開始使用資料」Windows 市集應用程式的方案。按一下 **[新增]**，然後按一下 **[現有專案]**。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png)

6.  在 [新增現有專案] 對話方塊中，導覽至先前移入 **JavaScript** 目錄中的行動服務專案資料夾。在服務子目錄中，選取 C\# 專案檔案 (.csproj)。按一下 **[開啟]**，將專案新增至您的方案。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png)

7.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下您剛剛新增的服務專案，然後按一下 **[建置]**，以驗證專案可順利建置而不會產生錯誤。在建置期間，NuGet 封裝管理員可能必須還原某些在專案中受到參考的 NuGet 封裝。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png)

8.  再次以滑鼠右鍵按一下服務專案。這次，請在 **[偵錯]** 內容功能表下按一下 **[Start new instance]**。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png)

    Visual Studio 會為您的服務開啟預設網頁。您可以按一下 **[try it now]**，以從行動服務中的預設網頁測試方法。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png)

    Visual Studio 依預設使用 IIS Express 在本機代管您的行動服務。您可以在工作列上以滑鼠右鍵按一下 IIS Express 的系統匣圖示，以檢視此設定。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png)

更新 Windows 市集應用程式更新 Windows 市集應用程式以使用行動服務
----------------------------------------------------------------

在本節中，您將會更新 Windows 市集應用程式，以使用行動服務作為應用程式的後端服務。

1.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後按一下 **[管理 NuGet 封裝]**。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png)

2.  在 [管理 NuGet 封裝] 對話方塊中，搜尋位於線上封裝集合中的 **WindowsAzure.MobileServices.WinJS** 並加以點選，以安裝 Azure 行動服務 Nuget 封裝。然後關閉對話方塊。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png)

3.  回到 Azure 管理入口網站，在您行動服務的概觀頁面上，找出標示為**「連接應用程式並將資料儲存在服務中」**的步驟。按一下 **[JavaScript]** 使其作為您的語言，然後複製用以建立 `MobileServiceClient` 的程式碼片段。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png)

4.  在 Visual Studio 的 [方案總管] 中展開 **js** 資料夾，並開啟 default.js。在 `app.onactivated` 事件處理常式中，將您複製的程式碼片段貼到 `todoItems` 變數的定義前面。請注意，此程式碼片段包含用來透過應用程式金鑰連接到 Azure 行動服務的已註解化建構函式。您將在後續的步驟中取消其註解。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png)

5.  在 default.js 中，將 `app.onactiviated` 事件處理常式的其餘程式碼取代為定義 `todoItems` 的下列程式碼，以及我們將對行動服務進行測試的作業。接著，請儲存檔案。

    此程式碼會使用 Mobile Services SDK for JavaScript，使應用程式能夠將其資料儲存在服務提供的資料表中，而不儲存在本機的記憶體內部。主要的三種方法為 `insertTodoItem`、`refreshTodoItems` 和 `updateCheckedTodoItem`。這三種方法可讓您以非同步方式插入、查詢資料集合，以及將其更新至 Azure 中的資料表。

             var todoTable = client.getTable('TodoItem');
        var todoItems = new WinJS.Binding.List();
        var insertTodoItem = function (todoItem) {
        // This code inserts a new TodoItem into the database. 
        // Mobile Services assigns each item an id and the 
        // item is added to the Binding List
        todoTable.insert(todoItem).done(function (item) {
        todoItems.push(item);
                 });
             };
        var refreshTodoItems = function () {
        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
        .read()
        .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
                     });
             };
        var updateCheckedTodoItem = function (todoItem) {
        // This code updates the database. 
        // When the MobileService responds, 
        // the item is removed from the list 
        todoTable.update(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
                 });
             };
        buttonSave.addEventListener("click", function () {
        insertTodoItem({
        text:textInput.value,
        complete :false
                 });
             });
        buttonRefresh.addEventListener("click", function () {
        refreshTodoItems();
             });
        listItems.addEventListener("change", function (eventArgs) {
        var todoItem = eventArgs.target.dataContext.backingData;
        todoItem.complete = eventArgs.target.checked;
        updateCheckedTodoItem(todoItem);
             });
        refreshTodoItems();

6.  在 Visual Studio 的 [方案總管] 中，開啟 default.html 檔案。在此檔案的頂端，為 MobileServices.js 新增新的 WinJS 指令碼參考，然後儲存檔案。

         <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png)

在本機測試 Windows 市集應用程式對本機代管的服務測試 Windows 市集應用程式
------------------------------------------------------------------------

在本節中，您將使用 Visual Studio 在 IIS Express 中的本機開發工作站上代管行動服務。接著，您將測試應用程式和後端服務。

1.  在 Visual Studio 中按 F7 鍵，或從 [建置] 功能表中按一下 **[建置方案]**，以建置 Windows 市集應用程式和行動服務。請在 Visual Studio 的輸出視窗中，驗證兩個專案都順利建置而未產生錯誤。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png)

2.  在 Visual Studio 中按 F5 鍵，或從 [偵錯] 功能表中按一下 **[開始偵錯]**，以執行應用程式，並在 IIS Express 中本機代管行動服務。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png)

3.  輸入新 todoitem 的文字。然後按一下 **[儲存]**。這會在 IIS Express 本機代管的行動服務所建立的資料庫中，插入新的 todoItem。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/new-local-todoitem.png)

4.  按一下其中一個項目的核取方塊，將其標示為已完成。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/local-item-checked.png)

5.  在 Visual Studio 中，您可以開啟 [伺服器總管] 並展開 [資料連線]，以檢視您為後端服務建立之資料庫中的變更。以滑鼠右鍵按一下 **[MS\_TableConnectionString]** 下的 TodoItems 資料表，然後按一下 **[顯示資料表資料]**。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-show-local-table-data.png)

將行動服務發佈至 Azure將行動服務發佈至 Azure
--------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

在 Azure 上測試行動服務測試發佈至 Azure 的行動服務
--------------------------------------------------

1.  在 Visual Studio 的 [方案總管] 中展開 **js** 資料夾，並開啟 default.js。將建立 `MobileServiceClient` 以連接到本機代管之行動服務的程式碼註解化。將建立 `MobileServiceClient` 以連接到 Azure 中之服務的程式碼取消註解。儲存檔案的變更。

         //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
         //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
        "https://todolist.preview.azure-mobile-preview.net/",
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

2.  在 Visual Studio 中按 F5 鍵，或從 [偵錯] 功能表中按一下 **[開始偵錯]**。這會使 Windows 市集應用程式以先前的變更重新建置，然後再執行應用程式，以連接到 Azure 遠端代管的行動服務。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png)

3.  輸入新的 todoitem，然後為每個項目按一下 **[儲存]**。按一下核取方塊，以完成部分的新項目。每個新的 todoItem，都會儲存在您先前在 Azure 管理入口網站中為行動服務設定的 SQL Database 中，並進行更新。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png)

    您可以重新啟動應用程式，以確認變更持續存留至 Azure 中的資料庫。您也可以使用 Azure 管理入口網站或 Visual Studio 的 SQL Server 物件總管來檢查資料庫。後續的兩個步驟將使用 Azure 管理入口網站檢視您資料庫中的變更。

4.  在 Azure 管理入口網站中，對您行動服務的相關資料庫按一下 [管理]。

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png)

5.  在管理入口網站中執行查詢，以檢視 Windows 市集應用程式所做的變更。您的查詢會與下列查詢相類似，但將使用您的資料庫名稱，而非 `todolist`。

         SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png)

這將結束**開始使用資料**教學課程。

後續步驟
--------

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令碼驗證及修改資料](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js)
    深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送的資料。

-   [使用分頁縮小查詢範圍](/en-us/develop/mobile/tutorials/add-paging-to-data-js)
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/)
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/)
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念性參考](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)
    深入了解如何搭配使用行動服務與 HTML 和 JavaScript。

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
