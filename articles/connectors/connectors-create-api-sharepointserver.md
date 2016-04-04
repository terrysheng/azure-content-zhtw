<properties
pageTitle="在您的 Logic Apps 或 Power Apps 中使用 SharePoint Online API | Microsoft Azure"
description="開始在邏輯應用程式及 PowerApps 中使用 Azure App Service SharePoint Online API。"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/26/2016"
ms.author="deonhe"/>

# 開始使用 SharePoint Online API

SharePoint 連線提供者提供 API 來讓您使用 Sharepoint 上的清單。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [SharePoint](../app-service-logic/app-service-logic-connector-SharePoint.md)。

您可以利用SharePoint 來：

* 建置邏輯應用程式
* 建置 PowerApps  

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 我們來談談觸發程序及動作。

SharePoint API 可當做動作來使用；它擁有觸發程序。所有 API 都支援 JSON 和 XML 格式的資料。

SharePoint API 提供下列動作及/或觸發程序：

### SharePoint 的動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|GetFileMetadata|用來取得文件庫中的檔案中繼資料|
|UpdateFile|用來更新文件庫中的檔案|
|DeleteFile|用來刪除文件庫中的檔案|
|GetFileMetadataByPath|用來取得文件庫中的檔案中繼資料|
|GetFileContentByPath|用來取得文件庫中的檔案|
|GetFileContent|用來取得文件庫中的檔案|
|CreateFile|用來上傳文件庫中的檔案|
|CopyFile|用來複製文件庫中的檔案|
|ExtractFolderV2|用來解壓縮文件庫中的資料夾|
|PostItem|在 SharePoint 清單中建立新項目|
|GetItem|擷取 SharePoint 清單中的單一項目|
|DeleteItem|刪除 SharePoint 清單中的項目|
|PatchItem|更新 SharePoint 清單中的項目|
### SharePoint 的觸發程序
您可以接聽下列事件：

|觸發程序 | 說明|
|--- | ---|
|OnNewFile|當某個 SharePoint 資料夾中有新檔案建立時，就會觸發某個流程。|
|OnUpdatedFile|當某個 SharePoint 資料夾中有檔案遭到修改時，就會觸發某個流程。|
|GetOnNewItems|當 SharePoint 清單中有新項目建立時|
|GetOnUpdatedItems|當 SharePoint 清單中的現有項目遭到修改時|


## 建立至 SharePoint 的連線
如要使用 SharePoint API，您必須先建立「連線」，再提供下列屬性的詳細資料：

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 SharePoint 的認證|

若要連接到 **SharePoint Online**，您需要向 SharePoint online 提供您的身分識別 (使用者名稱和密碼、智慧卡認證等)。通過驗證之後，您就可以在邏輯應用程式中使用 SharePoint Online API。

在邏輯應用程式的設計工具中，請依照下列步驟來登入 SharePoint，以便建立在邏輯應用程式中使用的 **connection** 連線。

1. 在搜尋方塊中輸入 SharePoint，並等候搜尋傳回所有名稱中有 SharePoint 的項目：![設定 SharePoint][1]  
2. 選取 [SharePoint Online - 當檔案建立時]   
3. 選取 [登入 SharePoint Online]：![設定 SharePoint][2]    
4. 提供您的 SharePoint 認證來登入，並以 SharePoint ![設定 SharePoint][3] 進行驗證     
5. 驗證完成後，只要設定 SharePoint 的 [當檔案建立時] 對話方塊，系統會將您重新導向至邏輯應用程式並完成作業。![設定 SharePoint][4]  
6. 接著，您可以新增所需的其他觸發和動作來完成邏輯應用程式。   
7. 選取上方功能表列的 [儲存]，即可儲存您的工作。  

>[AZURE.TIP] 您可以在其他邏輯應用程式、PowerApps 或這兩者中使用這個連接。

## SharePoint REST API 參考
#### 本文件適用的版本：1.0


### 用來取得文件庫中的檔案中繼資料
**```GET: /datasets/{dataset}/files/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|id|字串|yes|路徑|無|檔案的唯一識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 用來更新文件庫中的檔案
**```PUT: /datasets/{dataset}/files/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|id|字串|yes|路徑|無|檔案的唯一識別碼|
|body| |yes|body|無|檔案的內容|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 用來刪除文件庫中的檔案
**```DELETE: /datasets/{dataset}/files/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|id|字串|yes|路徑|無|檔案的唯一識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 用來取得文件庫中的檔案中繼資料
**```GET: /datasets/{dataset}/GetFileByPath```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|路徑|字串|yes|query|無|檔案的路徑|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 用來取得文件庫中的檔案
**```GET: /datasets/{dataset}/GetFileContentByPath```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|路徑|字串|yes|query|無|檔案的路徑|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 用來取得文件庫中的檔案
**```GET: /datasets/{dataset}/files/{id}/content```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|id|字串|yes|路徑|無|檔案的唯一識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 用來上傳文件庫中的檔案
**```POST: /datasets/{dataset}/files```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|folderPath|字串|yes|query|無|資料夾的路徑。|
|名稱|字串|yes|query|無|檔案名稱|
|body| |yes|body|無|檔案的內容|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 用來複製文件庫中的檔案
**```POST: /datasets/{dataset}/copyFile```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|來源|字串|yes|query|無|來源檔案的路徑|
|目的地|字串|yes|query|無|目的檔案的路徑|
|overwrite|布林值|no|query|false|是否要覆寫現有的檔案。|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 當某個 SharePoint 資料夾中有新檔案建立時，就會觸發某個流程。
**```GET: /datasets/{dataset}/triggers/onnewfile```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL|
|folderId|字串|yes|query|無|SharePoint 中資料夾的唯一識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 當某個 SharePoint 資料夾中有檔案遭到修改時，就會觸發某個流程。
**```GET: /datasets/{dataset}/triggers/onupdatedfile```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL|
|folderId|字串|yes|query|無|SharePoint 中資料夾的唯一識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 用來解壓縮文件庫中的資料夾
**```POST: /datasets/{dataset}/extractFolderV2```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL。例如 http://contoso.sharepoint.com/sites/mysite|
|來源|字串|yes|query|無|來源檔案的路徑|
|目的地|字串|yes|query|無|目的資料夾的路徑|
|overwrite|布林值|no|query|false|是否要覆寫現有的檔案。|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 當 SharePoint 清單中有新項目建立時
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite)|
|資料表|字串|yes|路徑|無|SharePoint 清單名稱|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 當 SharePoint 清單中的現有項目遭到修改時
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite)|
|資料表|字串|yes|路徑|無|SharePoint 清單名稱|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 在 SharePoint 清單中建立新項目
**```POST: /datasets/{dataset}/tables/{table}/items```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite)|
|資料表|字串|yes|路徑|無|SharePoint 清單名稱|
|item| |yes|body|無|要建立的項目|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 擷取 SharePoint 清單中的單一項目
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite)|
|資料表|字串|yes|路徑|無|SharePoint 清單名稱|
|id|integer|yes|路徑|無|要擷取之項目的唯一識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 刪除 SharePoint 清單中的項目
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite)|
|資料表|字串|yes|路徑|無|SharePoint 清單名稱|
|id|integer|yes|路徑|無|要刪除之項目的唯一識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 更新 SharePoint 清單中的項目
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|SharePoint 網站 URL (例如：http://contoso.sharepoint.com/sites/mysite)|
|資料表|字串|yes|路徑|無|SharePoint 清單名稱|
|id|integer|yes|路徑|無|要更新之項目的唯一識別碼|
|item| |yes|body|無|屬性已更新的項目|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



## 物件定義： 

 **DataSetsMetadata**：

DataSetsMetadata 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|tabular|未定義|
|blob|未定義|



 **TabularDataSetsMetadata**：

TabularDataSetsMetadata 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|來源|字串|
|displayName|字串|
|urlEncoding|字串|
|tableDisplayName|字串|
|tablePluralName|字串|



 **BlobDataSetsMetadata**：

BlobDataSetsMetadata 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|來源|字串|
|displayName|字串|
|urlEncoding|字串|



 **BlobMetadata**：

BlobMetadata 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|識別碼|字串|
|名稱|字串|
|DisplayName|字串|
|Path|字串|
|LastModified|字串|
|大小|integer|
|MediaType|字串|
|IsFolder|布林值|
|ETag|字串|
|FileLocator|字串|



 **Object**：

Object 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|



 **TableMetadata**：

TableMetadata 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|名稱|字串|
|title|字串|
|x-ms-permission|字串|
|結構描述|沒有定義|



 **DataSetsList**：

DataSetsList 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|



 **DataSet**：

DataSet 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|名稱|字串|
|DisplayName|字串|



 **資料表**：

Table 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|名稱|字串|
|DisplayName|字串|



 **Item**：

Item 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|ItemInternalId|字串|



 **ItemsList**：

ItemsList 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|



 **TablesList**：

TablesList 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md) [建立 Power 應用程式](../power-apps/powerapps-get-started-azure-portal.md)

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png

<!---HONumber=AcomDC_0323_2016-->