<properties
	pageTitle="開始使用 Web Apps 的生產測試"
	description="了解 Azure App Service Web Apps 中的生產測試 (TiP) 功能。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="cephalin"/>

# 開始使用 Web Apps 的生產測試

在生產環境中測試，或使用實際的客戶流量實測您的 Web 應用程式，是應用程式開發人員逐漸整合至其[靈活式開發](https://en.wikipedia.org/wiki/Agile_software_development)方法的測試策略。它可讓您在生產環境中使用實際使用者流量測試應用程式的品質，而不是在測試環境中以綜合資料進行測試。藉由將新的應用程式公開給實際的使用者，您得以獲知應用程式在部署之後可能發生的實際問題。您可以根據實際使用者流量的數量、速度和變化來確認應用程式更新的功能、效能和價值，這些都是您在測試環境中無法測得的。

## App Service Web Apps 中的流量路由

透過 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 的流量路由功能，您可以將部分的實際使用者流量導向至一或多個[部署位置](web-sites-staged-publishing.md)，然後使用 [Azure Application Insights](/services/application-insights/) 或 [Azure HDInsight](/services/hdinsight/) 來分析您的應用程式，或使用協力廠商工具 (例如 [New Relic](/marketplace/partners/newrelic/newrelic/)) 來驗證變更。例如，您可以使用 App Service 實作下列案例：

- 在部署至整個網站之前找出更新中的功能錯誤或效能瓶頸
- 對 beta 應用程式測量可用性計量，以執行變更的「受控試驗」。
- 逐漸建構出新的更新，並在錯誤發生時依正常程序回復為目前的版本 
- 在多個部署位置中執行 [A/B 測試](https://en.wikipedia.org/wiki/A/B_testing)或[多變量測試](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing)，以最佳化應用程式的商務成果

### 在 Web Apps 中使用流量路由的需求

- 您的 Web 應用程式必須在**標準**或**高階**層執行，因為多個部署位置有此需求。
- 若要正常運作流量路由，必須在使用者的瀏覽器中啟用 Cookie。流量路由會使用 Cookie 將用戶端瀏覽器固定到用戶端工作階段存留期間的部署位置。
- 流量路由可透過 Azure PowerShell Cmdlet 支援進階的TiP 案例。

## 將流量區段路由傳送至部署位置

在每個 TiP 案例的基本層級中，您會將預先定義百分比的實際流量路由傳送至非生產部署位置。若要這樣做，請遵循下面的步驟：

>[AZURE.NOTE]此處的步驟假設您已有[非生產部署位置](web-sites-staged-publishing.md)，而且所需的 Web 應用程式內容[已部署](web-sites-publish-source-control.md)給它。

1. 登入 [Azure Preview 入口網站](https://portal.azure.com)。
2. 在 Web 應用程式的刀鋒視窗中，按一下 [設定] > [流量路由]。![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. 選取您要將流量路由傳送到的位置，以及您所需的總流量百分比，然後按一下 [儲存]。

	![](./media/app-service-web-test-in-production/02-select-slot.png)

4. 移至部署位置的刀鋒視窗。現在您應該會看見路由傳送至該處的實際流量。

	![](./media/app-service-web-test-in-production/03-traffic-routed.png)

設定流量路由之後，會有指定百分比的用戶端隨機路由傳送至您的非生產位置。不過，要特別注意的是，一旦用戶端自動路由傳送至特定位置，它在用戶端工作階段存留期間都會「固定」到該位置。這會藉由使用 Cookie 固定使用者工作階段來完成。如果您檢視 HTTP 要求，您會發現每個後續要求中都有一個 `TipMix` Cookie。

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## 強制將用戶端要求送至特定位置

除了自動流量路由以外，App Service 也可以將要求路由傳送至特定位置。如果您想要讓使用者能夠選擇加入或退出您的 beta 應用程式，這就非常有用。若要這樣做，必須使用 `x-ms-routing-name` 查詢參數。

若要使用 `x-ms-routing-name` 將使用者重新路由傳送至特定位置，您必須確定該位置已新增至流量路由清單。由於您想要明確地路由傳送至某個位置，因此您所設定的實際路由百分比並不重要。如果您想，您可以建立經使用者點按即可存取 beta 應用程式的「beta 連結」。

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### 讓使用者選擇退出 beta 應用程式

舉例而言，若要讓使用者選擇退出您的 beta 應用程式，您可以在網頁中放入下列連結：

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

字串 `x-ms-routing-name=self` 會指定生產位置。一旦用戶端瀏覽器存取此連結，不僅瀏覽器會重新導向至生產位置，後續的每個要求也會包含將工作階段固定到生產位置的 `x-ms-routing-name=self` Cookie。

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### 讓使用者選擇加入 beta 應用程式

若要讓使用者選擇加入您的 beta 應用程式，請將相同的查詢參數設為非生產位置的名稱，例如：

		<webappname>.azurewebsites.net/?x-ms-routing-name=staging

## 其他資源 ##

-   [針對 Azure App Service 中的 Web 應用程式設定預備環境](web-sites-staged-publishing.md)
-	[透過可預測方式在 Azure 中部署複雜應用程式](app-service-deploy-complex-application-predictably.md)
-   [敏捷式軟體開發 (Agile Software Development) 與 Azure App Service](app-service-agile-software-development.md)
-	[為 Web 應用程式有效地使用 DevOps 環境](app-service-web-staged-publishing-realworld-scenarios.md)

<!---HONumber=Oct15_HO4-->