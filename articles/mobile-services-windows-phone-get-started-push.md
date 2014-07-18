<properties  pageTitle="Get started with push notifications (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

# 開始使用行動服務中的推播通知

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows 市集 C#">Windows 市集 C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a></div>

 
<div  class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET 後端">.NET 後端</a> | <a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-push/" title="JavaScript 後端" class="current">JavaScript 後端</a>
</div>

 本主題說明如何使用 Azure 行動服務傳送推播通知至 Windows Phone 8 應用程式。在本教學課程中，您會使用 Microsoft 推播通知服務 (MPNS) 將推播通知新增至快速入門專案。完成後，您的行動服務將在每次插入記錄時傳送推播通知。

> [WACOM.NOTE]行動服務現在與 Azure
> 通知中心整合，可支援更多推播通知功能，例如範本、多平台和調整。這項整合的功能目前為預覽版。如需詳細資訊，請參閱這個版本的[開始使用推播通知](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)。

本教學課程將引導您逐步完成以下啟用推播通知的基本步驟：

1.  [建立 Registrations 資料表](#create-table)
2.  [新增推播通知至應用程式](#add-push)
3.  [更新指令碼來傳送推播通知](#update-scripts)
4.  [插入資料來接收通知](#test)

本教學課程需使用 [Visual Studio 2012 Express for Windows Phone][1] 或更新版本。

本教學課程以行動服務快速入門為基礎。在開始本教學課程之前，請務必先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-wp8)。

> [WACOM.NOTE]當您每天為每位使用者傳送超過 500
> 則訊息，您必須改為使用通知中心。如需詳細資訊，請參閱[開始使用通知中心](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)。

## <a name="create-table"></a>建立新資料表

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a><span  class="short-header">新增推播通知</span>新增推播通知至應用程式</h2>


1.  在 Visual Studio 中開啟專案檔案 MainPage.xaml.cs，新增下列程式碼來建立新的 **Registrations** 類別：
    
         public class Registrations
         {
        public string Id { get; set; }
        
        [JsonProperty(PropertyName = "handle")]
        public string Handle { get; set; }
         }
    
    Handle 屬性用來儲存通道 URI。

2.  開啟檔案 App.xaml.cs，並新增下列 using 陳述式：
    
         using Microsoft.Phone.Notification;

3.  新增下列程式碼至 App.xaml.cs：
    
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.Uri };
	       await registrationsTable.InsertAsync(registration);
        }

        
	此程式碼會取得並儲存推播通知訂閱的通道，並將該通道繫結到應用程式的預設磚。

	<div class="dev-callout"><b>注意</b>
	<p>在本教學課程中，行動服務會傳送 Flip Tile (翻轉磚) 通知給裝置。當您傳送快顯通知時，必須改在通道上呼叫 <strong>BindToShellToast</strong> 方法。若要同時支援快顯和磚通知，請同時呼叫 <strong>BindToShellTile</strong> 和  <strong>BindToShellToast</strong> </p>
	</div>


4.  在 App.xaml.cs 中的 **Application\_Launching** 事件處理常式最前面，在新的 **AcquirePushChannel** 方法中新增下列呼叫：
    
         AcquirePushChannel();
        

	如此可確保每次應用程式啟動時都會初始化 **CurrentChannel** 屬性。

5.  在方案總管中展開 **屬性**，開啟 WMAppManifest.xml 檔案，按一下 **功能** 索引標籤，確定已核取 **ID___*CAP*___PUSH_NOTIFICATION** 功能。

	![](./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)

 	如此可確保您的應用程式可以收到推播通知。

 <h2><a name="update-scripts"></a><span  class="short-header">更新 insert 指令碼</span>在管理入口網站中更新已註冊的 insert 指令碼</h2>


[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  按一下 **TodoItem**，按一下 **指令碼**，然後選取 **插入**。
    
	![][10]

2.  使用下列程式碼來取代 insert 函數，然後按一下 **儲存**：
    
	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.mpns.sendFlipTile(registration.uri, {
                        	    title: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }
    
    這個 insert 指令碼會傳送推播通知 (含所插入項目的文字) 給 **Registrations** 資料表中儲存的所有通道。

<h2><a name="test"></a><span  class="short-header">測試應用程式</span>在應用程式中測試推播通知</h2>


1.  在 Visual Studio 中，選取 **建置** 功能表上的 **部署方案**。

2.  在模擬器中，向左撥動直到看到安裝的應用程式清單，然後找到新的 **TodoList** 應用程式。

3.  點選並按住該應用程式的圖示，然後從內容功能表中選取 **釘選到開始畫面**。

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png) 

	如此會將名為 **TodoList** 的磚釘選到開始功能表。 

1.  點選名為 **TodoList** 的磚來啟動應用程式。

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png) 
1.	在應用程式中，於文字方塊輸入文字 "hello push"，然後按一下 [儲存**。
    
	![][4]

	如此會傳送插入要求給行動服務來儲存新增的項目。

1.  按 **開始** 按鈕回到開始功能表。

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png)

	請注意，應用程式收到推播通知，磚的標題現在是 **hello push**。 

## <a name="next-steps"> </a>後續步驟

本教學課程將示範行動服務所提供的基本推播通知功能。如果您的應用程式需要更進階的功能，例如傳送跨平台通知、訂閱型路由或是極大的磁碟區，請考慮在您的行動服務中使用
Azure 通知中心。如需詳細資訊，請參閱下列通知中心主題：

* [開始使用通知中心](/en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/)
  
  了解如何在 Windows 市集應用程式中運用通知中心。

* [什麼是通知中心？](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
  
  了解如何建立及推播通知給多個平台的使用者。

* [傳送通知給訂戶](/en-us/manage/services/notification-hubs/breaking-news-wp8/)
  
  了解使用者可如何註冊及接收推播他們感興趣之種類的通知。

<!--+ [Send notifications to users] 	<br />Learn how to send push notifications from a Mobile Service to specific users on any device.  + [Send cross-platform notifications to users] 	<br />Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

您不妨再深入了解下列行動服務主題：

* [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)   
  深入了解使用行動服務來儲存及查詢資料。

* [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)  
  
  了解如何以 Windows 帳戶驗證應用程式的使用者。

* [行動服務伺服器指令碼參考][2] (英文)   
  深入了解伺服器指令碼的註冊與使用。

* [行動服務 .NET
  作法概念參考](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
  
  深入了解如何搭配使用行動服務與 .NET。

<!-- Anchors. -->

[Create the Registrations table]: #create-table
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->

[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png

<!-- URLs. -->



[1]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[2]: http://go.microsoft.com/fwlink/?LinkId=262293