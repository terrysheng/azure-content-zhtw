<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="開始使用推播通知 (iOS) | 行動開發人員中心" metaKeywords="" description="了解如何使用 Azure 行動服務傳送推播通知至 iOS 應用程式 (舊版推播)。" metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 將推播通知新增至行動服務應用程式 (舊版推播)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows 市集 C#</a>
    <a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows 市集 JavaScript</a>
    <a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/zh-tw/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
	<a href="/zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript backend" class="current">JavaScript 後端</a></div>

本主題說明如何使用 Azure 行動服務將推播通知傳送至 iOS 應用程式。在本教學課程中，您會使用 Apple 推播通知服務 (APNS) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。


>[WACOM.NOTE]本主題支援<em>尚未升級</em>到使用通知中樞整合的<em>現有</em>行動服務。當您建立「<em>新的</em>」行動服務時，會自動啟用這項整合功能。關於新的行動服務，請參閱[開始使用推播通知](/zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/)。
>
>行動服務與 Azure 通知中樞整合，以支援其他推播通知功能，例如範本、多個平台和改善的規模。<em>您應該升級現有的行動服務，以盡可能使用通知中樞</em>。升級後，請參閱這一版的[開始使用推播通知](/zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/)。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [產生憑證簽署要求]
2. [註冊您的應用程式並啟用推播通知]
3. [建立應用程式的佈建設定檔]
3. [設定行動服務]
4. [新增推播通知至應用程式]
5. [更新指令碼來傳送推播通知]
6. [插入資料以接收通知]

本教學課程需要下列各項：

+ [行動服務 iOS SDK]
+ [Xcode 4.5][安裝 Xcode]
+ iOS 5.0 (或以上版本) 功能裝置
+ iOS Developer Program 成員資格

   > [WACOM.NOTE] 基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]。

[WACOM.INCLUDE [啟用 Apple 推播通知](../includes/enable-apple-push-notifications.md)]

## 設定行動服務傳送推播要求

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## 將推播通知新增至應用程式

1. 在 Xcode 中，開啟 QSAppDelegate.h 檔，並且在 ***window** 屬性下面新增下列屬性：

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] 當行動服務上已啟用動態結構描述，且插入的新項目含有此屬性時，**TodoItem** 資料表中就會自動新增 'deviceToken' 資料行。

2. 在 QSAppDelegate.m 中，取代實作內的下列處理常式方法：

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. 在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4. 在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5. 在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：  

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

5. 在 QSTodoListViewController.m 中，匯入 QSAppDelegate.h 檔，以便使用委派來取得裝置權杖：

        #import "QSAppDelegate.h"

6. 在 QSTodoListViewController.m 中，找出下列一行，修改 **(IBAction)onAdd** 動作：

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

   	這將新增 **QSAppDelegate** 的參考來取得裝置權杖，然後修改要求裝載來包含該裝置權杖。

   	> [WACOM.NOTE] 您必須在呼叫 <strong>addItem</strong> 方法之前加入此程式碼。

您的應用程式現在已更新為支援推播通知。

## 在管理入口網站中更新已註冊的插入指令碼

1. 在管理入口網站中，按一下 [**資料**] 索引標籤，然後按一下 [**TodoItem**] 資料表。

   	![][21]

2. 在 [**todoitem**] 中，按一下 [**指令碼**] 索引標籤，然後選取 [**Insert**]。

  	![][22]

   	這會顯示 [**TodoItem**] 資料表中發生插入時所叫用的函數。

3. 使用下列程式碼來取代 insert 函數，然後按一下 [**儲存**]：

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

   	如此即會註冊新的 insert 指令碼，該指令碼會使用 [apns 物件]將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。


   	> [WACOM.NOTE] 此指令碼會延遲通知的傳送，讓您有時間關閉應用程式來接收快顯通知。

## 在應用程式中測試推播通知

1. 按 [**執行**] 按鈕以建置專案，並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [**確定**] 以接受推播通知

  	![][23]

    > [WACOM.NOTE] 您必須明確接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中，輸入有意義的文字，例如 _A new Mobile Services task_，然後按一下加號 (**+**) 圖示。

  	![][24]

3. 確認您已接收到通知，然後按一下 [**確定**] 以關閉通知。

  	![][25]

4. 重複步驟 2 並立即關閉應用程式，接著確認以下快顯通知可顯示。

  	![][26]

您已成功完成此教學課程。

## 後續步驟

在這個簡單範例中，使用者收到推播通知，其中含有剛插入的資料。用戶端會在要求中提供 APNS 所使用的裝置權杖給行動服務。在下一個教學課程[推播通知給應用程式使用者]中，您將另行建立 Devices 資料表來儲存裝置權杖，並在發生插入動作時傳送推播通知給已儲存的所有通道。

<!-- Anchors. -->
[產生憑證簽署要求]: #certificates
[註冊您的應用程式並啟用推播通知]: #register
[建立應用程式的佈建設定檔]: #profile
[設定行動服務]: #configure
[更新指令碼來傳送推播通知]: #update-scripts
[新增推播通知至應用程式]: #add-push
[插入資料以接收通知]: #test
[後續步驟]:#next-steps

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
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-ios
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-ios
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-ios
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-ios
[推播通知給應用程式使用者]: /zh-tw/develop/mobile/tutorials/push-notifications-to-users-ios
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Azure 管理入口網站]: https://manage.windowsazure.com/
[apns 物件]: http://go.microsoft.com/fwlink/p/?LinkId=272333
