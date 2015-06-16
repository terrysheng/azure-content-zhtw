<properties 
	pageTitle="使用 Azure 應用程式服務將推播通知新增至 iOS 應用程式" 
	description="了解如何使用 Azure 應用程式服務傳送推播通知至 iOS 應用程式。" 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="ysxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# 將推播通知新增至您的 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

本主題說明如何使用 Azure 應用程式服務，透過 Apple 推播通知服務 (APNS) 將推播通知傳送至 iOS 應用程式。在完成時，.NET 後端將會在每次有記錄插入時，將推播通知傳送至您的快速入門 ToDo 應用程式。此應用程式與 iOS 8 和舊版相容。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [產生 iOS 憑證簽署要求]
2. [註冊您的應用程式並啟用推播通知]
3. [建立應用程式的佈建設定檔]
4. [設定您的行動後端以傳送推播要求]
5. [更新伺服器以傳送推播通知](#update-server)
6. [將行動後端發佈至 Azure]
7. [新增推播通知至應用程式]
8. [測試應用程式]

本教學課程需要下列各項：

+ [Azure 行動應用程式 iOS SDK]
+ [Azure 通知中樞 Nuget]
+ [XCode 6.0][Install Xcode]
+ iOS 6.0 (或以上版本) 功能裝置
+ iOS Developer Program 成員資格

   >[AZURE.NOTE]基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

本教學課程以應用程式服務行動應用程式快速入門為基礎。在開始本教學課程之前，您必須先完成[開始使用應用程式服務行動應用程式]。

[AZURE.INCLUDE [啟用 Apple 推播通知](../../includes/enable-apple-push-notifications.md)]

## 設定行動應用程式以傳送推播要求

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

## <a id="update-server"></a>更新伺服器以傳送推播通知

1. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後按一下 [管理 NuGet 封裝]****。

2. 搜尋 **Microsoft.Azure.NotificationHubs**，然後為方案中的所有專案按一下 [**安裝**]。

3. 在 Visual Studio 的 [方案總管] 中，展開行動後端專案中的 **Controllers** 資料夾。開啟 TodoItemController.cs。在檔案頂端新增下列 `using` 陳述式：

		using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. 將下列程式碼片段新增至 **InsertAsync** 呼叫後面的 `PostTodoItem` 方法：

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    此程式碼會指示與此行動應用程式相關聯的通知中心，在 todo 項目插入後傳送推播通知。


## <a name="publish-the-service"></a>將行動後端發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## 將推播通知新增至應用程式
1. 在 xcode 中下載應用程式服務行動應用程式用戶端 SDK，並新增其參考。

2. 在 **QSAppDelegate.m** 中，將下列內容新增至 **application:didFinishLaunchingWithOptions**，以向 Apple 推播通知服務註冊用戶端：

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && 	
        	[[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        } else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. 在相同檔案中，在 **QSAppDelegate** 實作內新增下列處理常式方法：

        // registration with APNs is successful
        - (void)application:(UIApplication *)application 
            didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. 接著，在實作內新增失敗處理常式方法：

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. 在 QSAppDelegate.m 中，新增實作內的下列處理常式方法：

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application 
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" 
                                                            message:alertString 
                                                           delegate:nil 
                                                  cancelButtonTitle:@"OK" 
                                                  otherButtonTitles:nil];
            [alert show];
        }

您的應用程式現在已更新為支援推播通知。

## 在應用程式中測試推播通知

1. 按 [執行]**** 按鈕以組建專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [確定]**** 以接受推播通知。

  	![][23]

    > [AZURE.NOTE]您必須明確地接受來自應用程式的推播通知。只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中，輸入有意義的文字 (如 _A new Mobile Services task_)，然後按一下加號 (**+**) 圖示。

  	![][24]

3. 確認您已接收到通知，然後按一下 [確定]**** 以關閉通知。

  	![][25]

4. 重複執行步驟 2 並立即關閉應用程式，接著確認以下推播會出現。

  	![][26]

您已成功完成此教學課程。

<!-- Anchors.  -->
[產生 iOS 憑證簽署要求]: #certificates
[註冊您的應用程式並啟用推播通知]: #register
[建立應用程式的佈建設定檔]: #profile
[新增推播通知至應用程式]: #add-push
[設定您的行動後端以傳送推播要求]: #configure
[Update the server to send push notifications]: #update-server
[將行動後端發佈至 Azure]: #publish-mobile-service
[測試應用程式]: #test-the-service

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
[23]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push1-ios.png
[24]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push2-ios.png
[25]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push3-ios.png
[26]: ./media/app-service-mobile-dotnet-backend-ios-get-started-push-preview/mobile-quickstart-push4-ios.png
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
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Azure 行動應用程式 iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=529823
[Azure 通知中樞 Nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=54--> 