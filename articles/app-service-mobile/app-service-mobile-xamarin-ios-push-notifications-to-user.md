<properties 
	pageTitle="使用 Xamarin iOS 用戶端將推播通知傳送至特定使用者";" 
	description="description=";了解如何將推播通知傳送至使用者的所有裝置";" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/01/2015"
	ms.author="yuaxu"/>

# 傳送跨平台通知給特定使用者

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何從您的行動後端，將通知傳送給特定使用者的所有註冊裝置。其中將介紹[範本]；範本可讓用戶端應用程式在註冊時能夠指定裝載格式和變數預留位置。從伺服器傳送範本通知時，通知中樞會將其導向到每個具有這些預留位置的平台，啟用跨平台通知。

> [AZURE.NOTE]若要啟用跨平台用戶端的推播功能，您必須先為要啟用的每個平台完成本教學課程。您只需要為共用相同行動後端的用戶端執行一次[行動後端更新](#backend)。
 
##必要條件 

在開始本教學課程之前，您必須已為您要啟用的每個用戶端平台完成下列應用程式服務教學課程：

+ [開始使用驗證]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [開始使用推播通知]<br/>設定推播通知的 TodoList 範例應用程式。

##<a name="client"></a>更新您的用戶端，以註冊處理跨平台推播所需的範本

1. 將 APN 註冊程式碼片段從 **AppDelegate.cs** 中的 **FinishedLaunching** 移至 **QSTodoListViewController.cs** 中的 **RefreshAsync** 工作定義。驗證完成後，應該就會進行註冊。

        ...
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound, 
                new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        ...



##<a name="backend"></a>更新您的服務後端以傳送通知給特定使用者

1. 在 Visual Studio 中，使用下列程式碼更新 `PostTodoItem` 方法定義：  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create given user tag
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            // Sending the message so that all template registrations that contain "messageParam"
            // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string,string> templateParams = new Dictionary<string,string>();
            templateParams["messageParam"] = item.Text + " was added to the list.";

            try
            {
                await Hub.SendTemplateNotificationAsync(templateParams, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>測試應用程式

重新發佈您的行動後端專案，並執行任何已設定的用戶端應用程式。在插入項目時，後端將會傳送通知給使用者已登入的所有用戶端應用程式。

<!-- URLs. -->
[開始使用驗證]: app-service-mobile-xamarin-ios-get-started-users.md
[開始使用推播通知]: app-service-mobile-xamarin-ios-get-started-push.md
[範本]: ../notification-hubs/notification-hubs-templates.md

<!---HONumber=AcomDC_1203_2015-->