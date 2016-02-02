<properties 
	pageTitle="什麼是 Azure RemoteApp？ | Microsoft Azure" 
	description="了解如何透過 Azure RemoteApp 與任何裝置共用應用程式和資源。" 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="11/04/2015" 
	ms.author="elizapo"/>

# 什麼是 Azure RemoteApp？

Azure RemoteApp 可將遠端桌面服務所支援的內部部署 Microsoft RemoteApp 程式功能提供給 Azure。Azure RemoteApp 可協助您提供安全的遠端存取方式，讓許多不同的使用者裝置可以存取應用程式。Azure RemoteApp 基本上會在雲端裝載非持續性的終端機伺服器工作階段，而您可使用這些工作階段並與您的使用者共用。

Azure RemoteApp 幾乎可讓您在任何裝置上與使用者共用應用程式和資源。我們將您的 App 裝載在雲端中，這表示我們會負責處理硬體和調整以符合使用者需求。您只需要上傳要共用的 App 即可，並讓您的使用者使用這些 App。[使用者將保有自己的裝置](remoteapp-clients.md)，同時您可透過 Azure 入口網站管理所有項目。您甚至可以使用公司認證，可讓您確保 App 和資料的安全性。

請閱讀更多有關 Azure RemoteApp 的詳細資訊，或者如果我們已經說服您，請[立即試用](https://azure.microsoft.com/services/remoteapp/)。

有關於 Azure RemoteApp 的問題嗎？ 請查看我們的[常見問題集](remoteapp-faq.md)。

Azure RemoteApp 屬於 [Microsoft 虛擬桌面基礎結構](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)。

**新功能！** 想要深入了解 Azure RemoteApp 嗎？ 或準備要驗證 Azure RemoteApp？ 請加入我們每週的[專家請益網路研討會](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website)。

## Azure RemoteApp 收藏
[Azure RemoteApp 收藏](remoteapp-collections.md)分成兩種：


- **雲端收藏**由此雲端託管，並將程式資料儲存在此雲端。使用者可以使用其 Microsoft 帳戶或與 Azure Active Directory 同步處理或同盟的公司認證進行登入，以存取應用程式。

	當您想要共用的應用程式不需要連接至貴公司私人網路上的任何資源時 (例如，透過 VPN 裝置)，請選擇雲端收藏。如果應用程式使用網際網路、OneDrive 或 Azure 上的資源，則雲端收藏將適用於您。它也是最快建立的。

- **混合式收藏**由 Azure 雲端代管，並在其中儲存資料，但也會讓使用者存取儲存在您區域網路上的資料和資源。使用者可以使用與 Azure Active Directory 同步處理或同盟的公司認證進行登入，以存取應用程式。

	如果您需要連接至貴公司私人網路上的資源，請選擇混合式收藏。比方說，如果應用程式需要存取下列其中一項：

	- 位於內部網路上的檔案伺服器
	- Quicken
	- 防火牆後面的資料庫

	這通常對私人網路上有大量資源無法移至雲端的大型公司很有用。

不同集合有不同的選項，包括網路，因此請找出[哪一個集合](remoteapp-collections.md)最適合您。


### 升級收藏
混合式與雲端收藏之間的一個主要差異，在於軟體更新的處理方式。對於使用預先安裝之 Office 365 ProPlus 或 Office 2013 映像的雲端收藏，您不需擔心任何更新。此服務會自行維護並持續推出應用程式和作業系統的更新。

對於混合式收藏以及使用自訂範本映像的雲端收藏，您必須負責維護映像和應用程式。對於已加入網域的映像，您可以使用 Windows Update、群組原則或 System Center 等工具控制更新。

升級自訂的範本映像後，必須將新映像上傳至 Azure 雲端，然後更新收藏以使用新映像。(您可以從 Azure RemoteApp [快速入門] 頁面或 [儀表板] 執行此作業。)

如需詳細資訊，請參閱[更新您的收藏](remoteapp-update.md)。

## 支援的 RemoteApp 用戶端
Windows 和 Windows RT 的 RemoteApp 用戶端應用程式以及 Mac、iOS 和 Android 適用的 Microsoft 遠端桌面應用程式均可支援 Azure RemoteApp。您的使用者可以在其行動或計算裝置上使用這些應用程式，以存取新的 Azure RemoteApp 程式。

如需用戶端的詳細資訊，請參閱[在 Azure RemoteApp 存取您的應用程式](remoteapp-clients.md)。

## 後續步驟
快！ 立即試用！ 這些文章可幫助您開始使用 Azure RemoteApp：

- [Azure RemoteApp 需要何種集合？](remoteapp-collections.md)
- [建立 Azure RemoteApp 映像](remoteapp-imageoptions.md)
- [如何建立 Azure RemoteApp 的雲端收藏](remoteapp-create-cloud-deployment.md)
- [如何建立 Azure RemoteApp 的混合式收藏](remoteapp-create-hybrid-deployment.md)
- [Azure RemoteApp 中的授權如何運作？](remoteapp-licensing.md)
- [使用 Azure RemoteApp 的最佳作法](remoteapp-bestpractices.md)
- [Azure RemoteApp 常見問題集](remoteapp-faq.md)
 

### 幫我們來協助您 
您知道除了評比這篇文章以及在下面留言以外，您可以變更文件本身嗎？ 有所遺漏？ 有所錯誤？ 我是否撰寫了令人混淆的內容？ 向上捲動並按一下 [在 GitHub 上編輯] 以進行變更 - 系統會顯示這些變更以供我們檢閱，而我們簽核後，您就會在這裡看到您所進行的變更和改良。

<!---HONumber=AcomDC_0128_2016-->