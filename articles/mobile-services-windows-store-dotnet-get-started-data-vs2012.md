<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 開始以 Visual Studio 2012 使用行動服務中的資料

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows 市集 JavaScript" class="current">Windows 市集 JavaScript</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本主題將示範如何使用 Azure 行動服務，以在 Windows 市集應用程式中運用資料。在本教學課程中，您將下載 Visual Studio 2012 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。您可以按一下右方的短片，來檢視本教學課程的影片版。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">下午 03:33:00</span></div>

</div>

<div class="dev-callout"><b>注意</b>
<p>本教學課程會將行動服務功能加入 Visual Studio 2012 中建立的 Windows 市集應用程式。Visual Studio 2013 包含新功能，可讓您輕鬆地將您的 Windows 市集應用程式與行動服務連接。如需詳細資訊，請參閱<a href="/zh-tw/develop/mobile/tutorials/get-started-with-data-dotnet/">開始使用行動服務中的資料</a>。</p>
</div>

本教學課程將逐步引導您完成下列基本步驟：

1.  [下載 Windows 市集應用程式專案][下載 Windows 市集應用程式專案]
2.  [建立行動服務][建立行動服務]
3.  [新增用於儲存的資料表][新增用於儲存的資料表]
4.  [更新應用程式以使用行動服務][更新應用程式以使用行動服務]
5.  [針對行動服務進行應用程式測試][針對行動服務進行應用程式測試]

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。</p></div>

## <a name="download-app"></a><span class="short-header">下載專案</span>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithData 應用程式][GetStartedWithData 應用程式] (英文) (Windows 市集應用程式) 為基礎而建立。除了會將新增項目儲存在本機記憶體中之外，由此應用程式與行動服務快速入門所產生的應用程式兩者的 UI 完全相同。

1.  從[開發人員程式碼範例網站][GetStartedWithData 應用程式] (英文) 中下載 C# 版本的 GetStartedWithData 範例應用程式。

    ![][0]

2.  在 Visual Studio 2012 Express for Windows 8 中，開啟下載專案並檢查 MainPage.xaml.cs 檔案。

    請注意，新增的 **TodoItem** 物件會儲存在記憶體內部的 **ObservableCollection\<TodoItem\>** 中。

3.  按 **F5** 鍵，以重建專案並啟動應用程式。

4.  在應用程式的 [Insert a TodoItem] 中鍵入一些文字，然後按一下 [儲存]。

    ![][1]

    請注意，儲存的文字會顯示在 [查詢和更新資料] 下方的第二個資料欄中。

## <a name="create-service"></a><span class="short-header">建立行動服務</span>在管理入口網站中建立新的行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">新增資料表</span>將資料表新增至行動服務

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">更新應用程式</span>更新應用程式以使用行動服務進行資料存取

您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機收集) 中儲存項目。

1.  在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案名稱，然後選取 [管理 NuGet 封裝]。

2.  在左側窗格中依序選取 [線上] 類別、[Include Prerelease]，搜尋 `WindowsAzure.MobileServices`，按一下 [Azure 行動服務] 封裝上的 [安裝]，然後接受授權協定。

    ![][2]

    這會將行動服務用戶端程式庫新增至專案。

3.  在管理入口網站中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

4.  按一下 [儀表板] 索引標籤並記下 [網站 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

    ![][3]

    從應用程式程式碼存取行動服務時，您將會用到這些值。

5.  在 Visual Studio 中，開啟檔案 App.xaml.cs 並新增或取消註解下列 `using` 陳述式：

        using Microsoft.WindowsAzure.MobileServices;

6.  在此相同檔案中，依序取消註解定義 **MobileService** 變數的程式碼，並提供 **MobileServiceClient** 建構函式中行動服務的 URL 和應用程式金鑰。

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    這會建立用來存取行動服務的新 **MobileServiceClient** 執行個體。

7.  在檔案 MainPage.xaml.cs 中，新增或取消註解下列 `using` 陳述式：

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  在相同檔案中，使用下列程式碼來取代 **TodoItem** 類別定義：

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  針對定義現有 **items** 集合的程式碼行進行註解，然後取消註解下列程式碼行：

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

    此程式碼會建立行動服務感知繫結集合 (**items**)，和 SQL 資料庫資料表 **TodoItem** (**todoTable**) 的 Proxy 類別。

10. 在 **InsertTodoItem** 方法中，移除設定 **TodoItem**.**Id** 屬性的程式碼行，將 **async** 修正因子新增至方法，並取消註解下列程式碼行：

        await todoTable.InsertAsync(todoItem);

    此程式碼會將新的項目插入資料表中。

11. 在 **RefreshTodoItems** 方法中，將 **async** 修正因子新增至方法，然後取消註解下列程式碼行：

        items = await todoTable.ToCollectionAsync();

    這會設定 <code>todoTable</code> 中項目集合的繫結，其中包含從行動服務中傳回的所有 **TodoItem** 物件。

12. 在 **UpdateCheckedTodoItem** 方法中，將 **async** 修正因子新增至方法，並取消註解下列程式碼行：

         await todoTable.UpdateAsync(item);

    這會傳送項目更新給行動服務。

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

## <a name="test-app"></a><span class="short-header">測試應用程式</span>在您的新行動服務中測試應用程式

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  和之前一樣，在 [Insert a TodoItem] 中鍵入文字，然後按一下 [儲存]。

    這會傳送新項目以插入至行動服務。

3.  在[管理入口網站][管理入口網站]中按一下 [行動服務]，然後按一下您的行動服務。

4.  按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

    ![][4]

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

    請注意，所有已核取的項目都會從清單中消失。Each refresh results in a round-trip to the mobile service, which now returns filtered data.

這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [使用指令檔驗證與修改資料][使用指令檔驗證與修改資料]
    深入了解在行動服務中使用伺服器指令檔，來驗證並變更從應用程式傳送出來的資料。

-   [使用分頁縮小查詢範圍][使用分頁縮小查詢範圍]
    了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

-   [開始使用驗證][開始使用驗證]
    了解如何驗證應用程式的使用者。

-   [開始使用推播通知][開始使用推播通知]
    了解如何將極為基本的推播通知傳送到應用程式。

-   [行動服務 .NET 作法概念參考資料][行動服務 .NET 作法概念參考資料]
    深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [下載 Windows 市集應用程式專案]: #download-app
  [建立行動服務]: #create-service
  [新增用於儲存的資料表]: #add-table
  [更新應用程式以使用行動服務]: #update-app
  [針對行動服務進行應用程式測試]: #test-app
  [GetStartedWithData 應用程式]: http://go.microsoft.com/fwlink/?LinkId=262308
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
  [管理入口網站]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [使用指令檔驗證與修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-dotnet
  [開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
  [行動服務 .NET 作法概念參考資料]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
