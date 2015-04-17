<properties 
	pageTitle="將現有的 API 轉換成 API 應用程式" 
	description="了解如何將部署的 Web API Visual Studio 專案設定為 Azure 應用程式服務中的 API 應用程式。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# 將現有的 API 轉換成 API 應用程式

## 概觀

在本教學課程中，您會在 Visual Studio 中建立 ASP.NET Web API 專案，然後新增 NuGet 封裝和專案中繼資料，以便將專案發行及部署為 Azure 應用程式服務中的 API 應用程式。本教學課程也說明如何自訂 API 應用程式中繼資料。

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## 建立 Web API 專案

1.  在 Visual Studio 中開啟 [**新增專案**] 對話方塊。

2.  選取 [**已安裝的範本**] 窗格中的 [**雲端**] 節點，然後選取 [**ASP.NET Web 應用程式**] 範本。

3.  將專案命名為 *ContactsList*，然後按一下 [**確定**]。

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. 在 [**新增 ASP.NET 專案**] 對話方塊中，選取 [**空白**] 範本，按一下 [**Web API**] 核取方塊，清除 [**在雲端託管**] 核取方塊，然後按一下 [**確定**]。

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	Visual Studio 會建立空的 Web API 專案的專案檔案。

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. 在 [**方案總管**] 中，以滑鼠右鍵按一下 [**模型**] 資料夾，然後在內容功能表中按一下 **[新增] > [類別]**。 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. 將新檔案命名為 *Contact.cs*，然後按一下 [**新增**]。

5. 使用下列程式碼取代新檔案的內容。 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. 以滑鼠右鍵按一下 [**控制器**] 資料夾，然後在內容功能表中按一下 **[新增] > [控制器]**。 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. 在 [**新增 Scaffold**] 對話方塊中，選取 [**Web API 2 控制器 - 空的**] 選項，然後按一下 [**新增**]。 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. 將控制器命名為 **ContactsController**，然後按一下 [**新增**]。 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. 使用下列程式碼取代新控制器檔案中的程式碼。 

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
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


您現在有運作中的 Web API 專案。驗證是否運作的最簡單方法就是從瀏覽器呼叫 Get 方法。

6. 按 CTRL+F5 執行專案。

	瀏覽器會顯示 HTTP 403 錯誤，因為瀏覽器雖開啟但沒有指向 Get 方法的完整 URL。

7. 在瀏覽器位址列中將 "api/contacts/get/" 新增至 URL，然後按 Enter 鍵。  最終的 URL 會類似下列範例：

		http://localhost:25735/api/contacts/get/

	不同的瀏覽器會以不同的方式回應 API 呼叫。如果您使用 Internet Explorer，您會在瀏覽器視窗底部看到下載訊息：

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## 設定應用程式以便部署為 API 應用程式

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案 (而非方案)，然後按一下 **[新增] > [Azure API 應用程式 SDK]**。

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. 在 [**選擇 API 應用程式中繼資料來源**] 對話方塊中，按一下 [**自動中繼資料產生**]。

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	此選項會啟用動態 Swagger UI，您稍後可在教學課程中看到。此外，您可以提供靜態 Swagger API 定義檔案。若要這麼做，您應該選取 [**指定包含 Swagger 中繼資料的靜態 JSON 檔案**]，而 Visual Studio 會提示您上傳檔案。您上傳的檔案會在 *Metadata* 資料夾中儲存為  *apiDefinition.Swagger.json*。

3. 按一下 [**確定**]。

	Visual Studio 會加入  *apiapp.json* 檔案和  *Metadata* 資料夾。

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	Visual Studio 也會新增 Swashbuckle NuGet 封裝，而在下列步驟中您會嘗試 Swagger 提供的動態 API 定義 UI。 

6. 按 CTRL+F5 執行專案。

	如同前面，瀏覽器會顯示 HTTP 403 錯誤。

7. 在瀏覽器位址列中將 "swagger/" 新增至 URL，然後按 Enter 鍵。  最終的 URL 會類似下列範例：

		http://localhost:25735/swagger/

8. 在 Swagger 頁面中，按一下 [**連絡人**] 查看可用的方法。
 
	只會顯示  `Get`，因為這是您在連絡人控制器中建立的唯一方法。此頁面會顯示範例回應 JSON。

9. 按一下 Get 方法以查看範例 JSON 回應和 [**立即試用**] 按鈕。

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. 按一下 [**立即試用**]

	會傳回您在 ContactsController 中編碼的回應。

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

Web API 專案現在已準備要部署為 Azure 應用程式服務中的 API 應用程式。本教學課程的下列章節提供有關您可變更以自訂 API 應用程式的中繼資料相關資訊。 

## 檢閱 apiapp.json

 *apiapp.json* 檔案中的設定決定如何識別 API 應用程式以及如何在 Azure Marketplace 中加以呈現。在這個預覽版本中，Visual Studio 不具備將 API 應用程式發佈至 Marketplace 的功能，因此其中許多設定並沒有作用。

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

當您選擇 **Azure API 應用程式 SDK** 功能表項目時建立之檔案的初始內容如下列範例所示：

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

下表說明檔案內含欄位以及您加至檔案的其他選擇性欄位的格式與使用方式。 

**注意：**如上所述，此預覽版本中有許多欄位沒有作用，因為這些欄位用以決定在 Azure Marketplace 中呈現 API 應用程式的方式，但 Visual Studio 目前不包含將 API 應用程式發佈至 Marketplace 的功能。

| 名稱 (粗體 = 必要) | 類型 | 註解 |
|:-----------|:------------|:------------|
|**id**           |字串|此封裝的識別碼。在命名空間內必須是唯一的，而且不能包含句號、/ 或 @ 字元。當您部署專案時，Visual Studio 會驗證識別碼是否唯一並可讓您加以變更。 
|**namespace**    |字串|命名空間，連同 **id** 屬性可唯一識別 API 應用程式。這是必要屬性，但其值可能是空字串。 <br/><br/>這個屬性可讓您指定網域，例如 contoso.com。  如果您要使用的封裝識別碼已被接受，您即可新增網域以便使用該封裝識別碼。例如，如果 ContactsList 已存在於沒有命名空間的 API 應用程式組件庫，您可以新增具有 contoso.com 命名空間的 ContactsList 封裝。 <br/><br/>指定網域的另一個原因是要將封裝新增至只有您的組織成員可存取的 API 應用程式組件庫。 <br/><br/>將句號轉換為連字號，以及將連字號轉換為兩個連字號 (--) 之後，命名空間將會做為 Marketplace 中的發行者名稱。<br/><br/>Microsoft 提供的 API 應用程式的命名空間為 "microsoft.com"。  
|**version**      |字串|[Semver](http://docs.nuget.org/Create/Versioning) 格式，例如 1.0.1、1.1.0-alpha。
|**gateway**      |字串|閘道版本 (以日期表示)，例如：2015-01-14。 *gateway* 是特殊的 Web 應用程式，對資源群組中 API 應用程式的所有要求都會透過它路由傳送。它的其中一個主要功能是處理驗證。目前唯一的閘道版本是 2015-01-14。未來發行新的閘道版本時，此屬性將可讓您避免重大變更，並繼續使用先前的閘道 API。 
|**title**        |字串|API 應用程式的顯示名稱。
|**summary**      |字串|API 應用程式的簡短摘要，最多 100 個字元。
|description      |字串|API 應用程式的完整描述。可包含 HTML，最多 1500 個字元。
|**author**       |字串|API 應用程式的作者，最多 256 個字元。
|homepage         |URI|API 應用程式的首頁。
|endpoints        |object[]|可包含 API 定義端點的陣列 (只有一個元素)。 
|>>endpoints.apiDefinition|字串|動態 Swagger API 定義 UI (例如，"/swagger/docs/v1")，或靜態 Swagger API 定義檔的相對 URI。在 ASP.NET Web API 中，動態產生的 Swagger UI 通常是最佳選擇，但如果該 UI 不適用於您的 API，或您未使用 ASP.NET Web API，您可以提供靜態定義檔案。若要提供靜態定義檔案，您可以在此指定指向它的相對 URI，也可以將這個屬性留空並將靜態 API 定義檔案包含為中繼資料資料夾中的 [apiDefinition.swagger.json](#apidef)。 
|>>endpoints.status|URI|保留以供日後使用。
|categories       |string[]|決定封裝顯示在 Azure Marketplace 中的位置。有效值為：social、enterprise、integration、protocol、app-datasvc、other。預設值：other。
|license          |物件|API 應用程式的授權。
|>>**license.type**|字串|SPDX 授權識別碼，例如 MIT。
|>>license.url    |url|指向完整授權文字的絕對 url。
|>>license.requireAcceptance|bool|是否必須在安裝之前核准授權。預設值：false。
|links            |object[]|要加入至 Marketplace 網頁的連結陣列。
|>>**links.text** |字串|連結的文字。
|>>**links.url**  |url|連結的 URL。
|authentication|object[]|一個陣列，表示此 API 應用程式需要何種驗證才能進行連出 API 呼叫。  例如，DropBox 連接器需要向 DropBox 進行驗證，而 Salesforce 連接器需要向 Salesforce 進行驗證。
|>>authentication.type|字串|支援的值如下所示 (不區分大小寫)：Box、DropBox、Facebook、GitHub、Google、Instagram、Marketo、Office365、OneDrive、Quickbooks、Salesforce、SharePointOnline、SugarCRM、Twitter、Yammer。根據此值，入口網站就會知道需要哪些設定值，例如用戶端識別碼和用戶端密碼。 
|>>authentication.scopes|string[]|驗證類型特有的範圍陣列。
|copyright        |字串|API 應用程式的著作權通知。
|brandColor       |字串|用以驅動 UI 使用經驗的選擇性品牌色彩 (採用任何 CSS 相容格式，例如 #abc、red)。
|tags             |string[]|與封裝相關的標記清單。

<!--todo add when ready to document dependencies
|dependencies    |object[]|封裝相依性的陣列。
|>>dependencies.id|string|相依性封裝的識別碼。
|>>dependencies.domain|string|相依性套件的網域。
|>>dependencies.version|string|相依性封裝的版本。
-->

<!--todo add when ready to document status URI
Web 服務 Get 方法的 URI，該方法會傳回表示 API 應用程式目前狀態的值。如果您提供此 URI，入口網站將顯示 API 應用程式的目前作業狀態以及 API 應用程式的其他相關資訊：例如執行中、接近配額、SSL 憑證到期等。入口網站預期收到的 JSON 格式如下所示 (在此表格的結尾之後)。如果您未提供 endpoints.status URI，入口網站會將 Azure 平台狀態顯示為 API 應用程式的狀態。
以下範例從  `endpoints.status` 指向的 Get 方法顯示 JSON 回應的預期格式：
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
 `name` 屬性是狀態的簡短描述， `message` 是更長的描述，而  `level` 可以是「錯誤」、「警告」或「資訊」等一般狀態。
-->

## 檢閱 Metadata 資料夾

Metadata 資料夾可以包含 API 應用程式圖庫的圖示和螢幕擷取畫面、記載 API 的 Swagger 檔案，以及 Azure 入口網站的 UI 組態。這些資訊全都是選擇性的。

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**注意：**如先前所述的  *apiapps.json*，Azure Marketplace 中有關於 API 應用程式呈現方式的中繼資料在此預覽版本中沒有任何作用，因為 Visual Studio 不包含將 API 應用程式發佈至 Marketplace 的功能。

### Metadata/icons 資料夾

您可以提供要顯示在圖庫中的圖示。 
如果您未提供自訂圖示，將會使用一般 API 應用程式圖示。圖示檔案應該是 PNG 格式，並遵循下列名稱和大小慣例：

<!--todo: also used in the workflow designer--> 

|檔案名稱|大小
|:-----|:-----:
|small.png|40X40
|medium.png|90X90
|large.png|115X115
|hero.png|815X290
|wide.png|255X115

### 中繼資料/螢幕擷取畫面資料夾

您最多可以提供 5 個螢幕擷取畫面以顯示在圖庫中。影像檔應為 PNG 格式 (533 x 324)。

### <a id="apidef"></a>Metadata/apiDefinition.swagger.json 檔案

您可以使用 [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) 格式檔案來描述 API 應用程式的 API 定義。這可讓您在封裝中以靜態方式公開 API 定義。 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- todo: find out if there is something to replace this -- could be deploymentTemplates subfolder.
### resourceTemplate.delta.json 檔案
您可以指定在 API 應用程式部署期間執行的自訂 Azure 資源管理員範本。在此差異檔案中指定的資源會加入至預設針對 API 應用程式建立的資源。例如，如果您的 API 應用程式需要 SQL Database 執行個體，您可以使用這個檔案，讓資料庫自動佈建  以及在部署 API 應用程式時在組態設定中設定連接字串。
-->  

### Metadata/UIDefinition.json 檔案

您可以提供 UI 資訊 (例如提示) 和驗證 (採用 [Azure Marketplace 格式](https://auxdocs.azurewebsites.net/zh-tw/documentation/articles/gallery-items))。

### Metadata/deploymentTemplates 資料夾

Visual Studio 會在您選擇 **Azure API 應用程式 SDK** 功能表項目時建立此資料夾，但不會使用於此預覽版本。

## 後續步驟

您的 Web API 專案現已準備要部署為 API 應用程式，而您可以依照[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)教學課程進行部署。

如需詳細資訊，請參閱[什麼是 API 應用程式？](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->