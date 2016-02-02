<properties
	pageTitle="Azure App Service 中的 API Apps 驗證與授權 | Microsoft Azure"
	description="了解 Azure App Service 針對 API Apps 所提供的驗證和授權服務。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/21/2016"
	ms.author="tdykstra"/>

# Azure App Service 中的 API Apps 驗證與授權

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概觀 

Azure App Service 提供內建的驗證與授權服務，可實作 [OAuth 2.0](#oauth) 和 [OpenID Connect](#oauth)。本文描述 Azure App Service 中的 API Apps 可用的服務和選項。

下圖說明 App Service 驗證的幾個重要特性：

* 它會前置處理傳入的 API 要求，這表示它能使用 App Service 所支援的任何語言或架構。
* 它會提供您幾個選項讓您決定要在自有程式碼中進行多少驗證工作。
* 它適用於使用者與服務帳戶驗證。 
* 它支援五個識別提供者：Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帳戶。
* 它在 API Apps、Web Apps 和 Mobile Apps 的作用都相同。

![](./media/app-service-api-authentication/api-apps-overview.png)

## 不限語言

App Service 驗證處理程序是在要求進入 API 應用程式之前進行，這表示不管 API 應用程式是以任何語言或架構所撰寫，都能適用驗證功能。您可以根據 ASP.NET、Java、Node.js 或任何 App Service 所支援的架構建立 API。

App Service 會在 HTTP 要求的授權標頭中傳遞 JSON Web 權杖 (JWT)，以任何語言或架構撰寫的程式碼都可以從權杖中取得所需的資訊。此外，App Service 會透過設定某些特殊標頭 (如下所示)，讓您更容易地存取最常使用的宣告：

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
在 .NET API 中，您可以使用 `Authorize` 屬性，而且如果您需要更精細的授權，也能輕易地根據宣告來撰寫程式碼，因為它已為您在 .NET 類別中填入宣告資訊。

## 多個保護選項

App Service 可以防止匿名 HTTP 要求進入您的 API 應用程式、傳遞所有要求並驗證要求中所包含的權杖，或是不採取任何動作就放行所有要求：

1. 只允許通過驗證的要求進入 API 應用程式。

	如果 App Service 收到來自瀏覽器的匿名要求，便會將其重新導向至登入頁面。

	如果您事先知道所要使用的驗證提供者 (Google、Twitter 等)，您可以設定 App Service 來為您處理登入程序。或者，您也可以指定 App Service 將匿名要求重新導向至自有 URL。然後您就可以讓使用者選擇驗證提供者。

	使用此選項時，您不需要在應用程式中撰寫任何驗證程式碼，並且因為 HTTP 標頭中已提供最重要的宣告，所以授權程序會變得相當簡單。

2. 允許所有要求進入 API 應用程式，但只讓通過驗證的要求生效，並在 HTTP 標頭中傳遞驗證資訊。

	此選項可讓您更彈性地處理匿名要求，並輕鬆地撰寫需要存取最常見宣告的程式碼。不同於選項 1，如果您想要防止匿名使用者使用 API，則必須撰寫程式碼。

3. 允許所有要求進入 API，不對要求中的驗證資訊採取任何動作。

	這個選項會將驗證和授權工作全部交由應用程式程式碼來處理。

在 [Azure 入口網站](https://portal.azure.com/)中，請在 [驗證/授權] 刀鋒視窗中選取您要的選項。

![](./media/app-service-api-authentication/authblade.png)

如果使用選項 1 和 2，請開啟 [App Service 驗證]，然後在 [要求未通過驗證時所要採取的動作] 下拉式清單中，選擇 [登入] 或 [允許要求 (無動作)]。如果您選擇 [登入]，就必須選擇驗證提供者，並設定該提供者。

![](./media/app-service-api-authentication/actiontotake.png)

如需如何設定驗證的詳細資訊，請參閱[如何設定 App Service 應用程式使用 Azure Active Directory 登入](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。本文適用於 API 應用程式和行動應用程式，而且會連結到關於其他驗證提供者的其他文章。
 
## <a id="internal"></a> 服務帳戶驗證

App Service 驗證適用於從某個 API 應用程式呼叫另一個 API 應用程式之類的內部案例。在此案例中，您可以使用服務帳戶認證 (而非使用者認證) 來取得權杖。服務帳戶在 Azure Active Directory 中也稱為*服務主體*，使用這類帳戶的驗證也稱為服務對服務案例。

若為服務對服務案例，則請使用 Azure Active Directory 保護所呼叫的 API 應用程式，並在呼叫 API 應用程式時提供 AAD 服務主體授權權杖。透過提供用戶端識別碼和用戶端密碼，您就可以從 AAD 應用程式取得此權杖。不需要特殊的僅 Azure 適用的程式碼，例如在處理行動服務 Zumo 權杖時為 true。[API Apps 的服務主體驗證](app-service-api-dotnet-service-principal-auth.md)教學課程中有講述這個使用 ASP.NET API 應用程式之案例的範例。

如果您想要處理服務對服務案例，但不要使用 App Service 驗證，請使用用戶端憑證或基本驗證。如需 Azure 中用戶端憑證的詳細資訊，請參閱[如何設定 Web Apps 的 TLS 相互驗證](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。如需 ASP.NET 中的基本驗證相關資訊，請參閱 [ASP.NET Web API 2 中的驗證篩選](http://www.asp.net/web-api/overview/security/authentication-filters)。

App Service 邏輯應用程式至 API 應用程式的服務帳戶驗證屬於特殊案例，[將您裝載在 App Service 上的自訂 API 與邏輯應用程式一起使用](../app-service-logic/app-service-logic-custom-hosted-api.md)中有關於此案例的說明。

## 用戶端驗證

如需如何處理來自行動用戶端之驗證的相關資訊，請參閱[關於行動應用程式驗證的說明文件](../app-service-mobile/app-service-mobile-ios-get-started-users.md)。行動應用程式和 API 應用程式的 App Service 驗證具有相同的運作方式。
  
## 詳細資訊

如需 Azure App Service 中的驗證與授權的詳細資訊，請參閱下列資源：

* [擴充 App Service 驗證/授權](/blog/announcing-app-service-authentication-authorization/)
* [如何設定 App Service 應用程式使用 Azure Active Directory 登入](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (頁面頂端有其他驗證提供者的連結)。 

如需 OAuth 2.0、OpenID Connect 和 JSON Web 權杖 (JWT) 的詳細資訊，請參閱下列資源。

* [開始使用 OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "開始使用 OAuth 2.0") 
* [OAuth2、OpenID Connect 和 JSON Web 權杖 (JWT) 簡介 - PluralSight 課程](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [在 ASP.NET 中建置和保護多個用戶端的 RESTful API - PluralSight 課程](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

如需使用 Azure Active Directory 之驗證的詳細資訊，請參閱下列資源。

* [Azure AD 案例](http://aka.ms/aadscenarios)
* [Azure AD 開發人員指南](http://aka.ms/aaddev)
* [Azure AD 範例](http://aka.ms/aadsamples)

## 後續步驟

本文說明了可用於 API 應用程式之 App Service 的驗證和授權功能。

如果您正按照適用於 ASP.NET 和 API Apps 的入門教學課程順序進行，請在下一個教學課程中試用這些功能：[App Service API Apps 中的使用者驗證](app-service-api-dotnet-user-principal-auth.md)。

如需在 Azure App Service 中使用 Node 和 Java 的詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)和 [Java 開發人員中心](/develop/java/)。

<!---HONumber=AcomDC_0128_2016-->