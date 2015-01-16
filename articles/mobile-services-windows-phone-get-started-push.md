<properties pageTitle="開始使用推播通知 (舊版推播) | 行動開發人員中心" metaKeywords="" description="了解如何使用 Azure 行動服務傳送推播通知至 Windows Phone 應用程式 (舊版推播)。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="glenga" />


# 開始在行動服務中使用推播通知 (舊版為推播)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >Windows 市集 C#</a>
    <a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows 市集 JavaScript</a>
    <a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/zh-tw/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
	<a href="/zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET backend">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript backend" class="current">JavaScript 後端</a>
</div>

本主題說明如何使用 Azure 行動服務傳送推播通知至 Windows Phone 8 應用程式。 
在本教學課程中，您會使用 Microsoft 推播通知服務 (MPNS) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

>[WACOM.NOTE]本主題支援<em>尚未升級</em>到使用通知中樞整合的<em>現有</em>行動服務。當您建立「<em>新的</em>」行動服務時，會自動啟用這項整合功能。關於新的行動服務，請參閱[開始使用推播通知](/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)。
>
>行動服務與 Azure 通知中樞整合，以支援其他推播通知功能，例如範本、多個平台和改善的規模。<em>您應該升級現有的行動服務，以盡可能使用通知中樞</em>。升級後，請參閱這一版的[開始使用推播通知](/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [建立 Registrations 資料表]
2. [新增推播通知至應用程式]
3. [更新指令碼來傳送推播通知]
4. [插入資料以接收通知]

本教學課程需使用 [Visual Studio 2012 Express for Windows Phone] 或更新版本。

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]。 

   >[WACOM.NOTE]當您每天為每位使用者傳送超過 500 則訊息，您必須改為使用通知中樞。如需詳細資訊，請參閱<a href="/zh-tw/manage/services/notification-hubs/getting-started-windows-dotnet/">開始使用通知中心</a>。

## <a name="create-table"></a>建立新資料表

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a>將推播通知新增至應用程式</h2>
		
1. 在 Visual Studio 中開啟專案檔案 MainPage.xaml.cs，新增下列程式碼來建立新的 **Registrations** 類別：

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	Handle 屬性用來儲存通道 URI。

2. 開啟檔案 App.xaml.cs，並新增下列 using 陳述式：

        using Microsoft.Phone.Notification;

3. 新增下列程式碼至 App.xaml.cs：
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
	       await registrationsTable.InsertAsync(registration);
        }

   	此程式碼會取得並儲存推播通知訂閱的通道，並將該通道繫結到應用程式的預設磚。

	<div class="dev-callout"><b>注意</b>
		<p>在本教學課程中，行動服務會傳送 Flip Tile (翻轉磚) 通知給裝置。當您傳送快顯通知時，必須改在通道上呼叫 <strong>BindToShellToast</strong> 方法。若要同時支援快顯和磚通知，請同時呼叫 <strong>BindToShellTile</strong> 和 <strong>BindToShellToast</strong> </p>
	</div>
    
4. 在 App.xaml.cs 中的 **Application_Launching** 事件處理常式頂端，將下列呼叫新增至新的 **AcquirePushChannel** 方法：

        AcquirePushChannel();

   	如此可確保每次應用程式啟動時都會初始化 **CurrentChannel** 屬性。


5.	在 [方案總管] 中，展開 [**屬性**]，開啟 WMAppManifest.xml 檔案，然後按一下 [**功能**] 索引標籤，並確定已核取 [**ID___CAP___PUSH_NOTIFICATION**] 功能。

   	![][1]

   	如此可確保您的應用程式可以收到推播通知。

<h2><a name="update-scripts"></a>在管理入口網站中更新已註冊的 insert 指令碼</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. 按一下 [**TodoItem**]，按一下 [**指令碼**]，然後選取 [**Insert**]。 

   	![][10]

3. 使用下列程式碼來取代 insert 函數，然後按一下 [**儲存**]：

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
                    	    push.mpns.sendFlipTile(registration.handle, {
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

<h2><a name="test"></a>在應用程式中測試推播通知</h2>

1. 在 Visual Studio 中，選取 [**建置**] 功能表上的 [**部署方案**]。

2. 在模擬器中，向左撥動直到看到已安裝的應用程式清單，然後找出新的 **TodoList** 應用程式。

3. 點選並按住該應用程式的圖示，然後從內容功能表中選取 [**釘選到開始畫面**]。

  	![][2]

  	如此會將名為 **TodoList** 的磚釘選到開始功能表。

4. 點選名為 **TodoList** 的磚來啟動應用程式。 

  	![][3]

5. 在應用程式中，於文字方塊輸入文字 "hello push"，然後按一下 [**儲存**]。

   	![][4]

  	如此會傳送插入要求給行動服務來儲存新增的項目。

6. 按 [**開始**] 按鈕回到開始功能表。 

  	![][5]

  	請注意，應用程式收到推播通知，磚的標題現在是 **hello push**。

## <a name="next-steps"> </a>後續步驟

本教學課程將示範行動服務所提供的基本推播通知功能。如果您的應用程式需要更進階的功能，例如傳送跨平台通知、訂閱型路由或是極大的磁碟區，請考慮在您的行動服務中使用 Azure 通知中心。如需詳細資訊，請參閱下列通知中樞主題：

+ [開始使用通知中心]
  <br/>了解如何在 Windows 市集應用程式中利用通知中樞。

+ [什麼是通知中心？]
	<br/>了解如何建立及推播通知給多個平台的使用者。

+ [將通知傳送給訂閱者]
	<br/>了解使用者如何註冊及接收他們所需類別的推播通知。

<!--+ [Send notifications to users]
	<br/>Learn how to send push notifications from a Mobile Service to specific users on any device.

+ [Send cross-platform notifications to users]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

考慮更深入了解下列行動服務主題：

* [開始使用資料]
  <br/>進一步了解使用行動服務來儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何使用 Windows 帳戶驗證您的應用程式使用者。

* [行動服務伺服器指令碼參考]
  <br/>進一步了解註冊和使用伺服器指令碼。

* [行動服務 .NET 作法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET。 

<!-- Anchors. -->
[建立 Registrations 資料表]: #create-table
[更新指令碼來傳送推播通知]: #update-scripts
[新增推播通知至應用程式]: #add-push
[插入資料以接收通知]: #test
[後續步驟]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[行動服務 SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-wp8
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-wp8
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-wp8
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-wp8
[推播通知給應用程式使用者]: /zh-tw/develop/mobile/tutorials/push-notifications-to-users-wp8
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure 管理入口網站]: https://manage.windowsazure.com/
[mpns 物件]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[行動服務 .NET 作法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library/
[開始使用通知中心]: /zh-tw/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[什麼是通知中心？]: /zh-tw/develop/net/how-to-guides/service-bus-notification-hubs/
[將通知傳送給訂閱者]: /zh-tw/manage/services/notification-hubs/breaking-news-wp8/
[將通知傳送給使用者]: /zh-tw/manage/services/notification-hubs/notify-users/
[傳送跨平台通知給使用者]: /zh-tw/manage/services/notification-hubs/notify-users-xplat-mobile-services/
