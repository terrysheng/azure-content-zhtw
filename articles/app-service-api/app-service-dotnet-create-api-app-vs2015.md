<properties 
	pageTitle="在 Azure App Service 中使用 Visual Studio 2015 建立 ASP.NET API 應用程式" 
	description="了解如何在 Azure App Service 中使用 Visual Studio 2015 建立 ASP.NET API 應用程式" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="tdykstra"/>

# 在 Azure App Service 中使用 Visual Studio 2015 建立 ASP.NET API 應用程式

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)

## 概觀

在本教學課程中，您將使用 Visual Studio 2015 建立 ASP.NET Web API 2 專案，並設定它以部署到雲端，做為 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [API 應用程式](app-service-api-apps-why-best-platform.md)。您也會將專案部署至 Azure。在本教學課程的結尾，您將會在 Azure 雲端中執行 API 應用程式。

本教學課程假設您知道如何在 Visual Studio 的 [**總管**] 中使用檔案和資料夾。

本教學課程使用目前發行的 ASP.NET Web API 版本。如需有關如何建立 ASP.NET MVC 6 API 應用程式的資訊，請參閱這篇部落格文章：[https://alexanderzeitler.com/articles/Deploying-a-ASP-NET-MVC-6-API-as-Azure-API-App-in-Azure-App-Services/](https://alexanderzeitler.com/articles/Deploying-a-ASP-NET-MVC-6-API-as-Azure-API-App-in-Azure-App-Services/ "將 ASP.NET MVC 6 API 部署為 Azure App Services 中的 Azure API 應用程式")。

[AZURE.INCLUDE [install-sdk-2015-only](../../includes/install-sdk-2015-only.md)]

本教學課程需要 2.6 版或更新版本的 Azure SDK for.NET。

## 建立 API 應用程式專案 

Visual Studio 2015 RC 還沒有 API 應用程式專案範本，所以若要建立 API 應用程式專案，請從 Web API 專案範本著手。

1. 開啟 Visual Studio 2015 RC。

2. 按一下 **[檔案] > [新增專案]**。

3. 在 [**範本**] 底下按一下 [**Web**]，然後按一下 [**ASP.NET Web 應用程式**] 範本。

4. 將專案命名為 *ContactsList*

	![](./media/app-service-dotnet-create-api-app-vs2015/newproj.png)

5. 按一下 [確定]。

6. 在 [**新建 ASP.NET 專案**] 對話方塊的 [**ASP.NET 4.6 範本**] 之下，選取 [**空白**] 專案範本。

7. 選取 [**Web API**] 核取方塊。

8. 清除 [**雲端中的主機**] 核取方塊。

	![](./media/app-service-dotnet-create-api-app-vs2015/newaspnet.png)

7. 按一下 [確定]。

## 新增 NuGet 封裝

根據預設，API 應用程式專案會透過 Swashbuckle NuGet 封裝所提供的自動 [Swagger](http://swagger.io/ "官方 Swagger 資訊") 中繼資料產生啟用。當您安裝此封裝時，預設也會啟用 API 測試頁。

1. 按一下 **[工具] > [NuGet 封裝管理員] > [封裝管理員主控台]**。

2. 在 [**Package Manager Console**] 視窗中，輸入下列命令。

		install-package Swashbuckle

	在 PMC 顯示訊息表示正在檢查相依性之後，您可能必須等候數分鐘。

## 新增 API 應用程式中繼資料檔案

可讓 Web API 專案部署為 API 應用程式的中繼資料包含在 *apiapp.json* 檔案和 *Metadata* 資料夾中 (具有其子資料夾和檔案)。在下列步驟中，您會使用預設值新增這些檔案。

教學課程稍後的 [API 應用程式中繼資料](#api-app-metadata)一節，說明如何自訂此中繼資料。

1. 在專案資料夾中，建立名為 apiapp.json 的 *.json* 檔案，並以下列 JSON 文字取代新檔案的內容。

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsListTitle",
		    "summary": "Summary",
		    "author": "Author",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

3. 在專案資料夾中，建立名為 *Metadata* 的資料夾，然後在 *Metadata* 資料夾中，建立名為 *deploymentTemplates* 的資料夾。

5. 在 *deploymentTemplates* 資料夾中，建立名為 *apiappconfig.azureresource.json* 的 *.json* 檔案，並以下列 JSON 文字取代新檔案的內容。

		{
		  "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "$system": {
		      "type": "Object"
		    }
		  },
		  "resources": []
		}

## 新增 Web API 程式碼

在下列步驟中，您為簡單 HTTP Get 方法新增程式碼，以傳回寫在程式碼的連絡人清單。

1. 在專案資料夾中，建立 *Models* 資料夾。

2. 加入名為 *Contact.cs* 的類別檔案，並以下列程式碼取代檔案內容。

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. 以滑鼠右鍵按一下 [**控制器**] 資料夾，然後選取 [**新增 > 控制器**]。

	![](./media/app-service-dotnet-create-api-app-vs2015/05-new-controller-v3.png)

6. 在 [**新增 Scaffold**] 對話方塊中，選取 [**Web API 2 控制器 - 空的**] 選項，然後按一下 [**新增**]。

	![](./media/app-service-dotnet-create-api-app-vs2015/06-new-controller-dialog-v3.png)

7. 將控制器命名為 **ContactsController**，然後按一下 [**新增**]。

	![](./media/app-service-dotnet-create-api-app-vs2015/07-new-controller-name-v2.png)

8. 一旦建立 *ContactsController.cs* 檔案，就會以下列程式碼取代檔案內容。

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

## 測試 Web API

若要檢視 API 測試頁面，請執行下列步驟。

1. 在本機執行應用程式 (CTRL-F5)，然後導覽至 `/swagger`。 

	![](./media/app-service-dotnet-create-api-app-vs2015/14-swagger-ui.png)

2. 按一下 **[連絡人] > [取得] > [立即試用]**，您會看見 API 運作中並傳回預期的結果。

	![](./media/app-service-dotnet-create-api-app-vs2015/15-swagger-ui-post-test.png)

## 在 Azure 中建立 API 應用程式

1. 在 [Azure 預覽入口網站](https://portal.azure.com)中建立 API 應用程式。 

	* 按一下 **[新增] > [Web + 行動] > [API 應用程式]**。

		![](./media/app-service-dotnet-create-api-app-vs2015/createapiapp1.png)

	* 在 [**名稱**] 中輸入 ContactsList。

	* 在 [**應用程式服務方案**] 中按一下 [**建立新的**] 並輸入名稱，例如：**ContactsList**。

		如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md)。

	* 按一下 [**定價層**] 以取得選項清單，按一下 [**檢視全部**]，然後選取 [**免費**] 定價層。

		您可以使用已付費的定價層，但本教學課程不需要它。

	* 在 [**資源群組**] 中按一下 [**建立新的**] 並輸入名稱，例如：ContactsList。

		如需資源群組的詳細資訊，請參閱[使用資源群組管理您的 Azure 資源](resource-group-overview.md)。

	* 如果您有多個訂閱帳戶，請選取您要使用的訂閱帳戶。

	* 選擇接近您的位置。

	* 按一下 [建立]。

		![](./media/app-service-dotnet-create-api-app-vs2015/createapiapp2.png)

2. 當 Azure 完成 API 應用程式的建立時 (請參閱頁面左邊的 [**通知**])，將 API 應用程式的存取層級設為 [**公用 (匿名)**]。

	* 按一下 **[瀏覽] > [資源群組] > [您建立的資源群組] > [您建立的 API 應用程式]**。

	* 按一下 **[設定] > [應用程式設定]**。

	* 將 [**存取層級**] 設定為 [**公用 (匿名)**]。

		![](./media/app-service-dotnet-create-api-app-vs2015/setpublicanon.png)
	
2. 記下裝載 API 應用程式的基礎 Web 應用程式名稱。當您部署 Visual Studio 專案時，您將使用該應用程式。

	* 在 [**API 應用程式主機**] 下按一下 [**ContactsList**]。

		![](./media/app-service-dotnet-create-api-app-vs2015/clickapiapphost.png)

	* 名稱在 [**API 應用程式主機**] 刀鋒視窗的標題中。

		![](./media/app-service-dotnet-create-api-app-vs2015/apiapphostname.png)

## 將 Web API 專案部署到 Azure 中的新 API 應用程式
 
API 應用程式基本上是 Azure 提供其他功能作為 Web 服務功能的 Web 應用程式。在 Visual Studio 2015 RC 中，您會發行至 API 應用程式的基礎 Web 應用程式，因為 [發行 Web] 精靈沒有特別針對 API 應用程式的選取項目。

2. 在 Visual Studio 的 [**方案總管**] 中，以滑鼠右鍵按一下專案，然後在內容功能表中按一下 [**發行**]。

3. 在 [**發行 Web**] 精靈的 [**設定檔**] 步驟中，按一下 [**Microsoft Azure Web 應用程式**]。

	![](./media/app-service-dotnet-create-api-app-vs2015/pubwebselwebapp.png)

4. 在 [**現有 Web 應用程式**] 下拉式清單中，選取具有您先前所述 API 應用程式名稱的項目。

	![](./media/app-service-dotnet-create-api-app-vs2015/pubwebselapiapp.png)

5. 按一下 [發行]。

	您的瀏覽器會開啟至 Web 應用程式 URL，並顯示「已建立 API 應用程式」頁面。

6. 在瀏覽器位址列中，將 "swagger/" 新增至 URL 結尾，例如：

		https://microsoft-apiappb001b62a9033493a33748332233fca2.azurewebsites.net/swagger/

	您會看到稍早在本機執行時所見的相同 Swagger UI，但該 UI 現在在雲端執行。

2. 按一下 **[連絡人] > [取得] > [立即試用]**，您會看見 API 運作中並傳回預期的結果。

	![](./media/app-service-dotnet-create-api-app-vs2015/runninginazure.png)

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## 後續步驟

您現已使用 Visual Studio 2015 RC 建立及部署 API 應用程式。如需 API 應用程式的相關文件，請參閱顯示於頁面左邊 (適用於寬瀏覽器視窗) 或頁面頂端 (適用於窄瀏覽器視窗) 的瀏覽窗格中的項目。大部分 API 應用程式文件目前顯示 Visual Studio 2013，但其中許多內容適用於 VS 2015，因為 UI 類似，您所撰寫的程式碼相同，而且入口網站 UI 也相同。

<!---HONumber=58--> 