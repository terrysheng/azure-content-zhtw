<properties 
	pageTitle="從行動服務移轉到應用程式服務行動應用程式" 
	description="了解如何輕鬆地將您的行動服務應用程式移轉至應用程式服務行動應用程式" 
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
	ms.date="08/11/2015" 
	ms.author="mahender"/>

# 將您現有的 Azure 行動服務移轉至 Azure 應用程式服務行動應用程式

本主題說明如何將現有的應用程式從 Azure 行動服務移轉至新的應用程式服務行動應用程式。所有現有的行動服務應用程式都可以輕鬆地移轉至新的應用程式服務行動應用程式。在移轉期間，現有的行動服務應用程式可以繼續運作。經過一段時間，移轉程序將會變得更加簡單，但是想要立即移轉的人，可以使用下列步驟。

>[AZURE.NOTE]移轉目前僅支援使用行動服務 .NET 後端的客戶。使用 Node.JS 後端的應用程式目前仍須繼續使用行動服務。

##<a name="understand"></a>了解應用程式服務行動應用程式

應用程式服務行動應用程式是使用 Microsoft Azure 建置行動應用程式的新方式。您可以在[什麼是行動應用程式？]主題中進一步了解行動應用程式。

在移轉至行動應用程式時，所有現有的應用程式功能 (和程式碼) 都可保留。此外，新功能可用於應用程式。在行動應用程式模型中，您的程式碼實際上會執行於 Web 應用程式 (新版本的 Azure 網站)。您可以完全掌控 Web 應用程式及其運作方式。此外，過去無法讓行動服務客戶使用的 Web Apps 功能 (例如流量管理員和 Development Slots)，現在都可供使用。

新的模型也能解決使用行動服務的主要難題之一。現在，您可以部署任何版本的任何 NuGet 封裝，而不需擔心相依性衝突。有關移轉有何優點的詳細資訊，可在[我已經使用網站和行動服務 – 應用程式服務對我有何幫助？]主題中找到。

##<a name="overview"></a>基本移轉概觀
最簡單的移轉方法，是建立應用程式服務行動應用程式的新執行個體。在許多情況下，只需切換到新的伺服器 SDK 並將程式碼重新發佈至新的行動應用程式，即可完成移轉。但在某些情況下則需要一些額外的設定，例如進階驗證案例和使用排程工作。下列各節將逐一加以討論。

>[AZURE.NOTE]建議您先閱讀並徹底了解本主題的其餘部分，再開始移轉。請記下您使用的下列任何功能。

您可以依照自己的步調移動和測試程式碼。行動應用程式後端準備好時，您可以發行新版的用戶端應用程式。此時，您會有兩份並行執行的應用程式後端。您必須確定您所做的任何錯誤修正皆套用至兩者。最後，在您的使用者更新為最新版本後，您可以刪除原始的行動服務。

此移轉的完整步驟如下所示：

1. 建立及設定新的行動應用程式
2. 處理任何驗證考量
3. 發行新版的用戶端應用程式
4. 刪除原始的行動服務執行個體


##<a name="mobile-app-version"></a>設定您應用程式的行動應用程式版本
移轉的第一個步驟，是建立將主控新版應用程式的行動應用程式資源。您可以在[預覽 Azure Preview 入口網站]中建立新的行動應用程式。您可以查閱[建立行動應用程式]主題，以取得進一步的詳細資訊。

您可能會想要使用與行動服務中相同的資料庫和通知中心。您可以從 [Azure 管理入口網站]之 [行動服務] 區段的 [設定] 索引標籤來複製這些值。在 [連接字串] 下，複製 `MS_NotificationHubConnectionString` 和 `MS_TableConnectionString`。導覽至您的行動應用程式網站，然後選取 [設定]、[應用程式設定]，並將這些新增至 [連接字串] 區段並覆寫任何現有的值。

行動應用程式有新的[行動應用程式伺服器 SDK]，提供許多與行動服務執行階段相同的功能。首先，您應從現有的專案中移除行動服務 NuGet，並改為包含伺服器 SDK。在此移轉中，大部分的開發人員都會想要下載並安裝 `Microsoft.Azure.Mobile.Server.Quickstart` 封裝，因為這會在整個必要集中提取。然後您可以在 WebApiConfig.cs 中將

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

取代為

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]如果您想要深入了解新的 Server SDK 以及如何從您的應用程式新增/移除功能，請參閱[如何使用 .NET 伺服器 SDK] 主題。

行動服務與 Mobile Apps SDK 之間有一些其他變更，但很容易處理。在整個專案中，您可能需要修改某些 using 陳述式，這時 Visual Studio 將有所幫助。

您需要將 `[MobileAppController]` 屬性新增至所有 ApiControllers，只要將該裝飾項目直接放在類別定義之前。

`[AuthorizeLevel]` 屬性已不存在，您應該改用標準 ASP.NET `[Authorize]` 屬性裝飾您的控制站和方法。請注意，沒有 `[AuthorizeLevel]` 的控制器已不再受應用程式金鑰所保護；將會被視為公用。新的 SDK 不再利用應用程式金鑰和主要金鑰。

對於推播，您可能會發現伺服器 SDK 中遺漏的主要項目是 PushRegistrationHandler 類別。註冊在行動應用程式中處理方式稍有不同，依預設會啟用不具標記的註冊。管理標記可使用自訂 API 來完成。如需詳細資訊，請參閱[將推播通知新增至行動應用程式]主題。

排程工作並未內建於行動應用程式中，因此您在 .NET 後端中的任何現有工作都必須個別移轉。其中一個選項是在行動應用程式程式碼網站上建立排程 [Web 工作]。您也可以設定用來保存工作程式碼的控制器，並設定依預期的排程在端點上執行的 [Azure 排程器]。

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

>[AZURE.NOTE]某些提供者 (如 Twitter、Microsoft 帳戶) 不允許您在不同網域上指定多個重新導向 URI。如果您的應用程式使用其中一個提供者，並且依存於使用者識別碼，建議您此時不要嘗試移轉。

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

在您的客戶有機會接收這些更新後，您即可刪除應用程式的行動服務版本。此時，您完全移轉至應用程式服務行動應用程式。

<!-- URLs. -->

[預覽 Azure Preview 入口網站]: https://portal.azure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[什麼是行動應用程式？]: app-service-mobile-value-prop-preview.md
[我已經使用網站和行動服務 – 應用程式服務對我有何幫助？]: /zh-TW/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[行動應用程式伺服器 SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[建立行動應用程式]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[將推播通知新增至行動應用程式]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[將驗證新增至行動應用程式]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Azure 排程器]: /zh-TW/documentation/services/scheduler/
[Web 工作]: ../app-service-web/websites-webjobs-resources.md
[傳送跨平台推播通知]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md
[如何使用 .NET 伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

<!---HONumber=Oct15_HO1-->