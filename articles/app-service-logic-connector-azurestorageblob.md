<properties 
   pageTitle="Azure 儲存體 Blob 連接器" 
   description="開始使用 Azure 儲存體 Blob 連接器" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
# Azure 儲存體 Blob 連接器

## 概觀
Azure 儲存體 Blob 連接器可讓您從 Blob 容器上傳、下載及刪除 Blob。

## 建立新的 Azure 儲存體 Blob 連接器
若要建立新的 Azure 儲存體連接器，請遵循下面所述的步驟。
- 啟動 Azure 入口網站
- 使用 [+ 新增] \(位於頁面底部) -> [Web + 行動] --> [Azure Marketplace] 開啟 Azure Marketplace 

![Launch Azure Marketplace][1]
- 按一下 API 應用程式
- 搜尋 _Blob_，然後選取 Azure 儲存體 Blob 連接器

![Select Azure Storage Blob Connector][2]
- 按一下 [建立]
- 在開啟的 Azure 儲存體 Blob 連接器刀鋒視窗中，提供下列資料。

![Create Azure Storage Blob Connector][3]

- **位置** - 選擇您要部署連接器的地理位置
- **訂用** - 選擇您要建立此連接器的訂用
- **資源群組** - 選取或建立連接器所在的資源群組
- **Web 主控方案** - 選取或建立 Web 主控方案
- **定價層** - 選擇連接器的定價層
- **名稱** - 提供 FTP 連接器的名稱
- **封裝設定** 
	- **容器/SAS URI** - 指定 Blob 容器的 URI。此 URI 也可能包含 SAS 權杖。範例 http://storageaccountname.blob.core.windows.net/containername 或 http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **存取金鑰** - 指定有效的主要/次要儲存體帳戶存取金鑰。如果您使用 SAS 權杖進行驗證，請將此欄位空白。
- 按一下 [建立]。將建立新的 Azure 儲存體 Blob 連接器。

## 在邏輯應用程式中使用 Azure 儲存體 Blob 連接器
一旦建立 Azure 儲存體 Blob 連接器，即可從流程中取用。

透過 [+ 新增] -> [Web + 行動] -> [LogicApp] 建立新流程。提供流程的中繼資料 (包括資源群組)

![Create Logic App][4]

按一下  *triggers and actions*。流程設計工具隨即開啟

![Logic App empty flow designer][5]

Azure 儲存體 Blob 連接器可做為觸發程序和動作。 

### 觸發程序
在空的流程設計工具中，按一下右邊組件庫窗格中的 Azure 儲存體 Blob 連接器。

![Choose Blob Available Trigger][6]

Azure 儲存體 Blob 連接器有一個觸發程序 - _BlobAvailable_。這個觸發程序會輪詢存在指定容器中的 Blob。也會支援 Blob 的目錄層級輪詢和篩選。一旦 Blob 被挑選，它會從容器中刪除這些 Blob

按一下 _BlobAvailable_ trigger。

![Basic inputs Blob Available Trigger][7]

輸入可協助您設定依排定的頻率輪詢特定資料夾路徑。基本輸入如下：
- 頻率 - 指定 FTP 輪詢的頻率
- 間隔 - 指定排定頻率的間隔
- 資料夾路徑 - 指定要輪詢的虛擬資料夾路徑。使用 '.' 做為根容器資料夾
- 檔案類型 - 指定用以比對 Blob 檔案名稱的檔案遮罩。  只有具有符合此檔案遮罩之檔案名稱的 Blob 才會納入輪詢中。預設會納入所有的 Blob。

按一下 [...] 可顯示進階輸入。 

![Advanced inputs Blob Available Trigger][8]

進階輸入如下：

- 檔案遮罩 - 指定用以比對 Blob 檔案名稱的檔案遮罩。  只有具有符合此檔案遮罩之檔案名稱的 Blob 才會納入輪詢中。根據預設，將會納入所有的 Blob。
- 排除檔案遮罩 - 指定用以比對 Blob 檔案名稱的檔案遮罩。  將排除符合此檔案遮罩的 Blob。預設不會排除任何 Blob。

提供輸入，然後按一下刻度標記以完成輸入設定。

![Configured Blob Available Trigger][9]

請注意，設定的 _Blob Available_ 觸發程序會顯示已設定的輸入參數，以及輸出參數。 

建立邏輯應用程式後，_Blob Available_ 觸發程序會 


- 輪詢新檔案的資料夾路徑
- 具現化每個新檔案的邏輯流程
- 在具現化邏輯流程之後，從資料夾路徑中刪除檔案

#### 在後續動作中使用 Blob Available 觸發程序的輸出
_Blob Available_ 觸發程序的輸出可做為流程中其他某些動作的輸入。 

您可以按一下動作的輸入對話方塊中的 +，然後從下拉式方塊中直接選取 FTP 的輸出。

您也可以直接在動作的輸入方塊中撰寫運算式。要參考觸發程序輸出的流程運算式如下所示

	@triggers().outputs.body.Content

### 動作
按一下右窗格中的 Azure 儲存體 Blob 連接器。此連接器會列出支援的動作。

![List of Azure Storage Blob Actions][10]

Azure 儲存體 Blob 連接器支援四個動作。包括

- **取得 Blob** - 從容器取得特定的 Blob
- **上傳 Blob** - 上傳新的 Blob 或更新現有的 Blob
- **刪除 Blob** - 從容器中刪除特定的 Blob
- **列出 Blob** - 列出目錄中的所有 Blob
- **建立 Blob 快照** - 建立特定 Blob 的唯讀快照
- **複製 Blob** - 從另一個 Blob 複製以建立新的 Blob。  來源 Blob 可能位於相同的帳戶或另一個帳戶中。

舉例來說 - 上傳 Blob。按一下 [上傳 Blob]

![Inputs of Upload Blob action][11]


- **Blob 路徑** - 指定要上傳之 Blob 的路徑。  此路徑相對於已設定的容器路徑。
- **Blob 寫入內容** - 指定要上傳之 Blob 的內容和屬性。
- **內容轉移編碼** - 指定 [無] 或 Base64
- **覆寫** - 如果設定為 true，將會覆寫現有的 Blob。如果 Blob 已存在於相同的路徑，則會傳回錯誤。

提供輸入，然後按一下刻度標記以完成輸入設定。


請注意，已設定的 [Azure 儲存體 Blob 上傳 Blob] 動作會顯示輸入參數以及輸出參數。

#### 使用先前動作的輸出做為 FTP 動作的輸入
請注意，在已設定的螢幕擷取畫面中，[內容] 值會設定為運算式。

	@triggers().outputs.body.Content


您可以將它設定為任何您想要的值。這只是一個範例。運算式會採用邏輯應用程式觸發程序的輸出，並將它做為要上傳的檔案內容。假設您要使用前一個動作的輸出，像是轉換。在此情況下，運算式會是

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG

<!--HONumber=49-->