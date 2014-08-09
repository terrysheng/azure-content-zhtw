本節將以兩種不同方式示範傳送通知的方式：

* [從主控台應用程式](#console)
* [從行動服務](#mobile-services)

兩者的後端會傳送通知給 Windows 市集和 iOS 裝置。您可以使用[通知中心 REST 介面][1]，從任何後端傳送通知。

<h3><a name="console"></a>若要從 C# 中的主控台應用程式傳送通知</h3>


如果您在完成[開始使用通知中心](/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)時建立了主控台應用程式，則請略過步驟
1-3。

1.  在 Visual Studio 中建立新的 Visual C# 主控台應用程式：
    
   	![][13]

2.  在 Visual Studio 主功能表中，依序按一下 **工具**、**Library Package Manager** 和 **Package Manager Console**，然後在主控台視窗中輸入下列資訊並按 **Enter** 鍵：
    
         Install-Package WindowsAzure.ServiceBus
    
    透過使用 [WindowsAzure.ServiceBus NuGet 封裝][2]，將在 Azure 服務匯流排 SDK 中新增參照。

3.  開啟檔案 Program.cs，並新增下列 `using` 陳述式：
    
         using Microsoft.ServiceBus.Notifications;

4.  在 `Program` 類別中，新增或取代 (如果方法已存在) 下列方法：
    
         private static async void SendNotificationAsync()
         {
        	// 定義通知中心。
        	NotificationHubClient hub = 
        			 NotificationHubClient.CreateClientFromConnectionString(
        "<connection  string with full access>", "<hub  name>");
        	
        	// 建立重大消息類別的陣列。
        	var categories = new string[] { "World", "Politics", "Business", 
        		"Technology", "Science", "Sports"};
        	
        foreach (var category in categories)
             {
        		try
                 {
        	// 定義 Windows 市集快顯通知。
       		var wnsToast = "<toast><visual><binding  template=\"ToastText01\">" 
        		+ "<text  id=\"1\">Breaking " + category + " News!" 
        		+ "</text></binding></visual></toast>";
        	// 使用此範本傳送 WNS 通知。            
        	await hub.SendWindowsNativeNotificationAsync(wnsToast, category);
        	// 定義 Windows Phone 快顯通知。
        	var mpnsToast =
        		"<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
        		"<wp :Notification xmlns:wp=\"WPNotification\">" +
        		"<wp :Toast>" +
        		"<wp :Text1>Breaking " + category + " News!</wp:Text1>" +
        		"</wp:Toast> " +
        		"</wp:Notification>";
        	// 使用此範本傳送 MPNS 通知。            
        	await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);
        	// 定義 iOS 警示。
        	var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
        	// 使用此範本傳送 APNS 通知。
        	await hub.SendAppleNativeNotificationAsync(alert, category);
                 }
        		catch (ArgumentException)
                 {
        	// 當通知中心尚未在 iOS、Windows 市集或 Windows Phone 平台上註冊時， 
       	 	// 便會引發例外狀況。 
                 }
             }
          }
    
    此程式碼會將字串陣列中每個標籤 (共六個) 的通知傳送給 Windows 市集、Windows Phone 和 iOS 裝置。使用標籤可確保裝置只會收到已登錄類別的通知。
	<div class="dev-callout"><strong>注意</strong> 
	<p>此後端程式碼支援 Windows 市集、Windows Phone 和 iOS 用戶端。當尚未針對特定用戶端平台設定通知中心時，傳送方法會傳回錯誤回應。 </p>
	</div>


5.	在上述程式碼中，請使用您的通知中心名稱及先前取得的 *DefaultFullSharedAccessSignature* 連線字串，來取代 `<hub  name>` 和 `<connection string with full access>` 預留位置。

6.  在 **主要** 方法中新增下列命令列。
    
          SendNotificationAsync();
        Console.ReadLine();

您現在可以繼續進行[執行應用程式並產生通知](#test-app)。

### <a name="mobile-services"></a>若要從行動服務傳送通知

若要使用行動服務傳送通知，請執行下列動作：

1.  完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started/#create-new-service)教學課程，以建立您的行動服務。

2.  登入 [Azure 管理入口網站][3]，按一下 [行動服務]，然後按一下您的行動服務。

3.  按一下 **排程器]** 索引標籤，然後按一下 **[建立**。
    
   	![][15]

4.  在 **建立新工作]** 中輸入名稱、選取 **[隨選**，然後按一下核取以接受。
    
   	![][16]

5.  在工作建立之後，按一下此工作名稱，然後在 **指令碼** 索引標籤中，將下列指令碼插入排定工作函數內：
    
        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService(
        	'<hub  name>', 
        	'<connection  string with full access>');
       	 // 建立重大消息類別的陣列。
        	var categories = ['World', 'Politics', 'Business', 'Technology', 'Science', 'Sports'];
        	for (var i = 0; i < categories.length; i++) {
        		// 傳送 WNS 通知。	notificationHubService.wns.sendToastText01(categories[i], {
        		text1:'Breaking ' + categories[i] + ' News!'
        	}, sendComplete);
       	 // 傳送 MPNS 通知。
        notificationHubService.mpns.sendToast(categories[i], {
        	text1:'Breaking ' + categories[i] + ' News!'
        	}, sendComplete);
        	// 傳送 APNS 通知。
       	 notificationHubService.apns.send(categories[i], {
       	 警示：'Breaking ' + categories[i] + ' News!'
        	}, sendComplete);
        }
    
    此程式碼會將字串陣列中每個標籤 (共六個) 的通知傳送給 Windows 市集、Windows Phone 和 iOS 裝置。使用標籤可確保裝置只會收到已登錄類別的通知。

6.  在上述程式碼中，請使用您的通知中心名稱及先前取得的 *DefaultFullSharedAccessSignature* 連線字串，來取代 `<hub  name>` 和 `<connection string with full access>` 預留位置。

7.  在排定工作函數之後新增下列協助程式函數，然後按一下 **儲存**：
    
         function sendComplete(error) {
    
	    if (error) \{
                 // 當沒有裝置在 iOS、Windows 市集或 Windows Phone 平台上註冊時， 
        		// 便會引發例外狀況。請考慮改用範本 
        		// 通知。
        		//console.warn("Notification failed:"+ error);
             }
         }
<div class="dev-callout"><strong>注意</strong> 
<p>此程式碼支援 Windows 市集、Windows Phone 和 iOS 用戶端。當某個特定平台的註冊不存在時，傳送方法會傳回錯誤回應。若要避免此狀況，請考慮使用範本註冊來將單一通知傳送給多個平台。如需範例，請參閱<a href="/en-us/manage/services/notification-hubs/breaking-news-localized-dotnet/">使用通知中心來廣播已當地語系化的重大消息</a>。 </p>
</div>



您現在可以繼續進行[執行應用程式並產生通知](#test-app)。

<!-- Anchors -->

<!-- Images. -->

<!-- URLs. -->



[1]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn223264.aspx
[2]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[3]: https://manage.windowsazure.com/


<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Use Notification Hubs to send notifications to users]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/zh-tw/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/zh-tw/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn223264.aspx