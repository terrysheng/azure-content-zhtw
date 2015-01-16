<properties pageTitle="使用 JavaScript 後端行動服務開始使用推播通知" metaKeywords="" description="了解如何使用 Azure 行動服務及通知中心傳送推播通知至通用 Windows 應用程式。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />


# 將推播通知新增至行動服務應用程式

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

本主題說明如何使用 Azure 行動服務與 JavaScript 後端傳送推播通知至通用 Windows 應用程式。在本教學課程中，您會透過 Azure 通知中心，啟用通用 Windows 應用程式專案中的推播通知功能。完成之後，每次 TodoList 資料表中插入記錄時，您的行動服務將會從 JavaScript 後端將推播通知傳送至所有註冊的 Windows 市集與 Windows Phone 市集應用程式。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

>[WACOM.NOTE]本主題說明如何使用 Visual Studio 2013 with Update 3 的工具，從行動服務將推播通知支援新增至通用 Windows 應用程式。要將推播通知從行動服務新增到 Windows 市集或 Windows Phone 8.1 應用程式時，也可使用相同的步驟進行。如果您無法升級至 Visual Studio 2013 Update 3 或是您偏好將行動服務專案手動新增到 Windows 市集應用程式解決方案，請參閱[此版本](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push)的主題。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [針對推播通知註冊應用程式](#register)
2. [更新服務以傳送推播通知](#update-service)
3. [在應用程式中測試推播通知](#test)

若要完成此教學課程，您需要下列項目：

* 有效的 [Microsoft 市集帳戶](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* [Visual Studio 2013 Express for Windows](http://go.microsoft.com/fwlink/?LinkId=257546) (Update 3 或更新版本) 

## <a id="register"></a>針對推播通知註冊應用程式

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>瀏覽至 <code>\services\mobileServices\scripts</code> 專案資料夾，將產生的 &lt;<em>your_service_name</em>&gt;.push.register.js 指令碼檔案複製到共用 <code>\js</code> 資料夾，然後同時從個別 Windows 與 WindowsPhone 應用程式專案裡刪除這個檔案。</p></li> 
<li><p>開啟共用 <code>\js</code> 專案資料夾裡的這個指令碼檔案，並在 <em>activated</em> 事件接聽程式裡找出負責將裝置通道 URL 向通知中樞註冊的程式碼，然後刪除 <strong>done</strong> promise 函數。</p>
<p>本教學課程會在插入新項目時 (而非呼叫自訂 API 時) 傳送通知。</p></li>
<li><p>在 Windows 應用程式專案中，開啟 default.html 檔案並將指令碼檔案參考的路徑變更為共用 <code>\js</code> 專案資要夾，以便其如下列所示：</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>為 WindowsPhone 應用程式專案重複此步驟。</p>
<p>現在，兩個專案同時都會使用推播註冊指令碼的共用版本。</p></li>
</ol>

現在應用程式已經啟用了推播通知，您必須更新行動服務以傳送推播通知。 

## <a id="update-service"></a>更新服務以傳送推播通知

下列步驟會更新 TodoItem 資料表裡註冊的插入指令碼。您可以在任何伺服器指令碼或是後端服務的任何一處實作類似的程式碼。 

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <a id="test"></a> 在應用程式中測試推播通知

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

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
[Live SDK for Windows (英文)]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users

[傳送推播通知給已驗證的使用者]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/

[什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/

[如何使用 Azure 行動服務的 HTML/JavaScript 用戶端]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library
