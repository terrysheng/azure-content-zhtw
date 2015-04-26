<properties title="Azure RemoteApp FAQ" pageTitle="Azure RemoteApp 常見問題集" description="了解 Azure RemoteApp。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Azure RemoteApp 常見問題集
我們已聽見下列關於 Azure RemoteApp 的問題。還有其他問題嗎？請瀏覽 [RemoteApp 論壇](https://social.msdn.microsoft.com/Forums/azure/zh-tw/home?forum=AzureRemoteApp)，讓我們知道您需要知道些什麼。

##什麼是 Azure RemoteApp？##


- **什麼是 Azure RemoteApp？**RemoteApp 是一種 Azure 服務，可透過 Azure 提供內部部署之 Microsoft RemoteApp 的功能，這是由遠端桌面服務所支援。RemoteApp 可協助您提供安全的遠端存取方式，讓許多不同的使用者裝置可以存取應用程式。進一步了解 [Azure RemoteApp](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-whatis/)。
- **有哪兩種部署選項？**RemoteApp 部署 (或收藏) 有兩種：雲端和混合式。請找出何種[部署選項](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-whatis/)最適合您的組織。

##支援的組態##


- **是否支援自訂的企業營運 (LOB) 應用程式？**是。若要在 Azure RemoteApp 中使用自訂應用程式，請建立[自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/)，然後將其上傳至 RemoteApp 收藏。
- **我自訂的 LOB 應用程式可以在 Azure RemoteApp 中運作嗎？**要找出答案的最佳方式就是測試。請檢閱[應用程式相容性需求](http://www.microsoft.com/zh-tw/download/details.aspx?id=18704)，並查看 [RD 相容性中心](http://www.rdcompatibility.com/compatibility/default.aspx)。
- **何種部署方法 (雲端或混合式) 最適合我的組織？**如果您想要完全整合單一登入 (SSO) 和安全的內部部署網路連接，混合式收藏可以提供最完整的體驗。雲端收藏可透過使用多種驗證方法，為隔離您的部署提供彈性和簡單的方式。進一步了解[部署選項](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-whatis/)。
- **混合式收藏需要 VNET。我們可以使用現有的 NET 嗎？**現在不行，但我們知道您的需求。在我們在努力的同時，您可以依循[這些指示](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx)，將您現有的 VNET 連接至 Azure RemoteApp VNET。
- **我可以使用雲端或現有的虛擬機器做為 RemoteApp 收藏的範本嗎？**現在不行，但這是我們的[意見反應網站](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w)上很普遍的要求。
- **我們在內部部署或 Azure 中有 SQL 或其他資料庫。我們應使用何種部署類型？**這取決於您的 SQL 或後端資料庫的所在位置。如果資料庫在私人網路中，請使用混合式收藏。如果資料庫公開於網際網路上並可供用戶端連線，您可以使用雲端收藏。
- **磁碟機對應、USB 和序列埠、剪貼簿共用及印表機重新導向呢？**Azure RemoteApp 支援前述所有功能。剪貼簿共用和印表機重新導向預設為啟用。請[與我們連絡](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20enable%20Redirection%20request) 以啟用磁碟機對應或 USB 或序列埠重新導向。(我們正設法在入口網站中新增這項支援，但尚未完成。)
- **驗證呢？支援何種方法？**雲端收藏支援 Microsoft 帳戶與 Azure Active Directory 帳戶，而其同時也是 Office 365 帳戶。混合式收藏僅支援已透過 Windows Server Active Directory 部署同步 (使用類似 DirSync 的工具) 的 Azure Active Directory 帳戶；更具體而言，就是已同步「密碼同步化」選項，或已同步設定 Active Directory Federation Services (AD FS) 同盟。您也可以設定 [Multi-Factor Authentication (MFA)](http://azure.microsoft.com/zh-tw/documentation/services/multi-factor-authentication/)。

	**注意：** Azure Active Directory 使用者必須是與您的訂閱相關聯的租用戶。(您可以在入口網站的 [設定]**** 索引標籤中檢視並修改您的訂閱。如需詳細資訊，請參閱[變更 RemoteApp 使用的 Azure Active Directory 租用戶](http://msdn.microsoft.com/zh-tw/3d6c4fd1-c981-4c57-9402-59fe31b11883)。)

- **為何我無法提供我的 Azure Active Directory 帳戶存取權？** Azure Active Directory 使用者必須來自與您的訂閱相關聯的目錄。您可在入口網站的 [設定] 索引標籤中檢視或修改該目錄。如需詳細資訊，請參閱[變更 RemoteApp 使用的 Azure Active Directory 租用戶](http://msdn.microsoft.com/zh-tw/3d6c4fd1-c981-4c57-9402-59fe31b11883)。
- **用戶端應用程式支援哪些裝置和作業系統？**用戶端應用程式可用於 Windows 8.1、Windows 8、Windows 7 Service Pack 1、iOS、Mac OS X、Windows RT、Android 裝置及 Windows Phone。我們也支援 Windows 10 預覽。
 
	立即[下載](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) RemoteApp 用戶端。
- **Azure RemoteApp 是否支援精簡型用戶端？**是，下列是支援的 Windows Embedded 精簡型用戶端：
	- Windows Embedded Standard 7 with Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **遠端桌面工作階段主機 (RDSH) 支援哪一版的 Windows Server？**Windows Server 2012 R2。

##支援與意見反應

- **我可以免費試用這項服務嗎？**可以。您有 30 天可免費試用。在試用結束後，您可以轉換成付費帳戶 (以使用於實際執行) 或停止使用服務。請前往[manage.windowsazure.com](http://manage.windowsazure.com)開始免費試用 - 建立新的 RemoteApp 執行個體。透過免費試用可建立 2 個 RemoteApp 執行個體，每一執行個體是 10 位使用者。請記住，試用的效期為 30 天。
- **什麼是 RemoteApp 的支援方案？**計費和訂閱管理支援是免費提供。技術支援則是透過 [Azure 服務方案](http://azure.microsoft.com/support/plans/)提供。您也可透過我們的 [Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/zh-tw/home?forum=AzureRemoteApp)獲得免費的社群支援。
- **RemoteApp 需要多少成本？**查看 [Azure RemoteApp 定價詳細資訊](http://azure.microsoft.com/zh-tw/pricing/details/remoteapp/)。
- **如何送出意見反應？**請瀏覽[意見反應論壇](http://feedback.azure.com/forums/247748-azure-remoteapp)。
- **我可以和誰討論更多 Azure RemoteApp 的資訊？**除了我們的[論壇](http://social.msdn.microsoft.com/Forums/windowsazure/zh-tw/home?forum=AzureRemoteApp) (這是張貼問題的最佳去處) 外，您還可加入每週的[專家請益網路研討會](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)，在這裡我們會討論 RemoteApp 的一切。
- **RemoteApp 文件呢？**很高興您這麼問。除了入口網站說明庫的說明內容外 (只要按下入口網站任一頁面上的 **?**)，還有下列關於 RemoteApp 的文件：
	- **開始使用：**
		- [什麼是 RemoteApp？](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-whatis/)
		- [RemoteApp 範本映像中有什麼內容？](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-images/)
		- [授權如何運作？](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-licensing/)
		- [RemoteApp 與 Office 如何共同運作？](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-o365/)
	- **部署：**
		- [建立自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/)
		- [建立混合式收藏](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [建立雲端收藏](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-cloud-deployment/)
		- [設定 RemoteApp 的 Azure Active Directory](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-ad/)
		- [在 RemoteApp 中發佈應用程式](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-publish/)
	- **管理：**
		- [新增使用者](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-user/)
		- [設定和使用 RemoteApp 的最佳做法](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-bestpractices/)	

	影片！我們也有一些關於 RemoteApp 的影片。有些提供簡介 ([Azure RemoteApp 簡介](http://azure.microsoft.com/zh-tw/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/))，有些則是引導您完成部署 ([雲端部署](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be)和[混合式部署](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be))。請觀賞影片！


<!--HONumber=35.2-->
