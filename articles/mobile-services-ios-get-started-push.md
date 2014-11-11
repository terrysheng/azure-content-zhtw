<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app (legacy push)." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# 開始在行動服務中使用推播通知 (舊版為推播)

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a>
<a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<a href="/zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET 後端">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript 後端" class="current">JavaScript 後端</a></div>

本主題說明如何使用 Azure 行動服務將推播通知傳送至 iOS 應用程式。在本教學課程中，您會使用 Apple 推播通知服務 (APNS) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

> [WACOM.NOTE]此主題支援「尚未升級」來使用通知中心整合的「現有」行動服務。當您建立「新的」行動服務時，會自動啟用這個整合的功能。如需新的行動服務，請參閱[開始使用推播通知][開始使用推播通知]。
>
> 行動服務已與 Azure 通知中心整合，以支援其他推播通知功能，例如範本、多個平台和提升的範圍。*您應該升級現有的行動服務，以盡可能使用通知中樞*。升級之後，請參閱這一版的[開始使用推播通知][開始使用推播通知]。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [產生憑證簽署要求][產生憑證簽署要求]
2.  [註冊您的應用程式並啟用推播通知][註冊您的應用程式並啟用推播通知]
3.  [建立應用程式的佈建設定檔][建立應用程式的佈建設定檔]
4.  [設定行動服務][設定行動服務]
5.  [新增推播通知至應用程式][新增推播通知至應用程式]
6.  [更新指令碼來傳送推播通知][更新指令碼來傳送推播通知]
7.  [插入資料以接收通知][插入資料以接收通知]

本教學課程需要下列各項：

-   [行動服務 iOS SDK][行動服務 iOS SDK]
-   [Xcode 4.5][Xcode 4.5]
-   iOS 5.0 (或以上版本) 功能裝置
-   iOS Developer Program 成員資格

> [WACOM.NOTE] 基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務][開始使用行動服務]。

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## 設定行動服務傳送推播要求

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## 將推播通知新增至應用程式

1.  在 Xcode 中，開啟 QSAppDelegate.h 檔，並且在 ***window** 屬性下面新增下列屬性：

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] 對於您的行動服務啟用動態結構描述時，只要插入包含此屬性的新項目，就會自動將新的「deviceToken」欄新增至 [TodoItem] 資料表。

2.  在 QSAppDelegate.m 中，取代實作內的下列處理常式方法：

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5.  在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  在 QSTodoListViewController.m 中，匯入 QSAppDelegate.h 檔，以便使用委派來取得裝置權杖：

        #import "QSAppDelegate.h"

7.  在 QSTodoListViewController.m 中，找出下列一行，修改 **(IBAction)onAdd** 動作：

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

   使用下列程式碼來取代此項：

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    這會加入對 **QSAppDelegate** 的參考以取得裝置權杖，然後再修改要求裝載，以加入該裝置權杖。

    > [WACOM.NOTE] 您必須在呼叫 **addItem** 方法之前加入此程式碼。

您的應用程式現在已更新為支援推播通知。

## 在管理入口網站中更新已註冊的插入指令碼

1.  在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

    ![][0]

2.  在 [todoitem] 中，按一下 [指令碼] 索引標籤，然後選取 [插入]。

    ![][1]

    這會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。

3.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    這會註冊新的 insert 指令碼，它會使用 [apns 物件][apns 物件]將推播通知 (插入的文字) 傳送至插入要求中提供的裝置。

    > [WACOM.NOTE] 此指令碼會延遲傳送通知，讓您有時間關閉應用程式，以接收快顯通知。

## 在應用程式中測試推播通知

1.  按 [執行] 按鈕以組建專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [確定] 以接受推播通知。

    ![][2]

    > [WACOM.NOTE] 您必須明確接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

2.  在應用程式中，輸入有意義的文字，如 *A new Mobile Services task*，然後按一下加號 ([+]) 圖示。

    ![][3]

3.  確認您已接收到通知，然後按一下 [確定] 以關閉通知。

    ![][4]

4.  重複步驟 2 並立即關閉應用程式，接著確認以下快顯通知可顯示。

    ![][5]

您已成功完成此教學課程。

## 後續步驟

在這個簡單範例中，使用者收到推播通知，其中含有剛插入的資料。用戶端會在要求中提供 APNS 所使用的裝置權杖給行動服務。在下一個教學課程[推播通知至應用程式使用者][推播通知至應用程式使用者]中，您將另行建立 Devices 資料表來儲存裝置權杖，並在發生插入動作時傳送推播通知給已儲存的所有通道。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [產生憑證簽署要求]: #certificates
  [註冊您的應用程式並啟用推播通知]: #register
  [建立應用程式的佈建設定檔]: #profile
  [設定行動服務]: #configure
  [新增推播通知至應用程式]: #add-push
  [更新指令碼來傳送推播通知]: #update-scripts
  [插入資料以接收通知]: #test
  [行動服務 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [Xcode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-ios
  [Enable Apple Push Notifications]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [apns 物件]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [推播通知至應用程式使用者]: /zh-tw/develop/mobile/tutorials/push-notifications-to-users-ios
