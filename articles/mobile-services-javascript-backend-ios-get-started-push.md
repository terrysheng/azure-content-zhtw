<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="開始使用推播通知 (iOS) | 行動開發人員中心" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 將推播通知新增至行動服務應用程式

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

本主題說明如何透過 Apple 推播通知服務 (APNS)，使用 Azure 行動服務將推播通知傳送至 iOS 應用程式。在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至[快速入門專案]的功能(http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-ios-get-started/)。完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [產生憑證簽署要求](#certificates)
2. [註冊您的應用程式並啟用推播通知](#register)
3. [建立應用程式的佈建設定檔](#profile)
4. [設定行動服務](#configure)
5. [新增推播通知至應用程式](#add-push)
6. [更新指令碼來傳送推播通知](#update-scripts)
7. [插入資料以接收通知](#test)

本教學課程需要下列各項：

+ [行動服務 iOS SDK]
+ [XCode 4.5][Install Xcode]
+ iOS 6.0 (或以上版本) 功能裝置
+ iOS Developer Program 成員資格

   > [WACOM.NOTE] 基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須先完成[開始使用行動服務]。


[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>設定行動服務傳送推播要求

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>在管理入口網站中更新已註冊的插入指令碼

1. 在管理入口網站中，按一下 [**資料**] 索引標籤，然後按一下 [**TodoItem**] 資料表。

   	![][21]

2. 在 [**todoitem**] 中，按一下 [**指令碼**] 索引標籤，然後選取 [**插入**]。

  	![][22]

   	這會顯示 [**TodoItem**] 資料表中發生插入時所叫用的函數。

3. 使用下列程式碼來取代 insert 函數，然後按一下 [**儲存**]：

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

   	這會註冊新的 insert 指令碼，它會使用 [apns 物件]將推播通知 (插入的文字) 傳送至插入要求中提供的裝置。


   	> [WACOM.NOTE] 此指令碼會延遲通知的傳送，讓您有時間關閉應用程式來接收推播通知。

## <a id="add-push"></a>將推播通知新增至應用程式

1. 在 QSAppDelegate.m 中，插入下列程式碼片段來匯入行動服務 iOS SDK：

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. 在 QSAppDelegate.m 中，取代實作內的下列處理常式方法：

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. 在 QSAppDelegate.m 中，新增實作內的下列處理常式方法。確定複製行動服務 URL 和應用程式金鑰值，並在預留位置中切換它們：

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
			MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"];

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. 在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. 在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：  

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

您的應用程式現在已更新為支援推播通知。

## <a id="test"></a>在應用程式中測試推播通知

1. 按 [**執行**] 按鈕以建置專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [**確定**] 以接受推播通知

  	![][23]

    > [WACOM.NOTE] 您必須明確地接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中，輸入有意義的文字，如 _A new Mobile Services task_，然後按一下加號 ([**+**]) 圖示。

  	![][24]

3. 確認您已接收到通知，然後按一下 [**確定**] 以關閉通知。

  	![][25]

4. 重複執行步驟 2 並立即關閉應用程式，接著確認以下推播會出現。

  	![][26]

您已成功完成此教學課程。

## <a id="next-steps"></a>後續步驟

本教學課程說明了啟用 iOS 應用程式，以使用行動服務和通知中心傳送推播通知的基本概念。接著，請考慮完成下列其中一個教學課程：

+ [傳送推播通知給已驗證的使用者]
	<br/>了解如何利用標籤，從行動服務將推播通知只傳送給驗證的使用者。

+ [將廣播通知傳送給訂閱者]
	<br/>了解使用者如何註冊及接收其所需類別的推播通知。
<!---
+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->
在下列主題中深入了解行動服務和通知中心：

* [開始使用資料]
  <br/>深入了解如何使用行動服務儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中心？]
  <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵測通知中心應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>取得指引疑難排解和偵測通知中心解決方案。 

* [行動服務 Objective-C 作法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 Objective-C 和 iOS。

* [行動服務伺服器指令碼參考]
  <br/>深入了解如何在您的行動服務中實作商務邏輯。

<!-- Anchors. -->


<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs.   -->
[安裝 Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS 佈建入口網站]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[行動服務 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple 推播通知服務]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-ios-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-ios-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-ios-get-started-users
[Azure 管理入口網站]: https://manage.windowsazure.com/
[apns 物件]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293

[傳送推播通知給已驗證的使用者]: /zh-tw/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/

[什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/
[將廣播通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-ios-send-breaking-news/
[將範本型通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-ios-send-localized-breaking-news/

[行動服務 Objective-C 作法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library

<!--HONumber=35.1-->
