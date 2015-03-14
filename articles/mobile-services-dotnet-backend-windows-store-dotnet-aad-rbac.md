<properties 
	pageTitle="行動服務和 Azure Active Directory 中的角色型存取控制 (Windows 市集) | 行動開發人員中心" 
	description="了解如何在 Windows Store 應用程式中控制以 Azure Active Directory 角色為基礎的存取權。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="wesmc"/>

# 行動服務和 Azure Active Directory 中的角色型存取控制

[AZURE.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]

角色型存取控制 (RBAC) 是對使用者可擔任的角色指派權限的做法，可明確定義特定類別的使用者能夠與無法做什麼的界線。本教學課程將引導您了解如何將基本 RBAC 加入至 Azure 行動服務。

本教學課程將說明角色型存取控制，檢查每個使用者在 Azure Active Directory (AAD) 中定義之「銷售」群組的成員資格。存取檢查將在 .NET 行動服務後端中，使用 Azure Active Directory 的[圖形用戶端程式庫]來執行。只有屬於「銷售」群組的使用者能夠查詢資料。


>[AZURE.NOTE] 本教學課程的目的是要擴充驗證知識以加入授權實務作法。您應先使用 Azure Active Directory 驗證提供者完成[將驗證加入至您的應用程式]教學課程。本教學課程接著將更新[將驗證加入至您的應用程式]教學課程中使用的 TodoItem 應用程式。

本教學課程將逐步引導您完成下列步驟:

1. [建立具有成員資格的銷售群組]
2. [為整合的應用程式產生金鑰]
3. [建立自訂授權屬性] 
4. [將角色型存取檢查新增至資料庫作業]
5. [測試用戶端存取]

本教學課程需要下列各項:

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 使用 Azure Active Directory 驗證提供者完成[將驗證新增至您的應用程式]教學課程。
* 完成[儲存伺服器指令碼]教學課程，以熟悉如何使用 Git 儲存機制來儲存伺服器指令碼。
 


## <a name="create-group"></a>建立具有成員資格的銷售群組

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]


## <a name="generate-key"></a>為整合的應用程式產生金鑰


在進行[將驗證加入至您的應用程式]教學課程期間，您已在完成[註冊使用 Azure Active Directory 登入]步驟時，為整合的應用程式建立註冊。在本節中，您將產生在使用該整合的應用程式用戶端識別碼讀取目錄資訊時所將使用的金鑰。 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-custom-authorization-attribute"></a>在行動服務上建立自訂授權屬性 

在本節中，您將新建可用來對行動服務作業執行存取檢查的自訂授權屬性。屬性將會根據傳至 Active Directory 群組的角色名稱查閱該群組。接著，它會根據該群組的成員資格執行存取檢查。

1. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務.NET 後端專案，然後按一下 [**管理 NuGet 封裝**]。

2. 在 [NuGet Package Manager] 對話方塊中，在搜尋條件中輸入 **ADAL**，以尋找並安裝您的行動服務的 [**Active Directory 驗證程式庫**]。

3. 在 [NuGet Package Manager] 中，同時為您的行動服務安裝 [**Microsoft Azure Active Directory 圖形用戶端程式庫**]。


4. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務專案，然後依序按一下 [**新增**] 和 [**新資料夾**]。將新資料夾命名為 **Utilities**。

5. 在 Visual Studio 中，以滑鼠右鍵按一下新的 **Utilities** 資料夾，然後新增名為 **AuthorizeAadRole.cs** 的類別檔案。

    ![][0]

6. 在 AuthorizeAadRole.cs 檔案中，在檔案最上方新增下列 `using` 陳述式。 

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7. 在 AuthorizeAadRole.cs 中，將下列列舉類型新增至「公用程式」命名空間。在此範例中，我們只會處理 [**銷售**] 角色。其他角色只是您可能使用之群組的範例。

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8. 在 AuthorizeAadRole.cs 中，將下列 `AuthorizeAadRole` 類別定義新增至「公用程式」命名空間。

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9. 在 AuthorizeAadRole.cs 中，將下列 `GetAADToken` 方法新增至  `AuthorizeAadRole` 類別。

    >[AZURE.NOTE] 您應快取權杖，而不要為每個存取檢查建立一個新權杖。接著，請在嘗試使用權杖依照[圖形用戶端程式庫]中的說明擲出 AccessTokenExpiredException 時，重新整理快取。為求單純性，下方的程式碼中並不會說明此作業，但這將可以減輕對您 Active Directory 的額外網路流量。  

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;
            
            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
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

10. 在 AuthorizeAadRole.cs 中，以下列程式碼更新  `AuthorizeAadRole` 類別中的 `OnAuthorization` 方法。此程式碼會使用[圖形用戶端程式庫]查閱對應至角色的 Active Directory 群組。接著，它會檢查使用者在該群組中的成員資格，以授權給使用者。

    >[AZURE.NOTE] 此程式碼會依名稱查閱 Active Directory 群組。在許多情況下，將群組識別碼儲存為行動服務應用程式設定，都會是較理想的作法。這是因為群組名稱可能會變更，但識別碼會保持相同。但是，當群組名稱變更時，角色的領域中通常至少會有一項變更可能也需要行動服務程式碼的更新。  

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
			// through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
						"User is not logged in or not a member of the required group");
            }
        }

8. 將您的變更儲存至 AuthorizeAadRole.cs。

## <a name="add-access-checking"></a>將角色型存取檢查新增至資料庫作業

1. 在 Visual Studio 中，展開行動服務專案下的 **Controllers** 資料夾。開啟 TodoItemController.cs。

2. 在 TodoItemController.cs 中，為您包含自訂授權屬性的公用程式命名空間新增 `using` 陳述式。 

        using todolistService.Utilities;

3. 在 TodoItemController.cs 中，您可以根據檢查存取的方式，將屬性新增至您的控制器類別或個別方法。如果您要讓所有控制器作業根據相同的角色來檢查存取，您只需將屬性新增至類別即可。請依照下列方式將屬性新增至類別，以測試本教學課程。

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    如果您要存取檢查插入、更新和刪除作業，您必須以下列方式，僅設定這些方法的屬性。

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. 儲存 TodoItemController.cs 並建置行動服務，以驗證語法錯誤。
5. 將行動服務發行至您的 Azure 帳戶。


## <a name="test-client"></a>測試用戶端的存取

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]





<!-- Anchors. -->
[建立具有成員資格的銷售群組]: #create-group
[為整合的應用程式產生金鑰]: #generate-key
[建立自訂授權屬性]: #create-custom-authorization-attribute
[將角色型存取檢查新增至資料庫作業]: #add-access-checking
[測試用戶端存取]: #test-client



<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[將驗證加入至您的應用程式]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[將驗證新增至您的應用程式]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[如何向 Azure Active Directory 註冊]: /zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[目錄同步案例]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[儲存伺服器指令碼]: /zh-tw/documentation/articles/mobile-services-store-scripts-source-control/
[註冊以使用 Azure Active Directory 登入]: /zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[圖形用戶端程式庫]: http://go.microsoft.com/fwlink/?LinkId=510536
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx

<!--HONumber=42-->
