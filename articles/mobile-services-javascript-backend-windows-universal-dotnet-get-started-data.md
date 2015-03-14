<properties 
	pageTitle="開始使用資料 (Windows 通用) | 行動開發人員中心" 
	description="了解如何開始使用行動服務，在您的通用 Windows 應用程式中使用資料。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

本主題將示範如何使用 Azure 行動服務，充分運用通用 Windows 應用程式裡的資料。通用 Windows 應用程式解決方案包括 Windows 市集 8.1 和 Windows Phone 市集 8.1 應用程式的專案，以及一般共用專案。如需詳細資訊，請參閱[建置目標為 Windows 和 Windows Phone 的通用 Windows 應用程式](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx)。

在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的通用 Windows 應用程式、建立新的行動服務、整合行動服務與該應用程式，然後登入 Azure 管理入口網站來檢視執行應用程式時所做的資料變更。

>[AZURE.NOTE]本主題說明如何使用 Visual Studio Professional 2013 Update 3 的工具，將新的行動服務連接到通用 Windows 應用程式。要將行動服務連接到 Windows 市集或 Windows Phone 市集 8.1 應用程式時，也可使用相同的步驟進行。若要將行動服務連接至 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式，請參閱[開始使用 Windows Phone 的資料](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data)。

> 如果您無法升級至 Visual Studio Professional 2013 Update 3 或是您偏好將行動服務專案手動新增到 Windows 市集應用程式解決方案，請參閱[此版本](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data)的主題。

本教學課程將逐步引導您完成下列基本步驟：

1. [下載 Windows 市集應用程式專案][取得 Windows 市集應用程式]
2. [從 Visual Studio 建立行動服務]
3. [新增用於儲存的資料表]
4. [更新應用程式以使用行動服務]
5. [針對行動服務進行應用程式測試]
6. [檢視 Azure 管理入口網站裡的上傳資料]

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 for Windows</a> (Update 2 或更新版本)。


##<a name="download-app"></a>下載 GetStartedWithData 專案

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>從 Visual Studio 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8"><li><p>在 [解決方案總管] 中，於 [GetStartedWithData.Shared] 專案資料夾中開啟 App.xaml.cs 程式碼檔案，並注意在 Windows 市集應用程式條件式編譯區塊裡，新增至 <strong>App</strong> 類別的全新靜態欄位，如以下範例所示：</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>本程式碼透過使用 <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient</a> 類別的執行個體，提供對應用程式中新行動服務的存取權。用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。應用程式中的所有頁面都可以使用此靜態資料行。</p>
</li>
<li><p>以滑鼠右鍵按一下 [Windows Phone] 應用程式專案，然後依據按一下 [<strong>新增</strong>]、[<strong>已連接服務</strong>]，並選取您剛建立的行動服務，然後按一下 [<strong>確定</strong>]。 </p>
<p>相同的程式碼會新增到共用 App.xaml.cs 檔案，但是這次是新增到 Windows Phone 應用程式條件式編譯區塊。</p></li>
</ol>

這時，Windows 市集與 Windows Phone 市集應用程式都會同時連接到新的行動服務。下一步則是在行動服務中建立新的 TodoItem 資料表。

##<a name="add-table"></a>將新資料表新增至行動服務

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]新的資料表是使用 Id、__createdAt、__updatedAt 和 __version 等資料行建立而成。啟用動態結構描述時，行動服務會根據插入或更新要求中的 JSON 物件自動產生新資料行。如需詳細資訊，請參閱[動態結構描述](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx)。

#<a name="update-app"></a>更新應用程式以使用行動服務

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>測試 Azure 代管的行動服務

現在我們可以將兩個通用的 Windows 應用程式同時與 Azure 代管的行動服務一起測試。

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>在<a href="https://manage.windowsazure.com/" target="_blank">管理入口網站</a>中，按一下 [<strong>行動服務</strong>]，然後按一下您的行動服務。<p></li>
<li><p>按一下 [資料]<strong></strong> 索引標籤，然後按一下 [瀏覽]<strong></strong>。</p>
<p>請注意，<strong>TodoItem</strong> 表格現在包含資料，其中識別碼值由行動服務產生，且資料欄已自動新增到表格以符合應用程式中的 TodoItem 類別。</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
這將結束**開始使用資料**教學課程。

## <a name="next-steps"> </a>後續步驟

本教學課程示範讓通用 Windows 應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

* [使用指令碼驗證及修改資料]
  <br/>深入了解在行動服務中使用伺服器指令碼，來驗證及變更從應用程式傳送出來的資料。

* [使用分頁縮小查詢範圍]
  <br/>了解如何在查詢中使用分頁，來控制單一要求中所處理的資料量。

完成資料序列之後，請嘗試下列其中一個其他教學課程：

* [開始使用驗證]
  <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知] 
  <br/>了解如何傳送非常基本的推播通知到您的應用程式。

* [行動服務 .NET 做法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET
  
<!-- Anchors. -->

[取得 Windows 市集應用程式]: #download-app
[從 Visual Studio 建立行動服務]: #create-service
[新增用於儲存的資料表]: #add-table
[更新應用程式以使用行動服務]: #update-app
[針對行動服務進行應用程式測試]: #test-app
[檢視 Azure 管理入口網站裡的上傳資料]: #view-data
[後續步驟]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[使用指令碼驗證及修改資料]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
[使用分頁縮小查詢範圍]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[行動服務 .NET 做法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[管理入口網站]: https://manage.windowsazure.com/
[行動服務 SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[開發人員程式碼範例網站]:  http://go.microsoft.com/fwlink/p/?LinkID=510826

[MobileServiceClient 類別]: http://go.microsoft.com/fwlink/p/?LinkId=302030


<!--HONumber=42-->
