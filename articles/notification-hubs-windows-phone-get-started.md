<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

開始使用通知中心
================

[Windows 市集 C#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows 市集 C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

本主題將示範如何使用 Azure 通知中心，來將推播通知傳送到 Windows Phone 8 應用程式。在本教學課程中，您將使用 Microsoft 推播通知服務 (MPNS)，建立可接收推播通知的空白 Windows Phone 8 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程將逐步引導您完成下列啟用推播通知的步驟：

1.  [建立您的通知中心](#configure-hub)
2.  [將您的應用程式連接到通知中心](#connecting-app)
3.  [從後端傳送通知](#send)

本教學課程將示範使用通知中心的簡單廣播案例。請確定依照下一個教學課程的步驟進行，以了解如何使用通知中心來處理特定使用者和裝置群組。本教學課程需要下列項目：

-   [Visual Studio 2012 Express for Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) 或更新版本。

完成本教學課程是 Windows Phone 8 應用程式所有其他通知中心教學課程的先決條件。

**注意**

若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F)。

建立您的通知中心建立您的通知中心
--------------------------------

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，並按一下畫面底部的 **[+NEW]**。

2.  依序按一下 **[App Services]**、**[服務匯流排]**、**[Notification Hub]**、**[快速建立]**。

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png)

3.  為您的通知中心輸入名稱、選取所需的區域，然後按一下 **[Create a new Notification Hub]**。

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png)

4.  按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 **[設定]** 索引標籤。

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png)

5.  按一下頂端的 **[Notification Hubs]** 索引標籤，然後按一下您剛才建立的通知中心。

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png)

6.  按一下底部的 **[連接資訊]**。記下這兩個連接字串。

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png)

7.  按一下 **[設定]** 索引標籤，然後按一下 **[Windows Phone notifications settings]** 區段中的 **[Enable unauthenticated push notifications]** 核取方塊。

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png)

現在您已擁有註冊 Windows Phone 8 應用程式和傳送通知所需的連接字串。

**注意**

本教學課程使用處於未通過驗證模式的 MPNS。MPNS 未通過驗證模式內含您可傳送至每個通道的通知限制。通知中心支援 [MPNS 驗證模式](http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx) (英文)。

連接應用程式將您的應用程式連接到通知中心
----------------------------------------

1.  在 Visual Studio 中建立新的 Windows Phone 8 應用程式。

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png)

2.  使用 [WindowsAzure.Messaging.Managed NuGet 封裝](http://nuget.org/packages/WindowsAzure.Messaging.Managed/)，新增適用於 Windows 市集的 Azure 訊息程式庫參照。在 Visual Studio 功能表中，依序按一下 **[工具]**、**[Library Package Manager]**、**[Package Manager Console]**。接著，在主控台視窗中輸入：

		Install-Package WindowsAzure.Messaging.Managed

    並按 Enter 鍵。

3.  開啟檔案 App.xaml.cs，並新增下列 `using` 陳述式：

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;

4.  在 App.xaml.cs 中 **Application_Launching** 方法頂端上的下列程式碼：

		var channel = HttpNotificationChannel.Find("MyPushChannel");
		if (channel == null)
		{
			channel = new HttpNotificationChannel("MyPushChannel");
			channel.Open();
			channel.BindToShellToast();
		}

		channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
		{
			var hub = new NotificationHub("<hub name>", "<connection string>");
			await hub.RegisterNativeAsync(args.ChannelUri.ToString());
		});

    請確定插入您的中心名稱，及您在上一節中取得、名為 **DefaultListenSharedAccessSignature** 的連接字串。此程式碼會從 MPNS 中擷取應用程式的 ChannelURI，然後向您的通知中心註冊該 ChannelURI。它也保證每次啟動應用程式時，ChannelURI 便會在通知中心中註冊。

    **注意**

    本教學課程將傳送快顯通知給裝置。傳送磚通知時，您必須在通道上改為呼叫 **BindToShellTile** 方法。若要同時支援快顯和磚通知，請呼叫 **BindToShellTile** 和 **BindToShellToast**。

5.  在 [方案總管] 中展開 **[屬性]**、開啟 WMAppManifest.xml 檔案、按一下 **[功能]** 索引標籤，並確定已勾選 **ID__CAP__PUSH_NOTIFICATION** 功能。

   	![](./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png)

   	This ensures that your app can receive push notifications.

6.  按 F5 鍵以執行應用程式。

傳送通知從後端傳送通知
----------------------

您可以使用 [REST 介面](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx)，從任何後端使用通知中心傳送通知。本教學課程將透過 .NET 主控台應用程式和使用節點指令碼的行動服務來傳送通知。

使用 .NET 應用程式傳送通知：

1.  建立新的 Visual C# 主控台應用程式：

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png)

2.  使用 [WindowsAzure.ServiceBus NuGet 封裝](http://nuget.org/packages/WindowsAzure.ServiceBus/)來新增 Azure 服務匯流排 SDK 的參照。在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]**、**[Package Manager Console]**。接著，在主控台視窗中輸入：

		Install-Package WindowsAzure.ServiceBus

    並按 Enter 鍵。

3.  開啟檔案 Program.cs，並新增下列 `using` 陳述式：

		using Microsoft.ServiceBus.Notifications;

4.  在 `Program` 類別中，新增下列方法：

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
			string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
				"<wp:Notification xmlns:wp=\"WPNotification\">" +
					"<wp:Toast>" +
						"<wp:Text1>Hello from a .NET App!</wp:Text1>" +
					"</wp:Toast> " +
				"</wp:Notification>";
			await hub.SendMpnsNativeNotificationAsync(toast);
		}

    請確定插入您的中心名稱，及您在「設定您的通知中心」一節中取得、名為 DefaultFullSharedAccessSignature 的連接字串。請注意，這是包含完整存取權 (非接聽存取權) 的連接字串。

5.  接著在您的 Main 方法中新增下列程式碼行：

		SendNotificationAsync();
		Console.ReadLine();

6.  按 F5 鍵以執行應用程式。您應會收到快顯通知。請確保您的 Windows Phone 模擬器正在執行中且您的應用程式已關閉。

您可以在 MSDN 上的[快顯目錄](http://msdn.microsoft.com/en-us/library/windowsphone/develop/jj662938(v=vs.105).aspx) (英文) 和[磚目錄](http://msdn.microsoft.com/en-us/library/windowsphone/develop/hh202948(v=vs.105).aspx) (英文) 中找到所有可能的裝載。

後續步驟
--------

在此簡單範例中，您將廣播通知到您的所有 Windows Phone 8 裝置。為了鎖定特定使用者，請參閱教學課程[使用通知中心來推播通知給使用者](/en-us/manage/services/notification-hubs/notify-users-aspnet)。如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞](/en-us/manage/services/notification-hubs/breaking-news-dotnet)。在[通知中心指引](http://msdn.microsoft.com/en-us/library/jj927170.aspx)中深入了解如何使用通知中心。

