<properties
	pageTitle="如何使用適用於行動應用程式的 .NET 後端伺服器 SDK | Azure App Service"
	description="了解如何使用適用於 Azure App Service 行動應用程式的 .NET 後端伺服器 SDK。"
	keywords="App Service, Azure App Service, 行動應用程式, 行動服務, 級別, 可調整, 應用程式部署, Azure 應用程式部署"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/24/2016"
	ms.author="glenga"/>

# 使用適用於 Azure 行動應用程式的 .NET 後端伺服器 SDK

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何在主要的 Azure App Service 行動應用程式案例中使用 .NET 後端伺服器 SDKAzure 行動應用程式 SDK 可協助您從 ASP.NET 應用程式使用行動用戶端。

>[AZURE.TIP] [適用於 Azure Mobile Apps 的 .NET 伺服器 SDK](https://github.com/Azure/azure-mobile-apps-net-server) 是在 GitHub 上的開放原始碼。儲存機制包含整個伺服器 SDK 單元測試組件以及一些範例專案。

## 參考文件

伺服器 SDK 的參考文件位於此處：[Azure Mobile Apps .NET 參考資料](https://msdn.microsoft.com/library/azure/dn961176.aspx)。

## <a name="create-app"></a>如何：為您的行動應用程式建立 .NET 後端

如果您開始新的專案，您可以使用 [Azure 入口網站] 或 Visual Studio，建立 App Service 應用程式。本章節將協助您使用其中一個項目來建立新的行動應用程式後端，它會裝載簡單的待辦事項清單 API。您可以在本機執行此作業，或將專案發佈至雲端架構 App Service 行動應用程式。

如果您將行動功能新增至現有的專案，請參閱以下的[下載並初始化 SDK](#install-sdk) 一節。

### 使用 Azure 入口網站建立 .NET 後端

您可以在 [Azure 入口網站]中直接建立新的行動應用程式。您可以遵循下列步驟，或者依照[建立行動應用程式](app-service-mobile-ios-get-started.md)教學課程，一起建立新的用戶端和伺服器。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9.回到 [開始] 刀鋒視窗，在 [建立資料表 API] 底下，選擇 [C#] 作為您的 [後端語言]。

&nbsp;&nbsp;10.按一下 [下載]，將壓縮的專案檔案解壓縮至您的本機電腦，並在 Visual Studio 中開啟方案。

### 使用 Visual Studio 2013 和 Visual Studio 2015 建立 .NET 後端

若要在 Visual Studio 中建立 Mobile Apps 專案，您必須安裝 [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.8.1 版或更新版本。當您安裝 SDK 之後，建立新的 ASP.NET 應用程式：

1. 開啟 [新增專案] 對話方塊 (從 [檔案] > [新增] > [專案...])。

2. 展開 [範本] > [Visual C#]，然後選取 [Web]。

3. 選取 [ASP.NET Web 應用程式]。

4. 填入專案名稱。然後按一下 [確定]。

5. 在 [ASP.NET 4.5.2 範本] 底下，選取 [Azure 行動應用程式]。核取 [雲端中的主機] 以在雲端 (您可以在其中發佈這個專案) 中建立新的行動應用程式。

6. 按一下 [確定]。您的應用程式會建立並且在 [方案總管] 中顯示。

## <a name="install-sdk"></a>如何：下載並初始化 SDK

SDK 可於 [NuGet.org] 取得。此封裝包含開始使用 SDK 所需的基本功能。若要初始化 SDK，您需要在 **HttpConfiguration** 物件上執行動作。

###安裝 SDK

若要安裝 SDK，請以滑鼠右鍵按一下 Visual Studio 中的伺服器專案，選取 [管理 NuGet 封裝]，搜尋 [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 封裝，然後按一下 [安裝]。

###<a name="server-project-setup"></a> 初始化伺服器專案

初始化 .NET 後端伺服器專案的方式類似其他 ASP.NET 專案，可藉由包含 OWIN 啟動類別來完成。請確定您已參考 NuGet 封裝 `Microsoft.Owin.Host.SystemWeb`。若要在 Visual Studio 中新增這個類別，請以滑鼠右鍵按一下伺服器專案，選取 [新增] > [新項目]，然後依序選取 [Web] > [一般] > [OWIN 啟動類別]。

這將會產生具有下列屬性的類別：

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

在 OWIN 啟動類別的 `Configuration()` 方法中，使用 **HttpConfiguration** 物件來設定伺服器專案，該物件代表服務的設定選項。下列範例會初始化伺服器專案，不新增任何功能：

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

若要啟用個別功能，您必須在呼叫 **ApplyTo** 之前，於 **MobileAppConfiguration** 物件上呼叫擴充方法。例如，下列程式碼會在初始化期間，將預設路由加入具有屬性 `[MobileAppController]` 的所有 API 控制器中：

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

請注意，`MapApiControllers` 只會對應具有屬性 `[MobileAppController]` 的控制器。

許多功能的擴充方法都可透過其他您可以包含的 NuGet 封裝提供使用，於下節中說明。

Azure 入口網站的伺服器快速入門會呼叫 **UseDefaultConfiguration()**。此命令相當於下列設定：
    
		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### SDK 延伸模組

下列 NuGet 型擴充套件提供了許多您應用程式可以使用的行動功能。您可以使用 **MobileAppConfiguration** 物件，在初始化期間啟用擴充功能。

- [Microsoft.Azure.Mobile.Server.Quickstart] 支援基本的 Mobile Apps 設定。在初始化期間，透過呼叫 **UseDefaultConfiguration** 擴充方法來加入設定中。此延伸模組包含下列延伸模組：通知、驗證、實體、資料表，Crossdomain 與首頁封裝。這就相當於您從 Azure 入口網站下載的快速入門伺服器專案。

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 實作網站根目錄的預設 [此行動應用程式已啟動並執行中] 頁面。透過呼叫 **AddMobileAppHomeController** 擴充方法來加入設定中。

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 包含適用於處理資料與設定資料管線的類別。透過呼叫 **AddTables** 擴充方法來加入設定中。

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) 讓 Entity Framework 能存取 SQL Database 中的資料。透過呼叫 **AddTablesWithEntityFramework** 擴充方法來加入設定中。

- [Microsoft.Azure.Mobile.Server.Authentication] 啟用驗證，並設定用來驗證權杖的 OWIN 中介軟體。透過呼叫 **AddAppServiceAuthentication** 與 **IAppBuilder**.**UseAppServiceAuthentication** 擴充方法來加入設定中。

- [Microsoft.Azure.Mobile.Server.Notifications] 啟用推播通知，並定義推播註冊端點。透過呼叫 **AddPushNotifications** 擴充方法來加入設定中。

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 建立從行動應用程式提供資料給舊版網頁瀏覽器的控制器。透過呼叫 **MapLegacyCrossDomainController** 擴充方法來加入設定中。

- [Microsoft.Azure.Mobile.Server.Login] 透過 AppServiceLoginHandler.CreateToken() 方法提供自訂驗證的預覽支援。這是靜態方法，不需要在組態中啟用。

## <a name="publish-server-project"></a>做法：發佈伺服器專案

本節說明如何從 Visual Studio 發佈 .NET 後端專案。您也可以使用 Git 或 [Azure App Service 部署文件](../app-service-web/web-site-deploy.md)中涵蓋的其他任何方法，來部署您的後端專案。

1. 在 Visual Studio 中，重新建置專案以還原 NuGet 封裝。

2. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後按一下 [發佈]。第一次發佈時，您必須定義發行設定檔。在定義設定檔後，您可以選取該設定檔，然後按一下 [發佈]。

2. 如果系統要求您選取發佈目標，請按一下 [Microsoft Azure App Service] > [下一步]，然後如有需要，使用您的 Azure 認證登入。Visual Studio 會直接從 Azure 下載並安全地儲存您的發佈設定。

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. 選擇您的 [訂用帳戶]，從 [檢視] 中選取 [資源類型]，展開 [行動應用程式]，按一下您的行動應用程式後端，然後按一下 [確定]。

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. 驗證發行設定檔資訊，然後按一下 [發佈]。

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	當您的行動應用程式後端已成功發佈後，您會看到表示成功的登陸頁面。

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## 做法：定義資料表控制器

資料表控制器提供資料表型資料存放區 (例如 SQL Database 或 Azure 資料表儲存體) 中實體資料的存取權。資料表控制器是繼承自 **TableController** 泛型類別，其中泛型類別是模型中代表資料表結構描述的實體，如下所示：

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

透過使用 **AddTables** 擴充方法，可初始化資料表控制器。這會將路由加入 `TableController` 所有子類別的 `/tables/` 底下。

下列範例會初始化使用 Entity Framework 進行資料存取的資料表控制器：

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
如需使用 Entity Framework 存取 Azure SQL Database 資料之資料表控制器的範例，請參閱從 Azure 入口網站下載之快速入門伺服器專案中的 **TodoItemController** 類別。

## 做法：定義自訂 API 控制器

自訂 API 控制器透過公開端點，提供最基本的功能給您的行動應用程式後端。您可以使用屬性 [MobileAppController] 來註冊行動裝置特定 API 控制器。這個屬性會註冊路由，也會設定 Mobile Apps JSON 序列化程式。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 [控制器] 資料夾，然後按一下 [加入] > [控制器]，選取 [Web API 2 控制器&mdash;空白]，然後按一下 [加入]。

2. 提供 [控制器名稱] \(例如 `CustomController`)，然後按一下 [加入]。這會建立繼承自 **ApiController** 的新 **CustomController** 類別。

3. 在新的控制器類別檔案中，新增下列 Using 陳述式：

		using Microsoft.Azure.Mobile.Server.Config;

4. 將 **[MobileAppController]** 套用到 API 控制器類別定義，如下列範例所示：

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. 在 App\_Start/Startup.MobileApp.cs 檔案中，加入對 **MapApiControllers** 擴充方法的呼叫，如下列範例所示：

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	請注意，如果您改為呼叫 **UseDefaultConfiguration** (這會初始化所有功能)，則不需呼叫 **MapApiControllers**。

用戶端仍然可以存取任何沒有套用 **MobileAppControllerAttribute** 的控制器，但是使用任何行動應用程式用戶端 SDK 的用戶端可能會無法正確取用。

## 做法：使用驗證

Mobile Apps 會使用 App Service 驗證和 ASP.NET 的功能，簡化為您的應用程式啟用驗證的程序。本節說明如何在 .NET 後端伺服器專案中執行下列驗證相關工作：

+ [做法：將驗證新增至伺服器專案](#add-auth) 
+ [做法：針對應用程式使用自訂驗證](#custom-auth) 
+ [做法：擷取已驗證的使用者資訊](#user-info)

### <a name="add-auth"></a>做法：將驗證新增至伺服器專案

您可以透過擴充 **MobileAppConfiguration** 物件並設定 OWIN 中介軟體，將驗證加入您的伺服器專案中。安裝 [Microsoft.Azure.Mobile.Server.Quickstart] 封裝並呼叫 **UseDefaultConfiguration** 擴充方法時，您可以跳到步驟 3。

1. 在 Visual Studio 中，安裝 [Microsoft.Azure.Mobile.Server.Authentication] 封裝。 

2. 在 Startup.cs 專案檔案中，在 **Configuration** 方法的開頭加入下列程式碼行：

		app.UseAppServiceAuthentication(config);

	這樣會新增讓您的 Azure 行動應用程式可驗證相關聯 App Service 閘道器發出之權杖的 OWIN 中介軟體元件。

3. 將 `[Authorize]` 屬性加入任何需要驗證的控制器或方法中。現在必須驗證使用者才能存取該端點或特定 API。

若要了解如何在您的 Mobile Apps 後端驗證用戶端，請參閱[將驗證新增至您的應用程式](app-service-mobile-ios-get-started-users.md)。

### <a name="custom-auth"></a>如何：針對應用程式使用自訂驗證

如果您不想要使用其中一個 App Service 驗證/授權提供者，您可以選擇提供自己的登入系統。若要這樣做，請安裝 [Microsoft.Azure.Mobile.Server.Login] 封裝。

您必須提供您自己的邏輯來判斷使用者是否應該登入。例如，您可以針對資料庫中的 salted 和雜湊密碼進行檢查。在下列範例中，`isValidAssertion()` 方法會負責這些檢查，並在其他地方定義。

自訂驗證會公開，方法是建立新的 ApiController 並且公開如下的註冊和登入動作。用戶端可以嘗試登入，方法是向使用者收集相關資訊，然後將 HTTPS POST 提交給在本文中具有使用者資訊的 API。伺服器驗證這項資訊之後，可以使用 `AppServiceLoginHandler.CreateToken()` 方法發行權杖。

可能的範例登入動作為：

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

在上述範例中，LoginResult 和 LoginResultUser 只是公開顯示屬性的簡單物件。用戶端預期有以下格式的 JSON 物件的登入回應：

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

`MobileAppLoginHAppServiceLoginHandlerandler.CreateToken()` 方法包含 _audience_ 和 _issuer_ 參數。這兩個參數通常會使用 HTTPS 配置設定為應用程式根目錄的 URL。同樣地，您應該將 _secretKey_ 設定為您應用程式的簽署金鑰值。這是機密值，永遠不應共用或包含於用戶端。您可以藉由參考 _WEBSITE\_AUTH\_SIGNING\_KEY_ 環境變數，在裝載於 App Service 時取得這個值。如果在本機偵錯內容中有需要，請依照[使用驗證進行本機偵錯](#local-debug)一節中的指示以擷取金鑰，並將它儲存為應用程式設定。

您也必須提供已發行權杖的存留期，以及您想要包含的任何宣告。您必須提供主體宣告，如範例程式碼所示。

您也可以改為只使用 `loginAsync()` 方法 (名稱可能會視平台不同而改變) 來簡化用戶端程式碼，而不使用手動 HTTP POST 方式。您也可以使用接受額外權杖參數 (與您要 POST 的判斷提示物件關聯) 的多載。此案例中的提供者應該為您選擇的自定名稱。接著在伺服器上，您的登入動作應該會位於包含此自定名稱的路徑 _/.auth/login/{customProviderName}_ 上。若要將控制器置於此路徑，請在套用 MobileAppConfiguration 之前新增指向 HttpConfiguration 的路由。

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" }); 
		
使用裝載登入動作之控制器的名稱取代上述的 "CustomAuth" 字串。

>[AZURE.TIP] 使用 loginAsync() 方法以確保驗證權杖會附加至後續對服務的呼叫。

###<a name="user-info"></a>做法：擷取已驗證的使用者資訊

當 App Service 驗證使用者時，您可以存取指派的使用者識別碼及其他 .NET 後端程式碼中的資訊。這可用來在後端為指定的使用者進行授權決策，例如特定的使用者是否可以存取資料表資料列或其他資源。下列程式碼說明如何取得已登入使用者的使用者識別碼：

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID 衍生自提供者特定的使用者識別碼，且對指定的使用者和登入提供者而言都是靜態的。

App Service 也可讓您向登入提供者要求特定宣告。這可讓您向提供者要求更多資訊，例如藉由使用 Facebook 圖形 API。您可以在入口網站的提供者刀鋒視窗中指定宣告。某些宣告需要搭配提供者的額外設定。

下列程式碼會呼叫 **GetAppServiceIdentityAsync** 擴充方法以取得登入認證，其中包含對 Facebook 圖形 API 提出要求所需的存取權杖：

    // Get the credentials for the logged-in user.
    var credentials = 
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token=" 
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

請注意，您必須加入 `System.Security.Principal` 的 using 陳述式，**GetAppServiceIdentityAsync** 擴充方法才能運作。


## 做法：將推播通知新增至伺服器專案

您可以透過擴充 **MobileAppConfiguration** 物件並建立通知中樞用戶端，將推播通知加入您的伺服器專案中。安裝 [Microsoft.Azure.Mobile.Server.Quickstart] 封裝並呼叫 **UseDefaultConfiguration** 擴充方法時，您可以往下跳到步驟 3。

1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [管理 NuGet 封裝]，搜尋 Microsoft.Azure.Mobile.Server.Notifications，然後按一下 [安裝]。這會安裝 [Microsoft.Azure.Mobile.Server.Notifications] 封裝。
 
3. 重複這個步驟以安裝 `Microsoft.Azure.NotificationHubs` 封裝，其中包含通知中樞用戶端程式庫。

2. 在 App\_Start/Startup.MobileApp.cs 中，於初始化期間加入對 **AddPushNotifications** 擴充方法的呼叫，如下所示：

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	這會在您的伺服器專案中建立推播通知註冊端點。用戶端會使用此端點來註冊相關聯的通知中樞。現在您需要新增用來傳送通知的通知中樞用戶端。

3. 在您想要傳送推播通知的控制器中，新增下列 Using 陳述式：

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. 新增下列建立通知中樞用戶端的程式碼：

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

目前您可以使用「通知中樞」用戶端將推播通知傳送到已註冊的裝置。如需詳細資訊，請參閱[將推播通知新增至您的應用程式](app-service-mobile-ios-get-started-push.md)。若要深入了解您可以使用通知中樞執行的所有功能，請參閱[通知中樞概觀](../notification-hubs/notification-hubs-overview.md)。

##<a name="tags"></a>如何：將標記加入裝置安裝中以啟用推送至標記

在上述的**如何：定義自訂 API 控制器**之後，您會想要在後端設定自訂 API，以使用通知中樞將標記加入特定裝置安裝中。請確定您已傳遞儲存在用戶端本機儲存體的裝置識別碼和您想加入的標記 (可省略，因為您也可以直接在您的後端指定標記)。下列程式碼片段應新增至您的控制器，才能使用通知中樞新增標記至裝置安裝識別碼。

使用 [Azure 通知中樞 Nuget](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)([參考](https://msdn.microsoft.com/library/azure/mt414893.aspx))：

		var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

		hub.PatchInstallation("my-installation-id", new[]
		{
		    new PartialUpdateOperation
		    {
		        Operation = UpdateOperationType.Add,
		        Path = "/tags",
		        Value = "{my-tag}"
		    }
		});

若要推送至這些標記，請使用[通知中樞 API](https://msdn.microsoft.com/library/azure/dn495101.aspx)。

您也可以獨立出自訂 API 以使用通知中樞直接在您的後端上註冊裝置安裝。

## 做法：偵錯和疑難排解 .NET 伺服器 SDK

Azure App Service 提供了數個適用於 ASP.NET 應用程式的偵錯和疑難排解技術：

- [監視 Azure App Service](../app-service-web/web-sites-monitor.md)
- [在 Azure App Service 中啟用診斷記錄](../app-service-web/web-sites-enable-diagnostic-log.md)
- [在 Visual Studio 中疑難排解 Azure App Service](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### 記錄

您可以使用標準的 ASP.NET 追蹤寫入來寫入 App Service 診斷記錄：您必須在行動應用程式後端中啟用診斷，才能寫入至記錄檔。

若要啟用診斷並寫入至記錄檔：

1. 遵循[如何啟用診斷](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag)中的步驟。

2. 在您的程式碼檔案中新增下列 using 陳述式：

		using System.Web.Http.Tracing;

3. 建立從 .NET 後端寫入至診斷記錄的追蹤寫入器，如下所示：

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");  

4. 重新發佈您的伺服器專案，並存取行動應用程式後端，以執行記錄的程式碼路徑。

5. 下載並評估記錄檔，如[做法：下載記錄](../app-service-web/web-sites-enable-diagnostic-log.md#download)中所述。

### <a name="local-debug"></a>使用驗證進行本機偵錯

您可以在將變更發佈至雲端之前，在本機執行您的應用程式以測試變更。對於許多應用程式，在 Visual Studio 中時只需要按 *F5* 即可。不過，使用驗證時有一些其他考量。

您必須擁有雲端式行動應用程式並且已設定 App Service 驗證/授權，而且您的用戶端必須有指定的雲端端點做為替代登入主機。請參閱您所選擇的用戶端平台的文件 ([iOS](app-service-mobile-ios-how-to-use-client-library.md)、[Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)) 以取得所需的特定步驟。

確定您的應用程式已安裝 [Microsoft.Azure.Mobile.Server.Authentication]。然後，在 `MobileAppConfiguration` 已套用至您的 `HttpConfiguration` 之後，在您的應用程式的 OWIN 啟動類別中加入下列項目：
		
		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

在上述範例中，您應該使用 HTTPS 配置，將 Web.config 檔案中的 _authAudience_ 和 _authIssuer_ 應用程式設定，設定為每個應用程式根目錄的 URL。同樣地，您應該將 _authSigningKey_ 設定為您應用程式的簽署金鑰值。這是機密值，永遠不應共用或包含於用戶端。若要取得該值，請在 [Azure 入口網站]中巡覽至您的應用程式，然後按一下 [工具]。然後選取 [Kudu]，再按一下 [移至]。這樣會帶您前往您的網站的 Kudu 管理端點。按一下 [環境] 並且在 _WEBSITE\_AUTH\_SIGNING\_KEY_ 底下尋找值。這是您應該在本機應用程式設定中用於 _authSigningKey_ 的值。

您的本機執行伺服器現在已裝備，可以驗證用戶端從雲端式端點取得的權杖。


[Azure 入口網站]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_0128_2016-->