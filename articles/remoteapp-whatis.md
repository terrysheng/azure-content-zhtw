<properties title="What is RemoteApp?" pageTitle="什麼是 RemoteApp？" description="了解 Azure RemoteApp。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#什麼是 Azure RemoteApp？
 
Azure RemoteApp 可在 Azure 提供內部部署之 Microsoft RemoteApp 程式的功能，這是透過遠端桌面服務所支援。Azure RemoteApp 可協助您提供安全的遠端存取方式，讓許多不同的使用者裝置可以存取應用程式。

當您將 RemoteApp 移至 Azure 後，就可以利用 Azure 的儲存體、延展性和全球化特性，不需擔心複雜的內部部署組態。Microsoft 提供 Azure 維護服務以確保其可靠性，讓您可專注於更重要的議題，例如為您的企業建立最佳的應用程式。Azure RemoteApp 的另一項優點是可存取性，使用者可透過 Windows、iOS、Mac OS X 及 Android 裝置存取。他們可在慣用的環境中使用您的應用程式，而您則是透過 Azure 管理入口網站來管理那些應用程式。 

請繼續閱讀有關 RemoteApp 的更多資訊，或者，如果您已心動，請[馬上試用](http://azure.microsoft.com/zh-tw/services/remoteapp/)。

有關於 Azure RemoteApp 的問題嗎？請查看我們的[常見問題集](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-faq/)。

Azure RemoteApp 是 [Microsoft 虛擬桌面基礎結構]的一部分(http://www.microsoft.com/zh-tw/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)。

**新活動！**想深入了解 Azure RemoteApp 嗎？準備好在一定規模內驗證 RemoteApp 嗎？加入我們每週的[專家請益網路研討會](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)。

##RemoteApp 部署選項
RemoteApp 收藏分成兩種：


- 「雲端收藏」****會由 Azure 雲端代管，並將所有程式資料儲存在此雲端。使用者可利用個人的 Microsoft 帳戶或與 Azure Active Directory 同步的或同盟的公司認證登入，以存取應用程式。
- 「混合式收藏」****裝載於 Azure 雲端中，並在其中儲存資料，但也會讓使用者存取儲存在您區域網路上的資料和資源。使用者可利用與 Azure Active Directory 同步的或同盟的公司認證登入，以存取應用程式。

###雲端收藏

[雲端 RemoteApp 收藏](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-cloud-deployment/)提供在雲端中裝載應用程式的獨立方法。雲端收藏只存在 Azure 雲端中，而不是連接到您的區域網路。

在 RemoteApp 試用版中，我們提供預先安裝的 Office 365 ProPlus 或 Office 2013 應用程式，而且您可以和您的使用者共用。如果您選擇使用可用的軟體，您可以快速佈建您的服務。

使用含有 Office 應用程式的雲端收藏有另一項好處，您建置服務所在的應用程式和作業系統永遠可透過定期更新保持最新狀態，Microsoft 反惡意程式碼端點保護可提供不間斷的防護。使用者可利用個人的 Microsoft 帳戶或公司認證來存取應用程式。身為系統管理員，您只需考慮應用程式的使用者存取權。

您也可以為使用者建立雲端收藏來共用某個自訂應用程式或一組應用程式。若要這樣做，您需要[建立自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/) (這是我們將應用程式發佈到 RemoteApp 的方法)，而且只要在建立收藏時選擇該映像 (而非 Office 2013 映像) 即可。 

###混合式收藏
[混合式 RemoteApp 收藏](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-hybrid-deployment/)可讓您提供自訂的一組應用程式給使用者，並提供您區域網路中的資料和資源存取權。不同於雲端收藏使用的自訂映像，您為混合式收藏建立的映像是在加入網域的環境中執行，因此會授與您區域網路和資料的完整存取權。

藉由整合 Active Directory 與 Azure Active Directory (使用 DirSync)，您的使用者可以使用其公司認證來存取應用程式和資料。當您在 Active Directory 中使用工作帳戶時，可以在雲端中沿用公司政策，藉此控制您透過 RemoteApp 提供的應用程式。

只要您是在含有 RD 工作階段主機角色服務的 Windows Server 2012 R2 上建立您的範本映像，您可以為使用者發佈的應用程式就有些許限制。如果應用程式可在該範本映像環境中正常運作，使用者就能透過 RemoteApp 存取它們。  

###升級收藏
混合式與雲端收藏之間的一個主要差異，在於軟體更新的處理方式。對於使用預先安裝之 Office 365 ProPlus 或 Office 2013 映像的雲端收藏，您不需擔心任何更新。服務會自行維護並持續彙總更新至應用程式和作業系統。

對於混合式收藏以及使用自訂範本映像的雲端收藏，您必須負責維護映像和應用程式。對於加入網域的映像，您可以使用 Windows Update、群組原則或 System Center 等工具控制更新。

升級自訂的範本映像後，必須將新映像上傳至 Azure 雲端，然後更新收藏以使用新映像。(您可以從 RemoteApp 快速入門頁面或儀表板執行此工作。)

##支援的 RemoteApp 用戶端
Windows 和 Windows RT 的 RemoteApp 用戶端應用程式以及 Mac、iOS 和 Android 適用的 Microsoft 遠端桌面應用程式均可支援 Azure RemoteApp。您的使用者可在他們的行動或運算裝置上使用這些應用程式，以存取新的 RemoteApp 程式。

##後續步驟
來吧！立即試用！下列文章可協助您開始使用 RemoteApp：

- [如何為 RemoteApp 建立自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/)
- [如何建立 RemoteApp 的雲端收藏](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-cloud-deployment/)
- [如何建立 RemoteApp 的混合式收藏](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-hybrid-deployment/)
- [RemoteApp 中的授權如何運作？](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-licensing/)
- [使用 Azure RemoteApp 的最佳做法](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-bestpractices/)
- [Azure RemoteApp 常見問題集](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-faq/)

<!--HONumber=35.2-->
