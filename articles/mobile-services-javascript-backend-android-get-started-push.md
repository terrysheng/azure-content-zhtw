<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# <a name="getting-started-with-push"> </a>開始在行動服務中使用推播通知

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
<!-- <a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET 後端" >.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-android-get-started-push/" title="JavaScript 後端" class="current">JavaScript 後端</a></div>

本主題說明如何使用 Azure 行動服務傳送推播通知至 Android 應用程式。在本教學課程中，您會使用 Google 雲端通訊 (GCM) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

> [WACOM.NOTE]本教學課程示範行動服務與通知中心的整合，這是您從行動服務傳送推播通知的方式。如果您的行動服務較舊，是使用舊式的推播，而尚未升級為使用通知中心，建議您依照此教學課程中的說明進行升級。如果您選擇現在不升級，則應遵循下列版本的教學課程進行：[開始使用推播通知 (舊式)][]。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [啟用 Google 雲端通訊][]
2.  [設定行動服務][]
3.  [將推播通知新增至應用程式][]
4.  [更新指令碼來傳送推播通知][]
5.  [插入資料以接收通知][]

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務][]或[開始使用資料][]，將您的專案連接到行動服務。

## <span id="register"></span></a>啟用 Google 雲端通訊

> [WACOM.NOTE]若要完成此程序，您必須要有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 [accounts.google.com][]。

[WACOM.INCLUDE [啟用 GCM][]]

接下來，您將使用此 API 金鑰值，讓行動服務能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。

## <span id="configure"></span></a>設定行動服務來傳送推播要求

1.  登入 [Azure 管理入口網站][]，按一下 [行動服務]，然後按一下您的應用程式。

    ![][]

2.  按一下 [推播] 索引標籤，輸入前一個程序中從 GCM 取得的 [API Key] 值，然後按一下 [儲存]。

    > [WACOM.NOTE]如果您是使用較舊的行動服務來完成此教學課程，您可能會在 [推播] 索引標籤底部看到 [啟用增強型推播] 連結。現在按一下此連結，即可將您的行動服務升級，與通知中心整合。此變更無法回復。如需如何在生產環境的行動服務中啟用增強型推播通知的詳細資訊，請參閱[此指引][] (英文)。

    ![][1]

    <div class="dev-callout"><b>重要事項</b>
<p>如果您在入口網站的 [推播] 索引標籤中設定進階推播通知的 GCM 認證，這些認證將會與通知中心共用，以設定您的應用程式適用的通知中心。</p>
</div>

您的行動服務和應用程式現在都已完成設定，而可與 GCM 和通知中心搭配使用。

## <span id="add-push"></span></a>新增推播通知至應用程式

### 驗證 Android SDK 版本

[WACOM.INCLUDE [Verify SDK][]]

下一個步驟是安裝 Google Play 服務。Google 雲端通訊在開發和測試方面有一些 API 層級的最低需求，這些是資訊清單中的 **minSdkVersion** 屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考[設定 Google Play 服務 SDK][]，以確認此值可以設得多低，並加以適當設定。

### 新增 Google Play 服務至專案

[WACOM.INCLUDE [新增 Play 服務][]]

### 新增程式碼

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][]]

## <span id="update-scripts"></span></a>在管理入口網站中更新已註冊的插入指令碼

1.  在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

    ![][2]

2.  在 [todoitem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。

    ![][3]

    這會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。

3.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

        function insert(item, user, request) {
        // Define a payload for the Google Cloud Messaging toast notification.
        var payload = 
            '{"data":{"message" : ' + item.text + ' }}';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    如此即會註冊新的 insert 指令碼，該指令碼會在插入成功之後，使用 [gcm 物件][]將推播通知傳送給所有已註冊的裝置。

## <span id="test"></span></a>在應用程式中測試推播通知

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

### 設定用於測試的模擬器

當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

1.  重新啟動 Eclipse，然後在 Package Explorer 中以滑鼠右鍵按一下專案，按一下 [Properties]，按一下 \[Android]，核取 [Google API]，然後按一下 [OK]。

    ![][4]

    如此會使專案的目標變成 Google API。

2.  從 [Window] 中選取 [Android Virtual Device Manager]，選取您的裝置，按一下 [Edit]。

    ![][5]

3.  在 [Target] 中選取 [Google API]，然後按一下 [OK]。

    ![][6]

    如此會使 AVD 變成使用 Google API。

### 執行測試

1.  在 Eclipse 的 [執行] 功能表中按一下 [執行]，以啟動應用程式。

2.  在應用程式中，輸入有意義的文字，如 *A new Mobile Services task*，然後按一下 [加入] 按鈕。

    ![][7]

3.  從螢幕頂端向下撥動將裝置的通知中心開啟，以檢視通知。

您已成功完成此教學課程。

## <a name="next-steps"> </a>後續步驟

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

在下列主題中深入了解行動服務和通知中心：

-   [開始使用資料][]
    深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證][]
    了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

-   [什麼是通知中心？][]
    深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

-   [如何使用適用於行動服務的 Android 用戶端程式庫][]
    深入了解如何搭配使用行動服務與 Android。

-   [行動服務伺服器指令碼參考][]
    深入了解如何在您的行動服務中實作商務邏輯。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 市集 C#]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone"
  [iOS]: /zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push "iOS"
  [Android]: /zh-tw/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android"
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET 後端"
  [JavaScript 後端]: /zh-tw/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript 後端"
  [開始使用推播通知 (舊式)]: /zh-tw/documentation/articles/mobile-services-android-get-started-push/
  [啟用 Google 雲端通訊]: #register
  [設定行動服務]: #configure
  [將推播通知新增至應用程式]: #add-push
  [更新指令碼來傳送推播通知]: #update-scripts
  [插入資料以接收通知]: #test
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-android-get-started/
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-android-get-started-data/
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [啟用 GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [此指引]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Verify SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [設定 Google Play 服務 SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
  [新增 Play 服務]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [gcm 物件]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [4]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-android-get-started-users
  [什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/
  [如何使用適用於行動服務的 Android 用戶端程式庫]: /zh-tw/documentation/articles/mobile-services-android-how-to-use-client-library
  [行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
