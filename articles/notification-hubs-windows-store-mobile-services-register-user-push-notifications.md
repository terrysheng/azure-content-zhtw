<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="" solutions="" manager="" editor="" />

使用行動服務註冊目前使用者以取得推播通知
========================================

[Windows 市集 C\#](/en-us/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "Windows 市集 C#")[iOS](/en-us/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS")

本主題將說明以 Azure 行動服務執行註冊時，應如何向 Azure 通知中心要求推播通知註冊。這是[使用通知中心來通知使用者](/en-us/manage/services/notification-hubs/notify-users)教學課程的延伸主題。您必須已完成該教學課程中的必要步驟，才能建立已驗證的行動服務。如需通知使用者案例的詳細資訊，請參閱[使用通知中心來通知使用者](/en-us/manage/services/notification-hubs/notify-users)。

1.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成必要的教學課程[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)時所建立的專案。

2.  在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 **[市集]** 和 **[將應用程式與市集建立關聯...]**。

	![](./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png) 

	這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

1.  在此精靈中，按一下 **[登入]**，然後使用您的 Microsoft 帳戶登入。

2.  選取您在[使用通知中心來通知使用者](/en-us/manage/services/notification-hubs/notify-users)中註冊的應用程式，按 **[下一步]**，然後按一下 **[關聯]**。

	![][2]

	This adds the required Windows Store registration information to the application manifest.  

    **注意**

    這會在此行動服務應用程式中，使用通知中心教學課程應用程式中的 Windows 市集註冊。這可能會使通知中心教學課程應用程式無法接收通知。

3.  在 [方案總管] 中按兩下 Package.appxmanifest 專案檔案，在 Visual Studio 編輯器中加以開啟。

4.  向下捲動到 **[所有影像資產]**，並按一下 **[徽章標誌]**。在 **[通知]** 中，將 **[支援快顯通知]** 設定為 **[是]**：

 	![][3]

    這會使這個行動服務教學課程應用程式能夠接收快顯通知。

5.  在 Visual Studio 中開啟 MainPage.xaml.cs 檔案，然後新增下列程式碼以定義 **NotificationRequest** 和 **RegistrationResult** 類別：

         public class NotificationRequest
         {
        public string channelUri { get; set; }
        public string platform { get; set; }
         }

        public class RegistrationResult
         {
        public string RegistrationId { get; set; }
        public string ExpirationTime { get; set; }
         }

    這些類別分別會包含呼叫自訂 API 時所傳回的要求內文和註冊 ID。

6.  在 **MainPage** 類別中，新增下列方法：

         private async System.Threading.Tasks.Task RegisterNotification()
         {
        string message;

        // Get a channel for push notifications.
        var channel =
        await Windows.Networking.PushNotifications
        .PushNotificationChannelManager
        .CreatePushNotificationChannelForApplicationAsync();

        // Create the body of the request.
        var body = new NotificationRequest 
             {
        channelUri = channel.Uri, 
        platform = "win8" 
             }; 

        try
             {
        // Call the custom API POST method with the supplied body.
        var result = await App.MobileService
        .InvokeApiAsync<NotificationRequest, 
        RegistrationResult>("register_notifications", body,
        System.Net.Http.HttpMethod.Post, null);

        // Set the response, which is the ID of the registration.
        message = string.Format("Registration ID:{0}", result.RegistrationId);
             }
        catch (MobileServiceInvalidOperationException ex)
             {
        message = ex.Message;
             }

        // Display a message dialog.
        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
         }

    此方法會建立推播通知的通道，並將其連同裝置類型傳送至會在通知中心建立註冊的自訂 API 方法。此自訂 API 定義於[使用通知中心來通知使用者](/en-us/manage/services/notification-hubs/notify-users)中。

7.  在呼叫 **Authenticate** 方法後，隨即將下列程式碼行新增至 **OnNavigatedTo** 方法中：

         await RegisterNotification();

    **注意**

    這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。

現在，用戶端應用程式已更新，請回到[使用通知中心來通知使用者](/en-us/manage/services/notification-hubs/notify-users)，並更新行動服務，以使用通知中心傳送通知。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
[2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
[3]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png

<!-- URLs. -->
[Notify users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/