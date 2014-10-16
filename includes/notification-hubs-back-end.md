本節說明如何從 .NET 主控台和任何其他應用程式傳送通知。
如果您正在使用行動服務，請參閱[開始使用推送][]教學課程。如果您想要使用 Java 或 PHP，請參閱[如何從 Java/PHP 使用通知中樞][]。您可以使用[通知中心 REST 介面][]，從任何後端傳送通知。

下列程式碼會傳送通知給 Windows 市集、Windows Phone、iOS 和 Android 裝置。

如果您在完成[開始使用通知中心][]時建立了主控台應用程式，則請略過步驟 1-3。

1.  在 Visual Studio 中建立新的 Visual C# 主控台應用程式：

    ![][]

2.  在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]** 和 **[Package Manager Console]**，然後在主控台視窗中輸入下列資訊並按 **Enter** 鍵：

        Install-Package WindowsAzure.ServiceBus

    透過使用 [WindowsAzure.ServiceBus NuGet 封裝][]，將在 Azure 服務匯流排 SDK 中新增參照。

3.  開啟檔案 Program.cs，並新增下列`using` 陳述式：

        using Microsoft.ServiceBus.Notifications;

4.  在`Program` 類別中，新增或取代 (如果方法已存在) 下列方法：

        private static async void SendNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
                "Technology", "Science", "Sports"};

            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

                    // Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
         }

    此程式碼會將字串陣列中每個標籤 (共六個) 的通知傳送給 Windows 市集、Windows Phone 和 iOS 裝置。使用標籤可確保裝置只會收到已登錄類別的通知。

    <div class="dev-callout"><strong>注意</strong> 
<p>此後端程式碼支援 Windows 市集、Windows Phone、iOS 和 Android 用戶端。當尚未針對特定用戶端平台設定通知中心時，傳送方法會傳回錯誤回應。 </p>
</div>

5.  在上述程式碼中，將`<hub name>` and`<connection string with full access>` 預留位置以您的通知中心名稱和稍早取得的 *DefaultFullSharedAccessSignature* 連接字串取代。

6.  在 **[主要]** 方法中新增下列命令列。

         SendNotificationAsync();
         Console.ReadLine();

<!-- Anchors --> <!-- Images. --> <!-- URLs. -->

  [開始使用推送]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [如何從 Java/PHP 使用通知中樞]: /zh-tw/documentation/articles/notification-hubs-java-backend-how-to/
  [通知中心 REST 介面]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [開始使用通知中心]: /zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  []: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet 封裝]: http://nuget.org/packages/WindowsAzure.ServiceBus/
