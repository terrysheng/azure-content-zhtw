<properties urlDisplayName="Get Started with Data" pageTitle="開始使用資料 (Windows 市集) | 行動開發人員中心" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />

# 將行動服務新增至現有的應用程式

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

本主題將說明如何以 Azure 行動服務作為 Windows 市集應用程式的後端資料來源。在本教學課程中，您將下載 Visual Studio 2013 專案，以取得將資料儲存在記憶體中的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您在此教學課程中建立的行動服務，是一項 .NET 後端行動服務。.NET 後端服務讓您得以在行動服務中運用 .NET 語言與 Visual Studio 建立伺服器端商業邏輯，並在本機電腦上執行與偵錯行動服務。若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 JavaScript 後端版本。

>[WACOM.NOTE]本主題說明如何使用 Visual Studio Professional 2013 Update 3 的工具，將新的行動服務連接到通用 Windows 應用程式。要將行動服務連接到 Windows 市集或 Windows Phone 市集 8.1 應用程式時，也可使用相同的步驟進行。若要將行動服務連接至 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式，請參閱[開始使用 Windows Phone 的資料](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)。

> 如果您無法升級至 Visual Studio Professional 2013 Update 3，或是您偏好將行動服務專案手動新增到 Windows 市集應用程式解決方案，請參閱[此版本](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data)的主題。

本教學課程將逐步引導您完成下列基本步驟：

1. [下載 Windows 市集應用程式專案]
2. [從 Visual Studio 建立新的行動服務]
3. [在本機測試行動服務專案]
4. [更新應用程式以使用行動服務]
5. [將行動服務發佈至 Azure]
6. [對 Azure 代管的服務測試應用程式]
7. [檢視儲存在 SQL Database 裡的資料]

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/zh-tw/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 或更新版本)。<br/>您可以使用免費試用版。 

##<a name="download-app"></a>下載 GetStartedWithData 專案

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>從 Visual Studio 建立新的行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>在 [解決方案總管] 中，於 [GetStartedWithData.Shared] 專案資料夾中開啟 App.xaml.cs 程式碼檔案，並注意在 Windows 市集應用程式條件式編譯區塊裡，新增至 <strong>App</strong> 類別的全新靜態欄位，如以下範例所示：</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>本程式碼透過使用 <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient 類別</a>的執行個體，提供對應用程式中新行動服務的存取權。用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。應用程式中的所有頁面都可以使用此靜態資料行。</p>
</li>
<li><p>以滑鼠右鍵按一下 Windows Phone 應用程式專案，然後依據按一下 [<strong>新增</strong>]、[<strong>已連接服務</strong>]，並選取您剛建立的行動服務，然後按一下 [<strong>確定</strong>]。 </p>
<p>相同的程式碼會新增到共用 App.xaml.cs 檔案，但是這次是新增到 Windows Phone 應用程式條件式編譯區塊。</p></li>
</ol>

這時，Windows 市集與 Windows Phone 市集應用程式都會同時連接到新的行動服務。下個步驟是測試新的行動服務專案。


##<a name="test-the-service-locally"></a>在本機測試行動服務專案

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##<a name="update-app"></a>更新應用程式以使用行動服務

在本節中，您將會更新通用 Windows 應用程式，以使用行動服務作為應用程式的後端服務。您只需要變更 [GetStartedWithData.Shared] 專案資料夾的 MainPage.xaml.cs 專案檔案。 

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]


##<a name="publish-mobile-service"></a>將行動服務發佈至 Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>測試 Azure 代管的行動服務

現在我們可以將兩個通用的 Windows 應用程式同時與 Azure 代管的行動服務一起測試。

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

##<a name="view-stored-data"></a>檢視儲存在 SQL Database 裡的資料

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
**開始使用資料**教學課程到此結束。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓通用 Windows 應用程式專案能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

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
[從 Visual Studio 建立新的行動服務]: #create-service
[在本機測試行動服務專案]: #test-the-service-locally
[更新應用程式以使用行動服務]: #update-app
[對本機代管的服務測試應用程式]: #test-locally-hosted
[將行動服務發佈至 Azure]: #publish-mobile-service
[對 Azure 代管的服務測試應用程式]: #test-azure-hosted
[ 檢視儲存在 SQL Database 裡的資料]: #view-stored-data
[後續步驟]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[使用指令碼驗證和修改資料]: /zh-tw/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[使用分頁縮小查詢範圍]: /zh-tw/develop/mobile/tutorials/add-paging-to-data-dotnet
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[開始使用離線資料同步]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[行動服務 SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[開發人員程式碼範例網站]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[行動服務 .NET 作法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient 類別]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 
