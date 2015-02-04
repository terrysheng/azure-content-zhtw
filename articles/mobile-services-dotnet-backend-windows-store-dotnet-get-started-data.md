<properties urlDisplayName="Get Started with Data" pageTitle="開始使用資料 (Windows 市集) | 行動開發人員中心" metaKeywords="" description="了解如何開始使用行動服務，在您的 Windows Store 應用程式中使用資料。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# 將行動服務新增至現有的應用程式

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend" class="current">.NET 後端</a> | 
	<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="JavaScript backend">JavaScript 後端</a>
</div>

本主題將說明如何以 Azure 行動服務作為 Windows 市集應用程式的後端資料來源。在本教學課程中，您將下載 Visual Studio 2013 專案，以取得將資料儲存在記憶體中的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您在此教學課程中建立的行動服務，是一項 .NET 後端行動服務。.NET 後端服務讓您得以在行動服務中運用 .NET 語言與 Visual Studio 建立伺服器端商業邏輯，並在本機電腦上執行與偵錯行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 JavaScript 後端版本。

>[WACOM.NOTE]本主題說明如何將 Azure 行動服務新增至 Windows 市集專案。您可以使用 Visual Studio 2013 工具，將相同的 .NET 後端行動服務新增至通用 Windows 應用程式專案。如需詳細資訊，請參閱[通用 Windows 應用程式版本](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data) of this tutorial.

This tutorial walks you through these basic steps:

1. [下載 Windows 市集應用程式專案]
2. [建立新的行動服務]
3. [在本機下載行動服務]
4. [更新 Windows 市集應用程式以使用行動服務]
5. [對本機代管的服務測試 Windows 市集應用程式]
6. [將行動服務發佈至 Azure]
7. [對 Azure 代管的服務測試 Windows 市集應用程式]

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。您可以使用免費試用版。

## <a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithMobileServices 應用程式][Developer Code Samples site]為基礎，此應用程式是 Visual Studio 2013 中的 Windows 市集應用程式專案。除了會將新增項目儲存在本機記憶體中之外，此應用程式的 UI 與行動服務快速入門所產生的應用程式 UI 是類似的。 

1. 從 [開發人員程式碼範例網站] 中下載 C# 版本的 GetStartedWithMobileServices 範例應用程式。 

2. 以滑鼠右鍵按一下 Visual Studio 2013，然後按一下 [**以系統管理員身分執行**]。

3. 在 Visual Studio 2013 中，開啟下載的專案並檢查 MainPage.xaml.cs 檔案。

   	請注意，新增的 **TodoItem** 物件儲存在記憶體內部的 **ObservableCollection<TodoItem>** 中。

4. 按 **F5** 鍵，以重建專案並啟動應用程式。

5. 在應用程式的 [**Insert a TodoItem**] 中輸入一些文字，然後按一下 [**儲存**]。

   	![][0]  

   	請注意，儲存的文字會顯示在 [**查詢和更新資料**] 下方的第二個資料行中。

## <a name="create-service"></a>建立新的行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>下載行動服務專案並將其新增至方案

1. 在 [Azure 管理入口網站]中，按一下新的行動服務或是其雲端圖示索引標籤，以移至概觀頁面。

    ![][2]

2. 按一下 [**Windows 市集**] 平台。在 [**開始使用**] 區段下展開 [**連接現有的 Windows 市集應用程式**]，然後按一下 [**下載**] 按鈕，以下載行動服務的個人化起始專案。 

    ![][3]

3. 向下捲動至該 [**開始使用**] 區段的底部，找出標示為 [**Publish your service to the cloud**] 的步驟。按一下以下螢幕擷取畫面中顯示的連結，以下載您剛剛下載之行動服務的發佈設定檔。 

    > [WACOM.NOTE] 請將此檔案存放在安全位置，因為其中包含與您的 Azure 帳戶有關的敏感資訊。在本教學課程中，您將在發佈行動服務後刪除此檔案。 

    ![][5]


4. 解壓縮您所下載的個人化服務起始專案。將 zip 檔案中的資料夾，複製到「開始使用資料」方案檔案 (.sln) 所在的相同 **C#** 目錄中。這樣可讓 NuGet 封裝管理員輕易地將所有的封裝保持同步。

    ![][26]

5. 接著，在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下「開始使用資料」Windows 市集應用程式的方案。按一下 [**加入**]，然後按一下 [**現有專案**]。

    ![][4]

6. 在 [加入現有專案] 對話方塊中，導覽至先前移入 **C#** 目錄中的行動服務專案資料夾。在服務子目錄中，選取 C# 專案檔案 (.csproj)。按一下 [**開啟舊檔**]，將專案新增至您的方案。

    ![][6]

7. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下您剛剛新增的服務專案，然後按一下 [**建置**]，以驗證專案可順利建置而不會產生錯誤。在建置期間，NuGet 封裝管理員可能必須還原某些在專案中受到參考的 NuGet 封裝。

    ![][20]

8. 再次以滑鼠右鍵按一下服務專案。這次，請在 [**偵錯**] 內容功能表下按一下 [**開始新執行個體**]。

    ![][21]

    Visual Studio 會為您的服務開啟預設網頁。您可以按一下 [**立即嘗試**]，以從行動服務中的預設網頁測試方法。

    ![][22]

    Visual Studio 依預設使用 IIS Express 在本機代管您的行動服務。您可以在工作列上以滑鼠右鍵按一下 IIS Express 的系統匣圖示，以檢視此設定。

    ![][23]

# <a name="update-app"></a>更新 Windows 市集應用程式以使用行動服務

在本節中，您將會更新 Windows 市集應用程式，以使用行動服務作為應用程式的後端服務。


1. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後按一下 [**管理 NuGet 封裝**]。

    ![][7]

2. 在 [管理 NuGet 封裝] 對話方塊中，搜尋位於線上封裝集合中的 **WindowsAzure.MobileServices** 並加以點選，以安裝 Azure 行動服務 Nuget 封裝。然後關閉對話方塊。

    ![][8]

3. 回到 Azure 管理入口網站，找出標示為「**連接應用程式並將資料儲存在服務中**」的步驟。請確定已選取 **C#** 語言。複製建立 `MobileServiceClient` 連線的程式碼片段。

    ![][9]

4. 在 Visual Studio 中，開啟 App.xaml.cs。在 `App` 類別定義的開頭貼上程式碼片段，此外，請將下列 `using` 陳述式新增至檔案頂端，並儲存檔案。

		using Microsoft.WindowsAzure.MobileServices;

    ![][10]


5. 在 Visual Studio 中開啟 MainPage.xaml.cs，然後新增 using 陳述式： 

		using Microsoft.WindowsAzure.MobileServices;

6. 在 Visual Studio 中，將 MainPage.xaml.cs 中的 `MainPage` 類別定義取代為下列定義，並儲存檔案。 

    此程式碼會使用行動服務 SDK，使應用程式能夠將其資料儲存在服務提供的資料表中，而不儲存在本機的記憶體內部。主要的三種方法為 `InsertTodoItem`、`RefreshTodoItems` 和 `UpdateCheckedTodoItem`。這三種方法可讓您以非同步方式插入、查詢資料集合，以及將其更新至 Azure 中的資料表。

        public sealed partial class MainPage : Page
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable = 
                App.MobileService.GetTable<TodoItem>();            
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem); 
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable.ToCollectionAsync(); 
                ListItems.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);      
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = TextInput.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }


## <a name="test-locally-hosted"></a>對本機代管的服務測試 Windows 市集應用程式

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

## <a name="publish-mobile-service"></a>將行動服務發佈至 Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>測試發佈至 Azure 的行動服務

1. 在 Visual Studio 中，開啟 App.xaml.cs。將建立 `MobileServiceClient` 以連接到本機代管之行動服務的程式碼註解化。將建立 `MobileServiceClient` 以連接到 Azure 中之服務的程式碼取消註解。儲存檔案的變更。

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://localhost:59226"
            //);
            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.preview.azure-mobile-preview.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....


2. 在 Visual Studio 中按 F5 鍵，或從 [偵錯] 功能表中按一下 [**開始偵錯**]。這會使 Windows 市集應用程式以先前的變更重新建置，然後再執行應用程式，以連接到 Azure 遠端代管的行動服務。 


3. 輸入新的 todoitem，然後為每個項目按一下 [**儲存**]。按一下核取方塊，以完成部分的新項目。每個新的 todoItem，都會儲存在您先前在 Azure 管理入口網站中為行動服務設定的 SQL Database 中，並進行更新。 

    ![][16]

    您可以重新啟動應用程式，以確認變更持續存留至 Azure 中的資料庫。 

## <a name="view-stored-data"></a>檢視儲存在 SQL Database 裡的資料

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

**開始使用資料**教學課程到此結束。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

* [使用指令碼驗證和修改資料]
  <br/>進一步了解在行動服務中使用伺服器指令碼驗證並變更從您的應用程式傳送的資料。

* [使用分頁縮小查詢範圍]
  <br/>了解如何在查詢中使用分頁，來控制單一要求中的處理資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

* [開始使用驗證]
  <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知] 
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

* [行動服務 .NET 作法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET。
  
<!-- Anchors. -->

[下載 Windows 市集應用程式專案]: #download-app
[建立新的行動服務]: #create-service
[在本機下載行動服務]: #download-the-service-locally
[更新 Windows 市集應用程式以使用行動服務]: #update-app
[對本機代管的服務測試 Windows 市集應用程式]: #test-locally-hosted
[將行動服務發佈至 Azure]: #publish-mobile-service
[對 Azure 代管的服務測試 Windows 市集應用程式]: #test-azure-hosted
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/Add-Service-Project-To-Solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/Download-Publishing-Profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/Add-Existing-Project-Dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/VS-Manage-NuGet-Packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/Manage-Nuget-Packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/Copy-MobileServiceClient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/VS-pasted-MobileServiceClient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/VS-Build-Solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/VS-Run-Solution.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[使用指令碼驗證和修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[行動服務 SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[開發人員程式碼範例網站]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[行動服務 .NET 作法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient 類別]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 
