<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services (universal Windows apps)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 開始使用行動服務中的資料 (通用 Windows 應用程式)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-data/" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-android-get-started-data/" title="Android">Android</a><a href="/zh-tw/documentation/articles/mobile-services-html-get-started-data/" title="HTML">HTML</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/" title="Xamarin.Android">Xamarin.Android</a></div>

<!---<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">.NET backend</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/"  title="JavaScript backend" class="current">JavaScript backend</a></div>-->

本主題將說明如何使用 Azure 行動服務，進而運用通用 Windows 8.1 應用程式中的資料。在本教學課程中，您將下載 Visual Studio 專案，以取得在記憶體中儲存資料的通用 Windows 應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。

> [WACOM.NOTE]本教學課程需要 Visual Studio 2013 Update 2。

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案][]
2.  [從 Visual Studio 建立行動服務][]
3.  [新增要儲存的資料表並更新應用程式][]
4.  [針對行動服務進行應用程式測試][]

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][]。

## <a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程是採用 [GetStartedWithMobileServices 應用程式][]所建立，此應用程式是 Visual Studio 2013 中的 Windows 市集應用程式專案。除了會將新增項目儲存在本機記憶體中之外，此應用程式的 UI 與行動服務快速入門所產生的應用程式完全相同。

1.  從[開發人員程式碼範例網站][GetStartedWithMobileServices 應用程式]中下載 C\# 版本的 GetStartedWithMobileServices 範例應用程式。

    ![][]

2.  在 Visual Studio 2013 中，開啟下載的專案並檢查 MainPage.xaml.cs 檔案。

    請注意新增的 **TodoItem** 物件儲存在記憶體內部 **ObservableCollection\<TodoItem\>** 中。

3.  按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式的 [Insert a TodoItem] 中鍵入一些文字，然後按一下 [儲存]。

    ![][1]

    請注意儲存的文字會顯示在 [查詢和更新資料] 下方的第二個資料行中。

## <a name="create-service"></a><span class="short-header">建立行動服務</span>從 Visual Studio 建立行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][]]

1.  在 [方案總管] 中開啟 App.xaml.cs 程式碼檔案，並注意新增到 \*\*App\*\* 類別的新靜態資料行，其看起來會如下列範例所示：

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    本程式碼透過使用 [MobileServiceClient 類別][]的執行個體，提供對應用程式中新行動服務的存取權。用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。應用程式中的所有頁面都可以使用此靜態資料行。

## <a name="add-table"></a><span class="short-header">新增資料表</span>將新資料表新增至行動服務並更新應用程式

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][]]

1.  在檔案 MainPage.xaml.cs 中，使用陳述式新增或取消註解下列項目：

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

2.  在相同檔案中，使用下列程式碼來取代 TodoItem 類別定義：

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")] 
            public bool Complete { get; set; }
        }

    **JsonPropertyAttribute** 可用來定義用戶端類型中屬性名稱與基礎資料表中資料行名稱之間的對應。

3.  針對定義現有項目集合的資料列加上註解，然後取消註解或新增下列幾行，並使用將專案連接到行動服務時新增到 App.xaml.cs 檔案的 `MobileServiceClient` 欄位來取代 *\<yourClient\>*：

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    此程式碼會建立行動服務感知繫結集合 (項目)，和資料庫資料表 (todoTable) 的 Proxy 類別。

4.  在 **InsertTodoItem** 方法中，移除設定 **TodoItem.Id** 屬性的程式碼行，將 **async** 修正詞新增至方法，並取消註解下列程式碼行：

        await todoTable.InsertAsync(todoItem);

    此程式碼會將新的項目插入資料表中。

    <div class="dev-callout"><strong>注意</strong><p>新的資料表是使用 Id、__createdAt、__updatedAt 和 __version 等資料行建立而成。啟用動態結構描述時，行動服務會根據插入或更新要求中的 JSON 物件自動產生新資料行。如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx">動態結構描述</a>。</p></div>

5.  在 **RefreshTodoItems** 方法中，將 **async** 修正因子新增至方法，然後取消註解下列程式碼行：

        items = await todoTable.ToCollectionAsync();

    這會設定 `todoTable` 中項目集合的繫結，其中包含從行動服務中傳回的所有 **TodoItem** 物件。

6.  在 **UpdateCheckedTodoItem** 方法中，將 **async** 修正因子新增至方法，並取消註解下列程式碼行：

        await todoTable.UpdateAsync(item);

    在變更文字或勾選方塊時.

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

## <a name="test-app"></a><span class="short-header">測試應用程式</span>在您的新行動服務中測試應用程式

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  和之前一樣，在 [Insert a TodoItem] 中鍵入文字，然後按一下 [儲存]。

    這會傳送新項目以插入至行動服務。

3.  在[管理入口網站][]中按一下 [行動服務]，然後按一下您的行動服務。

4.  按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

    ![][2]

    請注意，[TodoItem] 資料表現在包含資料和行動服務產生的識別碼值，且資料行已自動新增至資料表，以符合應用程式中的 TodoItem 類別。

5.  在應用程式中檢查清單中的其中一個項目，然後返回入口網站中的 [瀏覽] 索引標籤，並按一下 [重新整理]。

    請注意，完成值已從 [false] 變更為 [true]。

6.  在 MainPage.xaml.cs 專案檔案中，使用下列可篩選出已完成項目的程式碼，來取代現有的 **RefreshTodoItems** 方法：

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  在應用程式中檢查清單中的其中一個項目，然後按一下 [重新整理] 按鈕。

    請注意，已檢查的項目此時會從清單中消失。每個重新整理結果會在行動服務間來回，目前傳回的是篩選過的資料。

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料][]
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍][]
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證][]
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知][]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念參考資料][]
    深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows 市集 C\#]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/ "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /zh-tw/documentation/articles/mobile-services-ios-get-started-data/ "iOS"
  [Android]: /zh-tw/documentation/articles/mobile-services-android-get-started-data/ "Android"
  [HTML]: /zh-tw/documentation/articles/mobile-services-html-get-started-data/ "HTML"
  [Xamarin.iOS]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/ "Xamarin.iOS"
  [Xamarin.Android]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/ "Xamarin.Android"
  [下載 Windows 市集應用程式專案]: #download-app
  [從 Visual Studio 建立行動服務]: #create-service
  [新增要儲存的資料表並更新應用程式]: #add-table
  [針對行動服務進行應用程式測試]: #test-app
  [Azure 免費試用]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28
  [GetStartedWithMobileServices 應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  []: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [MobileServiceClient 類別]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [動態結構描述]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx
  [管理入口網站]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [使用指令檔驗證與修改資料]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [使用分頁縮小查詢範圍]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
  [開始使用驗證]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
  [開始使用推播通知]: ../mobile-services-windows-store-dotnet-get-started-push/
  [行動服務 .NET 作法概念參考資料]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
