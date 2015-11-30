<properties 
	pageTitle="新增推播通知至 Windows 執行階段 8.1 通用 app | Azure Mobile Apps" 
	description="了解如何使用 Azure App Service 行動應用程式與 Azure 通知中樞傳送推播通知至 Windows 應用程式。" 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2015" 
	ms.author="glenga"/>

# 新增推播通知至 Windows 執行階段 8.1 通用 app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概觀

本主題將示範如何使用 Azure App Service 行動應用程式與 Azure 通知中樞將推播通知傳送至 Windows 執行階段 8.1 通用 app。在此案例中，加入新項目時，您的行動應用程式後端會傳送通知至所有註冊 Windows 通知服務 (WNS) 的 Windows 應用程式

本教學課程以應用程式服務行動應用程式快速入門為基礎。在開始本教學課程之前，您必須先完成快速入門教學課程：[建立 Windows 應用程式](../app-service-mobile-windows-store-dotnet-get-started.md)。如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##必要條件

若要完成此教學課程，您需要下列項目：

* 有效的 [Microsoft 市集帳戶](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* 完成[快速入門教學課程](../app-service-mobile-windows-store-dotnet-get-started.md)。  


##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##針對推播通知註冊應用程式

在您能從 Azure 傳送推播通知到您的 Windows 應用程式之前，您必須將應用程式提交至 Windows 市集。接著您可以設定您的伺服器專案與 WNS 整合。

1. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下 Windows 市集應用程式專案，然後按一下 [市集] > [將應用程式與市集建立關聯]。 

    ![建立應用程式與 Windows 市集的關聯](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-win8-app.png)
    
2. 在精靈中按 [下一步]、使用 Microsoft 帳戶登入，在 [保留新的應用程式名稱] 中輸入您應用程式的名稱，然後按一下 [保留]。

3. 成功建立應用程式註冊之後，選取新的應用程式名稱，按 [下一步]，然後按一下 [關聯]。這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

7. 使用您之前為 Windows 市集應用程式所建立的相同登錄，針對 Windows Phone 市集應用程式專案重複步驟 1 和 3。

7. 瀏覽至 [Windows 開發人員中心](https://dev.windows.com/zh-TW/overview)、使用您的 Microsoft 帳戶登入、在 [我的應用程式] 中按一下 [新增應用程式註冊]，然後展開 [服務] > [推播通知]。

8. 在 [推播通知] 頁面上，按一下 [Microsoft Azure 行動服務] 底下的 [線上服務網站]。

9. 在 [應用程式設定] 索引標籤上，記下 [用戶端密碼] 和 [封裝 SID] 的值。

    ![開發人員中心的應用程式設定](./media/app-service-mobile-windows-store-dotnet-get-started-push/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT]用戶端密碼和封裝 SID 是重要的安全性認證。請勿與任何人共用這些值，或與您的應用程式一起散發密碼。

##設定行動應用程式以傳送推播要求

1. 登入 [Azure 入口網站]，選取 [瀏覽] > [行動應用程式] > 您的應用程式 > [推播通知服務]。

2. 在 [Windows 通知服務] 中，輸入您從線上服務網站取得的**安全性金鑰** (用戶端密碼) 和**封裝 SID**，然後按一下 [儲存]。

您的行動應用程式後端現在已設定成使用 WNS。

##<a id="update-service"></a>更新伺服器以傳送推播通知

現在應用程式已經啟用了推播通知，您必須更新應用程式後端以傳送推播通知。

1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [管理 NuGet 封裝]，搜尋 `Microsoft.Azure.NotificationHubs`，然後按一下 [安裝]。這會安裝通知中樞用戶端程式庫。

2. 在伺服器專案中開啟 [控制器] > **TodoItemController.cs**，並新增下列 Using 陳述式：

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. 在 **PostTodoItem** 方法中，於呼叫 **InsertAsync** 之後新增下列程式碼：

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    此程式碼會告訴通知中樞在插入新項目之後傳送推播通知。


## <a name="publish-the-service"></a>將行動後端發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>將推播通知新增至應用程式

1. 開啟共用的 **App.xaml.cs** 專案檔案，並新增下列 `using` 陳述式：

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. 在相同檔案中，將下列 **InitNotificationsAsync** 方法定義新增至 [**應用程式**] 類別：
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    此程式碼會從 WNS 中擷取應用程式的 ChannelURI，然後向您的應用程式服務行動應用程式註冊該 ChannelURI。
    
3. 在 **App.xaml.cs** 中 **OnLaunched** 事件處理常式頂端，將 **async** 修飾詞新增到方法定義並加入對新的 **InitNotificationsAsync** 方法的下列呼叫，如下列範例所示：

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    這樣可保證在每次啟動應用程式時都會註冊存留期較短的 ChannelURI。

4. 在 [方案總管] 中，按兩下 Windows 市集應用程式的 **Package.appxmanifest**，並將 [**通知**] 中的 [**支援快顯通知**] 設為 [**是**]：

    從 [檔案] 功能表中，按一下 [全部儲存]。

5. 針對 Windows Phone 市集應用程式專案重複上一個步驟。

您的應用程式現在已能夠接收快顯通知。

##<a id="test"></a>在應用程式中測試推播通知

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##<a id="more"></a>更多資訊

* 範本可讓您彈性地傳送跨平台推播和當地語系化推播。[如何使用 Azure Mobile Apps 的 Managed 用戶端](app-service-mobile-dotnet-how-to-use-client-library.md)示範如何註冊範本。
* 標記可讓您使用推播鎖定區隔的客戶。[使用 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 示範如何在安裝裝置時加入標記。

<!-- Anchors. -->

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=Nov15_HO4-->