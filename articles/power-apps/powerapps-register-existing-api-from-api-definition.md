<properties
	pageTitle="從 PowerApps Enterprise 中的 API 建立 Swagger 2.0 API 定義 | Microsoft Azure"
	description="了解如何透過使用現有 API 建立的 Swagger 2.0 API 定義註冊 API"
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
   ms.date="03/02/2016"
   ms.author="guayan"/>

# 從 Swagger 2.0 API 定義註冊 API  
許多組織已經有一些現有的 API，使用者可以在其應用程式內使用和取用這些 API。若要在您的應用程式中使用這些 API，您必須在 Azure 入口網站中「註冊」API，方法是使用 Managed API、App Service 環境中現有的 API 或使用 Swagger 來建立 API。

> [AZURE.SELECTOR]
- [Managed API](../articles/power-apps/powerapps-register-from-available-apis.md)
- [在 ASE 中的 API](../articles/power-apps/powerapps-register-api-hosted-in-app-service.md)
- [Swagger API](../articles/power-apps/powerapps-register-existing-api-from-api-definition.md)

在本主題中，我們著重在第三個選項：使用 Swagger 2.0 API 定義註冊其中一個您自己的 API (您從現有 API 建立的 Swagger 2.0 API 定義)。

#### 開始使用的必要條件

- 註冊 [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- 建立 [App Service 環境](powerapps-get-started-azure-portal.md)

## 使用 Swagger 2.0 API 定義註冊現有的 API

註冊這些現有的 API 非常簡單。步驟包括：

1. 為您現有的 API 建立 [Swagger 2.0](http://swagger.io) API 定義。PowerApps 使用 Swagger 2.0 作為 API 定義格式。您可以使用 [Swagger 2.0 網站](http://swagger.io)上所指的工具，幫助您輕鬆撰寫 Swagger 2.0 API 定義。注意事項：  

	- ``host`` 屬性應指向您現有 API 的實際端點。請勿使用配置或任何子路徑。例如，輸入 ``api.contoso.com``<br/><br/>
	- ``basePath`` 屬性應列出您現有 API 端點 (若有的話) 的子路徑。以正斜線開頭 ``/``。例如，輸入 ``/purchaseorderapi``。

2. 確定您的 App Service 環境能安全地存取現有的 API：

	1. 如果您偏好讓您的 API 可透過網際網路存取，您可以在您的 App Service 環境與您現有的 API 之間設定 HTTP 基本存取驗證。[更新現有的 API 以了解如何進行。](powerapps-configure-apis.md)<br/><br/>
	2. 如果您想要將 API 保留在您組織的網路內，您可以在 App Service 環境中設定虛擬網路，以安全地存取您的組織。深入了解 [App Service 環境](../app-service-web/app-service-app-service-environment-intro.md)。

3. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [PowerApps]，然後選取 [Manage APIs] \(管理 API)：  
![][11]
4. 在 [Manage APIs] \(管理 API) 中，選取 [Add] \(新增)：  
![][12]
5. 在 [Add API] \(新增 API) 中，輸入 API 屬性：  

	- 在 [Name] \(名稱) 中，輸入 API 的名稱。請注意，您輸入的名稱會包含在 API 的執行階段 URL 中。設定有意義且在組織中獨一無二的名稱。
	- 在 [**來源**] 中選取 [**從 Swagger 2.0 匯入**]。

6. 在 [**API 定義 (Swagger 2.0)**] 中，上傳您的 Swagger 2.0 API 定義檔案：  
 ![][13]
7. 選取 [**新增**] 以完成這些步驟。

> [AZURE.TIP] 註冊 API 時，您是向您的 App Service 環境註冊 API。一旦其在 App Service 環境中，相同 App Service 環境內的其他應用程式便能使用它。

## 摘要和後續步驟

在本主題中，您已經了解如何從 Swagger 2.0 API 定義註冊 API。以下是一些讓您深入了解 PowerApps 的相關主題與資源：

- [設定 API 原則](powerapps-configure-apis.md)
- [讓使用者存取 API](powerapps-manage-api-connection-user-access.md)
- [開始在 PowerApps 中建立應用程式](https://powerapps.microsoft.com/tutorials/)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_0309_2016-->

