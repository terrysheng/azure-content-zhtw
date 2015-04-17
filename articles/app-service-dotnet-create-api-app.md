<properties 
	pageTitle="建立 Azure 應用程式服務 API 應用程式" 
	description="本文示範如何使用 Visual Studio 2013 在 Azure 應用程式服務中建立 API 應用程式" 
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
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# 在 Azure 應用程式服務中建立 API 應用程式

## 概觀

這是一系列教學課程中的第一個，協助您開始在 Azure 應用程式服務中使用 API 應用程式。

1. 在本教學課程中，您會建立新的 API 應用程式並準備部署到您的 Azure 訂用。 
* 在[部署 API 應用程式](app-service-dotnet-create-api-app.md)中，您會將 API 應用程式部署至您的 Azure 訂用。
* 在[偵錯 API 應用程式](app-service-dotnet-remotely-debug-api-app.md)中，您可以使用 Visual Studio，當程式碼在 Azure 中執行時進行遠端偵錯。

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## 建立第一個 API 應用程式 ##

開啟 Visual Studio 2013，然後選取 **[檔案] > [新增專案]**。選取 [**ASP.NET Web 應用程式**] 範本。  將專案命名為  *ContactsList*，然後按一下 [**確定**]。

![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

選取 [**Azure API 應用程式**] 專案範本，然後按一下 [**確定**]。

![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

以滑鼠右鍵按一下 Web API 專案中的 [**模型**] 資料夾，然後在內容功能表中選取 **[新增] > [類別]**。 

![](./media/app-service-dotnet-create-api-app/03-add-new-class-v3.png) 

將新檔案命名為  *Contact.cs*，然後按一下 [**新增**]。 

![](./media/app-service-dotnet-create-api-app/0301-add-new-class-dialog-v3.png) 

將檔案的整個內容取代為下列程式碼。 

	namespace ContactsList.Models
	{
		public class Contact
		{
			public int Id { get; set; }
			public string Name { get; set; }
			public string EmailAddress { get; set; }
		}
	}

以滑鼠右鍵按一下 [**控制器**] 資料夾，然後在內容功能表中選取 **[新增] > [控制器]**。 

![](./media/app-service-dotnet-create-api-app/05-new-controller-v3.png)

在 [**新增 Scaffold**] 對話方塊中，選取 [**Web API 2 控制器 - 空的**] 選項，然後按一下 [**新增**]。 

![](./media/app-service-dotnet-create-api-app/06-new-controller-dialog-v3.png)

將控制器命名為 **ContactsController**，然後按一下 [**新增**]。 

![](./media/app-service-dotnet-create-api-app/07-new-controller-name-v2.png)

將這個檔案中的程式碼取代為下列程式碼。 

	using ContactsList.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Net;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Web.Http;
	
	namespace ContactsList.Controllers
	{
	    public class ContactsController : ApiController
	    {
	        [HttpGet]
	        public IEnumerable<Contact> Get()
	        {
	            return new Contact[]{
					new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
					new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}

API 應用程式專案會透過自動 [Swagger](http://swagger.io/ "Official Swagger information") 中繼資料產生和 API 測試頁啟用。根據預設，API 測試頁會停用 。若要啟用 API 測試頁，請開啟  *App_Start/SwaggerConfig.cs* 檔案。搜尋 **EnableSwaggerUI**：

![](./media/app-service-dotnet-create-api-app/12-enable-swagger-ui-with-box.png)

將下列幾行程式碼取消註解：

        })
    .EnableSwaggerUi(c =>
        {

完成後，檔案在 Visual Studio 2013 中應如下所示。

![](./media/app-service-dotnet-create-api-app/13-enable-swagger-ui-with-box.png)

若要檢視 API 測試頁，請在本機執行應用程式並瀏覽至 `/swagger`。 

![](./media/app-service-dotnet-create-api-app/14-swagger-ui.png)

按一下 [**試試看**] 按鈕，您將看見 API 運作中並傳回預期的結果。 

![](./media/app-service-dotnet-create-api-app/15-swagger-ui-post-test.png)

## 後續步驟

您的 API 應用程式現已準備要部署，而您可以依照[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)教學課程進行部署。

如需詳細資訊，請參閱 [什麼事 API 應用程式？](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->