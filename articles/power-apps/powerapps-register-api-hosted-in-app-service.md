<properties
	pageTitle="在 PowerApps Enterprise 中開發或建立 App Service 環境代管的 API |Microsoft Azure"
	description="了解如何在 Azure 入口網站中註冊 App Service 環境代管的自訂 API"
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

# 註冊 App Service 環境代管的 API
PowerApps 支援註冊託管於雲端或內部部署任何位置的 API，其功能非常強大。在某些情況下，您可能想要開發或建立一些新的 API。例如，您可能會想要：

- 實作新的功能供貴組織使用。
- 建置在現有的功能或資料之上，可以為使用者提供更好的應用程式建置體驗。

當您在 App Service 環境託管 API 時，您會利用 [App Service 環境](../app-service-app-service-environment-intro.md)現有的全部功能，也會獲得較佳的整合經驗。

若要在應用程式中使用這些 API，您必須在 Azure 入口網站中「註冊」API。可以使用下列選項：

- 註冊 [Microsoft 管理的 API 或 IT 管理的 API](powerapps-register-from-available-apis.md)。
- 註冊 App Service 環境代管的 API。
- 使用 [Swagger 2.0 API 定義](powerapps-register-existing-api-from-api-definition.md)來註冊 API。

本文說明如何**註冊 App Service 環境代管的 API**。

#### 開始使用的必要條件

- 註冊 [PowerApps Enterprise](powerapps-get-started-azure-portal.md)。
- 建立 [App Service 環境](powerapps-get-started-azure-portal.md)。


## 在 App Service 環境中開發和部署 API

在 App Service 環境中開發 API 很簡單。選擇您慣用的程式設計語言建置 Web API，然後使用 [Swagger 2.0](http://swagger.io) 描述 API 定義。部分範例包括：

- [在 Azure App Service 中建置和部署 .NET](../app-service-api-dotnet-get-started.md)
- [在 Azure App Service 中建置和部署 Java API 應用程式](../app-service-api-java-api-app.md)
- [在 Azure App Service 中建置和部署 Node.js API 應用程式](../app-service-api-nodejs-api-app.md)

您也可以選擇在 App Service 環境中部署 Web API，包括從 Visual Studio 部署，以及使用原始檔控制系統連續部署。[在 Azure App Service 中部署 Web 應用程式](../web-sites-deploy.md)是很好的資源。

## 在 App Service 環境中註冊您的自訂 API

API 部署至 App Service 環境之後，請使用下列步驟註冊：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [PowerApps]，然後選取 [管理 API]：![][11]
2. 在 [管理 API] 中，選取 [加入]：![][12]  
3. 在 [加入 API] 中輸入 API 屬性：  

	- 在 [名稱] 中輸入 API 的名稱。請注意，您輸入的名稱會包含在 API 的執行階段 URL 中。請使用對貴組織有意義且為唯一的名稱。	
	- 在 [來源] 中選取 [從 App Service 環境代管的 API 匯入]：![][13]
4. 在 [App Service 環境代管的 API] 中選取要匯入的 API。這份清單會顯示已設定其 **apiDefinition.url** 屬性之 App Service 環境中的每一個 Web 應用程式、API 應用程式和行動應用程式。若要匯入 API，它會使用利用這個屬性公開的 Swagger 2.0 API 定義。當您註冊 API 時，請確定這個 URL 可公開存取：![][14]
5. 選取 [加入] 完成這些步驟。

> [AZURE.TIP]註冊 API 時，您是向您的 App Service 環境註冊 API。一旦其在 App Service 環境中，相同 App Service 環境內的其他應用程式便能使用它。

## 摘要和後續步驟
在本主題中，您已看到如何註冊 App Service 環境代管的 API。以下是一些讓您深入了解 PowerApps 的相關主題與資源：

- [設定 API](../powerapps-configure-apis.md)
- [加入新的 API](../powerapps-register-from-available-apis.md)

<!--Reference-->
[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png

<!---HONumber=AcomDC_1203_2015-->