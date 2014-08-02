<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="push notifications c#" description="Learn how to use push notifications with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services using Visual Studio 2012" authors="" />

開始使用 Visual Studio 2012 在行動服務中使用推播通知
====================================================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "Windows 市集 C#") [Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012 "Windows 市集 JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS") [Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android") [Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

本主題說明如何使用 Azure 行動服務傳送推播通知至 Windows 市集應用程式。在本教學課程中，您會使用 Windows 推播通知服務 (WNS) 將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

**注意**

本教學課程會將推播通知新增至在 Visual Studio 2012 中建立的 Windows 市集應用程式。Visual Studio 2013 的新功能，可讓您輕鬆地使用行動服務在 Windows 市集應用程式中設定推播通知。針對 Visual Studio 2013 版，請參閱[開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

1.  [針對推播通知註冊您的應用程式及設定行動服務](#register)
2.  [建立 Registrations 資料表](#create-table)
3.  [新增推播通知至應用程式](#add-push)
4.  [更新指令碼來傳送推播通知](#update-scripts)
5.  [插入資料以接收通知](#test)

本教學課程需要下列各項：

-   Microsoft Visual Studio 2012 Express for Windows 8
-   有效的 Windows 市集帳戶

本教學課程以[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)教學課程為基礎。在開始本教學課程之前，您必須先完成[此教學課程](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)。

註冊應用程式向 Windows 市集註冊應用程式
---------------------------------------

若要能夠從行動服務傳送推播通知給 Windows 市集應用程式，您必須將應用程式提交至 Windows 市集。接著您必須設定您的行動服務，以便與 WNS 進行整合。

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

您的行動服務和您的應用程式現在都已完成設定，而可與 WNS 搭配使用。接下來，您將建立用來儲存註冊的新資料表。

建立新資料表
------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

新增推播通知新增推播通知至應用程式
----------------------------------

1.  開啟專案檔案 MainPage.xaml.cs，然後新增下列程式碼以建立新的 **[註冊]** 類別：

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }

    Handle 屬性用來儲存通道 URI。

2.  開啟檔案 App.xaml.cs，並新增下列 using 陳述式：

        using Windows.Networking.PushNotifications;

3.  新增下列程式碼至 App.xaml.cs：

        private async void AcquirePushChannel()
	    {
	       CurrentChannel = 
               await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
	
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.Uri };
	       await registrationsTable.InsertAsync(registration);
        }

    此程式碼會將目前的通道插入「註冊」資料表中。

4.  在 App.xaml.cs 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新的 **AcquirePushChannel** 方法：

        AcquirePushChannel();

    如此可確保每次應用程式啟動時都會初始化 **CurrentChannel** 屬性。

5.  開啟 Package.appxmanifest 檔案，並在 **[應用程式 UI]** 索引標籤中確認 **[支援快顯通知]** 設為 **[是]**。

	![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png)

 	如此可確定您的應用程式可以提出快顯通知。

更新 insert 指令碼在管理入口網站中更新已註冊的 insert 指令碼
------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  按一下 **[TodoItem]**，按一下 **[指令碼]**，然後選取 **[插入]**。

	![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png)

2.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

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
                    	    push.wns.sendToastText04(registration.handle, {
                        	    text1: item.text
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

測試應用程式在您的應用程式中測試推播通知
----------------------------------------

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  在應用程式的 **[Insert a TodoItem]** 中輸入文字，然後按一下 **[儲存]**。

	![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png)

	注意插入完成之後，應用程式會收到來自 WNS 的推播通知。

	![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png)

後續步驟
--------

本教學課程將示範行動服務所提供的基本推播通知功能。如果您的應用程式需要更進階的功能，例如傳送跨平台通知、訂閱型路由或是極大的磁碟區，請考慮在您的行動服務中使用 Azure 通知中心。如需詳細資訊，請參閱下列通知中心主題：

-   [開始使用通知中心](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>了解如何在 Windows 市集應用程式中利用通知中心。

-   [什麼是通知中心？](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
    <br/>了解如何建立及推播通知給多個平台的使用者。

-   [將通知傳送給訂閱者](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>了解使用者如何註冊及接收其所需類別的推播通知。

-   [將通知傳送給使用者](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>了解如何將推播通知從行動服務傳送給任何裝置上的特定使用者。

-   [將交叉平台通知傳送給使用者](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。

考慮更深入了解下列行動服務主題：

-   [開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)
    <br/>深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    <br/>了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [行動服務伺服器指令碼參考](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>深入了解如何註冊和使用伺服器指令碼。

-   [行動服務 .NET 作法概念性參考](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    <br/>深入了解如何搭配使用行動服務與 .NET。


