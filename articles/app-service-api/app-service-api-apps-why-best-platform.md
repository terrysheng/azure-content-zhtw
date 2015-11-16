<properties 
	pageTitle="API Apps 概觀" 
	description="了解 Azure App Service 為何是開發、發佈及裝載 RESTful API 的最佳平台。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="tdykstra"/>

# API Apps 概觀

API Apps 是 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 提供的四個應用程式類型之一。

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

API Apps 提供一個豐富的平台，可讓您在雲端和內部部署中建置、裝載與取用 API。將您的 API 部署為 App Service 中的 API 應用程式，即可受益於企業級安全性、簡單的存取控制、混合式連線、自動 SDK 產生，並與 [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) 完美整合。

## 為何採用 API 應用程式？

API Apps 提供公開預覽的下列立即可用功能：

- **輕鬆使用** - 使用整合的 [Swagger](#concepts) 支援，讓各種用戶端都能輕鬆使用您的 API。輕鬆地設定 [CORS](#concepts)，就可以各種語言 (包括 C#、Java 和 Javascript) 自動為您的 API 產生用戶端程式碼。

- **簡單存取控制** - 您可以保護 API 應用程式，使其免於未經驗證的存取，且不必變更程式碼。其他服務或代表使用者的用戶端存取 API 時，內建的驗證服務會提供保護。支援的身分識別提供者包括 Azure Active Directory 和第三方提供者，例如 Facebook 和 Twitter。用戶端可以使用 Active Directory Authentication Library (ADAL) 或行動應用程式 SDK。如需詳細資訊，請參閱 [Azure App Service 中之 API 應用程式與行動裝置應用程式的驗證](../app-service/app-service-authentication-overview.md)。

- **Visual Studio 整合** - Visual Studio 中的專用工具可簡化建立、部署、使用、偵錯和管理 API Apps 的工作。

- **與邏輯應用程式整合** - 您建立的 API 應用程式可供 [App Service Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) 使用。

- **繼續使用現有的 API** - 您不必變更現有 API 的任何程式碼，就能利用 API Apps 功能，只要將程式碼部署至 API 應用程式即可。您的 API 可以使用 App Service 支援的任何語言或架構，包括 ASP.NET 和 C#、Java、PHP、Node.js 和 Python。

這些只是其中的幾個重點。如需 API Apps 可以利用的其他功能，請參閱 [Web Apps 概觀](../app-service-web/app-service-web-overview.md)。

>[AZURE.NOTE]您可以使用 [Azure API 管理](../api-management/api-management-key-concepts.md)來控制對 App Service API Apps 裝載的 API 的用戶端存取。雖然 API Apps 提供驗證服務，但有一些其他存取管理功能是它不提供而「API 管理」有提供的，例如端點合併和節流。
>
>API 應用程式目前處於公開預覽狀態。[App Service Web Apps](../app-service-web/app-service-web-overview.md) 是正式推出 (GA) 的服務，設計旨在建置及裝載全球規模的安全任務關鍵性應用程式。如果您目前正在尋找 GA 服務來組建 API，則 Web 應用程式正是最佳選項。當 API Apps 進入 GA 時，我們將提供一個採用現有 Web 應用程式並運用 API Apps 其他功能的路徑。

## API 應用程式概念 ##

- **Swagger** - 一種說明和探索 RESTful API 的架構，依預設用於 API Apps 中。如需詳細資訊，請參閱 [http://swagger.io/](http://swagger.io/)。
- **跨原始資源共用 (CORS)** -此種機制可讓 JavaScript 在瀏覽器中執行，以呼叫裝載於非網頁載入來源的網域上裝載的 API。
- **觸發程序** - 一種 REST API，[邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)可在符合特定條件時，呼叫它以初始化工作流程。例如，API 應用程式可以提供邏輯應用程式定期呼叫的方法，以在 Twitter 摘要中尋找特定詞組。如需詳細資訊，請參閱 [API 應用程式觸發程序](app-service-api-dotnet-triggers.md)。
- **動作** - 一種 REST API，[邏輯應用程式](../app-service-logic/app-service-logic-what-are-logic-apps.md)可在工作流程已由觸發程序啟動之後，呼叫它以處理資料.例如，API 應用程式可以提供一種方法，供邏輯應用程式呼叫，以回應 Twitter 觸發程序找到的推文。動作是由 Swagger API 定義公開的 API 方法。
- **閘道** - 一 種 Web 應用程式，可處理 API 管理功能，並驗證資源群組中的所有 API 應用程式。

## 開始使用

若要開始使用 API 應用程式，請遵循[建立 API 應用程式教學課程](app-service-dotnet-create-api-app.md)。

若要查看 API Apps 已知問題的清單，請參閱 [API Apps 已知問題論壇文章](https://social.msdn.microsoft.com/Forums/zh-TW/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps)。

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。

 

<!---HONumber=Nov15_HO2-->