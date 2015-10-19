<properties
	pageTitle="開始使用適用於 Windows 市集應用程式的 Azure 通知中樞 | Microsoft Azure"
	description="在本教學課程中，您會了解如何使用 Azure 通知中樞，將推播通知傳送到 Windows 市集或 Windows Phone 8.1 (非 Silverlight) 應用程式。"
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor="dwrede"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="10/05/2015"
	ms.author="wesmc"/>

# 開始使用適用於 Windows 市集應用程式的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概觀

本教學課程將示範如何使用 Azure 通知中樞，以將推播通知傳送到 Windows 市集或 Windows Phone 8.1 (非 Silverlight) 應用程式。如果您的目標是 Windows Phone 8.1 Silverlight，請參閱 [Windows Phone](notification-hubs-windows-phone-get-started.md) 版本。在本教學課程中，您將使用 Windows 推播通知服務 (WNS)，建立可接收推播通知的空白 Windows 市集應用程式。完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。

本教學課程將示範使用通知中樞的簡單廣播案例。請確定依照下一個教學課程的步驟進行，以了解如何使用通知中心來處理特定使用者和裝置群組。

您可以在[此處](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal)的 GitHub 上找到適用於此教學課程的完整程式碼。



##必要條件

本教學課程需要下列各項：

+ Microsoft Visual Studio Express 2013 for Windows (含 Update 2)<br/>若要建立通用應用程式專案，則必須具備這個版本的 Visual Studio。如果您要建立 Windows 市集應用程式，您需要 Visual Studio 2012 Express for Windows 8。

+ 有效的 Windows 市集帳戶

+ 作用中的 Azure 帳戶 <br/> 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F)。

完成本教學課程是 Windows 市集應用程式所有其他通知中心教學課程的先決條件。

##向 Windows 市集註冊應用程式

若要傳送推播通知給 Windows 市集應用程式，您必須將您的 App 與 Windows 市集產生關聯。接著您必須設定您的通知中心，以便與 WNS 進行整合。

1. 如果您尚未註冊您的 App，請瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Windows 開發人員中心</a>，使用 Microsoft 帳戶登入，然後按一下 [建立新的應用程式]。


2. 輸入您的應用程式名稱，然後按一下 [保留應用程式名稱]。

   	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-name.png)

   	This creates a new Windows Store registration for your app.

3. 在 Visual Studio 中，使用 [空白應用程式] 範本來建立新的 Visual C# 市集應用程式專案。

   	![][2]

4. 在 [方案總管] 中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後依序按一下 [市集] 和 [將應用程式與市集建立關聯...]。

   	![][3]

   	隨即顯示 [將您的應用程式與 Windows 市集建立關聯] 精靈。

5. 在此精靈中，按一下 [登入]，然後使用您的 Microsoft 帳戶登入。

6. 按一下您在步驟 2 中註冊的應用程式，按 [下一步]，然後按一下 [關聯]。

   	![][4]

   	這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

7. (選用) 針對 Windows Phone 市集應用程式專案重複步驟 4–6。

8. 回到新應用程式的 Windows 開發人員中心頁面，依序按一下 [服務]、[推播通知]，然後按一下「Windows 推播通知服務 (WNS) 和 Microsoft Azure 行動服務」底下的 [線上服務網站]。

   	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hubs-win8-app-live-services.png)

9. 在 [應用程式設定] 索引標籤上，記下 [用戶端密碼] 和 [封裝安全性識別碼 (SID)] 的值。

   	![][6]

 	> [AZURE.WARNING]用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些值，或與您的應用程式一起散發密碼。

##設定您的通知中樞

1. 登入 [Azure 入口網站]，並按一下畫面底部的 [新增]。

2. 依序按一下 [應用程式服務]、[服務匯流排]、[通知中樞]，然後按一下 [快速建立]。

   	![][7]

3. 為您的通知中樞輸入名稱、選取所需的區域，然後按一下 [建立新的通知中樞]。

   	![][8]

4. 按一下您剛才建立的命名空間 (通常為 ***通知中樞名稱*-ns**)，然後按一下頂端的 [設定] 索引標籤。

   	![][9]

5. 選取頂端的 [通知中樞] 索引標籤，然後按一下您剛才建立的通知中樞。

   	![][10]

6. 選取頂端的 [設定] 索引標籤，輸入您在上一節中從 WNS 取得的 [用戶端密碼] 和 [封裝 SID] 值，然後按一下 [儲存]。

   	![][11]

7. 選取頂端的 [**儀表板**] 索引標籤，然後按一下頁面底部的 [**連接資訊**] 按鈕。記下這兩個連接字串。

   	![][12]

現在已將您的通知中心設定成使用 WNS，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

##將您的應用程式連接到通知中樞

1. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後按一下 [管理 NuGet 封裝]。

	此時會顯示 [管理 NuGet 封裝] 對話方塊。

2. 搜尋 `WindowsAzure.Messaging.Managed`按一下 [**安裝**]、選取方案中的所有專案，並接受使用條款。

	![][20]

	此時會在所有專案中使用 <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet 封裝</a>下載、安裝並新增適用於 Windows 的 Azure 訊息程式庫參考。

3. 開啟 App.xaml.cs 專案檔案，並新增下列 `using` 陳述式。在通用專案中，此檔案位於 `<project_name>.Shared` 資料夾中。

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;



4. 接著，在 App.xaml.cs 中，將下列 **InitNotificationsAsync** 方法定義新增至 **App** 類別：

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

    此程式碼會從 WNS 中擷取應用程式的通道 URI，然後向您的通知中樞註冊該通道 URI。

    >[AZURE.NOTE]請一定要使用出現在入口網站 [**通知中心**] 索引標籤上的通知中心名稱，來取代 "hub name" 預留位置 (例如，上一個範例中的 **mynotificationhub2**)：另請使用在上一節中取得的 **DefaultListenSharedAccessSignature** 連接字串，來取代連線字串預留位置。

5. 在 App.xaml.cs 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新 **InitNotificationsAsync** 方法：

        InitNotificationsAsync();

    這會保證每次啟動應用程式時，通道 URI 便會在通知中樞中註冊。

6. 在 [方案總管] 中，按兩下 Windows 市集應用程式的 **Package.appxmanifest**，然後將 [通知] 中的 [支援快顯通知] 設為 [是]：

   	![][18]

   	從 [檔案] 功能表中，按一下 [全部儲存]。

7. (選用) 針對 Windows Phone 市集應用程式專案重複上一個步驟。

8. 按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。

   	![][19]

9. (選擇性) 重複上一個步驟來執行 Windows Phone 專案，以在 Windows Phone 裝置上註冊應用程式。



您的應用程式現在已能夠接收快顯通知。

##傳送通知 

在 Azure 入口網站中透過通知中樞上的偵錯索引標籤 (如下列螢幕畫面所示) 來傳送通知，即可在 app 中測試通知的接收。

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-debug.png)

推播通知通常會以後端服務傳送，例如行動服務或使用相容程式庫的 ASP.NET。如果程式庫不適用於您的後端，也可以直接使用 REST API 來傳送通知訊息。

在本教學課程中，為了簡單起見，我們只會在主控台應用程式 (而非後端服務) 中使用適用於通知中樞的 .NET SDK 傳送通知，示範如何測試您的用戶端應用程式。我們建議以[使用通知中樞將通知推播給使用者]教學課程做為下一個步驟，以便從 ASP.NET 後端傳送通知。不過，下列方法可用來傳送通知：

* **REST 介面**：您可以在使用 [REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)的任何後端平台上支援通知。

* **Microsoft Azure 通知中樞 .NET SDK**︰在適用於 Visual Studio 的 Nuget 封裝管理員中，執行 [Install-package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

* **Node.js** : [如何從 Node.js 使用通知中樞](notification-hubs-nodejs-how-to-use-notification-hubs.md)。

* **Azure Mobile Services**︰如需從整合通知中樞之 Azure 行動服務後端傳送通知的範例，請參閱＜開始在行動服務中使用推播通知＞([.NET 後端](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [JavaScript 後端](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md))。

* **Java / PHP**︰如需使用 REST API 傳送通知的範例，請參閱＜如何從 Java/PHP 使用通知中樞＞([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md))。


### 從主控台應用程式傳送通知


若要使用 .NET 主控台應用程式來傳送通知，請遵循下列步驟。

1. 以滑鼠右鍵按一下方案，選取 [加入] 和 [新增專案...]，然後按一下 [Visual C#] 下方的 [Windows] 和 [主控台應用程式]，再按一下 [確定]。

   	![][13]

	即會將新的 Visual C# 主控台應用程式新增到方案中。您也可以在個別方案中進行此項作業。

4. 在 Visual Studio 中，依序按一下 [工具]、[NuGet 封裝管理員] 和 [封裝管理員主控台]。

	這會在 Visual Studio 中顯示 [封裝管理員主控台]。

6. 在 [封裝管理員主控台] 視窗中，將 [預設專案] 設為新的主控台應用程式專案，然後在主控台視窗中執行下列命令：

        Install-Package Microsoft.Azure.NotificationHubs

	這會使用 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.通知中樞 NuGet 封裝</a>加入對 Azure 通知中樞 SDK 的參考。

	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


5. 開啟檔案 Program.cs，並新增下列 `using` 陳述式：

        using Microsoft.Azure.NotificationHubs;

6. 在 **Program** 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	請務必使用出現在入口網站 [通知中心] 索引標籤上的通知中心名稱，來取代 "hub name" 預留位置。此外，請將連接字串預留位置取代為您在＜設定通知中樞＞一節中取得且名為 **DefaultFullSharedAccessSignature** 的連接字串。

	>[AZURE.NOTE]請確定您使用的連接字串具有 [完整] 存取權，而非 [接聽] 存取權。接聽存取權的字串沒有傳送通知的權限。

7. 在 **[主要]** 方法中新增下列命令列。

         SendNotificationAsync();
		 Console.ReadLine();

8. 在 Visual Studio 中，以滑鼠右鍵按一下主控台應用程式專案，然後按一下 [設定為啟始專案]，將它設為啟始專案。然後按 **F5** 鍵執行應用程式。

   	![][14]

	您將會在所有註冊裝置上收到快顯通知。按一下或點選快顯橫幅即會載入應用程式。

您可以在 MSDN 上的[快顯目錄] (英文)、[磚目錄] (英文) 和[徽章概觀]主題中找到所有支援的裝載。

##後續步驟

在此簡單範例中，您會使用入口網站或主控台應用程式，將廣播通知傳送到您的所有 Windows 裝置。我們建議以[使用通知中樞將通知推播給使用者]教學課程做為下一個步驟。它會顯示如何從以特定使用者為目標的 ASP.NET 後端傳送通知。

如果您想要按興趣群組分隔使用者，請參閱[使用通知中心傳送即時新聞]。若要深入了解通知中樞的一般資訊，請參閱[通知中心指引]。






<!-- Images. -->
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
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
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Azure 入口網站]: https://manage.windowsazure.com/
[通知中心指引]: http://msdn.microsoft.com/library/jj927170.aspx

[使用通知中樞將通知推播給使用者]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[使用通知中心傳送即時新聞]: notification-hubs-windows-store-dotnet-send-breaking-news.md

[快顯目錄]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[磚目錄]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[徽章概觀]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx

<!---HONumber=Oct15_HO2-->