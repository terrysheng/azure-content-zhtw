<properties
	pageTitle="在 PowerApps Enterprise 中建立及設定 Microsoft 管理的與 IT 管理的 API |Microsoft Azure"
	description="深入了解 PowerApps 中可用的 API 及如何在 Azure 入口網站中註冊它們"
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
   ms.date="12/09/2015"
   ms.author="guayan"/>

# 註冊 Microsoft 管理的 API 或 IT 管理的 API
有「Microsoft 管理的」與「IT 管理的」API。當您啟用 PowerApps Enterprise 時，會自動提供 Microsoft 管理的 API 給您。此外，也會自動為您管理記憶體、連線、信任等。您的下一步是輸入任何特定的使用者設定，例如 Twitter 帳戶和密碼。

使用 IT 管理的 API 時，您可以控制及監視所有項目，包括記憶體、連線、信任等。IT 管理的 API 也包括可連線到內部部署系統 (例如 SQL Server 和 SharePoint Server) 的 API。

若要使用「Microsoft 管理的」或「IT 管理的」API，您必須在 Azure 入口網站中「註冊」那些 API。註冊之後，您就可以在您的 app 中使用這些 API。可以使用下列選項：

- 註冊預先建置的 Microsoft 管理的 API 或 IT 管理的 API (在本主題中)。
- 註冊[您的 App Service 環境](powerapps-register-api-hosted-in-app-service.md)內裝載的 Web 應用程式、API 應用程式，以及行動應用程式。
- 使用 [Swagger 2.0 API 定義](powerapps-register-existing-api-from-api-definition.md)註冊其中一個您自己的 Swagger API。

本文焦點在於**註冊預先建置的 Microsoft 管理的 API 與 IT 管理的 API**。

#### 開始使用的必要條件

- 註冊 [PowerApps Enterprise](powerapps-get-started-azure-portal.md)。
- 建立 [app 服務環境](powerapps-get-started-azure-portal.md)。


## 檢視可用的 Microsoft 管理的 API
「Microsoft 管理的」API 是隨 PowerApps Enterprise 提供，並且也由 Microsoft 裝載。在許多情況下，Microsoft 管理的 API 都很適合您的 app。例如，如果您的 app 傳送推文，將檔案上傳到 OneDrive，或顯示來自 Excel 檔案的資料，則這些 Microsoft 管理的 API 會是不錯的選擇。

一些其他優點包括：

- 您會取得可供您註冊自己執行個體的所有 Microsoft 管理的 API。 
- 資源 (包括網路、記憶體或安全性組態) 會自動受到監視。例如，如果您需要更多記憶體才能在您的 app 中顯示 Excel 資料，將會自動新增更多記憶體。 
- 您的 app 與 API (例如 Office 與 Twitter) 之間會自動建立信任。 


#### Microsoft 管理的 API

API | 說明 | 步驟連結
--- | --- | ---
![][31] | **Dropbox**<br/><br/> 您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-dropbox.md)
![][32] | **DynamicsCRM Online**<br/><br/> 您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/> 您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-excel.md)
![][34] | **Google 雲端硬碟**<br/><br/> 您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/>翻譯文字、偵測語言等。 | [**開始使用**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office365 Outlook**<br/><br/>管理您的電子郵件。 | [**開始使用**](powerapps-create-api-office365-outlook.md)
![][37] | **Office365 使用者**<br/><br/>存取使用者設定檔、其經理、其直屬員工等。 | [**開始使用**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/> 您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/> 您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/> 您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/> 傳送推文、搜尋推文、查看粉絲等。 | [**開始使用**](powerapps-create-api-twitter.md)


## 檢視可用的 IT 管理的 API
「 IT 管理的」API 是由您所控制及管理。它們並不在 Microsoft 管理的環境中執行。在某些情況下，在您自己的 IT 管理的環境中使用這些 API 可能會符合您的 app 需求。例如，您的 app 使用 Twitter API，而您需要使用您組織的 Twitter 金鑰 (而非 Microsoft Twitter 金鑰)。在此情況下，最好是將 Twitter API 設定成 IT 管理的 API。在另一個範例中，您的 app 使用 SQL Server API 來連線到內部部署資料庫。在 IT 管理的環境中，您可以設定虛擬網路或使用 Express Route 來連線到內部部署環境。決定權完全在您手上。

一些其他優點包括：

- 資源 (包括網路、記憶體或安全性組態) 會受您監視。例如，如果您需要更多記憶體才能在您的 app 中顯示 Excel 資料，您可以控制要在您的環境中新增多少記憶體。 
- 由您設定 app 與 API 之間的信任並控制安全性。例如，由您決定 Office365 API 是否可以受 Microsoft 管理 (自動信任)，或在您自己的環境內使用 Office365 API (建立您自己的信任)。 
- 「所有」Microsoft 管理的 API 也都可以是 IT 管理的 API。例如，如果您想要建立您自己的 Office365 執行個體，並完全掌控這個執行個體，您就可以這麼做。然後，您可以在相同環境中使用 Office365 IT 管理的 API 與 Office365 Microsoft 管理的 API。這實際上取決於您的 app 需求。
- 當連線到內部部署系統或使用「Bing 搜尋」API 時，您可以控制安全性、驗證、授權等。


#### IT 管理的 API
> [AZURE.NOTE]請記住，「所有」Microsoft 管理的 API 也都可以是 IT 管理的 API。下列 API 僅限於受 IT 管理；它們不能受 Microsoft 管理。

API | 說明 | 步驟連結
--- | --- | ---
![][30] | **Bing 搜尋**<br/><br/>內嵌搜尋結果、新增搜尋功能等。 | [**開始使用**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>您可以取得、更新、刪除項目等。 | [**開始使用**](powerapps-create-api-sharepointserver.md)


#### 為什麼要註冊您自己的執行個體

使用現成的 Microsoft 管理的 API 很方便。不過，將您自己的執行個體註冊為 IT 管理的 API 也有許多優點。概括而言，建議您建立 IT 管理的 API，如果您想要：

- 對 API 具有完整的管理能力，包括使用者存取權、連線到其他系統時的安全性、API 呼叫限制、監視及進階功能 (例如原則) 等。
- 存取內部部署資料，因為「App Service 環境」支援虛擬網路。
- 為業務使用者設定他們可能無法自己使用的 API。

下表比較 Microsoft 管理的 API 與 IT 管理的 API 的功能：

| 功能 | Microsoft 管理的 | IT 管理的 |
| ---------- | ----------------- | ------------ |
| API 呼叫限制 | 由 Microsoft 定義 | 您自行定義 (透過原則) |
| 連線到 SaaS 時自備金鑰 | 不支援 | 支援 |
| API 使用者存取權 | 已為每一位使用者啟用 | 可在 AAD 使用者與群組層級完整管理 |
| API 監視 | 不支援 | 支援 |
| API 原則 | 不支援 | 支援 |
| 連線使用者存取權 | 僅限檢視 | 可在 AAD 使用者與群組層級完整管理 |
| 連線管理 | 僅限檢視 | 可完整管理 |


## 註冊 Microsoft 管理的或 IT 管理的 API

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [PowerApps]，然後選取 \[Manage APIs] \(管理 API)：  
![][17]
2. 在 [Manage APIs] \(管理 API) 中，選取 [Add] \(新增)：  
![][18]  
3. 在 [Add API]\(新增 API) 中，輸入 API 屬性：  

	- 在 [Name] \(名稱) 中，輸入 API 的名稱。請注意，您輸入的名稱會包含在 API 的執行階段 URL 中。請設定有意義且在組織中獨一無二的名稱。
	- 在 [Source] \(來源) 中，選取 [From available APIs] \(從可用的 API)：  
	![][19]
4. 選取 [API]，然後選擇您想要註冊的 API：  
![][20]
5. 選取特定的 API，並新增任何可設定的屬性。
6. 選取 [ADD] \(新增) 以完成這些步驟。

> [AZURE.TIP]註冊 API 時，您是向您的 App Service 環境註冊 API。一旦其在 App Service 環境中，相同 App Service 環境內的其他應用程式便能使用它。


## 摘要和後續步驟

在本主題中，您已了解如何針對 PowerApps 所提供的現成可用 API，註冊您自己的 API 執行個體。以下是一些讓您深入了解 PowerApps 的相關主題與資源：


- [設定 API 原則](powerapps-configure-apis.md)
- [讓使用者存取 API](powerapps-manage-api-connection-user-access.md)
- [開始在 PowerApps 中建立應用程式](https://powerapps.microsoft.com/tutorials/)


<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!---------HONumber=AcomDC_1210_2015--->
