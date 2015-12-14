<properties
	pageTitle="在 Azure 入口網站中變更或更新您的 PowerApps API 屬性 |Microsoft Azure"
	description="新增自訂圖示、更新 XML 原則，或更新您 PowerApps API 的 Swagger 定義"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# 更新現有的 API 及其屬性

您在 App Service 環境中註冊的 API 實為您後端服務的 Proxy。當您建立的 API 之後，可能會想要變更其屬性。例如，您可能會想要：

- 為您的 API 新增自訂圖示。
- 變更 API 所使用之後端的保護方式。 
- 將 API 的顯示名稱更新為使用者易記的名稱。


#### 開始使用的必要條件

- 註冊 [PowerApps Enterprise](powerapps-get-started-azure-portal.md)。
- 建立 [App Service 環境](powerapps-get-started-azure-portal.md)。
- 在您的環境中註冊 [API](powerapps-register-from-available-apis)。

## 新增自訂圖示或新增使用者易記的顯示名稱

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您 API 的刀鋒視窗。
2. 選取 [所有設定]。
3. 在 [設定] 中，選取 [一般]：![][11]

您通常可以變更下列設定：

設定 | 說明
--- | ---
顯示名稱 | 在 PowerApps 的 [可用連線] 中列出時，通常會使用此名稱。預設會使用 API 的資源名稱，但對於您的 PowerApps 使用者而言，可能不是最好的名稱。您可以輸入的使用者易記的顯示名稱。例如，您可以將其命名為 **New Customer Orders** 或 **View Sales History**。  
圖示 URL | 您可以為您的 API 新增自訂圖示。在 PowerApps 的 [可用連線] 及 [我的連線] 中列出時，通常會使用此圖示。預設會使用下列圖示：<br/><br/>![][12] <br/><br/>當使用自訂圖示時：<br/><ul><li>圖示 URL 必須可供大眾存取。</li><li>其可以是 .png 或 .svg 檔案。若是使用 png 檔案，其大小為 40 x 40 像素。</li></ul>
URL 配置 | 選擇配置，或您希望 API 加以支援的配置。您可以選擇 [HTTP]、[HTTPS] 或 [HTTP 和 HTTPS]。預設會啟用 [HTTP 和 HTTPS]。<br/><br/>App Service 環境會依據後端設定自動設定配置。因此，若沒有其他配置需要設定，可以開發或變更您的後端服務。 
向後端服務驗證身分 | 在 App Service 環境中註冊您的後端服務之後，建議設定後端的安全性，讓用戶端只能使用您的 API 呼叫該後端服務。您可以依據後端的部署位置使用下列選項：<br/><br/><ul><li><strong>只可透過此 API 存取</strong>：僅當您的後端是部署在 App Service 環境中時，才可以使用此選項。如有選取，將會停用您的後端上的任何主機名稱。因為 API Proxy 也在同一個 App Service 環境中執行，所以其仍可存取您的後端。</li><li><strong>HTTP 基本驗證</strong>：無論您後端的部署在何處，皆可使用此選項。如有選取，必須輸入使用者名稱和密碼。當 Proxy 呼叫您的後端時，會使用 HTTP 基本驗證，在 HTTP 授權標頭中傳遞使用者名稱和密碼。最後，您的後端服務必須確認 (驗證) 輸入的使用者名稱及密碼。<br/><br/>如需深入了解 ASP.NET Web API 2 中實作的 HTTP 基本驗證，請參閱 [ASP.NET Web API 2 中的驗證篩選](http://www.asp.net/web-api/overview/security/authentication-filters)。</li></ul>


## 更新您 API 的 Swagger

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您 API 的刀鋒視窗。
2. 選取 [所有設定]。
3. 在 [設定] 中，選取 [API 定義]：![][13]

**Swagger 2.0** 是支援的 API 定義格式。目前的 API 定義位於內嵌的 JSON 編輯器中。您可以編輯該內嵌定義，或上傳新的 JSON 檔案。當您**儲存**變更之後，所有錯誤皆會顯示在此刀鋒視窗中，其中也包括 API 定義的所有錯誤。

- 如需深入了解 Swagger 2.0，請參閱 [Swagger 官方網站](http://swagger.io)。
- 開發您的 API 時，如需深入了解如何取得 Swagger 2.0，請參閱：  
	- [在 Azure App Service 中建立 ASP.NET API 應用程式](../app-service-dotnet-create-api-app.md)
	- [在 Azure App Service 中建置和部署 Java API 應用程式](../app-service-api-java-api-app.md)
	- [在 Azure App Service 中建置和部署 Node.js API 應用程式](../app-service-api-nodejs-api-app.md)
	- [自訂 Swashbuckle 產生的 API 定義](../app-service-api-dotnet-swashbuckle-customize.md)
- 如需深入了解使用 PowerApps Swagger 2.0 的最佳做法，請參閱[開發 PowerApps API](powerapps-develop-api.md)。

## 更新您 API 的 XML 原則

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您 API 的刀鋒視窗。
2. 選取 [所有設定]。
3. 在 [設定]，請選取 [原則]：![][14]

此原則是 [Azure API 管理](https://azure.microsoft.com/services/api-management)所支援的同一個原則。目前的原則內嵌在 XML 編輯器中。您可以編輯該內嵌原則，或上傳新的 XML 檔案。當您**儲存**變更之後，所有錯誤皆會顯示在此刀鋒視窗中，其中也包括 API 原則的所有錯誤。

[Azure API 管理中的原則](../api-management-howto-policies.md)十分適合用來深入了解設定及原則。


## 摘要和後續步驟
建立您的 API 之後，您可以使用本主題中的步驟變更其設定，甚至是自訂某些設定。

以下列出一些相關主題及資源，可以協助您深入了解 PowerApps：

- [設定 API 以連接到受 AAD 保護的後端](powerapps-configure-apis-aad.md)
- [開發 PowerApps 的 API](powerapps-develop-api.md)

[11]: ./media/powerapps-configure-apis/api-settings-general.png
[12]: ./media/powerapps-configure-apis/api-default-icon.png
[13]: ./media/powerapps-configure-apis/api-settings-api-definition.png
[14]: ./media/powerapps-configure-apis/api-settings-policy.png

<!---HONumber=AcomDC_1203_2015-->