## 建立 WebAPI 專案

請遵循下列步驟來建立新的 ASP.NET WebAPI 後端來驗證用戶端並產生通知，或修改來自先前專案或[將推播通知傳送給驗證的使用者](http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/)教學課程的現有後端。

> [AZURE.NOTE] **重要事項**：開始本教學課程之前，請確定您已安裝最新版本的 NuGet Package Manager。若要檢查版本，請啟動 Visual Studio。在 [**工具**] 功能表中，按一下 [**擴充功能和更新**]。搜尋 **NuGet Package Manager for Visual Studio 2013**，然後確定您已安裝 2.8.50313.46 版或更新版本。否則的話，請解除安裝，然後重新安裝 NuGet Package Manager。
> 
> ![][4]

> [AZURE.NOTE] 確定您已安裝 Visual Studio [Azure SDK](http://azure.microsoft.com/zh-tw/downloads/) 以供網站部署。

1. 啟動 Visual Studio 或 Visual Studio Express。
2. 在 Visual Studio 中，依序按一下 [**檔案**]、[**新增**]、[**專案**]，展開 [**範本**]、[**Visual C#**]，然後按一下 [**Web**] 和 [**ASP.NET Web 應用程式**]，輸入名稱 **AppBackend**，然後按一下 [**確定**]。 
	
	![][1]

3. 在 [**New ASP.NET 專案**] 對話方塊中，按一下 [**Web API**]，然後按一下 [**確定**]。

	![][2]

4. 在 [**設定 Azure 網站**] 對話方塊中，選擇要用於此專案的訂閱、區域和資料庫。然後按一下 [**確定**] 以建立專案。

	![][5]

5. 在 [方案總管] 中，以滑鼠右鍵按一下 **AppBackend** 專案，然後按一下 [**Manage NuGet Packages**]。

6. 在左側按一下 [**線上**]，並在 [**搜尋**] 方塊中搜尋 **servicebus**。

7. 按一下結果清單中的 **Windows Azure Service Bus**，然後按一下 [**安裝**]。請完成安裝，然後關閉 [NuGet Package Manager] 視窗。

	![][14]

8. 我們現在將建立新的類別 **Notifications.cs**。移至 [方案總管]，以滑鼠右鍵按一下 [**模型**] 資料夾，依序按一下 [**新增**] 和 [**類別**。將新類別命名為 **Notifications.cs** 之後, 按一下 [**新增**] 以產生類別。此模組便會顯示即將傳送的不同安全通知。在完整的實作中，通知會儲存在資料庫中。為了簡單起見，本教學課程會將它們儲存在記憶體中。

	![][6]

9. 在 Notifications.cs 中，將下列 'using' 陳述式新增在檔案頂端：

        using Microsoft.ServiceBus.Notifications;

10. 然後以下列內容取代 `Notifications` 類別定義，並確定以通知中心的連接字串 (含完整存取權) 和中心名稱 (可在 [Azure 管理入口網站](http://manage.windowsazure.com) 取代) 取代兩個預留位置：

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. 我們現在將建立新的類別 **AuthenticationTestHandler.cs**。在 [方案總管] 中，以滑鼠右鍵按一下 **AppBackend** 專案，然後依序按一下 [**新增**] 和 [**類別**]。將新類別命名為 **AuthenticationTestHandler.cs**, 然後按一下 [**新增**] 以產生類別。此類別可用來驗證使用者，方法是使用 *Basic Authentication*。請注意，您的應用程式可以使用任何驗證結構描述。

12. 在 AuthenticationTestHandler.cs 中，加入下列 'using' 陳述式：

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;

13. 在 AuthenticationTestHandler.cs 中，以下列內容取代 'AuthenticationTestHandler' 類別定義：

		public class AuthenticationTestHandler : DelegatingHandler
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
	            }
	            else return Unauthorised();
	
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

	> [AZURE.NOTE] **安全性注意事項**：`AuthenticationTestHandler` 類別不提供真正的驗證。它僅可用於模仿基本驗證而且並不安全。您必須在生產應用程式和服務中實作安全的驗證機制。				

14. 在 **App_Start/WebApiConfig.cs** 類別中 `Register` 方法的結尾加入下列程式碼：

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. 接下來，我們要建立新的控制器 **RegisterController**。在 [方案總管] 中，以滑鼠右鍵按一下 **Controllers** 資料夾，然後依序按一下 [**新增**] 和 [**控制站**]按一下 **Web API 2 Controller -- Empty** 項目，然後按一下 [**新增**]。將新類別命名為 **RegisterController**，然後再次按一下 [**新增**] 以產生控制器。

	![][7]

	![][8]

16. 在 RegiterController.cs 中，加入下列 'using' 陳述式：

        using Microsoft.ServiceBus.Notifications;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

17. 在 `RegisterController` 類別定義中新增下列程式碼。請注意，在此程式碼中，我們會為已通過處理常式驗證的使用者新增使用者標籤。您也可以新增選擇性檢查，以驗證使用者是否有權註冊所要求的標籤。

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

18. 建立新的控制器 **NotificationsController**，請遵循我們建立 **RegisterController** 的方式。此元件會公開一種讓裝置安全地擷取通知的方式，並提供方法讓使用者觸發對裝置的安全推播。請注意，將通知傳送至通知中樞時，我們會傳送只包含通知 ID 的原始通知 (而非實際訊息)。

19. 在 NotificationsController.cs 中，加入下列 'using' 陳述式：

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

20. 在 **NotificationsController** 類別定義中加入下列程式碼，請確定將您未使用之平台的程式碼片段註解化。

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;


            // windows
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);


            // apns
            var alert = "{\"aps\":{\"alert\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);


            // gcm
            var notif = "{ \"data\" : {\"msg\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);


            return Request.CreateResponse(HttpStatusCode.OK);
        }

21. 按 **F5** 鍵以執行應用程式，並確保工作到目前為止的準確性。應用程式應即啟動網頁瀏覽器，並顯示 ASP.NET 首頁。 

22. 為了可以從所有裝置存取此應用程式，我們現在可以將它部署到 Azure 網站。以滑鼠右鍵按一下 **AppBackend** 專案，然後選取 [**發行**]。

23. 選取 Azure 網站作為您的發行目標。

    ![][B15]

24. 使用您的 Azure 帳戶登入，並選取現有或新的網站。

    ![][B16]

25. 記下 [**連線**] 索引標籤中的 [**目的地 URL**] 屬性。我們後續會在本教學課程中將此 URL 視為您的 *後端端點*。按一下 [**發行**]。

    ![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
