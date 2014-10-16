<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-push-android" urlDisplayName="Get Started with Push" pageTitle="Get started with push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# 開始在行動服務中使用推播通知

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!---<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET 後端" class="current">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-android-get-started-push/"  title="JavaScript 後端">JavaScript 後端</a></div>

本主題說明如何使用 Azure 行動服務傳送推播通知至 Android 應用程式。在本教學課程中，您會使用 Google 雲端通訊 (GCM) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程將逐步引導您完成下列步驟：

1.  [啟用 Google 雲端通訊][]
2.  [設定行動服務以傳送推播要求][]
3.  [更新伺服器以傳送推播通知][]
4.  [將推播通知新增至應用程式][]
5.  [啟用推播通知以進行本機測試][]
6.  [對已發佈的行動服務進行應用程式測試][]

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務][]或[開始使用資料][]，將您的專案連接到行動服務。同樣地，本教學課程也需要 Visual Studio 2013。

> [WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][]。

## <span id="register"></span></a>啟用 Google 雲端通訊

[WACOM.INCLUDE [啟用 GCM][]]

## <span id="configure"></span></a>設定行動服務來傳送推播要求

1.  登入 [Azure 管理入口網站][]，按一下 [行動服務]，然後按一下您的應用程式。

    ![][]

2.  按一下 [推播] 索引標籤，輸入前一個程序中從 GCM 取得的 [API Key] 值，然後按一下 [儲存]。

    ![][1]

    <div class="dev-callout"><b>重要事項</b>
<p>如果您在入口網站的 [推播] 索引標籤中設定進階推播通知的 GCM 認證，這些認證將會與通知中心共用，以設定您的應用程式適用的通知中心。</p>
</div>

您的行動服務現在已設定成使用 GCM 和通知中心。

## <a name="download-the-service"></a><span class="short-header">下載服務</span>將服務下載至您的本機電腦

[WACOM.INCLUDE [mobile-services-download-service-locally][]]

## <a name="test-the-service"></a><span class="short-header">測試服務</span>測試行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <span id="update-server"></span></a>更新伺服器以傳送推播通知

1.  在 Visual Studio 的 [方案總管] 中，展開行動服務專案中的 **Controllers** 資料夾。開啟 TodoItemController.cs。在檔案的頂端，新增下列`using` 陳述式：

        using System;
        using System.Collections.Generic;

2.  使用下列程式碼更新`PostTodoItem` 方法定義：

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    此程式碼會在插入 Todo 項目之後傳送推播通知 (含所插入項目的文字)。如果發生錯誤，程式碼將會新增可透過管理入口網站從行動服務的 [記錄檔] 索引標籤來檢視的錯誤記錄項目。

## <a name="publish-the-service"></a><span class="short-header">發佈服務</span>將行動服務發佈至 Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="update-app"></a>新增推播通知至應用程式

### 驗證 Android SDK 版本

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][]]

下一個步驟是安裝 Google Play 服務。Google 雲端通訊在開發和測試方面有一些 API 層級的最低需求，這些是資訊清單中的 **minSdkVersion** 屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考 [設定 Google Play 服務 SDK]，以確認此值可以設得多低，並加以適當設定。

### 新增 Google Play 服務至專案

[WACOM.INCLUDE [新增 Play 服務][]]

### 新增程式碼

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][]]

## <a name="test-app"></a><span class="short-header">測試應用程式</span>對已發佈的行動服務進行應用程式測試

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

### 如果您使用模擬器進行測試...

請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

1.  從 [視窗] 中選取 [Android Virtual Device Manager]，選取您的裝置，按一下 **[編輯]** (如果您沒有任何裝置，則按 [新增])。

    ![][2]

2.  在 [目標] 中選取 \[Google API\] (或 [Google APIs x86])，然後按一下 [確定]。

    ![][3]

    如此會使 AVD 變成使用 Google API。如果您安裝了數個版本的 Android SDK，請確定 API 層級符合您先前在專案屬性中設定的層級。

### <span id="local-testing"></span></a> 啟用推播通知以進行本機測試

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][]]

### 執行測試

1.  在 Eclipse 的 [執行] 功能表中按一下 [執行]，以啟動應用程式。

2.  在應用程式中，輸入有意義的文字，如 *A new Mobile Services task*，然後按一下 [加入] 按鈕。

    ![][4]

3.  從螢幕頂端向下撥動將裝置的通知中心開啟，以檢視通知。

您已成功完成此教學課程。

## <a name="next-steps"> </a>後續步驟

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.   + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

在下列主題中深入了解行動服務和通知中心：

-   [開始使用資料][]  
    深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證][]  
    了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

-   [什麼是通知中心？][]  
    深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

-   [如何使用適用於行動服務的 Android 用戶端程式庫][]  
    深入了解如何搭配使用行動服務與 Android。



  [Windows 市集 C\#]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
  [iOS]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
  [Android]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET 後端"
  [JavaScript 後端]: /zh-tw/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript 後端"
  [啟用 Google 雲端通訊]: #register
  [設定行動服務以傳送推播要求]: #configure
  [更新伺服器以傳送推播通知]: #update-server
  [將推播通知新增至應用程式]: #update
  [啟用推播通知以進行本機測試]: #local-testing
  [對已發佈的行動服務進行應用程式測試]: #test-app
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Azure 免費試用]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [啟用 GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [新增 Play 服務]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [4]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/
  [如何使用適用於行動服務的 Android 用戶端程式庫]: /zh-tw/documentation/articles/mobile-services-android-how-to-use-client-library
