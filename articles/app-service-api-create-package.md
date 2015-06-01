<properties 
	pageTitle="建立 API 應用程式封裝" 
	description="了解如何建立 API 應用程式封裝。" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# 建立 API 應用程式封裝

## 概觀

本文說明如何建立可發佈至 [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) 的 API 應用程式封裝。

- 若要了解如何建立 API 應用程式，請參閱[使用 Visual Studio 建立 API 應用程式](app-service-dotnet-create-api-app.md)。
- 若要了解如何將 API 應用程式封裝發佈至 Azure Marketplace，請參閱[將 API 應用程式封裝發佈至 Azure Marketplace](app-service-api-publish-package)。

## 資料夾結構

API 應用程式的 Nuget 封裝 (*.nupkg* 檔) 於 *Content* 資料夾下，有下列檔案和資料夾：

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

當您封裝其中在專案資料夾中具有 *apiapp.json* 和 *Metadata* 的 Visual Studio 專案時，即會以此資料夾結構建立 *.nupkg* 檔，如下所示：

![方案總管中的 apiapp.json 和 Metadata](./media/app-service-api-create-package/metadatainse.png)

下列各節記載了API 應用程式資料夾結構中的各個檔及資料夾，並展示如何封裝與部署專案。

## apiapp.json

這是 API 應用程式的資訊清單檔。

|名稱 (粗體=必要)|類型|格式|說明|
|:---------------------|:-----|:-------|:------------|
|**id**|字串|[a-zA-Z0-9_.]|此封裝的識別碼。必須為命名空間中的唯一值，且只可包含英數字元、"_" 及 "."。開頭必須為英數字元。|
|**namespace**|字串|網域名稱|包含 **id** 屬性 (可唯一識別 API 應用程式) 的命名空間。必須為發佈者的 AAD 租用戶其中一個網域名稱。|
|**version**|字串|[semver](http://docs.nuget.org/Create/Versioning)|此封裝的版本。當使用者對此封裝啟用自動升級時，這只會套用至相同主要版本的新版本。|
|**gateway**|字串|2015-01-14|此封裝使用的閘道 API 版本。閘道是特殊的 Web 應用程式，用於路由傳送所有送往資源群組中 API 應用程式的要求。閘道的其中一項主要功能是處理驗證。目前唯一的閘道版本為 2015-01-14。未來如有新閘道版本發行，您可利用此屬性來避免重大變更並繼續使用先前的閘道 API。| 
|**title**|字串||API 應用程式的顯示名稱。|
|**summary**|字串|上限 100 個字元|API 應用程式的簡短摘要。
|說明|字串|上限 1500 個字元|API 應用程式的完整說明。可包含 HTML。允許的元素及屬性為 "h1"、"h2"、"h3"、"h4"、"h5"、"p"、"ol"、"ul"、"li"、"a[target|href]"、"br"、"strong"、"em"、"b"、"i"。|
|**author**|字串|上限 256 個字元|API 應用程式的作者。|
|homepage|字串|URL|API 應用程式的首頁。|
|endpoints|物件||端點清單，可供 API 應用程式平台查詢 API 應用程式之方法和狀態的相關資訊。|
|endpoints.apiDefinition|字串|URL 路徑|GET 要求時傳回 Swagger 2.0 API 定義之 API 應用程式，所公開 API 的相對 URL (例如，"/swagger/docs/v1")。若設定此值，則會使用此值，而非封裝中的 static apiDefinition.swagger.json 檔 (若有的話)。|
|endpoints.status|字串|URL 路徑|GET 要求時，傳回 API 應用程式相關執行時期狀態資訊的 API 應用程式，所公開 API 的相對 URL。|
|categories|字串[]|community、social、enterprise、integration、protocol、app-datasvc、other|此 API 應用程式將顯示於其中的 Azure Marketplace 類別。根據預設，API 應用程式會永遠顯示於 community 類別底下。核准 API 應用程式之後，其將會顯示於指定類別底下。|
|授權|物件||API 應用程式的授權。|
|**license.type**|字串||SPDX 授權識別碼，例如 MIT。|
|license.url|字串|URL|絕對的 URL，指向完整授權文字。|
|license.requireAcceptance|布林|true、false|是否必須在安裝之前接受授權。預設：false。|
|連結|物件[]||要加入至 Marketplace 頁面的連結陣列。|
|**links.text**|字串||連結文字。|
|**links.url**|字串|URL|連結的絕對 URL。|
|驗證|物件[]||陣列，指出此 API 應用程式需要何種驗證才能發出連出的 API 呼叫。現在，一個 API 應用程式封裝只能支援一種驗證。|
|**authentication.type**|字串|Box、DropBox、Facebook、Google、Office365、OneDrive、Quickbooks、Salesforce、SharePointOnline、Twitter、Yammer|API 應用程式所需的驗證。目前支援 11 種驗證類型。未來將加入更多。| 
|authentication.scopes|字串[]||驗證類型所特有的範圍陣列。|
|copyright|字串|API 應用程式的著作權標示。
|brandColor|字串|任何 CSS 相容的格式|選擇性的品牌色彩來強化 UI 使用體驗。比方說，邏輯應用程式的設計師會使用這個屬性來繪製卡片頁首的背景色彩。|

## metadata/apiDefinition.swagger.json

您可在此處選擇性提供靜態 Swagger 2.0 JSON 檔，以公開 API 應用程式的 API 定義。平台會先檢查是否已在 **apiapp.json** 中設定 **endpoints.apiDefinition** 屬性。如果是的話，它會從屬性中指定的 URL 來取得 API 定義。如果沒有，它會嘗試尋找此檔案。

- 如需 Swagger 2.0 標準的相關資訊，請參閱 [http://swagger.io/](http://swagger.io/)。<!-todo 提供 URL
- 如需如何自訂 API 定義，來為邏輯應用程式進行最佳化的相關資訊，請參閱[文件的標題]()。
- 如需如何公開動態 API 定義的相關資訊，請參閱[文件的標題]()。-->

## metadata/icons

您可以選擇性提供自己的 API 應用程式封裝圖示集。如果沒有提供必要的圖示檔案，就會使用預設圖示，如下所示。

![API 應用程式預設大型圖示](./media/app-service-api-create-package/api-app-default-large-icon.png)

|檔案 |寬|高|說明|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40px|40px|若您想使用自己的圖示則需要。|
|metadata/icons/medium.png|90px|90px|若您想使用自己的圖示則需要。|
|metadata/icons/large.png|115px|115px|若您想使用自己的圖示則需要。|
|metadata/icons/wide.png|255px|115px|若您想使用自己的圖示則需要。|
|metadata/icons/hero.png|815px|290px|若您想使用自己的圖示則可選用。|

## metadata/screenshots

您可以選擇性為 API 應用程式封裝提供最多 5 個螢幕擷取畫面。

|檔案|寬|高|說明|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/*.png|533px|324px|API 應用程式封裝的螢幕擷取畫面。|

## metadata/deploymentTemplates

有時，API 應用程式封裝需要在部署期間進行部分自訂設定。例如，[Azure 儲存體 Blob 連接器](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/)需要 Azure 儲存體 blob 容器 URI。或者，您也可以設定存取金鑰。

若要支援此案例，您可以在此資料夾中加入 Azure 資源管理員 (ARM) 範本 JSON 檔清單來自訂 API 應用程式部署。API 應用程式平台會將您的自訂 ARM 範本與我們的系統範本合併，以產生最終的部署範本。Azure Preview 入口網站的 [**建立**] 刀鋒視窗中會自動提示自訂 ARM 範本中定義的所有參數 (預期提供 **$system**)，以供 API 應用程式使用者輸入值。

下方為範例 ARM 範本，展示如何在 API 應用程式部署期間要求 blob 容器 URI 和存取金鑰。

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

下方螢幕擷取畫面顯示對應的 Azure Preview 入口網站 [建立] 刀鋒視窗。(螢幕擷取畫面顯示 API 應用程式封裝使用 UIDefinition.json 改善 [建立] 刀鋒視窗。如需詳細資料，請參閱 [metadata/UIDefinition.json](#metadata-uidefinition-json)。)

![自訂 ARM 範本 [建立] 刀鋒視窗範例](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

如需詳細資訊，請參閱 [Azure 資源管理員範本語言](https://msdn.microsoft.com/library/dn835138)。

## metadata/UIDefinition.json

當您需要使用自訂的 ARM 範本來指定部署 API 應用程式所需的自訂設定時，Azure Preview 入口網站 [**建立**] 刀鋒視窗會自動提示輸入 ARM 範本參數，讓 API 應用程式的使用者可以輸入值。您可以使用 *UIDefinition.json* 檔提供預設值、工具提示、驗證等，來改善此 [**建立**] 刀鋒視窗 UI。

若要提供 *UIDefinition.json file*，請先利用以下骨架，然後遵循下方資料表來設定每個參數。


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|名稱 |類型|說明|
|:---------------------|:-------|:------------|
|defaultValue|字串|在 Azure Preview 入口網站 [建立] 刀鋒視窗中輸入控制項的預設值。|
|displayName|字串|在 Azure Preview 入口網站 [建立] 刀鋒視窗中輸入控制項的標籤。此值應簡短。|
|說明|字串|輸入控制項的說明。|
|tooltip|字串|在 Azure Preview 入口網站 [建立] 刀鋒視窗中輸入控制項的工具提示。使用者按一下標籤右方的資訊泡泡圖時會出現此內容。此內容可以長且完整。|
|constraints|Object|用來檢查參數的限制。|
|constraints.required|布林|參數是否必要。預設值為 false。|

例如，以下是 [Azure 儲存體 Blob 連接器](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) 的 *UIDefinition.json* 檔：

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

這會設定 Azure Preview 入口網站 [**建立**] 刀鋒視窗，以顯示下列 [**封裝設定**] 刀鋒視窗。

![UIDefinition [建立] 刀鋒視窗範例](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
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
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## 建立 API 應用程式封裝

若要建立 API 應用程式封裝

1. [下載與安裝 Azure 跨平台命令列介面](xplat-cli)。
2. 執行下列命令以切換至 Azure 資源管理員模式

        azure config mode arm

3. 執行下列命令以建立 API 應用程式的 nuget 封裝

        azure apiapp package create -p <package folder> -o <destination folder>

    例如：

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

這會驗證封裝內容以確定：

- 它遵循上述的格式
- Metadata\\apiDefinition.swagger.json (如果有提供) 包含有效的 Swagger 2.0 API 定義

如果有任何問題，它會顯示詳細資料，讓您可以修正這個問題，並建立有效的 API 應用程式封裝。

## 後續步驟

API 應用程式封裝現在已就緒，可以發佈至 Azure Marketplace。若要了解如何執行，請遵循[將 API 應用程式封裝發佈至 Azure Marketplace](app-service-api-publish-package) 教學課程。

<!--HONumber=52-->
