

<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle conflicts with offline data in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

處理行動服務中的離線資料衝突
============================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data "Windows 市集 C#")

本主題將說明在使用 Windows Azure 行動服務的離線功能時，應如何同步處理資料及處理衝突。在本教學課程中，您將下載同時支援離線和線上資料的應用程式、將行動服務與該應用程式整合，然後登入 Windows Azure 管理入口網站，以檢視執行應用程式時所做的資料庫更新。

本教學課程會以先前的教學課程[開始使用資料](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data)中的步驟和範例應用程式為基礎。在開始本教學課程之前，您必須先完成[開始使用離線資料](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data)。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案](#download-app)
2.  [為資料庫新增到期日資料行](#add-column)
    -   [更新 .NET 後端行動服務的資料庫](#dotnet-backend)
    -   [更新 JavaScript 行動服務的資料庫](#javascript-backend)

3.  [對行動服務進行應用程式測試](#test-app)
4.  [手動更新後端中的資料以產生衝突](#handle-conflict)

本教學課程需要執行於 Windows 8.1 的 Visual Studio 2013。

<a name="download-app"></a>下載範例專案
------------

本教學課程以[處理衝突程式碼範例](http://go.microsoft.com/fwlink/?LinkId=394787) (這是 Visual Studio 2013 中的 Windows 市集應用程式專案之一) 做為建置基礎。此應用程式的 UI 與[開始使用離線資料](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data)教學課程中的應用程式相類似，差別在於前者的每個 TodoItem 都有新的日期資料行。

1.  下載 C\# 版本的[處理衝突程式碼範例](http://go.microsoft.com/fwlink/?LinkId=394787)。

2.  安裝 [SQLite for Windows 8.1](http://go.microsoft.com/fwlink/?LinkId=394776) (如果尚未安裝)。

3.  在 Visual Studio 2013 中，開啟下載的專案。按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式的 **[Insert a TodoItem]** 中鍵入一些文字，然後按一下 **[儲存]**。您也可以修改已新增之 todo 項目的到期日。

請注意，應用程式尚未連接到任何行動服務，因此 **[推送]** 和 **[提取]** 按鈕將會擲出例外狀況。

<a name="add-column"></a>將資料行新增至資料模型
----------------------

在本節中，您將會更新行動服務的資料庫，以納入具有到期日資料行的 TodoItem 資料表。應用程式允許您在執行階段變更項目的到期日，因此您可以在本教學課程的後續小節中產生同步衝突。

範例中的 `TodoItem` 類別定義於 MainPage.xaml.cs 中。請注意，此類別具有下列會將該資料表做為同步作業目標的屬性。

        [DataTable("TodoWithDate")]

更新您的資料庫以納入此資料表。

### <a name="dotnet-backend"></a>更新 .NET 後端行動服務的資料庫

如果您在行動服務中使用 .NET 後端，請依照下列步驟更新資料庫的結構描述。

1.  在 Visual Studio 中，開啟您的 .NET 後端行動服務專案。
2.  在 Visual Studio 的 [方案總管] 中，在您的服務專案中展開 **Models** 資料夾，並開啟 ToDoItem.cs。新增 `DueDate` 屬性，如下所示。

           public class TodoItem :EntityData
           {
        public string Text { get; set; }
        public bool Complete { get; set; }
        public System.DateTime DueDate { get; set; }
           }

3.  在 Visual Studio 的 [方案總管] 中展開 **App\_Start** 資料夾，然後開啟 WebApiConfig.cs 檔案。

    在 WebApiConfig.cs 檔案中，注意您的預設資料庫初始設定式類別是衍生自 `DropCreateDatabaseIfModelChanges` 類別。這意味對模型的任何變更都會導致資料表捨棄，並重新建立以容納新模型。因此資料表的資料將流失，並且將重新植入資料表。請修改資料庫初始設定式的 Seed 方法，使 `Seed()` 初始化函數如下所示，以初始化新的 DueDate 資料行。儲存 WebApiConfig.cs 檔案。

    > [WACOM.NOTE] 使用預設資料庫初始設定式時，每當 Entity Framework 在 Code First 模型定義中偵測到資料模型變更，就會捨棄並重新建立資料庫。若要進行此資料模型變更，並保有資料庫的現有資料，必須使用 Code First Migrations。如需詳細資訊，請參閱[如何使用 Code First Migrations 更新資料模型](./articles/mobile-services-dotnet-backend-use-code-first-migrations) (英文)。

         new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  在 Visual Studio 的 [方案總管] 中展開 **Controllers** 資料夾，然後開啟 ToDoItemController.cs。將 `TodoItemController` 類別重新命名為 `TodoWithDateController`。這將會變更資料表作業的 REST 端點。

         public class TodoWithDateController :TableController<TodoItem>

5.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下您的 .NET 後端行動服務專案，然後按一下 **[發佈]** 以發佈您的變更。

### <a name="javascript-backend"></a>更新 JavaScript 後端行動服務的資料庫

對於 JavaScript 後端行動服務，您將會新增名為 **TodoWithDate** 的新資料表。若要為 JavaScript 後端行動服務新增 **TodoWithDate** 資料表，請遵循下列步驟。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。

2.  導覽至行動服務的 **[資料]** 索引標籤。

3.  按一下頁面底部的 **[建立]**，然後建立名為 **TodoWithDate** 的新資料表。

<a name="test-app"></a>對行動服務進行應用程式測試
--------------------------

現在，我們將對行動服務進行應用程式測試。

1.  在 Azure 管理入口網站中，按一下命令列上的 **[管理金鑰]**，以找出您行動服務的應用程式金鑰。複製 **[應用程式金鑰]**。

2.  在 Visual Studio 的 [方案總管] 中，開啟 JavaScript 用戶端範例專案中的 App.xaml.cs 檔案。變更 **MobileServiceClient** 的初始化，使其使用您的行動服務 URL 和應用程式金鑰：

          public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://your-mobile-service.azure-mobile.net/",
        "Your AppKey"
         );

3.  在 Visual Studio 中按 **F5** 鍵，以建置並執行此應用程式。

4.  和之前一樣，在文字方塊中鍵入文字，然後按一下 **[儲存]**。這會將資料儲存至本機同步資料表，但不會儲存至伺服器。

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png)

5.  若要檢視資料庫的現行狀態，請登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

-   如果您在行動服務中使用 JavaScript 後端，請按一下 **[資料]** 索引標籤，然後按一下 **[TodoWithDate]** 資料表。按一下 **[瀏覽]** 以確認資料表仍是空白的，因為我們尚未將變更從應用程式推送至伺服器。

	![][1]

-   如果您在行動服務中使用 .NET 後端，請按一下 **[設定]** 索引標籤，然後按一下您的 SQL Database。按一下畫面底部的 **[管理]**，然後登入 SQL Azure 管理入口網站以檢視您的資料庫。

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png)

1.  返回應用程式，並按一下 **[推送]**。

2.  在管理入口網站中，按一下 **[TodoItem]** 資料表上的 **[重新整理]**。此時您應會看見您在應用程式中輸入的資料。

	[][3]

<a name="handle-conflict"></a>更新後端中的資料以產生衝突
--------------------------

在實際情況中，如果在一個應用程式推送資料庫中某個記錄的更新後，又有另一個應用程式根據該記錄的過期版本嘗試推送相同記錄的變更，就會發生同步衝突。如果一個應用程式執行個體在未提取更新記錄的情況下嘗試更新相同記錄，就會發生衝突，並且在應用程式中產生 `MobileServicePreconditionFailedException`。

如果您要將應用程式部署至另一部機器，以藉由執行兩個應用程式執行個體來產生衝突，您可以依照[處理資料庫衝突](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app)教學課程中的部署指示來操作。

下列步驟說明如何在 Visual Studio 中更新資料庫，以造成衝突。

1.  在 Visual Studio 中執行伺服器總管，並連接到您的 Azure 帳戶。展開您 Azure 帳戶的 **[SQL Databases]**。

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png)

2.  在清單中以滑鼠右鍵按一下您的 SQL Database，然後按一下 **[Open in SQL Server Object Explorer]**。
3.  在 SQL Server 物件總管中展開您的資料庫，然後展開 **[資料表]**。以滑鼠右鍵按一下 **[TodoWithDate]** 資料表，然後按一下 **[檢視資料]**。

4.  將其中一個項目的 **[完成]** 欄位變更為 True。

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png)

5.  返回 Todo 應用程式，並編輯您在資料庫中直接修改的相同項目。

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png)

6.  按一下 **[推送]** 按鈕。此時會出現一個對話方塊，詢問您要如何解決衝突。請選擇其中一個選項，以解決衝突。

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png)

檢閱處理同步衝突的程式碼
------------------------

若要設定用以偵測衝突的離線功能，您必須同時在本機資料庫與資料傳輸物件中加入版本資料行。`TodoItem` 類別具有下列成員：

        [Version]
    public string Version { get; set; }

資料行 `__version` 也會在設定於 `OnNavigatedTo()` 方法中的本機資料庫中指定。

若要使用程式碼處理離線同步衝突，您必須建立實作 `IMobileServiceSyncHandler` 的類別。請將此類型的物件傳至 `InitializeAsync` 的呼叫中：

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

**MainPage.xaml.cs** 中的 `SyncHandler` 類別會實作 `IMobileServiceSyncHandler`。每個推送作業傳送至伺服器時，都會呼叫 `ExecuteTableOperationAsync` 方法。如果擲出了 `MobileServicePreconditionFailedException` 類型的例外狀況，表示項目的本機與遠端版本之間具有衝突。

若要以有利於本機項目的方式解決衝突，您應直接重試作業。衝突發生後，本機項目版本會進行更新以符合伺服器版本，因此重新執行作業時，將會以本機變更覆寫伺服器變更：

    await operation.ExecuteAsync(); 

若要以有利於伺服器項目的方式解決衝突，請直接從 `ExecuteTableOperationAsync` 回復。物件的本機版本將會被捨棄，並取代為伺服器中的值。

若要停止推送作業 (但保留佇列上的變更)，請使用 `AbortPush()` 方法：

    operation.AbortPush();

這將會停止目前的推送作業，但仍將持續執行所有擱置變更，包括目前的作業 (如果從 `ExecuteTableOperationAsync` 呼叫了 `AbortPush`)。下次呼叫 `PushAsync()` 時，這些變更將會傳送至伺服器。

在取消推送時，`PushAsync` 將會擲出 `MobileServicePushFailedException`，且例外狀況屬性 `PushResult.Status` 的值將是 `MobileServicePushStatus.CancelledByOperation`。

<!-- Anchors. -->
[Download the Windows Store app project]: #download-app
[Create the mobile service]: #create-service
[Add a due date column for the database]: #add-column
[Updating the database for .NET backend mobile services]: #dotnet-backend  
[Updating the database for JavaScript mobile services]: #javascript-backend
[Test the app against a mobile service]: #test-app
[Manually update the data in the backend to create a conflict]: #handle-conflict
[Next Steps]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-design-edit.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png
[8]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: /zh-tw/documentation/articles/mobile-services-windows-store-get-started/
[Get started with offline data]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure Management Portal]: https://manage.windowsazure.com/
[Handling Database Conflicts]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app

