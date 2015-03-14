<properties 
	pageTitle="開始使用自訂驗證 | 行動開發人員中心" 
	description="了解如何藉由使用者名稱及密碼驗證使用者。" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="mahender"/>

# 開始使用自訂驗證

本主題說明如何簽發您自己的行動服務驗證權杖，以在 Azure 行動服務 .NET 後端中驗證使用者。在本教學課程中，您會使用應用程式的自訂使用者名稱和密碼，將驗證新增至快速入門專案。

>[AZURE.NOTE] 本教學課程將說明以自訂認證驗證行動服務的進階方法。有許多應用程式應改用內建社交身分識別提供者最為適當，以讓使用者能夠透過 Facebook、Twitter、Google、Microsoft 帳戶和 Azure Active Directory 進行登入。如果這是您第一次接觸行動服務中的驗證，請先參閱[使用者入門]教學課程。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [設定帳戶資料表]
2. [建立註冊端點]
3. [建立 LoginProvider]
4. [建立登入端點]
5. [將行動服務設定為需要驗證]
6. [使用測試用戶端測試登入流程]

本教學課程以行動服務快速入門為基礎。您也必須先完成教學課程[開始使用行動服務]。 

>[AZURE.NOTE] 本教學課程的目的是要說明如何簽發行動服務的驗證權杖。請不要將其視為安全性指引。在開發應用程式時，您必須留意密碼儲存的安全性問題，且必須要有管理暴力密碼破解攻擊的策略。


## <a name="table-setup"></a>設定帳戶資料表

由於您使用自訂驗證，且未仰賴其他身分識別提供者，因此您必須儲存使用者的登入資訊。在本節中，您將為帳戶建立資料表，並設定基本的安全性機制。帳戶資料表將包含使用者名稱和加料式雜湊密碼 (salted and hashed passwords)，且您也可以視需要加入其他使用者資訊。

1. 在您後端專案的  `DataObjects` 資料夾中，建立名為  `Account` 的新實體：

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }
    
    如此，我們的新資料表中將會顯示一個資料列，且其中會包含使用者名稱、該使用者的加料 (salt)，以及安全儲存的密碼。

2. 在  `Models` 資料夾下，您會看見以行動服務名稱命名的  `DbContext` 類別。本教學課程的其餘部分將以  `todoContext` 作為範例，而您必須適當更新程式碼片段。請開啟您的內容並加入下列程式碼，將帳戶資料表新增至您的資料模型：

        public DbSet<Account> Accounts { get; set; }

3. 接著，您將設定使用這項資料所需的安全性功能。您將需要適當機制以產生新的長加料，且需要將加料密碼雜湊化的能力，和比較兩個雜湊的安全方式。建立名為  `CustomLoginProviderUtils` 的類別，然後為其新增下列方法：


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }


## <a name="register-endpoint"></a>建立註冊端點

至此，建立使用者帳戶的各項準備皆已完成。在本節中，您將設定註冊端點以處理新的註冊要求。您將在此處強制執行新的使用者名稱和密碼原則，並確保使用者名稱不會遭到取用。接著，您會將使用者資訊安全地儲存在資料庫中。

1. 建立一個物件以代表傳入的註冊嘗試：

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    如果您要在註冊時收集其他資訊，您可以將其納入此物件中。

1. 在您的行動服務後端專案中，新增名為 CustomRegistrationController 的新自訂控制器，並貼到下列程式碼中：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomRegistrationController : ApiController
        {
            public ApiServices Services { get; set; }

            // POST api/CustomRegistration
            public HttpResponseMessage Post(RegistrationRequest registrationRequest)
            {
                if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
                }
                else if (registrationRequest.password.Length < 8)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
                }
	
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
                if (account != null)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
                }
                else
                {
                    byte[] salt = CustomLoginProviderUtils.generateSalt();
                    Account newAccount = new Account
                    {
                        Id = Guid.NewGuid().ToString(),
                        Username = registrationRequest.username,
                        Salt = salt,
                        SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
                    };
                    context.Accounts.Add(newAccount);
                    context.SaveChanges();
                    return this.Request.CreateResponse(HttpStatusCode.Created);
                }
            }
        }   

    請用下列程式碼修飾控制器，以確保您會允許所有對此端點的流量：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>建立 LoginProvider

 `LoginProvider` 是行動服務驗證管線中的基礎建構之一。在本節中，您將建立自己的  `CustomLoginProvider`。它並不會像內建提供者一樣插入管線中，但會為您提供方便的功能。

1. 建立新類別  `CustomLoginProvider`，此類別衍生自  `LoginProvider`：

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       `LoginProvider` 有其他三種抽象方法，您將在稍後加以實作。

2. 建立名為  `CustomLoginProviderCredentials` 的新類別。這代表使用者的相關資訊，將透過  `ServiceUser.getIdentitiesAsync()` 在後端上供您使用。如果您要新增自訂宣告，請確定這些宣告可從這個物件中擷取。

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3. 將抽象方法  `ConfigureMiddleware` 的下列實作方式新增至  `CustomLoginProvider`。這是一個無作業方法，因為  `CustomLoginProvider` 不會與驗證管線整合。

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4. 將抽象方法  `ParseCredentials` 的下列實作方式新增至  `CustomLoginProvider`。此方法將使後端能夠從傳入的驗證權杖將使用者資訊還原序列化。

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }


5. 將抽象方法  `CreateCredentials` 的下列實作方式新增至  `CustomLoginProvider`。此方法會將  `ClaimsIdentity` 轉譯為在驗證權杖發行階段使用的  `ProviderCredentials` 物件。此時，您可以再次擷取任何其他宣告。

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

## <a name="login-endpoint"></a>建立登入端點

系統會根據資料庫檢查您所接收到的使用者名稱和密碼；方法是套用使用者的加料 (salt)、將密碼雜湊化，然後確定傳入的值符合資料庫的值。如果符合，您即可建立  `ClaimsIdentity`，並將其傳至  `CustomLoginProvider`。接著，用戶端應用程式將會接收使用者識別碼和驗證權杖，以供進一步存取您的行動服務之用。

1. 在您的行動服務後端專案中建立一個物件，以代表傳入的登入嘗試：

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

1. 新增名為  `CustomLoginController` 的新自訂控制器，並貼到下列程式碼中：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    請用下列程式碼修飾控制器，以確保您會允許所有對此端點的流量：

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.NOTE] 您用於生產環境的  `CustomLoginController` 也應包含暴力密碼破解攻擊偵測策略。否則，您的登入解決方案可能會容易受到攻擊。

## <a name="require-authentication"></a>將行動服務設定為需要驗證

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## <a name="test-login"></a>使用測試用戶端測試登入流程

在您的用戶端應用程式中，您將必須開發自訂登入畫面，以擷取使用者名稱和密碼，並將其以 JSON 裝載的形式傳送至您的註冊和登入端點。要完成此教學課程，您只需使用行動服務 .NET 後端的內建測試用戶端。

>[AZURE.NOTE] 行動服務 SDK 會透過 HTTPS 與服務通訊。如果您要透過直接 REST 呼叫存取此端點，您必須確實使用 HTTPS 呼叫您的行動服務，因為密碼會以純文字的形式傳送。

1. 在 Visual Studio 中，以滑鼠右鍵按一下專案，然後選取 **[偵錯] -> [開始新執行個體]**，以啟動行動服務後端專案新的偵錯執行個體。

    ![][0]

2. 按一下 [**立即試用**]

    ![][1]

3. 選取您的註冊端點。您可以在某個基本文件中檢視您的 API。按一下 [**試試看**]。

    ![][2]

4. 在本文中，使用與您先前指定的準則相符的使用者名稱和密碼取代範例字串。然後按一下 [**傳送**]。回應應為 **201/Created**。

    ![][3]

5. 為您的登入端點重複此程序。在傳送您先前註冊的相同使用者名稱和密碼之後，您應該會接收到使用者識別碼和驗證權杖。

    ![][4]


<!-- Anchors. -->
[設定帳戶資料表]: #table-setup
[建立註冊端點]: #register-endpoint
[建立 LoginProvider]: #login-provider
[建立登入端點]: #login-endpoint
[將行動服務設定為需要驗證]: #require-authentication
[使用測試用戶端測試登入流程]: #test-login


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[使用者入門]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started

<!--HONumber=42-->
