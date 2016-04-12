
本節說明如何從 .NET 主控台應用程式將即時新聞以加註標記的範本通知形式傳送。

如果您使用 Mobile Apps，請參閱[新增 Mobile Apps 的推播通知](../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)教學課程，在頂端選取您的平台。

如果您想要使用 Java 或 PHP，請參閱[如何從 Java/PHP 使用通知中樞](../articles/notification-hubs/notification-hubs-java-backend-how-to.md)。您可以使用[通知中樞 REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)，從任何後端傳送通知。

如果您在完成[開始使用通知中樞][get-started]時建立了傳送通知用的主控台應用程式，則請略過步驟 1-3。

1. 在 Visual Studio 中建立新的 Visual C# 主控台應用程式： 

   	![][13]

2. 在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]** 和 **[Package Manager Console]**，然後在主控台視窗中輸入下列資訊並按 **Enter** 鍵：

        Install-Package Microsoft.Azure.NotificationHubs
 	
	這會使用 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet 封裝</a> 加入對 Azure 通知中樞 SDK 的參考。

3. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Azure.NotificationHubs;

4. 在 `Program` 類別中，新增或取代 (如果方法已存在) 下列方法：

        private static async void SendTemplateNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
											"Technology", "Science", "Sports"};

            // Sending the notification as a template notification. All template registrations that contain 
			// "messageParam" and the proper tags will receive the notifications. 
			// This includes APNS, GCM, WNS, and MPNS template registrations.

            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";            
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
		 }

	此程式碼會分別將範本通知傳送給字串陣列中的六個標籤。使用標籤可確保裝置只會收到已登錄類別的通知。

6. 在上述程式碼中，請使用您的通知中樞名稱及通知中樞儀表板的 *DefaultFullSharedAccessSignature* 連接字串，來取代 `<hub name>` 和 `<connection string with full access>` 預留位置。

7. 在 **[主要]** 方法中新增下列命令列。

         SendTemplateNotificationAsync();
		 Console.ReadLine();

8. 建置主控台應用程式。

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!---HONumber=AcomDC_0330_2016-->