<properties 
	pageTitle="從行動服務升級為 Azure App Service" 
	description="了解如何輕鬆地將您的行動服務應用程式升級為 App Service 行動應用程式" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="mahender"/>

# 將您現有的 .NET Azure 行動服務升級為 App Service

App Service Mobile 是一種使用 Microsoft Azure 建置行動應用程式的新方式。若要深入了解，請參閱[何謂 Mobile Apps？]。

本主題說明如何將現有的 .NET 後端應用程式從 Azure 行動服務升級為新的 App Service 行動應用程式。執行此升級時，現有的行動服務應用程式可以繼續運作。

當行動後端升級為 Azure App Service 時，就對所有 App Service 功能具有存取權，而且會根據 [App Service 價格]而不是行動服務價格計費。

##移轉與升級

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

###Mobile Apps .NET 伺服器 SDK 中的增強功能

升級為新的 [Mobile Apps SDK NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)提供下列優點：

- NuGet 相依性有更多彈性。裝載環境不再提供它自己的 NuGet 封裝版本，因此您可以使用替代的相容版本。不過，如果行動伺服器 SDK 或相依性有新的重要 Bug 修正或安全性更新，您必須手動更新您的服務。

- 支援新的 App Service 驗證功能，可讓您跨 Web 和行動應用程式使用常見的驗證設定。驗證功能現在也在自己的 OWIN 中介軟體元件中，這表示相同的驗證設定可用於行動和 Web 的路由。

- 支援其他 ASP.NET 專案類型和路由。您現在可以在與行動後端專案相同的專案中裝載 MVC 和 Web API 控制器。

- Mobile SDK 有更多彈性。您可以明確地控制設定哪些功能和路由，例如驗證、資料表 API，以及推播註冊端點。若要深入了解，請參閱[如何使用適用於 Azure Mobile Apps 的 .NET 伺服器 SDK](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup)。

- DI 架構有更多選項。行動服務 .NET 伺服器 SDK 相依於 autofac，但 Mobile Apps 伺服器 SDK 可讓您使用其他架構，例如 [Unity](https://unity.codeplex.com/)。

>[AZURE.NOTE]行動服務用戶端 SDK 與新的 Mobile Apps 伺服器 SDK「不」相容。如果您有行動用戶端應用程式連接到現有的行動服務，您應該讓服務一直執行，直到所有的行動用戶端都已升級為 Mobile Apps **用戶端 SDK** 為止。
> 
> 現有的行動服務可以自動「移轉」到 App Service，不需要對現有的用戶端應用程式做任何變更。若要深入了解移轉，請參閱[將您現有的行動服務移轉至 App Service]。


##<a name="overview"></a>基本升級概觀
最簡單的升級方法，是建立新的 App Service 行動應用程式執行個體。在許多情況下，只需切換到新的 Mobile Apps 伺服器 SDK 並將程式碼重新發佈至新的行動應用程式，即可完成升級。但在某些情況下則需要一些額外的設定，例如進階驗證案例和使用排程工作。下列各節將逐一加以討論。

>[AZURE.NOTE]建議您先閱讀並徹底了解本主題的其餘部分，再開始升級。請記下您使用的下列任何功能。

您可以依照自己的步調移動和測試程式碼。行動應用程式後端準備好時，您可以發行新版的用戶端應用程式。此時，您會有兩份並行執行的應用程式後端。您必須確定您所做的任何錯誤修正皆套用至兩者。最後，在您的使用者更新為最新版本後，您可以刪除原始的行動服務。

此升級的完整步驟如下所示：

1. 建立及設定新的行動應用程式
2. 處理任何驗證考量
3. 發行新版的用戶端應用程式
4. 刪除原始的行動服務執行個體


##<a name="mobile-app-version"></a>設定您應用程式的行動應用程式版本
升級的第一個步驟，是建立將主控新版應用程式的行動應用程式資源。您可以在[預覽 Azure 管理入口網站]中建立新的行動應用程式。您可以查閱[建立行動應用程式]主題，以取得進一步的詳細資訊。

您可能會想要使用與行動服務中相同的資料庫和通知中心。您可以從 [Azure 管理入口網站]之 [行動服務] 區段的 [**設定**] 索引標籤來複製這些值。在 [**連接字串**] 下，複製 `MS_NotificationHubConnectionString` 和 `MS_TableConnectionString`。導覽至您的行動應用程式網站，然後選取 [設定]、[應用程式設定]，並將這些新增至 [連接字串] 區段並覆寫任何現有的值。

行動應用程式有新的[行動應用程式伺服器 SDK]，提供許多與行動服務執行階段相同的功能。首先，您應從現有的專案中移除行動服務 NuGet，並改為包含伺服器 SDK。在此升級中，大部分的開發人員都會想要下載並安裝 `Microsoft.Azure.Mobile.Server.Quickstart` 封裝，因為這會在整個必要集中提取。然後您可以在 WebApiConfig.cs 中將

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

取代為

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]如果您想要深入了解新的 .NET 伺服器 SDK 以及如何從您的應用程式新增/移除功能，請參閱[如何使用 .NET 伺服器 SDK] 主題。

行動服務與 Mobile Apps SDK 之間有一些其他變更，但很容易處理。在整個專案中，您可能需要修改某些 using 陳述式，這時 Visual Studio 將有所幫助。

您需要將 `[MobileAppController]` 屬性新增至所有 ApiControllers，只要將該裝飾項目直接放在類別定義之前即可。

`[AuthorizeLevel]` 屬性已不存在，您應該改用標準 ASP.NET `[Authorize]` 屬性裝飾您的控制器和方法。請注意，沒有 `[AuthorizeLevel]` 的控制器已不再受應用程式金鑰所保護；將會被視為公用。新的 SDK 不再利用應用程式金鑰和主要金鑰。

對於推播，您可能會發現伺服器 SDK 中遺漏的主要項目是 PushRegistrationHandler 類別。註冊在行動應用程式中處理方式稍有不同，依預設會啟用不具標記的註冊。管理標記可使用自訂 API 來完成。如需詳細資訊，請參閱[將推播通知新增至行動應用程式]主題。

Mobile Apps 中並未內建排程工作，因此您在 .NET 後端中的任何現有工作都必須個別升級。其中一個選項是在行動應用程式程式碼網站上建立排程 [Web 工作]。您也可以設定用來保存工作程式碼的控制器，並設定依預期的排程在端點上執行的 [Azure 排程器]。

`ApiServices` 物件不再是 SDK 的一部分。若要存取行動應用程式設定，您可以使用下列：

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

同樣地，現在已使用標準 ASP.NET 追蹤寫入完成記錄︰

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>驗證考量
Mobile Apps 與行動服務之間最大的差異之一在於，在 Mobile Apps 中，登入現在是由 App Service 閘道所處理，而非執行您的程式碼的網站。如果資源群組尚未有閘道器，可以在管理入口網站中瀏覽至您的 Azure 行動應用程式以佈建閘道器。選取 [設定]，然後選取 [行動] 類別下的 [使用者驗證]。按一下 [建立]，讓閘道器與您的行動應用程式產生關聯。

此外，大部分的應用程式都不需要額外的動作，但是有幾個進階案例應特別留意。

大部分的應用程式只要使用目標身分識別提供者的新註冊，即可順利運作，您可以依照[將驗證新增至行動應用程式]教學課程的指示，了解如何將身分識別新增至應用程式服務應用程式。

如果您的應用程式依存於使用者識別碼 (例如將它們儲存在資料庫中)，請務必注意，行動服務和應用程式服務行動應用程式的使用者識別碼是不同的。不過，您可以使用下列項目 (以 Facebook 做為範例)，在您的應用程式服務行動應用程式中取得行動服務使用者識別碼：

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

此外，如果您依存於使用者識別碼，請務必盡可能使用相同的身分識別提供者註冊。使用者識別碼的範圍通常限定在已使用的應用程式註冊，因此引入新的註冊，可能會讓使用者在比對其資料時發生問題。如果您的應用程式因故需要使用相同的身分識別提供者註冊，您可以使用下列步驟：

1. 為您的應用程式所使用的每個提供者，複製用戶端識別碼和用戶端密碼連接資訊。
2. 為每個提供者新增閘道器的 /signin-* 端點，做為額外的重新導向 URI。 

>[AZURE.NOTE]某些提供者 (如 Twitter、Microsoft 帳戶) 不允許您在不同網域上指定多個重新導向 URI。如果您的應用程式使用其中一個提供者，並且依存於使用者識別碼，建議您此時不要嘗試升級。

##<a name="updating-clients"></a>更新用戶端
在您擁有可運作的行動應用程式後端之後，您可以更新用戶端應用程式，以使用新的行動應用程式。行動應用程式也會包含新版的行動服務用戶端 SDK，可以讓開發人員利用新的應用程式服務功能。在您擁有行動應用程式版本的後端之後，您可以發行運用新 SDK 版本的新版用戶端應用程式。

用戶端程式碼所需的主要變更將是建構函式。除了行動應用程式網站的 URL 以外，您還必須提供主控驗證設定的 App Service 閘道的 URL：

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

這可讓用戶端將要求路由傳送至行動應用程式的元件。您可以使用適當的[建立行動應用程式]主題，找到更多目標平台的特定詳細資料。

在相同的更新中，您將必須調整您所做的任何推播通知註冊呼叫。有新的 API 可強化註冊程序 (以 Windows 做為範例)：

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

如需目標平台的特定詳細資料，請參閱[將推播通知新增至行動應用程式]和[傳送跨平台推播通知]主題。

在您的客戶有機會接收這些更新後，您即可刪除應用程式的行動服務版本。現在，您已使用最新的 Mobile Apps 伺服器 SDK 完全升級為 App Service 行動應用程式。

<!-- URLs. -->

[預覽 Azure 管理入口網站]: https://portal.azure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[何謂 Mobile Apps？]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /zh-TW/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[行動應用程式伺服器 SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[建立行動應用程式]: app-service-mobile-xamarin-ios-get-started.md
[將推播通知新增至行動應用程式]: app-service-mobile-xamarin-ios-get-started-push.md
[將驗證新增至行動應用程式]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure 排程器]: /zh-TW/documentation/services/scheduler/
[Web 工作]: ../app-service-web/websites-webjobs-resources.md
[傳送跨平台推播通知]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[如何使用 .NET 伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[將您現有的行動服務移轉至 App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[App Service 價格]: https://azure.microsoft.com/zh-TW/pricing/details/app-service/

<!---HONumber=Nov15_HO3-->