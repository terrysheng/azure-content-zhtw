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
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="mahender"/>

# 將您現有的 Azure 行動服務移轉至 App Service

本主題說明如何將現有的應用程式從 Azure 行動服務移轉至新的應用程式服務行動應用程式。任何現有的行動服務都可以輕鬆地移轉至新的行動 App 後端。移轉期間，現有的行動服務將繼續運作。

當行動服務移轉到 App Service 時，就對所有 App Service 功能具有存取權，而且會根據 [App Service 定價]而不是行動服務定價進行計費。

另一點要注意的是，在您自己的訂用帳戶和控制下，所有排程的工作都將從 Microsoft Managed Azure 排程器方案移至 Azure 排程器方案。類似於移轉至 App Service 的優點，這可讓您運用 Azure 排程器的完整功能。

### <a name="why-host"></a>為何裝載於 App Service 中？

App Service 可為您的應用程式提供功能更豐富的裝載環境。藉由在 App Service 中裝載，您的服務將可存取預備位置、自訂網域、流量管理員支援等功能。雖然您可以在移轉到 App Service 之後，將行動服務升級至行動 App 後端，但有些客戶可能尚未執行 SDK 更新，就想要立即運用這些功能。

若想進一步了解 App Service 的優點，請參閱[比較行動服務與App Service]。

## 移轉與升級

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

- 對於以 Node.js 為基礎的伺服器專案，新的 [Mobile Apps Node.js SDK](https://github.com/Azure/azure-mobile-apps-node) 提供許多新功能。例如，您現在可以執行本機開發和偵錯、使用 0.10 以上的任何 Node.js 版本，以及使用任何 Express.js 中介軟體自訂。

- 對於以 .NET 為基礎的伺服器專案，新的 [Mobile Apps SDK NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)在 NuGet 相依性具有更大的彈性、支援新的 App Service 驗證功能，而且是由任何 ASP.NET 專案 (包括 MVC) 所組成。若要深入了解升級，請參閱[將您現有的 .NET 行動服務升級為 App Service](app-service-mobile-net-upgrading-from-mobile-services.md)。


## <a name="understand"></a>了解應用程式服務行動應用程式

App Service 中的 Mobile Apps 是使用 Azure 建置行動應用程式的新方式。您可以在[什麼是 Mobile Apps？]主題中深入了解 Mobile Apps。

移轉到 Mobile Apps 時，可保留所有現有的 app 功能，包括您的商務邏輯。此外，新功能可用於應用程式。在 Mobile Apps 模型中，您的程式碼實際上會在 App Service 的 Web app 上執行，此為新版的 Azure 網站。您可以完全掌控 Web 應用程式及其運作方式。此外，過去無法讓行動服務客戶使用的 Web app 功能 (例如，流量管理員和 Development Slots)，現在都可供使用。

升級的主要限制在於行動服務排程的工作不會整合，而必須設定 Azure 排程器來叫用自訂 API，或必須啟用 Web 工作功能。

## 使用移轉至 App Service 精靈來移轉您的行動服務

將行動服務移轉至 App Service 的最簡單且建議的方式是使用 [移轉至 App Service] 精靈，這適用於 Azure 傳統入口網站。瀏覽至 [Azure 傳統入口網站]、瀏覽您的行動服務，然後選取要移轉的行動服務。在畫面底部，您將會看到 [移轉至 App Service] 按鈕，逐步引導您完成移轉行動服務的程序。

### <a name="what-gets-migrated"></a>移轉哪些項目？

移轉精靈會將主控行動服務的伺服器陣列，從受行動服務管理變更為受 App Service 管理。它也會將行動服務所管理的任何排程器方案，從 Microsoft 管理的訂用帳戶移至您的訂用帳戶。將伺服器陣列傳輸到 App Service 的管理控制項時，所有與該伺服器陣列相關聯的行動服務會同時移動。

行動服務會根據區域和 SKU (免費、基本、標準)，將行動服務組織到伺服器陣列。您在某個區域中的所有免費服務都位於同一個伺服器陣列且會一併移轉；某個區域中的部分基本服務會一併裝載於同一個伺服器陣列上，但是，如果您具有大量基本服務，它們將會位於個別方案中；每個標準服務都會位於它自己的伺服器陣列上。如果您想要移轉單一服務，或避免將服務移動至您的其他網站，您可以將它升級到基本，而它將會在自己的獨立方案中。

如果您正在使用任何排程的工作，也必須將 Azure 排程器方案加入至您的訂用帳戶。每個訂用帳戶允許的免費排程工作數目有限，因此，可能會產生一些與此動作相關聯的成本，而您會想要檢閱貼文訂用帳戶。

###  使用移轉精靈

1. 瀏覽至 [Azure 傳統入口網站]，然後按一下 [行動服務]。 

2. 選取要移轉的行動服務，然後按一下底部功能表列上的 [移轉至 App Service]。這將會顯示於快顯視窗上，其中包含將移轉的行動服務清單。請參閱[上一節](#what-gets-migrated)，以了解哪些服務將會顯示及其原因的詳細資訊。

3. 輸入行動服務的名稱來確認移轉，然後按一下核取記號繼續。當移轉中包含多個行動服務時，您仍然必須輸入所選取的原始服務名稱。

4. 開始移轉之後，您可以從 [Azure 傳統入口網站]的行動服務清單中檢視目前的狀態。它將針對目前正在移轉的所有服務顯示為「正在移轉」。一旦所有移轉都顯示為已完成之後，您就可以在 [Azure 入口網站]的 Mobile Apps 下方檢視它們。在移轉期間和之後，您的行動服務將繼續運作且 URL 將不會變更。

## 手動移轉行動服務 .NET 後端

>[AZURE.NOTE]請注意，移轉行動服務的建議方式是透過上一節中所述的 [移轉至 App Service] 精靈 。只有在無法使用此精靈的情況下才應使用手動移轉。

本節說明如何將 .NET 行動服務專案裝載於 Azure App Service 內。以這種方式裝載的 app 可以使用所有的「行動服務」.NET 執行階段教學課程，但必須以 App Service 執行個體的網域來取代任何使用 azure-mobile.net 網域的 URL。

行動服務專案也可裝載於 [App Service 環境]。本主題中的所有內容仍然適用，但網站格式為 `contoso.contosoase.p.azurewebsites.net` 而不是 `contoso.azurewebsites.net`。

>[AZURE.NOTE]如果您執行手動移轉，就「無法」保留現有的 **service.azure-mobile.net** URL。當您具有連接到此 URL 的行動用戶端時，您應該使用自動移轉選項，或讓您的行動服務保持執行，直到所有的行動用戶端都升級為新的 URL 為止。


### <a name="app-settings"></a>應用程式設定
若要使用行動服務，必須在環境中完成幾項應用程式設定，本節將說明這幾項設定。

若要在您的行動 App 後端上進行應用程式設定，請先登入 [Azure 入口網站]。瀏覽至您想要做為行動 App 後端使用的 App Service app、按一下 [設定] > [應用程式設定]，然後向下捲動至 [App 設定]，您可以在此處設定下列必要的索引鍵值組：

+ **MS\_MobileServiceName** 應設定為您的 app 名稱。例如，當您的後端 URL 是 `contoso.azurewebsites.net` 時，則 *contoso* 是正確的值。

+ **MS\_MobileServiceDomainSuffix** 應設定為您的 web app 名稱。例如，當您的後端 URL 是 `contoso.azurewebsites.net` 時，則 *azurewebsites.net* 是正確的值。

+ **MS\_ApplicationKey** 可以設為任何值，但必須是用戶端 SDK 所使用的相同值。建議使用 GUID。

+ **MS\_MasterKey** 可以設為任何值，但必須與任何管理 API/用戶端使用相同的值。建議使用 GUID。

移轉行動服務時，可以在 [Azure 傳統入口網站]中，從 [行動服務] 區段的 [設定] 索引標籤取得主要索引鍵和應用程式索引鍵。按一下底部的 [管理索引鍵]，並複製索引鍵。


### <a name="client-sdk"></a>作法：修改用戶端 SDK

在用戶端 app 專案中修改行動服務用戶端物件的建構函式，使其接受新的 app URL (例如 `https://contoso.azurewebsites.net`) 和先前設定的應用程式索引鍵。用戶端 SDK 版本應為**行動服務**版本，且**不應**升級。如果是 IOS 和 Android 用戶端，請使用 2.x 版，Windows/Xamarin 用戶端請使用 1.3.2。Javascript 用戶端應使用 1.2.7。

### <a name="data"></a>作法：啟用資料功能

若要在行動服務中使用預設的 Entity Framework 類別，必須完成兩項額外的設定。

連接字串會儲存於 [應用程式設定] 刀鋒視窗的 [連接字串] 區段中，就在 [App 設定] 區段之下。資料庫的連接字串應設定於 **MS\_TableConnectionString** 索引鍵下。將現有的行動服務移轉至 App Service 時，請瀏覽至 [Azure 傳統入口網站](https://manage.windowsazure.com/)中行動服務的 [設定] 索引標籤的 [連接字串] 區段。按一下 [顯示連接字串] 並複製值。

預設使用的結構描述是 **MS\_MobileServiceName**，但這可以使用 **MS\_TableSchema** 設定來覆寫。回到 [App 設定] 之下，將 **MS\_TableSchema** 設為要使用的結構描述名稱。如果您要移動現有的行動服務應用程式，目前已有使用 Entity Framework 建立的結構描述 - 這是行動服務的名稱，而不是現在會裝載程式碼的 App Service 執行個體。

### <a name="push"></a>作法：啟用推送功能

若要讓推送功能運作，Web app 還必須知道通知中樞的相關資訊。

在 [連接字串] 下，為通知中樞設定具有 DefaultFullSharedAccessSignature 連接字串的 **MS\_NotificationHubConnectionString**。若要移轉現有的行動服務，請瀏覽至 [Azure 傳統入口網站](https://manage.windowsazure.com/)中行動服務 [設定] 索引標籤的 [連接字串] 區段。按一下 [顯示連接字串] 並複製值。

**MS\_NotificationHubName** app 設定應設為中樞的名稱。在移動現有的行動服務時，您可以從 [Azure 傳統入口網站](https://manage.windowsazure.com/)中行動服務的 [推送] 索引標籤取得此值。此索引標籤上的其他欄位會連結至中樞本身，不需要複製到任何位置。

### <a name="auth"></a>作法：啟用驗證功能

身分識別功能也有必須為個別提供者進行應用程式設定的需求。如果要移動現有的行動服務，Azure 傳統入口網站的 [身分識別] 索引標籤中的每個欄位都會有對應的 app 設定。

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

* **MS\_AadTenants** - 附註：**MS\_AadTenants** 會儲存為租用戶網域 (Azure 傳統入口網站中的 [允許的租用戶] 欄位) 的逗號分隔清單。

### <a name="publish"></a>作法：發佈行動服務專案

1. 在 [Azure 入口網站]中，瀏覽至您的 app、按一下命令列中的 [取得發行設定檔]，然後將下載的設定檔儲存到本機電腦。
2. 在 Visual Studio 中，以滑鼠右鍵按一下行動服務伺服器專案，然後按一下 [發佈]。 
3. 在 [設定檔] 索引標籤上選擇 [匯入]，然後瀏覽至下載的設定檔。
3. 按一下 [發佈]，將程式碼部署至 App Service。

記錄檔會透過標準 App Service 記錄功能來處理。若要深入了解記錄功能，請參閱[在 Azure App Service 中啟用診斷記錄]。



<!-- URLs. -->

[Azure 入口網站]: https://portal.azure.com/
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[什麼是 Mobile Apps？]: app-service-mobile-value-prop.md
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
[App Service 定價]: https://azure.microsoft.com/zh-TW/pricing/details/app-service/
[App Service 環境]: app-service-app-service-environment-intro.md
[比較行動服務與App Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1203_2015-->