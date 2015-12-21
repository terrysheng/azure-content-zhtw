<properties 
	pageTitle="傳送跨平台通知給 iOS 中的特定使用者" 
	description="了解如何將推播通知傳送至特定使用者的所有裝置。"
	services="app-service\mobile,notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-dotnet" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="11/17/2015"
	ms.author="yuaxu"/>

# 傳送跨平台通知給特定使用者

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何從您的行動後端，將通知傳送給特定使用者的所有註冊裝置。其中將介紹[範本]的概念；範本可讓用戶端應用程式在註冊時能夠指定裝載格式和變數預留位置。然後，這些預留位置會傳送到每個平台，而啟用跨平台通知。

> [AZURE.NOTE]若要啟用跨平台用戶端的推播功能，您必須先為要啟用的每個平台完成本教學課程。您只需要為共用相同行動後端的用戶端執行一次[行動後端更新](#backend)。
 
##必要條件 

在開始本教學課程之前，您必須已為您要啟用的每個用戶端平台完成下列應用程式服務教學課程：

+ [開始使用驗證]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [開始使用推播通知]<br/>設定推播通知的 TodoList 範例應用程式。

##<a name="client"></a>更新您的用戶端，以註冊處理跨平台推播所需的範本

1. 將 **QSAppDelegate.m** 的 **application:didFinishLaunchingWithOptions** 中的 APN 註冊程式碼片段移至 **QSTodoListViewController.m** 中的 **loginWithProvider** 呼叫，使其在驗證完成後執行：

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. 將 **application:didRegisterForRemoteNotificationsWithDeviceToken** 中的 **registerDeviceToken** 呼叫取代為下列內容，以使用範本：

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

	註冊推播通知之前請務必驗證使用者。當驗證的使用者註冊推播通知之後，便會自動新增包含使用者識別碼的標記。

##<a name="backend"></a>更新您的服務後端以傳送通知給特定使用者

[AZURE.INCLUDE [app-service-mobile-push-notifications-to-users](../../includes/app-service-mobile-push-notifications-to-users.md)]

##<a name="test"></a>測試應用程式

重新發佈您的行動後端專案，並執行任何已設定的用戶端應用程式。在插入項目時，後端將會傳送通知給使用者已登入的所有用戶端應用程式。

<!-- URLs. -->
[開始使用驗證]: app-service-mobile-ios-get-started-users.md
[開始使用推播通知]: app-service-mobile-ios-get-started-push.md
[範本]: https://msdn.microsoft.com/library/dn530748.aspx
 

<!---HONumber=AcomDC_1210_2015--->