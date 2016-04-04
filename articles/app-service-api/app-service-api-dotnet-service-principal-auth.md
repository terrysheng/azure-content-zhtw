<properties
	pageTitle="Azure App Service 中 API Apps 的服務主體驗證 | Microsoft Azure"
	description="深入了解如何保護服務對服務案例的 Azure App Service 的 API 應用程式。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016" 
	ms.author="tdykstra"/>

# 在 Azure App Service 中 API Apps 的服務主體驗證

[AZURE.INCLUDE [選取器](../../includes/app-service-api-auth-selector.md)]

## 概觀

這篇文章說明如何使用 App Service 驗證以[內部](app-service-api-authentication.md#internal)存取 API 應用程式。內部案例是您具有只想要讓您自己的應用程式程式碼使用的 API 應用程式。在 App Service 中實作這種案例最簡單的方法是使用 Azure AD 保護被呼叫的 API 應用程式。您可以藉由提供應用程式身分識別 (服務主體) 認證，使用您從 Azure AD 取得的持有人權杖，呼叫受保護的 API 應用程式。

在本文中，您將了解：

* 如何使用 Azure Active Directory (Azure AD) 防止未經驗證存取 API 應用程式。
* 如何使用 Azure AD 服務主體 (應用程式身分識別) 認證，從 API 應用程式、Web 應用程式或行動應用程式取用受保護的 API 應用程式。如需如何從邏輯應用程式取用的詳細資訊，請參閱[將您裝載在 App Service 上的自訂 API 與邏輯應用程式一起使用](../app-service-logic/app-service-logic-custom-hosted-api.md)。
* 如何確保登入的使用者不能從瀏覽器呼叫受保護的 API 應用程式。
* 如何確保只能由特定 Azure AD 服務主體呼叫受保護的 API 應用程式。

本文包含兩個部分：

* [如何在 Azure App Service 中設定服務主體驗證](#authconfig)一節大致說明如何為 API 應用程式設定驗證，以及如何取用受保護的 API 應用程式。本節一體適用於 App Service 支援的所有架構，包括 .NET、Node.js 和 Java。

* 從[繼續進行 .NET 入門教學課程](#tutorialstart)章節開始，教學課程會引導您針對 App Service 中執行的 .NET 範例應用程式設定「內部存取」案例。

## <a id="authconfig"></a>如何在 Azure App Service 中設定服務主體驗證

本節提供適用於任何 API 應用程式的一般指示。如需「待辦事項清單」.NET 範例應用程式的專用步驟，請移至[繼續進行 .NET 入門教學課程](#tutorialstart)。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您想要保護的 API 應用程式的 [設定] 刀鋒視窗，然後尋找 [功能] 區段，再按一下 [驗證/授權]。

	![Azure 入口網站中的驗證/授權](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. 在 [驗證/授權] 刀鋒視窗中，按一下 [開啟]。

4. 在 [要求未經驗證時所採取的動作] 下拉式清單中，選取 [登入 Azure Active Directory]。

5. 在 [驗證提供者] 底下，選取 [Azure Active Directory]。

	![Azure 入口網站中的驗證/授權刀鋒視窗](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. 設定 [Azure Active Directory 設定] 刀鋒視窗以建立新的 Azure AD 應用程式，或者，如果您已有想要使用的現有 Azure AD 應用程式，則請使用該應用程式。

	內部案例通常涉及 API 應用程式呼叫 API 應用程式。您可以為每一個 API 應用程式使用個別的 Azure AD 應用程式，或是全都使用同一個 Azure AD 應用程式。

	如需關於此刀鋒視窗的詳細指示，請參閱[如何設定 App Service 應用程式使用 Azure Active Directory 登入](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)。

7. [驗證提供者設定] 刀鋒視窗使用完畢時，按一下 [確定]。

7. 在 [驗證/授權] 刀鋒視窗中，按一下 [儲存]。

	![按一下 [Save] (儲存)。](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

完成時，App Service 只會允許來自已設定 Azure AD 租用戶的呼叫端的要求。受保護的 API 應用程式則不需要有驗證或授權程式碼。持有人權杖會與 HTTP 標頭中的常用宣告一起傳遞至 API 應用程式，您可以在程式碼中閱讀該資訊，以驗證要求是來自特定的呼叫端，例如服務主體。

凡是 App Service 所支援的語言 (包括 .NET、Node.js 和 Java)，此驗證功能的運作方式都相同。

#### 如何取用受保護的 API 應用程式

呼叫端必須在 API 呼叫中提供 Azure AD 持有人權杖。若要取得使用服務主體認證的持有人權杖，呼叫端必須使用 Active Directory Authentication Library (適用於 [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)、[Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) 或 [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 的 ADAL)。為了取得權杖，呼叫 ADAL 的程式碼會對 ADAL 提供下列資訊：

* Azure AD 租用戶的名稱。
* 與呼叫端相關聯之 Azure AD 應用程式的用戶端識別碼和用戶端密碼 (應用程式金鑰)。
* 與受保護 API 應用程式相關聯之 Azure AD 應用程式的用戶端識別碼 (如果只有使用一個 Azure AD 應用程式，則此識別碼就是與呼叫端相同的用戶端識別碼)。

這些值可在 [Azure 傳統入口網站](https://manage.windowsazure.com/)的 Azure AD 頁面中取得。

一旦取得權杖，呼叫端就會將它放入 HTTP 要求的 Authorization 標頭中。App Service 會驗證權杖，並允許要求觸達受保護的 API 應用程式。

#### 如何防止同一租用戶中的使用者存取 API 應用程式

同一租用戶中的使用者所擁有的持有人權杖也會對受保護的 API 應用程式有效。如果您想要確保只有服務主體可以呼叫受保護的 API 應用程式，請在受保護的 API 應用程式中新增程式碼以驗證來自權杖的下列宣告：

* `appid` 應該是與呼叫端相關聯之 Azure AD 應用程式的用戶端識別碼。 
* `oid` (`objectidentifier`) 應該是呼叫端的服務主體識別碼。 

App Service 也在 X-MS-CLIENT-PRINCIPAL-ID 標頭中提供 `objectidentifier` 宣告。

### 如何防止瀏覽器存取 API 應用程式

如果您並未驗證受保護 API 應用程式之程式碼中的宣告，而且您為受保護的 API 應用程式使用個別的 Azure AD 應用程式，請確定 Azure AD 應用程式的回覆 URL 與 API 應用程式的基底 URL 不同。如果回覆 URL 直接指向受保護的 API 應用程式，同一 Azure AD 租用戶中的使用者將能夠瀏覽至 API 應用程式、進行登入，並成功呼叫 API。

## <a id="tutorialstart"></a>繼續進行 .NET 入門教學課程

如果您要遵循適用於 API 應用程式的 Node.js 或 Java 入門系列，請跳至[後續步驟](#next-steps)一節。

本文其餘部分將接續說明適用於 API 應用程式的 .NET 入門系列，並且假設您已完成[使用者驗證教學課程](app-service-api-dotnet-user-principal-auth.md)而且擁有在 Azure 中執行、已啟用使用者驗證的範例應用程式。

## 在 Azure 中設定驗證

在本節中，您將會設定 App Service，以便只有其允許觸達資料層 API 應用程式的 HTTP 要求，能擁有有效的 Azure AD 持有人權杖。

在下一節中，則會設定中介層 API 應用程式，以傳送應用程式認證至 Azure AD、取回持有人權杖，並將持有人權杖傳送至資料層 API 應用程式。下圖可說明此程序。

![服務驗證圖表](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

如果您遵循教學課程指示進行時遇到問題，請參閱教學課程尾端的[疑難排解](#troubleshooting) 一節。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您為 ToDoListDataAPI (資料層) API 應用程式建立之 API 應用程式的 [設定] 刀鋒視窗，然後按一下 [設定]。

2. 在 [設定] 刀鋒視窗中，尋找 [功能] 區段，然後按一下 [驗證/授權]。

	![Azure 入口網站中的驗證/授權](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. 在 [驗證/授權] 刀鋒視窗中，按一下 [開啟]。

4. 在 [要求未經驗證時所採取的動作] 下拉式清單中，選取 [登入 Azure Active Directory]。

	這是能夠讓 App Service 確保唯有經過驗證的要求能觸達 API 應用程式的設定。對於具有有效持有人權杖的要求，App Service 會將權杖傳遞至 API 應用程式，並於 HTTP 標頭中填入常用宣告，以讓程式碼更輕鬆地取得該資訊。

5. 在 [驗證提供者] 底下，按一下 [Azure Active Directory]。

	![Azure 入口網站中的驗證/授權刀鋒視窗](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. 在 [Azure Active Directory 設定] 刀鋒視窗中，按一下 [快速]。

	在使用 [快速] 選項時，Azure 可以自動在 Azure AD [租用戶](https://msdn.microsoft.com/zh-TW/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)中建立 AAD 應用程式。

	您不必建立租用戶，因為每個 Azure 帳戶都會自動擁有一個。

7. 在 [管理模式] 底下，按一下 [建立新的 AD 應用程式] (若尚未選取)。

	入口網站會在 [建立應用程式] 輸入方塊中填入預設值。根據預設，Azure AD 應用程式的名稱會與 API 應用程式相同。如有需要，也可以輸入不同名稱。
	
	![Azure AD 設定](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

	附註：或者，您可以讓呼叫端 API 應用程式和受保護的 API 應用程式使用同一個 Azure AD 應用程式。如果您選擇後者，則這邊不需要 [建立新的 AD 應用程式] 選項，因為您稍早已在使用者驗證教學課程中建立了 Azure AD 應用程式。在本教學課程中，您會讓呼叫端 API 應用程式和受保護的 API 應用程式使用個別的 Azure AD 應用程式。

8. 記下 [建立應用程式] 輸入方塊中的值，您稍後將在 Azure 傳統入口網站中查詢此 AAD 應用程式。

7. 按一下 [確定]。

10. 在 [驗證/授權] 刀鋒視窗中，按一下 [儲存]。

	![按一下 [Save] (儲存)。](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

	App Service 在建立 Azure Active Directory 應用程式時，會自動將 [登入 URL] 和 [回覆 URL] 設定為 API 應用程式的 URL。第二個值可讓 AAD 租用戶中的使用者登入和存取 API 應用程式。

### 確認 API 應用程式已受到保護

1. 在瀏覽器中，移至 API 應用程式的 URL：在 Azure 入口網站的 [API 應用程式] 刀鋒視窗中，按一下 [URL] 下方的連結。 

	由於未經驗證的要求不得觸達 API 應用程式，因此系統會將您重新導向至登入畫面。

	如果瀏覽器的確移至 Swagger UI，則表示瀏覽器可能已登入，若是如此，請開啟 InPrivate 或 Incognito 視窗並移至 Swagger UI URL。

18. 使用 AAD 租用戶中使用者的認證進行登入。

	登入後，[已成功建立] 頁面隨即出現在瀏覽器中。

## 設定 ToDoListAPI 專案以取得和傳送 Azure AD 權杖

您將在本節執行下列工作：

* 在中介層 API 應用程式中新增使用 Azure AD 應用程式認證的程式碼，以取得權杖，然後透過 HTTP 要求將其傳送至資料層 API 應用程式。
* 從 Azure AD 取得您需要的認證。
* 在中介層 API 應用程式的 Azure App Service 執行階段環境設定中輸入認證。 

### 設定 ToDoListAPI 專案以取得和傳送 Azure AD 權杖

在 Visual Studio 中對 ToDoListAPI 專案進行下列變更。

1. 取消註解 ServicePrincipal.cs 檔案中的所有程式碼。

	這便是使用適用於 .NET 的 ADAL 來取得 Azure AD 持有人權杖的程式碼。此程式碼會使用數個設定值，而您稍後將會在 Azure 執行階段環境中設定這些值。此程式碼如下：

		public static class ServicePrincipal
		{
		    static string authority = ConfigurationManager.AppSettings["ida:Authority"];
		    static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
		    static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
		    static string resource = ConfigurationManager.AppSettings["ida:Resource"];
		
		    public static AuthenticationResult GetS2SAccessTokenForProdMSA()
		    {
		        return GetS2SAccessToken(authority, resource, clientId, clientSecret);
		    }
		
		    static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
		    {
		        var clientCredential = new ClientCredential(clientId, clientSecret);
		        AuthenticationContext context = new AuthenticationContext(authority, false);
		        AuthenticationResult authenticationResult = context.AcquireToken(
		            resource,
		            clientCredential);
		        return authenticationResult;
		    }
		}

	附註：此程式碼需要 .NET 適用之 ADAL 的 NuGet 封裝 (Microsoft.IdentityModel.Clients.ActiveDirectory)，其早已安裝於專案中。如果您是從頭建立此專案，則必須安裝此封裝。API 應用程式的新專案範本不會自動安裝此封裝。

2. 在 Controllers/ToDoListController 中，將 `NewDataAPIClient` 方法中會將權杖新增至 HTTP 要求之 authorization 標頭的程式碼取消註解。

		client.HttpClient.DefaultRequestHeaders.Authorization =
		    new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. 部署 ToDoListAPI 專案 (以滑鼠右鍵按一下專案，然後按一下 [發佈] > [發佈])。

	Visual Studio 會部署專案並將瀏覽器開啟至 Web 應用程式的基底 URL。這會顯示 403 錯誤頁面，這是嘗試從瀏覽器移至 Web API 基底 URL 時的正常現象。

4. 關閉瀏覽器。

### 取得 Azure AD 設定值

11. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，移至 [Azure Active Directory]。

12. 在 [目錄] 索引標籤中，選取您的 AAD 租用戶。

14. 按一下 [應用程式] > [我的公司擁有的應用程式]，然後按一下核取記號。

15. 在應用程式清單中，按一下當您針對 ToDoListDataAPI (資料層) API 應用程式啟用驗證時 Azure 為您建立的應用程式名稱。

16. 按一下 [設定] 索引標籤。

5. 複製 [用戶端識別碼] 值，並將其儲存至可在稍後取得此值的位置。

8. 在 Azure 傳統入口網站中，返回 [我公司所擁有的應用程式] 清單，然後按一下您為中介層 ToDoListAPI API 應用程式 (您在先前的教學課程中所建立，不是您在本教學課程中建立) 建立的 AAD 應用程式。

16. 按一下 [設定] 索引標籤。

5. 複製 [用戶端識別碼] 值，並將其儲存至可在稍後取得此值的位置。

6. 在 [金鑰] 中，從 [選取持續時間] 下拉式清單選取 [1 年]。

6. 按一下 [儲存]。

	![產生應用程式金鑰](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. 複製金鑰值，並將其儲存至可在稍後取得此值的位置。

	![複製新的應用程式金鑰](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### 在中介層 API 應用程式的執行階段環境中設定 Azure AD 設定

1. 移至 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至裝載 TodoListAPI (中介層) 專案之 API 應用程式的 [API 應用程式] 刀鋒視窗。

2. 按一下 [設定] > [應用程式設定]。

3. 在 [應用程式設定] 區段中，新增下列金鑰和值：

	| 金鑰 | ida:Authority |
	|---|---|
	| 值 | https://login.microsoftonline.com/{your Azure AD 租用戶名稱} |
	| 範例 | https://login.microsoftonline.com/contoso.onmicrosoft.com |

	| 金鑰 | ida:ClientId |
	|---|---|
	| 值 | 呼叫端應用程式 (中介層 - ToDoListAPI) 的用戶端識別碼 |
	| 範例 | 960adec2-b74a-484a-960adec2-b74a-484a |

	| 金鑰 | ida:ClientSecret |
	|---|---|
	| 值 | 呼叫端應用程式 (中介層 - ToDoListAPI) 的應用程式金鑰 |
	| 範例 | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

	| 金鑰 | ida:Resource |
	|---|---|
	| 值 | 呼叫端應用程式 (資料層 - ToDoListAPI) 的用戶端識別碼 |
	| 範例 | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

	附註︰針對 `ida:Resource`，請確定您使用呼叫的應用程式的用戶端識別碼，而非其應用程式識別碼 URI。

	`ida:ClientId` 和 `ida:Resource` 對本教學課程是不同的值，因為您正在為中介層和資料層使用個別的 Azure AD 應用程式。如果您讓呼叫端 API 應用程式和受保護的 API 應用程式使用同一個 Azure AD 應用程式，則會在 `ida:ClientId` 和 `ida:Resource` 中使用相同的值。

	程式碼會使用 ConfigurationManager 來取得這些值，因此這些值可以儲存在專案的 Web.config 檔案或 Azure 執行階段環境中。當 ASP.NET 應用程式在 Azure App Service 中執行時，環境設定會自動覆寫 Web.config 的設定。一般來說，環境設定是[比 Web.config 檔案更安全的機密資訊儲存方式](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)。

6. 按一下 [儲存]。

	![按一下 [Save] (儲存)。](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### 測試應用程式

1. 在瀏覽器中，移至 AngularJS 前端 Web 應用程式的 HTTPS URL。

2. 按一下 [待辦事項清單] 索引標籤，並以 Azure AD 租用戶中使用者的認證進行登入。

4. 新增待辦事項項目，以確認應用程式可以運作。

	![待辦事項清單頁面](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

	如果應用程式未如預期般運作，請仔細檢查所有您在 Azure 入口網站中輸入的設定。如果所有設定看起來都沒問題，請參閱本教學課程稍後的[疑難排解](#troubleshooting)一節。

## 防止瀏覽器存取 API 應用程式

在本教學課程中，您已為 ToDoListDataAPI (資料層) API 應用程式建立了個別的 Azure AD 應用程式。如您所見，App Service 在建立 AAD 應用程式時，會將 AAD 應用程式設定成可讓使用者在瀏覽器中移至 API 應用程式的 URL 來進行登入。這表示不只是服務主體，連 Azure AD 租用戶中的使用者都有可能可以存取 API。

如果您想要防止瀏覽器存取，但不要在受保護的 API 應用程式中撰寫任何程式碼，您可以變更 AAD 應用程式中的 [回覆 URL]，使其不同於 API 應用程式的基底 URL。

### 停用瀏覽器存取

1. 在傳統入口網站的 [設定] 索引標籤中，針對為 TodoListService 所建立的 AAD 應用程式，變更 [回覆 URL] 欄位中的值，以讓它屬於有效 URL，但卻不是 API 應用程式的 URL。
 
2. 按一下 [儲存]。

### 確認瀏覽器存取無法再運作

您稍早已確認過，您可以從瀏覽器以個別使用者的認證進行登入，藉此來移至 API 應用程式的 URL。在本節中，您將要確認此動作已不可行。

1. 在新的瀏覽器視窗中，再次移至 API 應用程式的 URL。

2. 在系統提示時進行登入。

3. 登入成功，但卻導致錯誤頁面。

	您已設定 AAD 應用程式，讓 AAD 租用戶中的使用者無法從瀏覽器登入和存取 API。您仍然可以使用服務主體權杖存取 API 應用程式，若要確認這一點，請移至 Web 應用程式的 URL，並新增更多待辦事項項目。

## 限制對特定服務主體的存取  

現在，Azure AD 租用戶中可以取得使用者或服務主體之權杖的任何呼叫端，都可以呼叫 TodoListDataAPI (資料層) API 應用程式。您或許會想要確定資料層 API 應用程式只會接受 TodoListAPI (中介層) API 應用程式的呼叫，以及特定服務主體的呼叫。

您可以透過新增程式碼來驗證傳入呼叫上的 `appid` 和 `objectidentifier` 宣告，以便加上這些限制。

在本教學課程中，您會將用來驗證應用程式識別碼和服務主體識別碼的程式碼直接放在控制器的動作之中。或者，您也可以使用自訂 `Authorize` 屬性，或是在啟動順序 (例如 OWIN 中介軟體) 中執行這項驗證。如需後者的範例，請參閱[此範例應用程式](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs)。

對 TodoListDataAPI 專案進行下列變更。

2. 開啟 Controllers/TodoListController.cs 檔案。

3. 取消註解用來設定 `trustedCallerClientId` 和 `trustedCallerServicePrincipalId` 的程式行。

		private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
		private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. 取消註解 CheckCallerId 方法中的程式碼。控制器中的每個動作方法開始時會呼叫此方法。

		private static void CheckCallerId()
		{
		    string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
		    string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		    if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
		    {
		        throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
		    }
		}

5. 將 ToDoListDataAPI 專案重新部署至 Azure App Service。

6. 在瀏覽器中，移至 AngularJS 前端 Web 應用程式的 HTTPS URL，然後在首頁中按一下 [待辦事項清單] 索引標籤。

	對後端的呼叫會失敗，因此應用程式不會運作。新的程式碼會檢查實際的 appid 和 objectidentifier，但它還沒有正確值可用來對這兩項進行檢查。瀏覽器的開發人員工具主控台會報告伺服器傳回 HTTP 401 錯誤。

	![開發人員工具主控台中發生錯誤](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

	在下列步驟中，您將會設定預期值。

8. 使用 Azure AD PowerShell，為針對 TodoListWebApp 專案所建立的 Azure AD 應用程式取得服務主體的值。

	a.如需有關如何安裝 Azure PowerShell 和連線至訂用帳戶的指示，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。

	b.若要取得服務主體清單，請依序執行 `Login-AzureRmAccount` 命令和 `Get-AzureRmADServicePrincipal` 命令。

	c.尋找 TodoListAPI 應用程式之服務主體的 objectid，並將它儲存在您稍後可以從中複製此值的位置。

7. 在 Azure 入口網站中，瀏覽至您部署 ToDoListDataAPI 專案所在之 API 應用程式的 [API 應用程式] 刀鋒視窗。

9. 按一下 **[設定] > [應用程式設定]**。

3. 在 [應用程式設定] 區段中，新增下列金鑰和值：

	| 金鑰 | todo:TrustedCallerServicePrincipalId |
	|---|---|
	| 值 | 呼叫端應用程式的服務主體識別碼 |
	| 範例 | 4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |

	| 金鑰 | todo:TrustedCallerClientId |
	|---|---|
	| 值 | 呼叫端應用程式的用戶端識別碼 - 從 TodoListAPI Azure AD 應用程式複製而來 |
	| 範例 | 960adec2-b74a-484a-960adec2-b74a-484a |

6. 按一下 [儲存]。

	![按一下 [Save] (儲存)。](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. 在瀏覽器中，返回 Web 應用程式的 URL，然後在首頁中按一下 [待辦事項清單] 索引標籤。

	受信任呼叫端的應用程式識別碼和服務主體識別碼皆為預期值，因此這次應用程式會如預期般運作。

	![待辦事項清單頁面](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## 從頭建置專案

兩個 Web API 專案是透過使用 Azure API 應用程式專案範本並以 ToDoList 控制器取代預設值控制器所建立。為了在 ToDoListAPI 專案中取得 Azure AD 服務主體權杖，我們已安裝 [.NET 適用的 Active Directory 驗證程式庫 (ADAL)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) NuGet 封裝。
 
如需如何使用 ToDoListAngular 之類的 Web API 後端建立 AngularJS 單一頁面應用程式的相關資訊，請參閱[實習實驗室：使用 ASP.NET Web API 和 Angular.js 建置單一頁面應用程式 (SPA)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)。如需如何新增 Azure AD 驗證程式碼的相關資訊，請參閱[使用 Azure AD 保護 AngularJS 單一頁面應用程式](../active-directory/active-directory-devquickstarts-angular.md)。

## 疑難排解

[AZURE.INCLUDE [疑難排解](../../includes/app-service-api-auth-troubleshooting.md)]

* 請確定不要混淆 ToDoListAPI (中介層) 和 ToDoListDataAPI (資料層)。例如，您會在本教學課程中將驗證新增至資料層 API 應用程式，但應用程式金鑰必須來自您為中介層 API 應用程式建立的 Azure AD 應用程式。

## 後續步驟

這是開始使用 API Apps 系列的最後一篇文章。

如需 Azure Active Directory 的詳細資訊，請參閱下列資源。

* [Azure AD 開發人員指南](http://aka.ms/aaddev)
* [Azure AD 案例](http://aka.ms/aadscenarios)
* [Azure AD 範例](http://aka.ms/aadsamples)

	[WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) 範例類似於本教學課程中顯示的項目，但是未使用 App Service 驗證。

如需了解藉由使用 Visual Studio，或是藉由使用[原始檔控制系統](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)來[自動化部署](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)，以將 Visual Studio 專案部署到 API 應用程式的其他方式相關資訊，請參閱[如何部署 Azure App Service 應用程式](../app-service-web/web-sites-deploy.md)。

<!---HONumber=AcomDC_0323_2016-->