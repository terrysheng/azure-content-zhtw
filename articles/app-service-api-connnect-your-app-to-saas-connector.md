<properties 
	pageTitle="將您的應用程式連接到現有的 SaaS 連接器" 
	description="本文示範如何連接到現有的 SaaS 連接器" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# 將您的應用程式連接到現有的 SaaS 連接器

## 概觀

本教學課程示範如何取用 Azure Marketplace 中的 SaaS 連接器。 

您會執行下列步驟：
- 佈建 Dropbox 連接器。
- 設定 Dropbox API 應用程式。
- 設定閘道。
- 取得權杖並儲存在權杖存放區中。 
- 呼叫 Dropbox API 並確認已驗證的存取權有作用。

## 佈建 Dropbox 連接器

1. 請移至預覽入口網站首頁，然後按一下 Marketplace。

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. 在 Marketplace 圖庫中搜尋 Dropbox。

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. 按一下 [Dropbox] 圖示來佈建 Dropbox 連接器。按一下 [建立] 按鈕來佈建 Dropbox 連接器。確定先填入名稱和所有欄位的所需值，再按一下 [建立] 按鈕。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4. 在[預覽入口網站](https://portal.azure.com/)中查看資源群組的刀鋒視窗。您會看到 Dropbox 連接器與閘道。當您佈建 API 應用程式時，您會將它佈建為資源群組的一部分。當入口網站為您建立資源群組時，它也會建立一個閘道。閘道是一個特別的 Web 應用程式，可處理以資源群組中的 API 應用程式為目標的所有要求。

	您可以將 Web 應用程式和其他 API 應用程式加入至相同的資源群組，而資源群組內的每個 API 應用程式可以有下列其中一個可存取性設定：

	* 公用 (匿名) - 任何人都可以從資源群組外部呼叫 API 應用程式，而不需登入。
	* 公用 (已驗證) - 只允許經過驗證的使用者從資源群組外部呼叫 API 應用程式。
	* 內部 - 只允許相同資源群組中的其他 API 應用程式或 Web 應用程式呼叫 API 應用程式。

## 設定 Dropbox API 應用程式

首先，設定 Dropbox 只接受經過驗證的要求。您會將其可存取性設定為 [**公用 (已驗證)**]，而且您會將閘道設定為需要提供者 (例如 Azure Active Directory、Google 或 Facebook) 的驗證。

1.	在 Azure [預覽入口網站](https://portal.azure.com/)中按一下 [**瀏覽 > API 應用程式**]，然後按一下您要保護的 API 應用程式名稱。

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	在 API 應用程式刀鋒視窗中，按一下 [設定]，然後按一下 [應用程式設定]。
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	在 [**應用程式設定**] 刀鋒視窗中，將 [**存取層級**] 變更為 [**公用 (已驗證)**]。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	您現已保護 Dropbox 連接器，使其免於遭受未經驗證的存取。現在即可設定 Dropbox 驗證。在以下所示的 UI 中填入 [用戶端識別碼] 和 [用戶端密碼] 詳細資料 (您可以從 [Dropbox 開發人員帳戶](https://www.dropbox.com/developers/apps)連接 [*用戶端識別碼*] 和 [用戶端密碼])。

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

接下來，您必須設定閘道來指定要使用的驗證提供者。

## 設定閘道

1. 回到 Dropbox API 應用程式刀鋒視窗，然後按一下閘道的連結。
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. 選擇您要使用的身分識別提供者，並遵循對應文章中的步驟，透過該提供者來設定您的 API 應用程式。這些文章是針對行動應用程式所撰寫的，但與 API 應用程式所用的程序相同。其中有些程序需要您同時使用[管理入口網站](https://manage.windowsazure.com/)和[預覽入口網站](https://portal.azure.com/)。
  
	- [Microsoft 帳戶](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Facebook 登入](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Twitter 登入](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Google 登入](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Azure Active Directory](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	查看「保護 API 應用程式：加入 Azure Active Directory 或社交提供者驗證」一文，以取得如何進行設定的逐步指示。 

## 取得 OAuth 權杖並儲存在權杖存放區中。

1.	在瀏覽器中，移至登入 URL： 

	您可以從閘道 Active Directory 安裝刀鋒視窗抓取 URL。
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	您的 url 會採用下列格式...
	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	例如，如果將您的資源群組命名為 'MyResource' 並設定閘道以供 Azure Active Directory 驗證，URL 會如下：

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	確定先啟動開發人員工具，然後在瀏覽器中輸入 URL 並按 Enter 鍵。我們應會收到登入完成訊息。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	從網路索引標籤 (chrome) 抓取 zumo 驗證權杖和值。
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	在 Chrome 中啟動 postman 延伸模組。輸入閘道同意 URL 和 Zumo 驗證權杖 (標頭) 和值，並提出 POST 要求。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	抓取 post 要求所傳回的重新導向 url，並確認重新導向 URL 有作用。 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	如果重新導向 URL 有作用，就表示權杖有效。對 SaaS 連接器所做的任何呼叫將使用我們所建立和驗證的權杖。此權杖必須隨附於來自通過閘道之外部來源的所有要求。當您使用瀏覽器存取 API 時，瀏覽器通常會在 cookie 中儲存權杖，並將它連同所有後續的呼叫一起傳送至 API。

相同的流程適用於所有 SaaS 連接器，例如 Safesforce、Facebook 等。 

## 呼叫 Dropbox API 並確認已驗證的存取權有作用。

1. 回到 API 應用程式清單並選取 Dropbox 連接器。 

1. 請記下最上方的 URL。

2. 按一下 Dropbox 連接器 (如圖所示)，以查看所有支援的 API。

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	您可以在 [API 定義] 刀鋒視窗中，看到 API 應用程式中支援哪些作業。您可以下載 swagger，以在 Visual Studio 中用來產生強型別用戶端。此外，您可以下載可用於 Sienna 和 PowerApp studio 的 swaddle。 

2. 在瀏覽器視窗中，輸入您從入口網站複製的 URL 並附加任何支援的 API，以存取 Dropbox 帳戶中的檔案或其他詳細資料。 

	格式：`<URL>`/Operation

	例如：

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	假設我們已建立正確的驗證，而上述呼叫成功並在瀏覽器中顯示 photos 資料夾詳細資料。 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--todo<Copy the image of the browser>-->

## 後續步驟

您已了解如何使用權杖存放區來保護 API 應用程式，以及設定閘道來存取 SaaS 連接器。如需詳細資訊，請參閱[什麼是 API 應用程式？](app-service-api-apps-why-best-platform.md). 

<!--HONumber=49-->