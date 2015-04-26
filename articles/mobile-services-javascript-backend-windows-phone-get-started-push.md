<properties 
	pageTitle="開始使用推播通知 (Windows 市集) | 行動開發人員中心" 
	description="了解如何使用 Azure 行動服務及通知中心傳送推播通知至通用 Windows 市集應用程式。" 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>


# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

本主題說明如何使用 Azure 行動服務將推播通知傳送至 Windows Phone Silverlight 應用程式。在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至快速入門專案的功能。完成後，您的行動服務就會在每次插入記錄時使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [更新應用程式以註冊通知](#update-app)
2. [更新伺服器指令碼以傳送推播通知](#update-scripts)
3. [插入資料以接收推播通知](#test)

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務]或[開始使用資料]，將您的專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈會為您建立此連線。 

>[AZURE.NOTE]若要將推播通知傳送至 Windows Phone 8.1 市集應用程式，請遵循本教學課程的 [Windows 市集應用程式](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) 版本。

##<a id="update-app"></a>更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1. 在 Visual Studio 中，開啟 App.xaml.cs 檔案，並新增下列  `using` 陳述式：

        using Microsoft.Phone.Notification;

3. 新增下列程式碼至 App.xaml.cs：
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                    // Register for notifications using the new channel
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    此程式碼會從 Windows Phone 8.x "Silverlight" 使用的 Microsoft 推播通知服務 (MPNS)，擷取適用於應用程式的 ChannelURI，然後註冊該 ChannelURI，以進行推播通知。

	>[AZURE.NOTE]在本教學課程中，行動服務會傳送快顯通知給裝置。傳送磚通知時，您必須在通道上改為呼叫 **BindToShellTile** 方法。

4. 在 App.xaml.cs 中的 **Application_Launching** 事件處理常式頂端，將下列呼叫新增至新的 **AcquirePushChannel** 方法：

        AcquirePushChannel();

	這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。 

5. 按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。
  
6.	在 [方案總管] 中，展開 [**屬性**]，開啟 WMAppManifest.xml 檔案，然後按一下 [**功能**] 索引標籤，並確定已核取 [**ID___CAP___PUSH_NOTIFICATION**] 功能。

   	![][1]

   	如此可確定您的應用程式可以提出快顯通知。 

##<a id="update-scripts"></a>更新伺服器指令碼以傳送推播通知

最後，您必須在 TodoItem 資料表上更新對插入作業註冊的指令碼，以傳送通知。

1. 按一下 [**TodoItem**]，按一下 [**指令碼**]，然後選取 [**插入**]。 

   	![][10]

2. 使用下列程式碼來取代 insert 函數，然後按一下 [**儲存**]：

		function insert(item, user, request) {
		// Define a payload for the Windows Phone toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
		    '</wp:Text2></wp:Toast></wp:Notification>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.mpns.send(null, payload, 'toast', 22, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });      
		}

	這個 insert 指令碼會在插入成功後，傳送推播通知 (含所插入項目的文字) 給 Windows Phone 應用程式註冊。

3. 按一下 [**推播**] 索引標籤，核取 [**Enable unauthenticated push notifications**]，然後按一下 [**儲存**]。

	>[AZURE.NOTE]當您使用舊版行動服務完成本教學課程時，可能會在 [**推播**] 索引標籤底部看見名為 [**啟用進階推播**] 的連結。立即按一下這個連結，即可升級您的行動服務來與通知中心整合。這個變更無法回復。如需如何在生產行動服務中啟用進階推播通知的詳細資料，請參閱<a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">此指引</a>。

	![][11]

	這會使行動服務以未經驗證的模式連接到 MPNS，以傳送推播通知。

	>[AZURE.NOTE]本教學課程使用處於未通過驗證模式的 MPNS。在此模式中，MPNS 會限制可傳送至裝置通道的通知數。若要移除此限制，您必須按一下 [**上傳**] 並選取憑證，以產生憑證並加以上傳。如需產生憑證的詳細資訊，請參閱[設定已驗證的 Web 服務以傳送 Windows Phone 的推播通知]。

##<a id="test"></a>在應用程式中測試推播通知

1. 在 Visual Studio 中，按 F5 鍵以執行此應用程式。

    >[AZURE.NOTE] 在 Windows Phone 模擬器上測試時，可能會發生 401 未授權的 RegistrationAuthorizationException。因為 Windows Phone 模擬器將其時鐘與主機電腦同步的方式，可能會在  `RegisterNativeAsync()` 呼叫期間發生這種情形。這樣可能會產生將被拒絕的安全性權杖。若要解決這個問題，只要在測試之前，手動設定模擬器中的時鐘即可。

5. 在應用程式的文字方塊中，輸入文字 "hello push"，按一下 [**儲存**]，然後立即按一下啟動按鈕或返回按鈕，以離開應用程式。

   	![][4]

  	如此會傳送插入要求給行動服務來儲存新增的項目。請注意，裝置會接收到顯示 **hello push** 的快顯通知。

	![][5]

	>[AZURE.NOTE]如果您仍在應用程式中，就不會收到該通知。若要在應用程式作用中的情況下接收快顯通知，您必須處理 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx) 事件。



## <a name="next-steps"> </a>後續步驟

本教學課程示範如何啟用 Windows 市集應用程式以便使用行動服務與通知中心來傳送推播通知的基礎。接著，請考慮完成下列其中一個教學課程：

+ [傳送推播通知給已驗證的使用者]
	<br/>了解如何利用標籤，從行動服務將推播通知只傳送給驗證的使用者。

+ [將廣播通知傳送給訂閱者]
	<br/>了解使用者如何註冊及接收其所需類別的推播通知。

<!---+ [將範本型通知傳送給訂閱者]
	<br/>了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。
-->
在下列主題中深入了解行動服務和通知中心：

* [開始使用資料]
  <br/>深入了解如何使用行動服務儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中心？]
  <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [行動服務 .NET 做法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET

* [行動服務伺服器指令碼參考]
  <br/>深入了解如何在您的行動服務中實作商務邏輯。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users

[設定已驗證的 Web 服務以傳送 Windows Phone 的推播通知]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[行動服務 .NET 做法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[傳送推播通知給已驗證的使用者]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/

[什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/
[將廣播通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[將範本型通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/



<!--HONumber=42-->
