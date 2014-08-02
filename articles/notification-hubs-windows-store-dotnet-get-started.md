<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

開始使用通知中心
================

[Windows 市集 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows 市集 C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

本主題將示範如何使用 Azure 通知中心，來將推播通知傳送到 Windows 市集應用程式。在本教學課程中，您將使用 Windows 推播通知服務 (WNS)，建立可接收推播通知的空白 Windows 市集應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [註冊應用程式以取得推播通知](#register)
2.  [設定您的通知中心](#configure-hub)
3.  [將您的應用程式連接到通知中心](#connecting-app)
4.  [從後端傳送通知](#send)

本教學課程將示範使用通知中心的簡單廣播案例。請確定依照下一個教學課程的步驟進行，以了解如何使用通知中心來處理特定使用者和裝置群組。本教學課程需要下列項目：

-   Microsoft Visual Studio 2012 Express for Windows 8
-   有效的 Windows 市集帳戶

完成本教學課程是 Windows 市集應用程式所有其他通知中心教學課程的先決條件。

**注意**

若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F)。

註冊應用程式向 Windows 市集註冊應用程式
---------------------------------------

若要從行動服務傳送推播通知給 Windows 市集應用程式，您必須將應用程式提交到 Windows 市集。接著您必須設定您的通知中心，以便與 WNS 進行整合。

1.  如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面](http://go.microsoft.com/fwlink/p/?LinkID=266582)，使用您的 Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。

	![][0]

2.  在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

	![][1]

	This creates a new Windows Store registration for your app.

3.  在 Visual Studio 2012 Express for Windows 8 中，使用 **[空白應用程式]** 範本來建立新的 Visual C\# Windows 市集專案。

	![][2]

4.  在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 **[市集]** 和 **[將應用程式與市集建立關聯...]**。

	![][3]

	The **Associate Your App with the Windows Store** wizard appears.

5.  在此精靈中，按一下 **[登入]**，然後使用您的 Microsoft 帳戶登入。

6.  按一下您在步驟 2 中註冊的應用程式，按 **[下一步]**，然後按一下 **[關聯]**。

	![][4]

	This adds the required Windows Store registration information to the application manifest.    

7.  回到新應用程式的 Windows 開發人員中心頁面，按一下 **[服務]**。

	![][5] 

8.  在 **[服務]** 頁面中，按一下 **[Azure 行動服務]** 下的 **[Live Services site]**。

	![][17]

9.  按一下 **[驗證您的服務]**，並記下 **[用戶端密碼]** 和 **[封裝安全性識別碼 (SID)]** 的值。

	![][6]

	<div class="dev-callout"\><b>安全性注意事項</b>

    <p>The client secret and package SID are important security credentials.Do not share these values with anyone or distribute them with your app.</p>
    </div>

設定您的通知中心設定您的通知中心
--------------------------------

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，並按一下畫面底部的 **[新增]**。

2.  依序按一下 **[App Services]**、**[服務匯流排]**、**[Notification Hub]**、**[快速建立]**。

	![][7]

3.  為您的通知中心輸入名稱、選取所需的區域，然後按一下 **[Create a new Notification Hub]**。

	![][8]

4.  按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 **[設定]** 索引標籤。

	![][9]

5.  選取頂端的 **[Notification Hubs]** 索引標籤，然後按一下您剛才建立的通知中心。

	![][10]

6.  選取頂端的 **[設定]** 索引標籤，輸入您在上一節中從 WNS 取得的 **[用戶端密碼]** 和 **[封裝 SID]** 值，然後按一下 **[儲存]**。

	![][11]

7.  選取頂端的 **[儀表板]** 索引標籤，然後按一下 **[連接資訊]**。記下這兩個連接字串。

	![][12]

現在已將您的通知中心設定成使用 WNS，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

連接應用程式將您的應用程式連接到通知中心
----------------------------------------

1.  使用 [WindowsAzure.Messaging.Managed NuGet 封裝](http://nuget.org/packages/WindowsAzure.Messaging.Managed/)，新增適用於 Windows 市集的 Azure 訊息程式庫參照。在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]**、**[Package Manager Console]**。接著，在主控台視窗中輸入：

         Install-Package WindowsAzure.Messaging.Managed

    然後按 **Enter** 鍵。

2.  開啟檔案 App.xaml.cs，並新增下列 `using` 陳述式：

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

3.  將下列程式碼新增至 App.xaml.cs 的 `App` 類別中。請確保使用出現在入口網站 **[Notification Hubs]** 索引標籤上的通知中心名稱，來取代 "hub name" 預留位置 (例如，上一個範例中的 **mynotificationhub2**)：

	    private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
			
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
			var result = await hub.RegisterNativeAsync(channel.Uri);
            
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    請確定插入您的中心名稱，及您在上一節中取得、名為 **DefaultListenSharedAccessSignature** 的連接字串。此程式碼會從 WNS 中擷取應用程式的 ChannelURI，然後向您的通知中心註冊該 ChannelURI。

4.  在 App.xaml.cs 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新 **InitNotificationsAsync** 方法：

         InitNotificationsAsync();

    這會保證每次啟動應用程式時，ChannelURI 便會在通知中心中註冊。

5.  按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。

	![][19]

傳送通知從後端傳送通知
----------------------

您可以使用 [REST 介面](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx)，從任何後端使用通知中心傳送通知。在本教學課程中，您將透過 .NET 主控台應用程式和使用節點指令碼的行動服務來傳送通知。

使用 .NET 應用程式傳送通知：

1.  在 Visual Studio 中仍然開著上一個解決方案的情況下，在 [方案總管] 中按兩下 **Package.appxmanifest**，以在 Visual Studio 編輯器中將其開啟。

2.  向下捲動到 **[所有影像資產]**，並按一下 **[徽章標誌]**。在 **[通知]** 中，將 **[支援快顯通知]** 設定為 **[是]**：

	![][18]

	From the **File** menu, click **Save All**.

3.  現在建立新的 Visual C\# 主控台應用程式：

	![][13]

4.  使用 [WindowsAzure.ServiceBus NuGet 封裝](http://nuget.org/packages/WindowsAzure.ServiceBus/)來新增 Azure 服務匯流排 SDK 的參照。在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]**、**[Package Manager Console]**。接著，在主控台視窗中輸入下列程式碼：

         Install-Package WindowsAzure.ServiceBus

    然後按 **Enter** 鍵。

5.  開啟檔案 Program.cs，並新增下列 `using` 陳述式：

         using Microsoft.ServiceBus.Notifications;

6.  在 `Program` 類別中，新增下列方法。請確保使用出現在入口網站 **[Notification Hubs]** 索引標籤上的通知中心名稱，來取代 "hub name" 預留位置：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

	Make sure to insert the name of your hub and the connection string called **DefaultFullSharedAccessSignature** that you obtained in the section "Configure your Notification Hub."Note that this is the connection string with **Full** access, not **Listen** access.

7.  接著在 `Main` 方法中新增下列程式碼行。

        SendNotificationAsync();
        Console.ReadLine();

8.  按 **F5** 鍵以執行應用程式。您應會收到快顯通知。

	![][14]

您可以在 MSDN 上的[快顯目錄](http://msdn.microsoft.com/en-us/library/windows/apps/hh761494.aspx) (英文)、[磚目錄](http://msdn.microsoft.com/en-us/library/windows/apps/hh761491.aspx) (英文) 和[徽章概觀](http://msdn.microsoft.com/en-us/library/windows/apps/hh779719.aspx)中找到所有可能的裝載。

若要使用行動服務傳送通知，請依照[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started/#create-new-service)中的步驟進行，然後執行下列動作：

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，然後按一下您的行動服務。

2.  選取頂端的 **[排程器]** 索引標籤。

	![][15]

3.  建立新的排定工作、插入名稱，然後按一下 **[隨選]**。

	![][16]

4.  在工作建立之後，按一下此工作名稱。然後按一下頂列中的 **[指令碼]** 索引標籤。

5.  將下列指令碼插入您的排程器函數內。請確定使用您的通知中心名稱和稍早取得的 *DefaultFullSharedAccessSignature* 連接字串，來取代預留位置。完成時，請按一下底列上的 **[儲存]**。

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
        notificationHubService.wns.sendToastText01(
            null,
            {
                text1: 'Hello from Mobile Services!!!'
            },
            function (error) {
                if (!error) {
                    console.warn("Notification successful");
                }
        });

6.  按一下底列上的 **[執行一次]**。您應會收到快顯通知。

後續步驟
--------

在此簡單範例中，您將傳送廣播通知到您的所有 Windows 裝置。為了鎖定特定使用者，請參閱教學課程[使用通知中心來推播通知給使用者](/en-us/manage/services/notification-hubs/notify-users-aspnet)。如果您想要按興趣群組分隔使用者，請參閱[使用通知中心傳送即時新聞](/en-us/manage/services/notification-hubs/breaking-news-dotnet)。若要深入了解如何使用通知中心，請參閱 [Azure 通知中心概觀](http://msdn.microsoft.com/en-us/library/jj927170.aspx)和[Windows 市集的通知中心作法](http://msdn.microsoft.com/en-us/library/jj927172.aspx)。

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png