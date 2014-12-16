<properties pageTitle="使用 .NET 後端行動服務開始使用推播通知" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />


# 將推播通知新增至行動服務應用程式

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

本主題說明如何使用 Azure 行動服務與 .NET 後端傳送推播通知至通用 Windows 應用程式。在本教學課程中，您會透過 Azure 通知中心，啟用通用 Windows 應用程式專案中的推播通知功能。完成之後，每次 TodoList 資料表中插入記錄時，您的行動服務將會從 .NET 後端將推播通知傳送至所有註冊的 Windows 市集與 Windows Phone 市集應用程式。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

>[WACOM.NOTE]本主題說明如何使用 Visual Studio Professional 2013 with Update 3 的工具，從行動服務將推播通知支援新增至通用 Windows 應用程式。要將推播通知從行動服務新增到 Windows 市集或 Windows Phone 8.1 應用程式時，也可使用相同的步驟進行。如果您無法升級至 Visual Studio Professional 2013 Update 3 或是您偏好將行動服務專案手動新增到 Windows 市集應用程式解決方案，請參閱[此版本](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push)的主題。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [針對推播通知註冊應用程式](#register)
2. [更新服務以傳送推播通知](#update-service)
3. [啟用推播通知以進行本機測試](#local-testing)
4. [在應用程式中測試推播通知](#test)

若要完成此教學課程，您需要下列項目：

* 有效的 [Microsoft 市集帳戶](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 或更新版本)。<br/>您可以使用免費試用版。 

##<a id="register"></a>針對推播通知註冊應用程式

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>瀏覽至 <code>\services\mobileServices\scripts</code> 專案資料夾，將產生的 <<em>your_service_name</em>>.push.register.js 指令碼檔案複製到共用 <code>\js</code> 資料夾，然後同時從個別 Windows 與 WindowsPhone 應用程式專案裡刪除這個檔案。</p></li> 
<li><p>開啟共用 <code>\js</code> 專案資料夾裡的這個指令碼檔案，並在 <em>activated</em> 事件接聽程式裡找出負責將裝置通道 URL 向通知中樞註冊的程式碼，然後刪除 <strong>done</strong> promise 函數。</p>
<p>本教學課程會在插入新項目時 (而非呼叫自訂 API 時) 傳送通知。</p></li>
<li><p>在 Windows 應用程式專案中，開啟 default.html 檔案並將指令碼檔案參考的路徑變更為共用 <code>\js</code> 專案資要夾，以便其如下列所示：</p><pre><code><script src="/js/your_service_name.push.register.js"></script></code></pre></li>
<li><p>為 WindowsPhone 應用程式專案重複此步驟。</p>
<p>現在，兩個專案同時都會使用推播註冊指令碼的共用版本。</p></li>
</ol>

現在應用程式已經啟用了推播通知，您必須更新行動服務以傳送推播通知。 

##<a id="update-service"></a>更新服務以傳送推播通知

以下步驟會更新現有的 TodoItemController 類別，以便在插入新的項目時將推播通知傳送給所有註冊的裝置。您可以在任何自訂的 [ApiController]、[TableController] 或是後端服務裡的任何一處，實作類似的程式碼。 

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> 啟用推播通知以進行本機測試

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

本節剩下步驟皆為選擇使用。您可使用這些步驟，測試您的應用程式與本機電腦上執行的行動服務是否相互運作正常。如果您打算使用 Azure 執行的行動服務來測試推播通知，可以直接跳至最後一節。這是因為 [新增推播通知] 精靈已經將您的應用程式設定為將您的應用程式連接至 Azure 上執行的服務。  

>[WACOM.NOTE]切勿使用生產環境的行動服務來進行測試與開發作業。請一律將您的行動服務專案發佈至個別的預備服務以利測試。

<ol start="5">
<li><p>瀏覽至 <code>\services\mobileServices\settings</code> 專案資料夾，將產生的 <<em>your_service_name</em>>.js 指令碼檔案複製到共用 <code>\js</code> 專案資料夾，然後同時從個別 Windows 與 WindowsPhone 應用程式專案裡刪除這個檔案。請同時在每個應用程式專案裡，刪除 <code>\services\mobileServices\scripts</code> 資料夾裡面的這個檔案 (如果同時存在的話)。</p></li> 
<li><p>開啟共用 <code>\js</code> 專案資料夾裡的這個指令碼檔案，然後將用來定義 <a href="http://msdn.microsoft.com/zh-tw/library/azure/jj554219.aspx">MobileServiceClient 物件</a>以便存取 Azure 中執行之行動服務的現有程式碼予以註解化。</p></li>
<li><p>使用相同名稱新增一個 <strong>MobileServiceClient</strong> 物件定義 (但在建構函式裡使用本機主機 URL)，類似如下範例：</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
var todolistClient = new WindowsAzure.MobileServiceClient(
	"http://localhost:4584");
</code></pre><p>透過這個 <strong>MobileServiceClient</strong> 物件，應用程式將連接到本機服務，而非 Azure 上代管的版本。當您想要改為在 Azure 中代管的行動服務上執行應用程式時，請換為原來的 <strong>MobileServiceClient</strong> 物件定義。</p></li>
</ol>

##<a id="test"></a> 在應用程式中測試推播通知

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>後續步驟

本教學課程示範如何啟用 Windows 市集應用程式以便使用行動服務與通知中樞來傳送推播通知的基礎。接下來，請考慮閱讀下個教學課程，[將推播通知傳送給驗證的使用者]，此課程說明如何使用標籤將行動服務中的推播通知指定傳送給驗證的使用者。

在下列主題中深入了解行動服務和通知中心：

* [開始使用資料]
  <br/>進一步了解使用行動服務來儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何使用行動服務以不同帳戶類型驗證應用程式的使用者。

* [什麼是通知中心？]
  <br/>深入了解通知中樞如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵錯通知中樞應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>取得通知中樞解決方案的疑難排解和偵錯指引。 

* [如何使用 Azure 行動服務的 HTML/JavaScript 用戶端]
  <br/>進一步了解如何從 HTML 和 JavaScript 應用程式中使用行動服務。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users

[將推播通知傳送給驗證的使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[什麼是通知中樞？]: /zh-tw/documentation/articles/notification-hubs-overview/

[如何使用 Azure 行動服務的 HTML/JavaScript 用戶端]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library
