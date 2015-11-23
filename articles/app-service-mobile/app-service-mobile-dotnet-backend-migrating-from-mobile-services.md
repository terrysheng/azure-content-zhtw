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
	ms.date="11/04/2015" 
	ms.author="mahender"/>

# 將您現有的 Azure 行動服務移轉至 App Service 

本主題說明如何將現有的應用程式從 Azure 行動服務移轉至新的應用程式服務行動應用程式。所有現有的行動服務應用程式都可以輕鬆地移轉至新的應用程式服務行動應用程式。在移轉期間，現有的行動服務應用程式可以繼續運作。

>[AZURE.NOTE]目前，.NET 後端行動服務可以使用手動移轉來移轉到 App Service。即將推出 Node.js 和 .NET 的周全移轉體驗。不過，如果您執行手動移轉，就「無法」保留現有的 **service.azure-mobile.net** URL。

當 app 移轉到 Azure App Service 時，就對所有 App Service 功能具有存取權，而且會根據 [App Service 價格]而不是行動服務價格計費。

### <a name="why-host"></a>為何裝載於 App Service 中？

App Service 可為您的應用程式提供功能更豐富的裝載環境。藉由在 App Service 中裝載，您的服務將可存取預備位置、自訂網域、流量管理員支援等功能。雖然您可以[將行動服務移轉至 App Service 上的行動應用程式]，但有些客戶可能尚未執行 SDK 更新就想要立即運用這些功能。

在 App Service 中裝載的主要限制，在於行動服務排程的工作不會整合，而必須設定 Azure 排程器來叫用自訂 API，或必須啟用 Web 工作功能。

若想進一步了解 App Service 的優點，請參閱[比較行動服務與App Service] 主題。

##移轉與升級

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

  - 對於以 Node.js 為基礎的伺服器專案，新的 [Mobile Apps Node.js SDK](https://github.com/Azure/azure-mobile-apps-node) 提供許多新功能。例如，您現在可以執行本機開發和偵錯、使用 0.10 以上的任何 Node.js 版本，以及使用任何 Express.js 中介軟體自訂。

  - 對於以 .NET 為基礎的伺服器專案，新的 [Mobile Apps SDK NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)在 NuGet 相依性有更大的彈性、支援新的 App Service 驗證功能，以及由任何 ASP.NET 專案 (包括 MVC) 組成。若要深入了解升級，請參閱[將您現有的 .NET 行動服務升級為 App Service](app-service-mobile-net-upgrading-from-mobile-services.md)。


##<a name="understand"></a>了解 App Service Mobile 

App Service Mobile 是一種使用 Microsoft Azure 建置行動應用程式的新方式。您可以在[什麼是行動應用程式？]主題中進一步了解行動應用程式。

在移轉至行動應用程式時，所有現有的應用程式功能 (和程式碼) 都可保留。此外，新功能可用於應用程式。在行動應用程式模型中，您的程式碼實際上會執行於 Web 應用程式 (新版本的 Azure 網站)。您可以完全掌控 Web 應用程式及其運作方式。此外，過去無法讓行動服務客戶使用的 Web Apps 功能 (例如流量管理員和 Development Slots)，現在都可供使用。


##手動移轉行動服務 .NET 後端

本節說明如何將 .NET 行動服務專案裝載在 Azure App Service 內。以這種方式裝載的 app 可使用所有的行動服務 .NET 執行階段教學課程，但必須以 App Service 執行個體的網域取代任何使用 azure-mobile.net 網域的 URL。

行動服務專案也可裝載於 [App Service 環境]。本主題中的所有內容仍然適用，但網站的格式將是 contoso.contosoase.p.azurewebsites.net，而不是 contoso.azurewebsites.net。

>[AZURE.NOTE]如果您執行手動移轉，就「無法」保留現有的 **service.azure-mobile.net** URL。如果您有連接到此 URL 的行動用戶端，您應該使用自動移轉選項，或讓您的行動服務一直執行，直到所有的行動用戶端都升級為新的 URL 為止。


### <a name="app-settings"></a>應用程式設定
要使用行動服務，必須在環境中完成幾項應用程式設定，本節將說明這幾項設定。

若要在您的 Web 應用程式上進行應用程式設定，請先登入 [Preview Azure 管理入口網站]。瀏覽至您要使用的 Web、行動或 API 應用程式並選取 [設定]，然後選取 [應用程式設定]。 向下捲動至標示為「應用程式設定」的區段。 您可以在這裡設定必要的索引鍵-值組。
 
**MS\_MobileServiceName** 應該設定為您的應用程式名稱。例如，如果您將在具有 URL contoso.azurewebsites.net 的應用程式中執行，則 "contoso" 將是正確值。
 
**MS\_MobileServiceDomainSuffix** 應該設定為您的 Web 應用程式名稱。例如，如果您將在具有 URL contoso.azurewebsites.net 的應用程式中執行，則 "azurewebsites.net" 將是正確值。
 
**MS\_ApplicationKey** 可以設為任何值，但必須與用戶端 SDK 使用相同的值。建議使用 GUID。
 
**MS\_MasterKey** 可以設為任何值，但必須與任何管理 API/用戶端使用相同的值。建議使用 GUID。
 
如果要移動現有的行動服務應用程式，可以使用 [Azure 管理入口網站]，從 [行動服務] 區段的 [設定] 索引標籤中取得主要金鑰和應用程式金鑰。選取底部的 [管理金鑰] 動作，並將金鑰複製出來。


### <a name="client-sdk"></a>作法：修改用戶端 SDK

在用戶端應用程式專案中修改行動服務用戶端物件的建構函式，使其接受新的應用程式 URL (例如 `https://contoso.azurewebsites.net`) 和先前設定的應用程式金鑰。用戶端 SDK 版本應為**行動服務**版本，且**不應**升級。如果是 IOS 和 Android 用戶端，請使用 2.x 版，Windows/Xamarin 用戶端請使用 1.3.2。Javascript 用戶端應使用 1.2.7。

### <a name="data"></a>作法：啟用資料功能

若要在行動服務中使用預設的 Entity Framework 類別，必須進一步完成兩項應用程式設定。
 
連接字串會儲存在 [應用程式設定] 刀鋒視窗的 [連接字串] 區段中，就在 [應用程式設定] 區段之下。資料庫的連接字串應設定於 **MS\_TableConnectionString** 鍵值下。若要移動現有的行動服務應用程式，請瀏覽至 [行動服務入口網站] 之 [設定] 索引標籤的 [連接字串] 區段。按一下 [顯示連接字串]，並將值複製出來。
 
依預設使用的結構描述是 **MS\_MobileServiceName**，但可以使用 **MS\_TableSchema** 設定加以覆寫。回到 [應用程式設定] 之下，將 **MS\_TableSchema** 設為要使用之結構描述的名稱。如果您要移動現有的行動服務應用程式，目前已有使用 Entity Framework 建立的結構描述 - 這是行動服務的名稱，而不是現在會裝載程式碼的 App Service 執行個體。

### <a name="push"></a>作法：啟用推播功能

要讓推播功能運作，Web 應用程式還必須知道通知中樞的相關資訊。
 
在 [連接字串] 下，為通知中樞使用 DefaultFullSharedAccessSignature 連接字串設定 **MS\_NotificationHubConnectionString**。若要移動現有的行動服務應用程式，請瀏覽至 [行動服務入口網站] 之 [設定] 索引標籤的 [連接字串] 區段。按一下 [顯示連接字串]，並將值複製出來。

**MS\_NotificationHubName** 應用程式設定應設為中樞的名稱。在移動現有的行動服務應用程式時，您可以從 [行動服務入口網站] 中的 [推播] 索引標籤取得此值。此索引標籤上的其他欄位會連結至中樞本身，不需要複製到任何位置。
 
### <a name="auth"></a>作法：啟用驗證功能

身分識別功能也有必須為個別提供者進行應用程式設定的需求。如果要移動現有的行動服務應用程式，[行動服務入口網站] 的 [身分識別] 索引標籤中的每個欄位都會有對應的應用程式設定。
 
Microsoft 帳戶

* **MS\_MicrosoftClientID**

* **MS\_MicrosoftClientSecret**

* **MS\_MicrosoftPackageSID**
 
Facebook

* **MS\_FacebookAppID**

* **MS\_FacebookAppSecret**
 
Twitter

* **MS\_TwitterConsumerKey**

* **MS\_TwitterConsumerSecret**
 
Google

* **MS\_GoogleClientID**

* **MS\_GoogleClientSecret**
 
AAD

* **MS\_AadClientID**

* **MS\_AadTenants** - 注意：**MS\_AadTenants** 會儲存為租用戶網域 ([行動服務入口網站] 中的 [允許的租用戶] 欄位) 的逗號分隔清單。

### <a name="publish"></a>作法：發佈行動服務專案

1. 在 Preview 入口網站中瀏覽至您的應用程式，然後從命令列中選取 [取得發佈設定檔]。將下載的設定檔儲存到本機電腦。
2. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務伺服器專案，然後選取 [發佈]。 在 [設定檔] 索引標籤上選擇 [匯入]，然後瀏覽至下載的設定檔。
3. 按一下 [發佈]，將程式碼部署至 App Service。

記錄檔會透過標準 App Service 記錄功能來處理。若要深入了解記錄功能，請參閱[在 Azure App Service 中啟用診斷記錄]。



<!-- URLs. -->

[Preview Azure 管理入口網站]: https://portal.azure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[什麼是行動應用程式？]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /zh-TW/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /zh-TW/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[在 Azure App Service 中啟用診斷記錄]: web-sites-enable-diagnostic-log.md
[App Service 價格]: https://azure.microsoft.com/zh-TW/pricing/details/app-service/
[App Service 環境]: app-service-app-service-environment-intro.md
[比較行動服務與App Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[將行動服務移轉至 App Service 上的行動應用程式]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO3-->