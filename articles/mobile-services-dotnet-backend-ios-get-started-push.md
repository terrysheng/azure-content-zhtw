<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="開始使用推播通知 (iOS) | 行動開發人員中心" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />


# 將推播通知新增至行動服務應用程式

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

本主題說明如何使用 Azure 行動服務將推播通知傳送至 iOS 應用程式。在本教學課程中，您會使用 Apple 推播通知服務 (APNS) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。


本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [產生憑證簽署要求]
2. [註冊您的應用程式並啟用推播通知]
3. [建立應用程式的佈建設定檔]
4. [在本機下載服務]
5. [測試行動服務]
6. [更新伺服器以傳送推播通知](#update-server)
7. [將行動服務發佈至 Azure]
8. [新增推播通知至應用程式]
9. [更新指令碼來傳送推播通知]
10. [啟用推播通知以進行本機測試](#local-testing)
11. [對已發佈的行動服務進行應用程式測試]

本教學課程需要下列各項：

+ [行動服務 iOS SDK]
+ [XCode 4.5][安裝 Xcode]
+ iOS 6.0 (或以上版本) 功能裝置
+ iOS Developer Program 成員資格

   > [WACOM.NOTE] 基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]。


[WACOM.INCLUDE [啟用 Apple 推播通知](../includes/enable-apple-push-notifications.md)]


## 設定行動服務傳送推播要求

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

<h2><a name="download-the-service"></a>將服務下載至您的本機電腦</h2>

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

<h2><a name="test-the-service"></a>測試行動服務</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>更新伺服器以傳送推播通知

1. 在 Visual Studio 的 [方案總管] 中，展開行動服務專案中的 **Controllers** 資料夾。開啟 TodoItemController.cs。在檔案的頂端，新增下列 `using` 陳述式：


		using System;
		using System.Collections.Generic;

2. 使用下列程式碼更新 `PostTodoItem` 方法定義：  

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

    此程式碼會在插入 Todo 項目之後傳送推播通知 (含所插入項目的文字)。如果發生錯誤，程式碼將會新增可透過管理入口網站從行動服務的 [**記錄檔**] 索引標籤來檢視的錯誤記錄項目。


<h2><a name="publish-the-service"></a>將行動服務發佈至 Azure</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 將推播通知新增至應用程式

1. 在 qsappdelegate.m 中，插入下列程式碼片段來匯入行動服務 iOS SDK：

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

3. 在 QSAppDelegate.m 中，在實作內新增下列處理常式方法。請確定您複製行動服務 Url 和應用程式金鑰值，並將它們貼到預留位置：

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
            MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

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

   > [WACOM.NOTE] 您必須在呼叫 <strong>addItem</strong> 方法之前加入此程式碼。

您的應用程式現在已更新為支援推播通知。

##<a id="local-testing"></a> 啟用推播通知以進行本機測試

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## 在應用程式中測試推播通知

1. 按 [**執行**] 按鈕以建置專案，並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [**確定**] 以接受推播通知

  	![][23]

    > [WACOM.NOTE] 您必須明確接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中，輸入有意義的文字，例如 _A new Mobile Services task_，然後按一下加號 (**+**) 圖示。

  	![][24]

3. 確認您已接收到通知，然後按一下 [**確定**] 以關閉通知。

  	![][25]

4. 重複執行步驟 2 並立即關閉應用程式，接著確認以下推播會出現。

  	![][26]

您已成功完成此教學課程。

## 後續步驟

本教學課程說明了啟用 iOS 應用程式，以使用行動服務和通知中心傳送推播通知的基本概念。接下來，請考慮閱讀下個教學課程，[將推播通知傳送給驗證的使用者]，此課程說明如何使用標籤將行動服務中的推播通知指定傳送給驗證的使用者。

<!--+ [Send push notifications to authenticated users]
	<br/>了解如何使用標記從行動服務將推播通知只傳送給已驗證的使用者。

+ [將廣播通知傳送給訂閱者]
	<br/>了解使用者如何註冊及接收他們所需類別的推播通知。

+ [將範本型通知傳送給訂閱者]
	<br/>了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。
-->
在下列主題中深入了解行動服務和通知中心：

* [開始使用資料]
  <br/>進一步了解使用行動服務來儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何使用行動服務以不同帳戶類型驗證應用程式的使用者。

* [什麼是通知中心？]
  <br/>深入了解通知中樞如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵錯通知中樞應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>取得通知中樞解決方案的疑難排解和偵錯指引。 

<!-- Anchors.  -->
[產生憑證簽署要求]: #certificates
[註冊您的應用程式並啟用推播通知]: #register
[建立應用程式的佈建設定檔]: #profile
[設定行動服務]: #configure
[更新指令碼來傳送推播通知]: #update-scripts
[新增推播通知至應用程式]: #add-push
[插入資料以接收通知]: #test
[對已發佈的行動服務進行應用程式測試]: #test-app
[後續步驟]:#next-steps
[在本機下載服務]: #download-the-service-locally
[測試行動服務]: #test-the-service
[將行動服務發佈至 Azure]: #publish-mobile-service

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

<!-- URLs. -->
[安裝 Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS 佈建入口網站]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[行動服務 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple 推播通知服務]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Azure 管理入口網站]: https://manage.windowsazure.com/
[apns 物件]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users

[傳送推播通知給已驗證的使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/

[什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/
[將廣播通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-ios-send-breaking-news/
[將範本型通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-ios-send-localized-breaking-news/
