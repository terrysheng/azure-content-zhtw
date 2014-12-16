<properties urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="存取 Azure Active Directory Graph 資訊 (Windows 市集) | 行動開發人員中心" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# 存取 Azure Active Directory 圖形資訊

[WACOM.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]



如同其他隨行動服務提供的身分識別提供者，Azure Active Directory (AAD) 提供者也支援豐富的 [Graph 用戶端程式庫]，可用於透過程式設計來存取目錄。在本教學課程中，您會根據使用 [Graph 用戶端程式庫]從目錄中擷取的其他使用者資訊來更新 ToDoList 應用程式，以個人化已驗證的使用者應用程式體驗。

>[AZURE.NOTE] 本教學課程的目的是要擴充您使用 Azure Active Directory 進行驗證的知識。您應已使用 Azure Active Directory 驗證提供者完成[將驗證加入至您的應用程式]教學課程。本教學課程接著將更新[將驗證加入至您的應用程式]教學課程中使用的 TodoItem 應用程式。 



本教學課程將逐步引導您完成下列步驟：


1. [在 AAD 中產生應用程式註冊的存取金鑰] 
2. [建立 GetUserInfo 自訂 API] 
3. [更新應用程式以使用自訂 API]
4. [測試應用程式]

##必要條件 

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [開始使用驗證]<br/>將登入需求新增至 TodoList 範例應用程式。

+ [自訂 API 教學課程]<br/>說明如何呼叫自訂 API。 



## <a name="generate-key"></a>在 AAD 中產生應用程式註冊的存取金鑰


在進行[將驗證加入至您的應用程式]教學課程期間，您已在完成[註冊使用 Azure Active Directory 登入]步驟時，為整合的應用程式建立註冊。在本節中，您將產生在使用該整合的應用程式用戶端識別碼讀取目錄資訊時所將使用的金鑰。 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>建立 GetUserInfo 自訂 API

在本節中，您將建立 GetUserInfo 自訂 API，以使用 [Graph 用戶端程式庫]從 AAD 擷取使用者的其他相關資訊。

如果您未曾搭配使用自訂 API 與行動服務，請先參閱[自訂 API 教學課程]，再開始執行本節步驟。

1. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務.NET 後端專案，然後按一下 [**管理 NuGet 封裝**]。
2. 在 [NuGet 封裝管理員] 對話方塊中，在搜尋條件中輸入 **ADAL**，以尋找並安裝您的行動服務的 [**Active Directory Authentication Library**]。
3. 在 [NuGet 封裝管理員] 中，同時為您的行動服務安裝 [**Microsoft Azure Active Directory Graph 用戶端程式庫**]。

4. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務專案的 **Controllers** 資料夾，然後按一下 [**加入**]，加入新的 **Microsoft Azure 行動服務自訂控制器**，並命名為 `GetUserInfoController`。用戶端會呼叫此 API，從 Active Directory 中取得使用者資訊。

5. 在新的 GetUserInfoController.cs 檔案中，新增下列 `using` 陳述式。

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. 在 GetUserInfoController.cs 中，將下列 `GetAADToken` 方法新增至類別。

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    此方法會使用您在 [Azure 管理入口網站]中為行動服務設定的應用程式設定，取得用來存取 Active Directory 的權杖。

7. 在 GetUserInfoController.cs 中，將下列 `GetAADUser` 方法新增至類別。

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    此方法會取得授權使用者的 Active Directory 物件識別碼，然後使用圖形用戶端程式庫從 Active Diretory 中取得使用者的資訊。


8. 在 GetUserInfoController.cs 中，以下列方法取代 `Get` 方法。此方法會傳回 Graph 用戶端程式庫的 `User` 物件，並要求授權使用者呼叫 API。

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. 儲存變更，並建置服務以驗證沒有語法錯誤。
10. 將行動服務專案發行至您的 Azure 帳戶。 


## <a name="update-app"></a>更新應用程式以使用 GetUserInfo

在本節中，您會更新您在[將驗證加入至您的應用程式]教學課程中實作的 `AuthenticateAsync` 方法，以呼叫自訂 API 並從 AAD 傳回使用者的其他相關資訊。 

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>測試應用程式

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>後續步驟

在下一個教學課程[行動服務中使用 AAD 的角色型存取控制]中，您將會搭配使用角色型存取控制與 Azure Active Directory (AAD) 來檢查群組成員資格，然後允許存取。 



<!-- Anchors. -->
[在 AAD 中產生應用程式註冊的存取金鑰]: #generate-key
[建立 GetUserInfo 自訂 API]: #create-api
[更新應用程式以使用自訂 API]: #update-app
[測試應用程式]: #test-app
[後續步驟]:#next-steps

<!-- Images -->


<!-- URLs. -->
[將驗證新增至您的應用程式]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[如何向 Azure Active Directory 註冊]: /zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[自訂 API 教學課程]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[儲存伺服器指令碼]: /zh-tw/documentation/articles/mobile-services-store-scripts-source-control/
[註冊以使用 Azure Active Directory 登入]: /zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph 用戶端程式庫]: http://go.microsoft.com/fwlink/?LinkId=510536
[取得使用者]: http://msdn.microsoft.com/zh-tw/library/azure/dn151678.aspx
[行動服務中使用 AAD 的角色型存取控制]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
