<properties 
	pageTitle="在 Azure App Service 中部署 API 應用程式" 
	description="了解如何將 API 應用程式專案部署到您的 Azure 訂用帳戶。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="bradyg"/>

# 在 Azure App Service 中部署 API 應用程式 

## 概觀

在本教學課程中，您會將[前一個教學課程](app-service-dotnet-create-api-app.md)中建立的 Web API 專案部署到新的 [API 應用程式](app-service-api-apps-why-best-platform.md)。您將使用 Visual Studio，在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中建立 API 應用程式資源，並將您的 Web API 程式碼部署到 Azure API 應用程式。

### 其他部署選項

有許多部署 API 應用程式的其他方法。API 應用程式是一種 [Web 應用程式](../app-service-web/app-service-web-overview.md)，具有額外的功能來裝載 Web 服務，而且[可供 Web 應用程式使用的部署方法](../app-service-web/web-sites-deploy.md)也可與 API 應用程式搭配使用。裝載 API 應用程式的 Web 應用程式稱為 Azure 預覽入口網站中的 API 應用程式主機，而且您可以使用 API 應用程式主機入口網站刀鋒視窗來設定部署。如需 API 應用程式主機刀鋒視窗的相關資訊，請參閱[管理 API 應用程式](app-service-api-manage-in-portal.md)。

API 應用程式是以 Web 應用程式為基礎的事實，也表示您可以將針對 ASP.NET 以外平台撰寫的程式碼部署到 API 應用程式。如需使用 Git 將 Node.js 程式碼部署到 API 應用程式的範例。請參閱[在 Azure App Service 中建立 Node.js API 應用程式](app-service-api-nodejs-api-app.md)。
 
## <a id="provision"></a>在 Azure 中建立 API 應用程式 

在此區段中，您將使用 Visual Studio [發行 Web 精靈] 在 Azure 中建立 API 應用程式。指示您輸入 API 應用程式名稱時，請輸入 *ContactsList*。

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>將您的程式碼部署到新的 API 應用程式

您可使用同一個 [發行 Web 精靈] 將程式碼部署到新的 API 應用程式。

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## 在 Azure 預覽入口網站中檢視應用程式

在此區段中，您將檢視入口網站中 API 應用程式可用的基本設定，並對您的 API 應用程式進行反覆變更。每次部署時，入口網站都會反映您對 API 應用程式所做的變更。

1. 在 [Azure Preview 入口網站](https://portal.azure.com)中，移至所部署之 API 應用程式的 [API 應用程式] 刀鋒視窗。

4. 按一下 [**API 定義**]。
 
	應用程式的 [**API 定義**] 分頁會顯示您建立應用程式時定義的 API 作業清單。

	![API 定義](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. 現在，請回到 Visual Studio 中的專案，並將下列程式碼新增至 **ContactsController.cs** 檔案。此程式碼會新增 **Post** 方法，可以用來將新的 `Contact` 執行個體張貼至 API。

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![新增 Post 方法至控制器](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

	此程式碼會新增 **Post** 方法，可以用來將新的 `Contact` 執行個體張貼至 API。

6. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案，然後選取 [**發佈**]。

	![專案發佈內容功能表](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. 按一下 [設定] 索引標籤。

8. 從 [**設定**] 下拉清單中，選取 [**偵錯**]。

	![發佈 Web 設定](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. 按一下 [**預覽**] 索引標籤

10. 按一下 [**開始預覽**]，以檢視將進行的變更。

	![發佈 Web 對話方塊](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. 按一下 [發佈]。

12. 在發佈程序完成後，回到入口網站，然後關閉並重新開啟 [**API 定義**] 刀鋒視窗。您會看到您剛建立並直接部署到 Azure 訂閱中的新 API 端點。

	![API 定義](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## 後續步驟

您已了解 Visual Studio 中的直接部署功能如何讓您輕鬆逐一查看和快速部署您的 API，以及測試其是否運作正常。在[下一個教學課程](../app-service-dotnet-remotely-debug-api-app.md)中，您會了解如何在 API 應用程式於 Azure 中執行時進行偵錯。
 

<!---HONumber=August15_HO6-->