
<properties 
    pageTitle="如何搭配 Azure RemoteApp 使用 Office 365 訂用帳戶 | Microsoft Azure"
	description="了解如何使用 Azure RemoteApp 中的 Office 365 訂閱共用 Office 應用程式。"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/24/2015" 
    ms.author="elizapo" />



# 如何搭配 Azure RemoteApp 使用 Office 365 訂閱

您知道可以使用 Azure RemoteApp 中的現有 Office 365 訂閱來共用雲端中的 Office 應用程式嗎？ 請繼續閱讀以了解 Office 365 + Azure RemoteApp 選項的相關資訊，其中包含可協助您充分運用 Office 365 訂閱的相關文章連結。

## 可以在 Azure RemoteApp 中使用 Office 365 訂閱執行 Office 應用程式嗎？

可以！ 事實上，使用 Office 365 訂閱是將您的 Office 應用程式帶入 Azure RemoteApp 的唯一方式。

(請注意：如果您的 Azure RemoteApp 部署由託管合作夥伴傳遞，它們可能會根據[服務提供者授權協定](http://www.microsoft.com/zh-TW/Licensing/licensing-programs/spla-program.aspx)為您提供 Office 授權)


Office 365 訂閱最棒的一點是，它可讓您在許多不同平台和環境 (包括 Azure 雲端) 中使用相同的使用者授權。當您在 Azure RemoteApp 中使用 Office 應用程式時，無須以任何特別方式購買其他授權或設定現有的授權。您只需要包含 [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx) 的 Office 365 訂閱。

Office 365 ProPlus 會提供[共用電腦啟用](https://technet.microsoft.com/library/Dn782860.aspx)，這項功能可在虛擬和雲端環境 (例如，Azure RemoteApp 和遠端桌面服務) 中，提供適用於 Office 的暫時使用者型啟用方式。

哪些 Office 365 方案包含 Office 365 ProPlus？ 請查看[各方案中可用的服務](https://technet.microsoft.com/library/office-365-plan-options.aspx)資料表。請注意，並非所有的方案都包含 Office 365 ProPlus (例如 Office 365 商務方案)。如果您的方案不包含 Office 365 ProPlus，請考慮升級為包含該產品的方案 (例如 Office 365 教育版 E3)。

## 那麼，如何搭配 Azure RemoteApp 使用我的 Office 365 ProPlus 授權呢？

每個 Office 365 ProPlus 使用者授權僅可讓單一使用者在最多 5 台電腦 (含平板電腦和手機) 上啟用 Office 應用程式。每次啟用都會註冊使用者，直到其在裝置上停用 Office 為止。(使用者可在 [Office 365 入口網站](https://portal.office365.com/)中管理其裝置。)

單一使用者透過 Azure RemoteApp 可能會在同一天登入多部電腦而不自知。這是因為服務會自動管理及調整在雲端中的資源，而使用者只會看到應用程式和您所共用的程式。針對這種情況，Office 365 ProPlus 提供共用電腦啟用模式。意思是該使用者不需要進行任何授權管理來存取這些資源，且個別電腦不列入 5 部電腦的啟用限制。

只要您 (系統管理員) 將 Office 365 ProPlus 授權指派給使用者，他們即可在個人裝置上或透過 Azure RemoteApp 集合使用 Office。

## 我可以搭配 Office 365 和 Azure RemoteApp 使用哪些 Office 應用程式？

您可以使用 Office 365 ProPlus 訂閱共用 Office 2013 和 Office 2016 (發行之後)。Azure RemoteApp 不支援舊版的 Office。

## 那是否支援 Visio Pro 或 Project Pro ？

RemoteApp 訂用帳戶中的 Office 365 ProPlus 映像包含 Visio Pro 和 Project Pro。但您無法使用 Office 365 ProPlus 訂閱啟用這些程式，因為這些程式各自擁有專用授權。您可以在 [Office 365 入口網站](https://portal.office365.com/)中啟用這些程式。

如果您不想要使用這些程式，則無須進行授權。您只要啟用想要使用的程式，然後略過其他程式即可。您還是會在映像中看見這些程式，但無法使用。

## 如何開始使用 Office 365 和 Azure RemoteApp？

現在您已了解 Office 365 授權的詳細資料，讓我們協助您開始在 Azure RemoteApp 中使用該授權—使用方式相當容易：

當您建立 Azure RemoteApp 集合時，請使用 **Office 365 ProPlus (需要訂用帳戶)** 映像。

![含 Office 365 ProPlus 的 Azure RemoteApp 映像](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


此映像包含最新版的 Windows Server 和 Office 365 ProPlus。設定您的集合 (包含發佈應用程式) 後，使用者僅須登入 Azure RemoteApp (透過使用其 RemoteApp 用戶端)，然後提供適用於任何 Office 應用程式的 Office 365 認證即可。無須進行任何設定或管理，即可自動傳遞授權。

## 可以使用 Office 365 ProPlus 建立自訂映像嗎？

您可以建立集合的自訂映像，其中包含 Office 365 ProPlus。以下有 2 種方式供您選擇：使用我們提供的 Azure 資源庫映像，或者建立自訂映像並在其中安裝 Office 365 ProPlus。

### 使用 Azure 資源庫映像

將 Office 365 ProPlus 部署至集合的最簡易方式為，[從其中一個 Azure 資源庫映像開始](remoteapp-image-on-azurevm.md)，該映像包含在 Azure RemoteApp 訂用帳戶中。請務必選擇 **Windows Server 遠端桌面工作階段主機與預先安裝的 Office 365 ProPlus** 映像。接著，在該映像中安裝想要的任何其他應用程式，便可開始使用。

### 使用自訂映像

您可以持續建立自訂映像：建立 [Azure VM](remoteapp-image-on-azurevm.md) 或[在本機建立映像](remoteapp-create-custom-image.md)，然後上傳至 Azure。不論何種情況，都務必使用共用電腦啟用節點安裝 Office 365 ProPlus。使用 [Office 部署工具](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx)，並遵循[指示](https://technet.microsoft.com/library/Dn782858.aspx)進行安裝。

### 重要：請在自訂映像中停用 Office 365 ProPlus 的自動更新。

自訂映像可供 Azure RemoteApp 做為範本使用，並可依據增加的使用者需求新增其他資源。若要避免延遲和連線問題，請停用映像中的 Office 自動更新。如果您不這樣做，則每個使用該範本建立的資源將會在啟動時自動更新。請改用標準 Azure RemoteApp 程序來更新自訂映像。如此一來，您便可在範本映像中一次更新所有 Office 應用程式，然後由 Azure RemoteApp 負責將更新提供給您的使用者。

若要停用自動更新，請將下列項目新增至 Office 部署工具組態檔：

		<Updates Enabled="FALSE" />

現在您的組態檔應該會包含這幾行：
	
		<Display Level="NONE" AcceptEULA="TRUE" />
		<Property Name="SharedComputerLicensing" Value="1" />
		<Updates Enabled="FALSE" />

## 那麼，要如何使用 Office 365 ProPlus 更新映像呢？

須更新集合中的映像有許多原因。以下列出幾個原因：

- 取得最新的 Windows 更新 
- 取得最新的 Office 365 ProPlus 應用程式更新
- 更新您的自訂應用程式
- 變更映像本身的其他組態設定

如需更新集合以使用您更新之映像的詳細步驟，請到[這裡](remoteapp-update.md)。但如需關於如何更新映像和 Office 365 ProPlus 的資訊，請參閱下列資訊。

您可以選擇下列其中一種方式更新映像：將映像替換為全新的映像，或者手動更新現有的映像。

### 將映像替換為最新的 Azure 資源庫映像，並新增自訂項目
若選擇此方式，您可以讓 Microsoft 負責處理 Windows Server 和 Office 365 ProPlus 的更新。您將依據最新的資源庫映像建立全新的映像，而不是更新現有的映像。然後，重複之前所做的所有步驟以自訂映像—安裝自訂應用程式、修改映像組態等。

由於資源庫映像會定期進行更新，因此您無須花費心力，即可知道 Windows Server 和 Office 365 ProPlus 應用程式永遠是最新版本。當然，缺點是當您每次取得新的映像時，就必須套用自訂項目。您可以建立指令碼來自動設定自訂項目。

### 手動更新您現有的映像

若選擇此方式，您可以使用標準 Windows 工具將更新套用至映像。若為 Office 365 ProPlus，請使用 Office 部署工具下載並安裝 Office 365 ProPlus 的最新更新或最新版本。

> [AZURE.IMPORTANT]請記得停用 Office 365 ProPlus 的自動更新。

需要關於使用 Office 部署工具進行更新的詳細資訊嗎？

- [使用 Office 部署工具部署 Office 365 產品的隨選即用功能](https://technet.microsoft.com/library/JJ219423.aspx)
- [使用 Office 部署工具部署和更新 Office 365 ProPlus](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (影片)
- [設定 Office 365 ProPlus 的更新設定](https://technet.microsoft.com/library/dn761708.aspx)

<!---HONumber=Oct15_HO3-->