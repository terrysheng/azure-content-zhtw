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
	ms.topic="get-started-article"
	ms.date="11/28/2015"
	ms.author="tdykstra"/>

# 在 Azure App Service 中 API Apps 的服務主體驗證

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概觀

本教學課程示範如何使用 Azure App Service 的驗證和授權功能來保護 API 應用程式，以及如何代表服務帳戶取用受保護的 API 應用程式。本教學課程中所示的驗證提供者是 Azure Active Directory，而且用戶端和 API 是在 API 應用程式中執行的 ASP.NET Web API。

## App Service 中的驗證與授權

如需本教學課程中所用驗證功能的簡介，請參閱此系列的上一個教學課程：[Azure App Service 中 API Apps 的驗證和授權](app-service-api-dotnet-get-started.md)。

## 如何遵循本教學課程

本教學課程會以您在 [API Apps 和 ASP.NET 入門系列的第一個教學課程](app-service-api-dotnet-get-started.md)中所下載並為其建立 API 應用程式的範例應用程式為基礎。

## CompanyUsers.API 範例專案

在 [ContactsList 範例應用程式](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list)中，CompanyUsers.API 專案是簡單的 Web API 專案，其中包含一個 Get 方法，它會傳回硬式編碼的連絡人清單。為了示範服務對服務案例，ContactsList.API 中的 Get 方法會呼叫 CompanyContacts.API 中的 Get 方法，並將取得的連絡人新增回它在自己的資料存放區中所擁有的任何項目，然後傳回合併的清單。

以下是 CompanyUsers.API 中的 Get 方法。

		public async Task<IEnumerable<Contact>> Get()
		{
		    var contacts = new Contact[]{
		                new Contact { Id = 1, EmailAddress = "nancy@contoso.com", Name = "Nancy Davolio"},
		                new Contact { Id = 2, EmailAddress = "alexander@contoso.com", Name = "Alexander Carson"}
		            };
		
		    return contacts;
		}


以下是 ContactsList.API 中的 Get 方法，顯示如何呼叫 CompanyContacts.API，並將結果新增至它傳回的項目。(為了清楚起見，這裡會省略部分程式碼。)

		private async Task<IEnumerable<Contact>> GetContacts()
		{
		    var contacts = await _storage.Get(FILENAME);
		
		    var contactsList = contacts.ToList<Contact>();
		    using (var client = CompanyContactsAPIClientWithAuth())
		    {
		        var results = await client.Contacts.GetAsync();
		        foreach (Contact c in results)
		        {
		            contactsList.Add(c);
		        }
		    }
		
		    return contactsList;
		}

上述程式碼中的 `CompanyContactsAPIClientWithAuth()` 所傳回的用戶端物件會以所產生的用戶端程式碼為基礎，但會在 HTTP 要求中新增授權權杖。

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## 在 Azure 中建立 API 應用程式並加以部署 CompanyContacts.API 專案

1. 在 [方案總管] 中，以滑鼠右鍵按一下 CompanyContacts.API 專案，然後按一下 [發佈]。

3.  在 [發佈 Web] 精靈的 [設定檔] 步驟中，按一下 [Microsoft Azure App Service]。

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. 如果您尚未登入，請登入您的 Azure 帳戶；或者如果過期，請重新整理您的認證。

4. 在 [App Service] 對話方塊中，選擇您想要使用的 Azure [訂用帳戶]，然後按一下 [新增]。

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. 在 [建立 App Service] 對話方塊的 [主控] 索引標籤中，按一下 [變更類型]，然後按一下 [API 應用程式]。

4. 輸入在 *azurewebsites.net* 網域中唯一的 [API 應用程式名稱]。

6. 在 [資源群組] 下拉式清單中，選取您已在這些教學課程中使用的資源群組。

4. 在 [App Service 計劃] 下拉式清單中，選取您已在這些教學課程中使用的計劃。

7. 按一下 [建立]。

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio 會建立 API 應用程式，並建立發佈設定檔，其中包含新的 API 應用程式所需要的所有設定。

8. 在 [發佈 Web] 精靈的 [連接] 索引標籤中，按 [發佈]。

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio 會將專案部署到新的 API 應用程式，並在瀏覽器中 API 應用程式的 URL。[已成功建立] 頁面隨即出現在瀏覽器中。

9. 關閉瀏覽器。

## 更新 ContactsList.API 專案中產生的用戶端程式碼

ContactsList.API 專案已有產生的用戶端程式碼，但是您要將其刪除，然後從自己的 API 應用程式重新產生。

1. 在 Visual Studio [方案總管] 的 ContactsList.API 專案中刪除 [CompanyContactsAPI] 資料夾。

2. 以滑鼠右鍵按一下 ContactsList.API 專案，然後按一下 [新增] > [REST API 用戶端]。

3. 在 [加入 REST API 用戶端] 對話方塊中，按一下 [從 Microsoft Azure API 應用程式下載]，然後按一下 [瀏覽]。

8. 在 [App Service] 對話方塊中，展開您在本教學課程中使用的資源群組，然後選取您剛才建立的 API 應用程式。

	如果在清單中沒有看到 API 應用程式，可能是因為您在建立 API 應用程式時，不小心略過了將類型從 Web 應用程式變更為 API 應用程式的步驟。在此情況下，您可以藉由重複進行稍早的步驟建立新的 API 應用程式。除非您先前往入口網站刪除 Web 應用程式，否則您必須為 API 應用程式選擇不同的名稱。

10. 按一下 [確定]。

9. 在 [加入 REST API 用戶端] 對話方塊中，按一下 [確定]。

	Visual Studio 會建立以 API 應用程式命名的資料夾，並且產生用戶端類別。

## 更新 ContactsList.API 中的程式碼，並且部署專案

在 ContactsList.API 中呼叫 CompanyContacts.API 的程式碼會註解為先前教學課程。本節中您將該程式碼取消註解並且部署應用程式。

1. 在 ContactsList.API 專案中，開啟 *Controllers/ContactsController.cs*。

2. 在 `ContactsController` 類別頂端附近，在使用產生的用戶端類別來新增授權權杖的程式碼中，將類別名稱 `CompanyContactsAPI` 取代為 API 應用程式所產生的類別名稱。

	例如，如果 API 應用程式的名稱為 CompanyContactsAPI3，程式碼看起來如下所示：

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. 在 `Get` 方法中，取消註解負責呼叫 CompanyContacts.API 的程式碼區塊。

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. 以滑鼠右鍵按一下 ContactsList.API 專案，然後按一下 [發佈]。

	[發佈 Web] 精靈會開啟至您先前使用的發佈設定檔。

3. 在 [發行 Web] 精靈中，按一下 [發行]。

	Visual Studio 會部署專案並將瀏覽器視窗開啟至 API 應用程式基底 URL。請關閉此瀏覽器視窗。

## 在 Azure 中設定新 API 應用程式的驗證和授權

1. 在 [Azure 入口網站](https://portal.azure.com/) 中，瀏覽至您在本教學課程中為 CompanyContacts.API 專案建立的 API 應用程式的 API 應用程式刀鋒視窗，然後按一下 [設定]。

2. 尋找 [功能] 區段，然後按一下 [驗證/授權]。

3. 在 [驗證/授權] 刀鋒視窗中，按一下 [開啟]。

4. 在 [要求未經驗證時所採取的動作] 下拉式清單中，選取 [登入 Azure Active Directory]。

5. 在 [驗證提供者] 底下，按一下 [Azure Active Directory]。

6. 在 [Azure Active Directory 設定] 刀鋒視窗中，按一下 [快速]。

	Azure 會自動在 AAD 租用戶中建立 AAD 應用程式。請記下新的 AAD 應用程式的名稱，因為您稍後在移至 Azure 傳統入口網站以取得其用戶端識別碼時需要選取它。

7. 按一下 [確定]。

10. 在 [驗證/授權] 刀鋒視窗中，按一下 [儲存]。
 
11. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，移至 [Azure Active Directory]。

12. 在 [目錄] 索引標籤中，選取您的 AAD 租用戶。

14. 按一下 [應用程式] > [我的公司擁有的應用程式]，然後按一下核取記號。

15. 在應用程式清單中，按一下當您針對 API 應用程式啟用驗證時 Azure 為您建立的應用程式名稱。

16. 按一下 [設定]。

17. 一直保持頁面開啟，讓您可以複製並貼上值，並且在稍後的教學課程步驟中更新頁面上的值。

## 更新執行 ContactsList.API 專案程式碼的 API 應用程式設定

3. 在另一個瀏覽器視窗中，移至[傳統 Azure 入口網站](https://manage.windowsazure.com/)，然後移至您為 ContactsList.API API 應用程式建立的 AAD 應用程式的 [設定] 索引標籤。

5. 在 [金鑰] 中，從 [選取持續時間] 下拉式清單選取 [1 年]。

6. 按一下 [儲存]。

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. 複製金鑰值。

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

1. 在另一個瀏覽器視窗中，移至 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至 ContactsList.API 專案所部署到的 API 應用程式的 API 應用程式刀鋒視窗 (這是呼叫端 API 應用程式，而非受到呼叫的 API 應用程式：是 ContactsList.API，而非 CompanyContacts.API)。

2. 按一下 **[設定] > [應用程式設定]**。

3. 在 [應用程式設定] 區段中，新增名為 ida:ClientSecret 的金鑰，並且在 [值] 欄位中貼上您剛剛建立的金鑰。

3. 新增名為 ida:ClientId 的金鑰，並且在 [值] 欄位中貼上來自相同 AAD [設定] 頁面的用戶端識別碼。

4. 新增名為 ida:Authority 的金鑰，並且在 [值] 欄位中輸入 "https://login.windows.net/{您的租用戶}"，例如 "https://login.windows.net/contoso.onmicrosoft.com"。

3. 在傳統 Azure 入口網站中，請移至您為 CompanyContacts.API API 應用程式建立的 AAD 應用程式的 [設定] 索引標籤。

4. 複製 [用戶端識別碼]。

3. 在 Azure 入口網站 [應用程式設定] 刀鋒視窗的 [應用程式設定] 區段中，新增名為 ida:Resource 的金鑰，並且在 [值] 欄位中貼上您剛剛複製的用戶端識別碼。

4. 新增名為 CompanyContactsAPIUrl 的金鑰，並且在 [值] 欄位中輸入 "https://{您的 API 應用程式名稱}.azurewebsites.net"，例如 "https://companycontactsapi.azurewebsites.net"。

6. 按一下 [儲存]。

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## 在 Azure 中測試

1. 移至您部署 ContactsList.Angular.AAD 專案所在的 Web 應用程式的 URL。

2. 按一下 [連絡人] 索引標籤，然後登入。

	您會看到 [連絡人] 頁面上具有從 CompanyContacts.API API 應用程式擷取的其他連絡人。

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

跟在上一個教學課程中一樣，您也可以使用 localhost SSL URL 設定 Visual Studio 專案，並在本機執行應用程式。在此情況下，您可以在 Web.config 檔案中儲存為了在 Azure 中執行而儲存在 Azure 中的設定 (用戶端識別碼、用戶端密碼等等)。不過請小心進行，不要將具有用戶端密碼等敏感資訊的 Web.config 檔案簽入到原始檔控制。如需詳細資訊，請參閱[將密碼和其他機密資料部署到 ASP.NET 和 Azure App Service 的最佳作法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)。

## 防止瀏覽器存取 API 應用程式

在本教學課程中，您已使用 Azure 入口網站的 [快速] 選項，設定您想要用服務主體驗證來存取之 API 應用程式的 AAD 驗證。根據預設，App Service 會將新的 AAD 應用程式設定成可讓使用者在瀏覽器中移至 API 應用程式的 URL 來進行登入。這表示不只是服務主體，連使用者都有可能可以存取 API。如果您只想讓服務主體能夠存取 API，您可以變更 AAD 應用程式中的 [回覆 URL]，使其不同於 API 應用程式的基底 URL，藉此來防止瀏覽器存取。

### 確認瀏覽器存取是否能運作

1. 在新的瀏覽器視窗中，移至您為 CompanyContacts.API 專案建立的 API 應用程式的 HTTPS URL。

	瀏覽器會移至登入畫面。
	
2. 在 AAD 租用戶中以使用者的認證進行登入。

3. 瀏覽器會顯示 API 應用程式的「建立成功」畫面。

	如果已啟用 Swagger UI，您將可以移至 `/swagger` URL 並呼叫 API。您可以在 URL 中新增 `/api/contacts/get` 以從瀏覽器呼叫 API。

### 停用瀏覽器存取

1. 在傳統入口網站的 [設定] 索引標籤中，針對為 CompanyContacts.API 專案所建立的 AAD 應用程式，變更 [回覆 URL] 欄位中的值，以讓它屬於有效 URL，但卻不是 API 應用程式的 URL。
 
2. 按一下 [儲存]。

### 確認瀏覽器存取無法再運作

1. 在新的瀏覽器視窗中，再次移至 API 應用程式的 URL。

2. 在系統提示時進行登入。

3. 登入成功，但卻導致錯誤頁面。

	您仍然可以使用服務主體權杖存取 API 應用程式，但 AAD 租用戶中的使用者無法登入並從瀏覽器存取 API。

## 後續步驟

這是開始使用 API Apps 系列的最後一個教學課程。本節提供進一步了解如何使用 API 應用程式的其他建議。

* 部署 App Service 應用程式的其他方式

	如需了解藉由使用 Visual Studio，或是藉由使用[原始檔控制系統](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)來[自動化部署](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)，來將 Web 專案部署到 Web 應用程式的其他方式，請參閱[如何部署 Azure Web 應用程式](web-sites-deploy.md)。

	Visual Studio 也可產生您可以用來將部署自動化的 Windows PowerShell 指令碼。如需詳細資訊，請參閱[自動化各個項目 (使用 Azure 建置真實世界的雲端應用程式)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything) (英文)。

* 如何疑難排解 App Service 應用程式

	Visual Studio 提供的功能，讓您能輕鬆檢視即時產生的 Azure 記錄。您也可以在 Azure 中遠端執行偵錯模式。如需詳細資訊，請參閱[在 Visual Studio 中疑難排解 Azure Web 應用程式](web-sites-dotnet-troubleshoot-visual-studio.md)。

* 如何新增自訂網域名稱和 SSL

	如需如何使用 SSL 和您自己的網域 (例如 www.contoso.com，而非 contoso.azurewebsites.net) 的相關資訊，請參閱下列資源：

	* [在 Azure App Service 中設定自訂網域名稱](web-sites-custom-domain-name.md)
	* [對 Azure 網站啟用 HTTPS](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1210_2015-->