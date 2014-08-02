<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notify Users xplat aspnet" pageTitle="Send cross-platform notifications to users with Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

使用通知中心傳送跨平台通知給使用者
==================================

[行動服務](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "行動服務")[ASP.NET](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

在先前的教學課程[使用通知中心來通知使用者](/en-us/manage/services/notification-hubs/notify-users-aspnet)中，您已了解如何將通知推播至已驗證的特定使用者所註冊的所有裝置。在該教學課程中，我們必須透過多個要求，將通知傳送至每個支援的用戶端平台。通知中心所支援的範本，可讓您指定某個裝置要以何種方式接收通知。這可以簡化傳送跨平台通知的作業。本主題將示範如何運用範本，以單一要求傳送以所有平台為目標、且平台無從驗證的通知。如需範本的詳細資訊，請參閱 [Azure 通知中心概觀](http://go.microsoft.com/fwlink/p/?LinkId=317339)。

**注意**

通知中心可讓裝置以相同的標籤註冊多個範本。在此情況下，一個以該標籤為目標的傳入訊息將會使多個通知傳遞至裝置，每個範本各一個。這可讓您以多個視覺化通知顯示相同的訊息，例如，在一個 Windows 市集應用程式中同時顯示徽章和快顯通知。

完成下列步驟，可使用範本傳送跨平台通知：

1.  在 Visual Studio 的 [方案總管] 中展開 **[控制器]** 資料夾，然後開啟 RegisterController.cs 檔案。

2.  在 **Post** 方法中找出在 `updated` 的值為 **false** 時建立新註冊的程式碼區塊，並將其取代為下列程式碼：

		if (!updated)
        {
            switch (platform)
            {
                case "windows":
                    var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                    await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });
                    break;
                case "ios":
                    template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";
                    await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });
                    break;
            } 
        }

    此程式碼會呼叫平台特定方法以建立範本註冊，而非原生註冊。現有的註冊無須修改，因為範本註冊衍生自原生註冊。

3.  以下列程式碼取代 **sendNotification** 方法：

        // Send a cross-plaform notification by using templates. 
        private async Task sendNotification(string notificationText, string tag)
        {           
                var notification = new Dictionary<string, string> { { "message", "Hello, " + tag } };
                await hubClient.SendTemplateNotificationAsync(notification, tag);        
        }

    此程式碼會同時傳送通知至所有平台，而無須指定原生裝載。通知中心會使用提供的*標籤*值建置及傳遞正確的裝載給每個裝置，如註冊的範本中所指定。

4.  重新執行用戶端應用程式，然後驗證註冊已成功。

5.  (選用) 將此用戶端應用程式部署到第二個裝置，然後執行此應用程式。

    請留意到，每個裝置上都會顯示通知。

後續步驟
--------

現在您已完成此教學課程，您可以進一步在下列主題中了解通知中心和範本的相關資訊：

-   **使用通知中心傳送即時新聞 ([Windows 市集 C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-dotnet))**
    說明另一個使用範例的案例

-   **[Azure 通知中心概觀](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
    「概觀」主題會提供範本的詳細資訊。

-   **[Windows 市集的通知中心作法](http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx)**
    包含範本運算式語言參考。


