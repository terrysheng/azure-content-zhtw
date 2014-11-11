<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# 開始在行動服務中使用推播通知

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!-- <a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET 後端" class="current">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="JavaScript 後端">JavaScript 後端</a></div>

本主題說明如何使用 Azure 行動服務將推播通知傳送至 iOS 應用程式。在本教學課程中，您會使用 Apple 推播通知服務 (APNS) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [產生憑證簽署要求][產生憑證簽署要求]
2.  [註冊您的應用程式並啟用推播通知][註冊您的應用程式並啟用推播通知]
3.  [建立應用程式的佈建設定檔][建立應用程式的佈建設定檔]
4.  [在本機下載服務][在本機下載服務]
5.  [測試行動服務][測試行動服務]
6.  [更新伺服器以傳送推播通知][更新伺服器以傳送推播通知]
7.  [將行動服務發佈至 Azure][將行動服務發佈至 Azure]
8.  [新增推播通知至應用程式][新增推播通知至應用程式]
9.  [更新指令碼來傳送推播通知][更新指令碼來傳送推播通知]
10. [啟用推播通知以進行本機測試][啟用推播通知以進行本機測試]
11. [對已發佈的行動服務進行應用程式測試][對已發佈的行動服務進行應用程式測試]

本教學課程需要下列各項：

-   [行動服務 iOS SDK][行動服務 iOS SDK]
-   [XCode 4.5][XCode 4.5]
-   iOS 6.0 (或以上版本) 功能裝置
-   iOS Developer Program 成員資格

> [WACOM.NOTE] 基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務][開始使用行動服務]。

[WACOM.INCLUDE [啟用 Apple 推播通知](../includes/enable-apple-push-notifications.md)]

## 設定行動服務傳送推播要求

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a name="download-the-service"></a><span class="short-header">下載服務</span>將服務下載至您的本機電腦

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">測試服務</span>測試行動服務

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a>更新伺服器以傳送推播通知

1.  在 Visual Studio 的 [方案總管] 中，展開行動服務專案中的 **Controllers** 資料夾。開啟 TodoItemController.cs。在檔案的頂端，新增下列 `using` 陳述式：

        using System;
        using System.Collections.Generic;

2.  以下列程式碼取代 `PostTodoItem` 方法：

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

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

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 將推播通知新增至應用程式

1.  在 QSAppDelegate.m 中，取代實作內的下列處理常式方法：

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5.  在 QSTodoListViewController.m 中，匯入 QSAppDelegate.h 檔，以便使用委派來取得裝置權杖：

        #import "QSAppDelegate.h"

6.  在 QSTodoListViewController.m 中，找出下列一行，修改 **(IBAction)onAdd** 動作：

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

使用下列程式碼來取代此項：

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

這將新增 **QSAppDelegate** 的參考來取得裝置權杖，然後修改要求裝載來包含該裝置權杖。

> [WACOM.NOTE] 您必須先新增此程式碼，再呼叫 **addItem** 方法。

您的應用程式現在已更新為支援推播通知。

## <span id="local-testing"></span></a> 啟用推播通知以進行本機測試

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## 在應用程式中測試推播通知

1.  按 [執行] 按鈕以組建專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [確定] 以接受推播通知。

    ![][0]

    > [WACOM.NOTE] 您必須明確接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

2.  在應用程式中，輸入有意義的文字，如 *A new Mobile Services task*，然後按一下加號 ([+]) 圖示。

    ![][1]

3.  確認您已接收到通知，然後按一下 [確定] 以關閉通知。

    ![][2]

4.  重複步驟 2 並立即關閉應用程式，接著確認以下推播可顯示。

    ![][3]

您已成功完成此教學課程。

## 後續步驟

本教學課程說明了啟用 iOS 應用程式，以使用行動服務和通知中心傳送推播通知的基本概念。接著，建議您完成下一個教學課程[將推播通知傳送給已驗證的使用者][將推播通知傳送給已驗證的使用者]，以了解如何使用標記將推播通知從行動服務傳送給已驗證的使用者。

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

在下列主題中深入了解行動服務和通知中心：

-   [開始使用資料][開始使用資料]
    深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證][開始使用驗證]
    了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

-   [什麼是通知中心？][什麼是通知中心？]
    深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

<!-- Anchors.  --> <!-- Images. --> <!-- URLs. -->

  [產生憑證簽署要求]: #certificates
  [註冊您的應用程式並啟用推播通知]: #register
  [建立應用程式的佈建設定檔]: #profile
  [在本機下載服務]: #download-the-service-locally
  [測試行動服務]: #test-the-service
  [更新伺服器以傳送推播通知]: #update-server
  [將行動服務發佈至 Azure]: #publish-mobile-service
  [新增推播通知至應用程式]: #add-push
  [更新指令碼來傳送推播通知]: #update-scripts
  [啟用推播通知以進行本機測試]: #local-testing
  [對已發佈的行動服務進行應用程式測試]: #test-app
  [行動服務 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  [啟用 Apple 推播通知]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [將推播通知傳送給已驗證的使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/
