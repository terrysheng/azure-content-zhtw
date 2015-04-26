<properties 
	pageTitle="開始使用資料 (WP8) - Azure 行動服務" 
	description="了解如何開始從 Windows Phone 8 應用程式使用 Azure 行動服務。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="glenga"/>


# 新增行動服務到現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主題將示範如何使用 Azure 行動服務，進而運用 Windows Phone 8 應用程式中的資料。在本教學課程中，您將下載應用程式，並在記憶體中儲存資料、建立新的行動服務、將行動服務與該應用程式整合，然後登入 Azure 管理入口網站查看執行應用程式時所做的資料變更。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">下午 12:54</span></div>
</div>

本教學課程將逐步引導您完成下列基本步驟：

1. [下載 Windows Phone 8 應用程式專案]
2. [建立行動服務]
3. [新增用於儲存的資料表]
4. [更新應用程式以使用行動服務]
5. [針對行動服務進行應用程式測試]

本教學課程需要 Visual Studio 2012 Express for Windows Phone 8 和可在 Windows 8 上執行的 [Windows Phone 8 SDK]。若要完成本教學課程，以建立新的 Windows Phone 8.1 應用程式，您必須使用 Visual Studio 2013 Update 2 或更新版本。

>[AZURE.NOTE]若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F" target="_blank">Azure 免費試用</a>。

##<a name="download-app"></a>下載 GetStartedWithData 專案

本教學課程以 [GetStartedWithData 應用程式][開發人員程式碼範例網站]為基礎 (Windows Phone Silverlight 8 應用程式專案)。  

1. 從[開發人員程式碼範例網站]下載 GetStartedWithData 範例應用程式專案。 

	>[AZURE.NOTE]若要建立 Windows Phone Silverlght 8.1 應用程式，只要將已下載之 Windows Phone Silverlight 8 應用程式專案的目標 OS 變更為 Windows Phone 8.1 即可。若要建立 Windows Phone 市集應用程式，請下載 GetStartedWithData 範例應用程式專案的 [Windows Phone 市集應用程式版本](http://go.microsoft.com/fwlink/p/?LinkId=397372)。 

2. 在 Visual Studio 中，開啟下載的專案並檢查 MainPage.xaml.cs 檔案。

   	請注意，新增的 **TodoItem** 物件儲存在記憶體內部 **ObservableCollection&lt;TodoItem&gt;** 中。

3. 按 **F5** 鍵，以重建專案並啟動應用程式。

4. 在應用程式的文字方塊中鍵入一些文字，然後按一下 [儲存] 按鈕。

   	![][0]  

   	請注意，儲存的文件會顯示在清單下方。

<h2><a name="create-service"></a>在管理入口網站中建立新的行動服務</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>將新資料表新增至行動服務</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>更新應用程式以使用行動服務進行資料存取</h2>

您的行動服務已準備就緒，現在可以更新應用程式以便在行動服務 (而非本機集合) 中儲存項目。 

1. 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後選取 [管理 NuGet 封裝]。

2. 在左側窗格中選取 [**線上**] 類別，搜尋  `WindowsAzure.MobileServices`，按一下 [**Azure 行動服務**] 封裝上的 [**安裝**]，然後接受授權協定。

  	![][7]

  	這會將行動服務用戶端程式庫新增至專案。

3. 在「管理入口網站」中，按一下 [行動服務]，然後按一下您剛剛建立的行動服務。

4. 按一下 [儀表板] 索引標籤並記下 [網站 URL]，然後按一下 [管理金鑰] 並記下 [應用程式金鑰]。

   	![][8]

  	從應用程式程式碼存取行動服務時，您將會用到這些值。

5. 在 Visual Studio 中，開啟檔案 App.xaml.cs 並新增或取消註解下列  `using` 陳述式：

       	using Microsoft.WindowsAzure.MobileServices;

6. 在此相同檔案中，依序取消註解定義 **MobileService** 變數的程式碼，並提供 **MobileServiceClient** 建構函式中行動服務的 URL 和應用程式金鑰。

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	這會建立 **MobileServiceClient** 的新執行個體，可用來存取您的行動服務。

6. 在檔案 MainPage.xaml.cs 中，新增或取消註解下列  `using` 陳述式：

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. 在相同檔案中，使用下列程式碼來取代 **TodoItem** 類別定義：

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. 針對定義現有 **items** 集合的程式碼行進行註解，然後取消註解下列程式碼行：

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	此程式碼會建立行動服務感知繫結集合 (**items**)，和 SQL 資料庫資料表 **TodoItem** (**todoTable**) 的 Proxy 類別。 

7. 在 **InsertTodoItem** 方法中，移除設定 **TodoItem**.*Id** 屬性的程式碼行，將 **async** 修正因子新增至方法，並取消註解下列程式碼行：

        await todoTable.InsertAsync(todoItem);

  	此程式碼會將新的項目插入資料表中。

8. 在 **RefreshTodoItems** 方法中，將 **async** 修正因子新增至方法，然後取消註解下列程式碼行：

        items = await todoTable.ToCollectionAsync();

   	這會對 todoTable 中的項目集合設定繫結，其中包含從行動服務傳回的所有 TodoItem 物件。 

9. 在 **UpdateCheckedTodoItem** 方法中，將 **async** 修正因子新增至方法，並取消註解下列程式碼行：

         await todoTable.UpdateAsync(item);

   	這會將項目更新傳送到行動服務

應用程式現已更新為使用行動服務進行後端儲存，我們可以開始在行動服務中測試應用程式。

<h2><a name="test-app"></a>對新的行動服務進行應用程式測試</h2>

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2. 和之前一樣，在文字方塊中鍵入文字，然後按一下 [儲存]。

   	這會傳送新項目以插入至行動服務。

3. 在[管理入口網站]中，按一下 [行動服務]，然後按一下您的行動服務。

4. 按一下 [資料] 索引標籤，然後按一下 [瀏覽]。

   	![][9]
  
   	請注意，**TodoItem** 表格現在包含資料，其中識別碼值由行動服務產生，且資料欄已自動新增到表格以符合應用程式中的 TodoItem 類別。

這將結束 Windows Phone 8 的「開始使用資料」教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程將示範啟用 Windows Phone 8 應用程式，進而使用行動服務資料的基本概念。接下來，請考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列教學課程：

* [使用指令碼驗證及修改資料]
  <br/>深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍]
  <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料數列之後，您可以嘗試下列其中一個 Windows Phone 8 教學課程：

* [開始使用驗證] 
  <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知] 
  <br/>了解如何使用「行動服務」傳送非常基本的推播通知到您的應用程式。
 
<!-- Anchors. -->
[下載 Windows Phone 8 應用程式專案]: #download-app
[建立行動服務]: #create-service
[新增用於儲存的資料表]: #add-table
[更新應用程式以使用行動服務]: #update-app
[針對行動服務進行應用程式測試]: #test-app
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png






[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[使用指令碼驗證及修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-wp8
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-wp8
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-wp8
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-wp8
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-wp8

[Azure 管理入口網站]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[行動服務 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[開發人員程式碼範例網站]:  http://go.microsoft.com/fwlink/p/?LinkId=271146



<!--HONumber=42-->
