<properties
	pageTitle="如何使用適用於行動應用程式的 .NET 後端伺服器 SDK | Azure App Service"
	description="了解如何使用適用於 Azure App Service 行動應用程式的 .NET 後端伺服器 SDK。"
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
	ms.date="09/18/2015"
	ms.author="glenga"/>

# 使用適用於 Azure 行動應用程式的 .NET 後端伺服器 SDK

本主題說明如何在主要的 Azure App Service 行動應用程式案例中使用 .NET 後端伺服器 SDKAzure 行動應用程式 SDK 可協助您從 ASP.NET 應用程式使用行動用戶端。

## 做法：下載並初始化 SDK

SDK 可於 [NuGet.org] 取得。此封裝包含開始使用 SDK 所需的基本功能。若要初始化 SDK，您需要在 **HttpConfiguration** 物件上執行動作。

###安裝 SDK

若要安裝 SDK，以滑鼠右鍵按一下 Visual Studio 中的伺服器專案，選取 [管理 NuGet 封裝]，搜尋 [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 封裝，然後按一下 [安裝]。

###初始化伺服器專案

初始化 .NET 後端伺服器專案的方式類似其他 ASP.NET 專案，可藉由包含 OWIN 啟動類別來完成。若要在 Visual Studio 中新增這個類別，可使用滑鼠右鍵按一下伺服器專案、選取 [新增] -> [新項目]，然後依序選取 [Web] -> [一般] -> [OWIN 啟動類別]。

這將會產生具有下列屬性的類別：

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

在 OWIN 啟動類別的 `Configuration()` 方法中，使用 **HttpConfiguration** 物件來設定伺服器專案，該物件代表服務的組態選項。下列範例會初始化伺服器專案，不新增任何功能：

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

若要啟用個別功能，您必須在呼叫 **ApplyTo** 之前，於 **MobileAppConfiguration** 物件上呼叫擴充方法。例如，下列程式碼會在初始化期間，將預設路由新增到所有 API 控制器：

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

許多功能的擴充方法都可透過其他您可以包含的 NuGet 封裝提供使用，於下節中說明。從 Azure 入口網站的伺服器快速入門呼叫 **UseDefaultConfiguration()**。此命令相當於下列設定：
    
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

下列 NuGet 型擴充套件提供了許多您應用程式可以使用的行動功能。您可以使用 **MobileAppConfiguration** 物件，在初始化期間啟用擴充套件。

- [Microsoft.Azure.Mobile.Server.Quickstart] 支援基本的 Mobile Apps 設定。在初始化期間，透過呼叫 **UseDefaultConfiguration** 擴充方法來新增到組態。此延伸模組包含下列延伸模組：通知、驗證、實體、資料表，Crossdomain 與首頁封裝。這就相當於您從 Azure 入口網站下載的快速入門伺服器專案。

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 實作網站根目錄的預設 [此行動裝置應用程式已啟動並執行中] 頁面。透過呼叫 **AddMobileAppHomeController** 擴充方法來新增到組態。

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 包含適用於處理資料與設定資料管線的類別。透過呼叫 **AddTables** 擴充方法來新增到組態。

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) 讓 Entity Framework 能存取 SQL Database 中的資料。透過呼叫 **AddTablesWithEntityFramework** 擴充方法來新增到組態。

- [Microsoft.Azure.Mobile.Server.Authentication] 啟用驗證，並設定用來驗證權杖的 OWIN 中介軟體。透過呼叫 **AddAppServiceAuthentication** 與 **IAppBuilder**.**UseMobileAppAuthentication** 擴充方法來新增到組態。

- [Microsoft.Azure.Mobile.Server.Notifications] 啟用推播通知，並定義推播註冊端點。透過呼叫 **AddPushNotifications** 擴充方法來新增到組態。

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 建立從行動裝置應用程式提供資料給舊版網頁瀏覽器的控制器。透過呼叫 **MapLegacyCrossDomainController** 擴充方法來新增到組態。

## 做法：定義自訂 API 控制器

自訂 API 控制器透過公開端點，提供最基本的功能給您的行動應用程式後端。自訂 API 控制器

1. 在 Visual Studio 中，以滑鼠右鍵按一下 [Controllers] 資料夾，然後按一下 [新增] > [控制器]、選取 [Web API 2 控制器&mdash;空的]，然後按一下 [新增]。

2. 提供**控制器名稱** (例如 `CustomController`)，然後按一下 [新增]。這會建立繼承自 **ApiController** 的新 **CustomController** 類別。

3. 在新的控制器類別檔案中，新增下列 Using 陳述式：

		using Microsoft.Azure.Mobile.Server.Config;

4. 將 **MobileAppControllerAttribute** 套用到 API 控制器類別定義，如下列範例所示：

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. 在 App\_Start/Startup.MobileApp.cs 檔案中，新增對 **MapApiControllers** 擴充方法的呼叫，如下列範例所示：

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	請注意，如果您改為呼叫 **UseDefaultConfiguration** (這會初始化所有功能)，則不需呼叫 **MapApiControllers**。

用戶端仍然可以存取任何沒有套用 **MobileAppControllerAttribute** 的控制器，但是使用任何行動裝置應用程式用戶端 SDK 的用戶端將無法正確取用。

## 做法：定義資料表控制器

資料表控制器提供資料表型資料存放區 (例如 SQL Database 或 Azure 資料表儲存體) 中實體資料的存取權。資料表控制器是繼承自 **TableController** 泛型類別，其中泛型類別是模型中代表資料表結構描述的實體，如下所示：

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

透過使用 **AddTables** 擴充方法，可初始化資料表控制器。下列範例會初始化使用 Entity Framework 進行資料存取的資料表控制器：

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
如需使用存取 Azure SQL Database 資料之資料表控制器的範例，請參閱從 Azure 入口網站下載之快速入門伺服器專案中的 **TodoItemController** 類別。

## 做法：將驗證新增至伺服器專案

您可以透過擴充 **MobileAppConfiguration** 物件並設定 OWIN 中介軟體，將驗證新增至您的伺服器專案。安裝 [Microsoft.Azure.Mobile.Server.Quickstart] 封裝並呼叫 **UseDefaultConfiguration** 擴充方法時，您可以跳到步驟 3。

1. 在 Visual Studio 中安裝 [Microsoft.Azure.Mobile.Server.Authentication] 封裝。 

2. 在 Startup.cs 專案檔案中，在 **Configuration** 方法的開頭新增下列程式碼行：

		app.UseMobileAppAuthentication(config);

	這樣會新增讓您的 Azure 行動應用程式可驗證相關聯 App Service 閘道器發出之權杖的 OWIN 中介軟體元件。

3. 將 `[Authorize]` 屬性新增至任何需要驗證的控制器或方法。現在必須驗證使用者才能存取該端點或特定 API。

若要了解如何在您的 Mobile Apps 後端驗證用戶端，請參閱[將驗證新增至您的應用程式](app-service-mobile-dotnet-backend-ios-get-started-users.md)。

## 做法：將推播通知新增至伺服器專案

您可以透過擴充 **MobileAppConfiguration** 物件並建立通知中樞用戶端，來新增推播通知。安裝 [Microsoft.Azure.Mobile.Server.Quickstart] 封裝並呼叫 **UseDefaultConfiguration** 擴充方法時，您可以跳到步驟 3。

1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [管理 NuGet 封裝]、尋找 Microsoft.Azure.Mobile.Server.Notifications，然後按一下 [安裝]。這會安裝 [Microsoft.Azure.Mobile.Server.Notifications] 封裝。
 
3. 重複此步驟以安裝 `Microsoft.Azure.NotificationHubs` 封裝，其會包含通知中樞用戶端程式庫。

2. 在 App\_Start/Startup.MobileApp.cs 中，於初始化期間新增對 **AddPushNotifications** 擴充方法的呼叫，如下所示：

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

目前您可以使用「通知中樞」用戶端將推播通知傳送到已註冊的裝置。如需詳細資訊，請參閱[將推播通知新增至應用程式](app-service-mobile-ios-get-started-push.md)。若要深入了解您可以使用通知中樞執行的所有功能，請參閱[通知中樞概觀](../notification-hubs/notification-hubs-overview.md)。

## 做法：發行伺服器專案

使用下列步驟將伺服器專案發行至 Azure：

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Nov15_HO1-->