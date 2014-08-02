<properties linkid="notification-hubs-how-to-guides-howto-notify-users-aspnet" urlDisplayName="Notify Users" pageTitle="Notify Users of your ASP.NET service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your ASP.NET service by using Notification Hubs" metaCanonical="" services="notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

使用通知中心來通知使用者
========================

[行動服務](/en-us/manage/services/notification-hubs/notify-users "行動服務")[ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。ASP.NET Web API 後端可用來驗證用戶端並產生通知。本教學課程會以您在上一個**開始使用通知中心**教學課程中所建立的通知中心為基礎。將通知註冊程式碼從用戶端移至後端服務。這可確保只有在服務已明確地驗證用戶端之後才會完成註冊。這也代表通知中心認證不會隨著用戶端應用程式一起散佈。服務也可控制註冊期間所要求的標籤。

本教學課程將逐步引導您完成下列基本步驟：

-   [使用驗證建立 ASP.NET 應用程式](#create-application)
-   [更新您的 ASP.NET 應用程式以註冊通知](#register-notification)
-   [更新應用程式以登入並要求註冊](#update-app)

必要條件
--------

-   Visual Studio 2012。您也可以分別使用 Visual Studio Express 2012 for Web 和 Visual Studio Express 2012 for Windows 8 來建立 ASP.NET 應用程式和 Windows 市集應用程式。
-   本教學課程會以您在**開始使用通知中心**中所建立的應用程式和通知中心為基礎。在開始本教學課程之前，您必須首先完成教學課程**開始使用通知中心** ([Windows 市集 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/en-us/manage/services/notification-hubs/get-started-notification-hubs-android))。

**注意**

您在本教學課程中所建立的 ASP.NET Web API 專案會在您的本機電腦上執行。您也可以將 ASP.NET Web API 專案發佈至 Azure。如需詳細資訊，請參閱[使用 ASP.NET Web API 和 SQL Database 建立行動便利的 REST 服務](/en-us/develop/net/tutorials/rest-service-using-web-api/)。

建立 ASP.NET 應用程式使用驗證建立 ASP.NET 應用程式
--------------------------------------------------

首先，您將建立 ASP.NET Web API 應用程式。此後端服務將會驗證用戶端、代替通過驗證的使用者註冊推播通知，及傳送通知。

1.  在 Visual Studio 或 Visual Studio Express 2012 for Web 中，依序按一下 **[檔案]**、[檔案] 功能表中的 [新增專案]、依序展開 **[範本]**、**[Visual C\#]**，然後按一下 **[Web]** 和 **[ASP.NET MVC 4 Web 應用程式]**，輸入名稱 *NotificationService*，然後按一下 **[確定]**。

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png)

2.  在 **[新增 ASP.NET MVC 專案]** 對話方塊中，按一下 **[空白]**，然後按一下 **[確定]**。

    這會建立 ASP.NET MVC 專案。

3.  在 [方案總管] 中，以滑鼠右鍵依序按一下 **[新增]**、**[類別]**，然後鍵入 `AuthenticationTestHandler` 並按一下 **[新增]**。

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png)

    這會將 **AuthenticationTestHandler** 類別的程式碼檔案新增至專案。

4.  開啟新 AuthenticationTestHandler.cs 專案檔案，並使用下列程式碼取代類別定義：

         using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Security.Principal;
        using System.Text;
        using System.Web;
            
        namespace NotificationService
         {
        public class AuthenticationTestHandler :DelegatingHandler
             {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
                 {
        var authorizationHeader = request.Headers.GetValues("Authorization").First();
            
        if (authorizationHeader != null && authorizationHeader
        .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
                     {
        string authorizationUserAndPwdBase64 = 
        authorizationHeader.Substring("Basic ".Length);
        string authorizationUserAndPwd = Encoding.Default
        .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
        string user = authorizationUserAndPwd.Split(':')[0];
        string password = authorizationUserAndPwd.Split(':')[1];
            
        if (verifyUserAndPwd(user, password))
                         {
        // Attach the new principal object to the current HttpContext object
        HttpContext.Current.User = 
        new GenericPrincipal(new GenericIdentity(user), new string[0]);
        System.Threading.Thread.CurrentPrincipal = 
        System.Web.HttpContext.Current.User;
                         }
        else return Unauthorised();
        } else return Unauthorised();
            
        return base.SendAsync(request, cancellationToken);
                 }
            
        private bool verifyUserAndPwd(string user, string password)
                 {
        // This is not a real authentication scheme.
        return user == password;
                 }
            
        private Task<HttpResponseMessage> Unauthorised()
                 {
        var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
        var tsc = new TaskCompletionSource<HttpResponseMessage>();
        tsc.SetResult(response);
        return tsc.Task;
                 }
             }
         } 

    **安全性注意事項**

    **AuthenticationTestHandler** 類別不提供真正的驗證。它僅可用於模仿基本驗證並傳回主體。使用者名稱是建立通知中心註冊的必要資訊。上方的實作並不安全。您必須在生產應用程式和服務中實作安全的驗證機制。

5.  展開 **App\_Start** 資料夾、開啟 WebApiConfig.cs 檔案，並在 **Register** 方法的結束位置新增下列程式碼行：

         config.MessageHandlers.Add(new AuthenticationTestHandler());

    這需要對 ASP.NET 應用程式提出的要求包含授權標頭。

現在，我們已使用模擬驗證結構描述建立基本應用程式，以便提供使用者名稱。

註冊通知更新您的 ASP.NET 應用程式以註冊通知
-------------------------------------------

下一步是將通知中心的註冊邏輯新增至 ASP.NET 應用程式，方法是建立新的 **[註冊]** 控制器。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[服務匯流排]**、您的命名空間、**[通知中心]**，然後選擇您的通知中心並按一下 **[連接資訊]**。

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png)

2.  記下您的通知中心名稱，並複製 **DefaultFullSharedAccessSignature** 的連接字串。

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png)

    您將使用此連接字串以及通知中心名稱來註冊與傳送通知。

3.  在 [方案總管] **** 中以滑鼠右鍵按一下專案名稱，然後選取 **[管理 NuGet 封裝]**。

4.  在左側窗格中選取 **[線上]** 類別、搜尋 `WindowsAzure.ServiceBus`、按一下 **[Azure 服務匯流排]** 封裝上的 **[安裝]**，然後接受授權協定。

	![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png) 

	這會將 Microsoft.ServiceBus.dll 組件新增至您的專案。

1.  在 [方案總管] 中，以滑鼠右鍵依序按一下 **[控制器]** 資料夾、**[新增]**、**[控制器...]**，然後在 **[控制器名稱]** 中鍵入 `RegisterController`、選擇 **[Empty API controller]**，然後按一下 **[新增]**。

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png)

    這會將控制器類別新增至專案。此控制器會在叫用時執行向通知中心註冊裝置的工作。

2.  開啟新的 RegisterController.cs 專案檔案，並新增下列 **using** 陳述式。

         using Microsoft.ServiceBus.Notifications;
        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;
        using System.Web;

3.  將下列程式碼新增至新的 RegisterController 類別：

         // Define the Notification Hubs client.
        private NotificationHubClient hubClient;

        // Create the client in the constructor.
        public RegisterController()
         {
        var cn = "<FULL_SAS_CONNECTION_STRING>";
        hubClient = NotificationHubClient
        .CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
         }

4.  在建構函式中更新程式碼，使用通知中心的值來取代 *`<NOTIFICATION_HUB_NAME>`* 和 *`<FULL_SAS_CONNECTION_STRING>`*，然後按一下 **[儲存]**。

    **注意**

    務必在 *`<FULL_SAS_CONNECTION_STRING>`* 中使用 **DefaultFullSharedAccessSignature**。此宣告可讓 Web API 建立並更新註冊。

5.  將下列 Post 方法程式碼新增至 RegisterController 類別：

        public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
        {
            // Get the registration info that we need from the request. 
            var platform = registrationCall["platform"].ToString();
            var installationId = registrationCall["instId"].ToString();
            var channelUri = registrationCall["channelUri"] != null ? 
                registrationCall["channelUri"].ToString() : null;
            var deviceToken = registrationCall["deviceToken"] != null ? 
                registrationCall["deviceToken"].ToString() : null;       
            var userName = HttpContext.Current.User.Identity.Name;

            // Get registrations for the current installation ID.
            var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

            bool updated = false;
            bool firstRegistration = true;
            RegistrationDescription registration = null;

            // Check for existing registrations.
            foreach (var registrationDescription in regsForInstId)
            {
                if (firstRegistration)
                {
 					// Update the tags.
                    registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                    // We need to handle each platform separately.
                    switch (platform)
                    {
                        case "windows":
                            var winReg = registrationDescription as WindowsRegistrationDescription;
                            winReg.ChannelUri = new Uri(channelUri);
                            registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                            break;
                        case "ios":
                            var iosReg = registrationDescription as AppleRegistrationDescription;
                            iosReg.DeviceToken = deviceToken;
                            registration = await hubClient.UpdateRegistrationAsync(iosReg);
                            break;
                    }
                    updated = true;
                    firstRegistration = false;
                }
                else
                {
                    // We shouldn't have any extra registrations; delete if we do.
                    await hubClient.DeleteRegistrationAsync(registrationDescription);
                }
            }

            // Create a new registration.
            if (!updated)
            {
                switch (platform)
                {
                    case "windows":
                        registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                            new string[] { installationId, userName });
                        break;
                    case "ios":
                        registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                            new string[] { installationId, userName });
                        break;
                }
            }

            // Send out a test notification.
            sendNotification(string.Format("Test notification for {0}", userName), userName);

            return registration;
        }

    此程式碼會由 POST 要求所叫用，並從訊息內文中取得平台和 deviceID 資訊。此資料 (以及要求標頭的安裝識別碼和已登入使用者的使用者識別碼) 可用來更新註冊。如果註冊不存在，便會建立新的註冊。此註冊上會附有使用者識別碼和安裝識別碼。

6.  新增下列 sendNotification 方法：

        // Basic implementation that sends a not ification to Windows Store and iOS app clients.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Create notifications for both Windows Store and iOS platforms.
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                    notificationText + "</text></binding></visual></toast>";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Send a notification to the logged-in user on both platforms.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // This is expected when an APNS registration doesn't exist.
                Console.WriteLine(ex.Message);
            }
        }

    完成註冊時，便會立即呼叫此方法來傳送通知。

接下來，我們將更新您在完成教學課程**開始使用通知中心**時所建立的用戶端應用程式。

更新應用程式更新應用程式以登入及要求註冊
----------------------------------------

您在完成教學課程**開始使用通知中心**時所建立的應用程式可從通知中心直接要求註冊。之後您會將此註冊程式碼從用戶端應用程式中移除，並使用呼叫 ASP.NET Web API 應用程式中的新註冊 API 來取代該註冊程式碼。

1.  按 F5 以啟動 ASP.NET 應用程式，並嘗試載入預設頁面。

    顯示瀏覽器時，請記下要求網站的主機名稱。更新用戶端應用程式時，您將會需要此根目錄 URL。

    **注意**

    使用本機 IIS Web 伺服器或 Visual Studio 程式開發伺服器時，您還必須指定連接埠號碼。請注意，因為我們尚未在此應用程式中實作預設頁面，所以會傳回 404 錯誤。

2.  視您的用戶端平台而定，請依照下列其中一個**使用 ASP.NET Web API 來註冊目前使用者以取得推播通知**版本中的步驟進行：

    -   [Windows 市集 C\# 版本](/en-us/manage/services/notification-hubs/register-users-aspnet-dotnet)
    -   [iOS 版本](/en-us/manage/services/notification-hubs/howto-register-user-with-aspnet-ios)

3.  執行更新的應用程式、透過使用相同的使用者名稱和密碼字串來登入服務，然後驗證是否顯示指派給通知的註冊 ID。

    您還會收到推播通知。

    **注意**

    當要求傳送通知的目標平台沒有註冊時，後端上便會發生錯誤。在此情況下，可以忽略此錯誤。若要查看如何使用範本避免此狀況，請參閱[使用通知中心傳送跨平台通知給使用者](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet)。

4.  (選擇性) 將此用戶端應用程式部署到第二台裝置，然後執行此應用程式並插入文字。

    每台裝置上都會顯示通知。

後續步驟
--------

現在您已完成本教學課程，請考慮完成下列教學課程：

-   **使用通知中心傳送重大消息 ([Windows 市集 C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-dotnet))**
    此平台特定教學課程將示範如何使用標籤，來讓使用者訂閱他們感興趣的通知類型。

-   **[使用通知中心傳送跨平台通知給使用者](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet)**
    此教學課程會延伸目前的**使用通知中心通知使用者**教學課程，來使用平台特定範本註冊通知。這可讓您在伺服器端程式碼中，使用單一方法傳送通知。

如需有關通知中心的詳細資訊，請參閱 [Azure 通知中心](http://msdn.microsoft.com/en-us/library/windowsazure/jj927170.aspx)。

