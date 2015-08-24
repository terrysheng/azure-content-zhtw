<properties
	pageTitle="SaaS 連接器和 API Apps 中的 OAUTH 安全性 | Azure"
	description="閱讀有關在 Azure App Service 的連接器和 API Apps 中的 OAUTH 安全性；微服務架構；saas"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="mandia"/>


# 了解 SaaS 連接器中的 OAUTH 安全性
許多軟體即服務 (SaaS) 連接器 (像是 Facebook、Twitter、DropBox 等) 要求使用者授權使用 OAUTH 通訊協定。當您使用來自 Logic Apps 的 SaaS 連接器時，我們會提供簡化的使用者體驗，您可以按一下 Logic Apps 設計工具中的 [授權]。當您**授權**時，您必須登入 (如果尚未登入) 並同意以您的名義連接到 SaaS 服務。當您同意並授權時，Logic Apps 就可以存取這些 SaaS 服務。

## 建立您自己的 SaaS 應用程式
這個簡化的體驗是可行的，因為我們先前已在這些 SaaS 服務中建立並註冊我們的應用程式。在某些情況下，您可能想要註冊及使用自己的應用程式。這是必要的，例如，當您想要在自訂應用程式中使用這些 SaaS 連接器時，例如[部署 SaaS 連接器 API 應用程式](app-service-api-connnect-your-app-to-saas-connector.md)。這個範例會使用 DropBox 連接器，但程序與所有依賴 OAUTH 的連接器相同。

即使在 Logic Apps 的內容中，您也可以使用自己的應用程式，而不是使用我們提供的預設應用程式。如果 [授權] 按鈕無法連接，您可以嘗試建立您自己的應用程式。以下列出 Twitter 連接器的步驟：

1. 在 Azure Preview 入口網站中開啟您的 Twitter 連接器。移至 [瀏覽] > API Apps。搜尋您的 Twitter 連接器：![][1]

2. 選取 [設定] > [驗證]：![][2]

3. 複製**重新導向 URI** 值：![][3]

4. 移至 [Twitter](http://apps.twitter.com) 並**建立新的應用程式**。在**回呼 URL** 屬性中，貼上從您的 Twitter 連接器複製的**重新導向 URI** 值：![][4]
5. 建立您的 Twitter 應用程式時，請選取 [**金鑰和存取權杖**。複製這些值。
6. 在您的 Twitter 連接器驗證設定中，將這些值貼到**用戶端識別碼**和**用戶端密碼**屬性：![][5]  
7. 儲存您的連接器設定。  

現在，您應該能夠從 Logic Apps 使用您的連接器。當您從 Logic Apps 使用此連接器時，它會使用您的應用程式，而不是使用預設的應用程式。

> [AZURE.NOTE]如果您先前已授權應用程式，您可能必須重新授權應用程式。


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

<!---HONumber=August15_HO7-->