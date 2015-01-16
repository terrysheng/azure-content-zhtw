<properties title="What is RemoteApp?" pageTitle="什麼是 RemoteApp？" description="了解 Azure RemoteApp。" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

#什麼是 Azure RemoteApp？

Azure RemoteApp 可將遠端桌面服務所支援的內部部署 Microsoft RemoteApp 程式功能提供給 Azure。Azure RemoteApp 可讓您從許多不同的使用者裝置提供安全的應用程式遠端存取。

當您將 RemoteApp 移至 Azure 時，就可以利用 Azure 的儲存體、延展性和全球市場，而不必擔心複雜的內部部署組態。Microsoft 提供 Azure 的維護，以確保其可靠性，讓您得以專注於處理更重要的問題，例如建立最佳應用程式以供您的企業使用。Azure RemoteApp 的另一個優點是可存取性 - 您的使用者可以從 Windows、iOS、Mac OS X 和 Android 裝置存取 RemoteApp 程式。他們可以在自己偏好的環境中使用您的應用程式，而您會使用 Azure 管理入口網站來管理這些應用程式。 

如需 RemoteApp 的詳細資訊，或者如果我們已經說服您，請[立即試用](http://azure.microsoft.com/zh-tw/services/remoteapp/)。

Azure RemoteApp 屬於 [Microsoft 虛擬桌面基礎結構](http://www.microsoft.com/zh-tw/server-cloud/products/virtual-desktop-infrastructure/explore.aspx)。

**新增！**想要深入了解 Azure RemoteApp 嗎？或準備要驗證 RemoteApp？請加入我們 Weekly [Ask the Experts Webinar](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)。

##RemoteApp 部署選項
RemoteApp 部署分成兩種：


- **雲端部署**會由 Azure 雲端代管，並將所有程式資料儲存在此雲端。使用者可以使用其 Microsoft 帳戶或與 Azure Active Directory 同步處理或同盟的公司認證進行登入，以存取應用程式。
- **混合式部署**裝載於並將資料儲存在 Azure 雲端，但也可讓使用者存取您區域網路上儲存的資料和資源。使用者可以使用與 Azure Active Directory 同步處理或同盟的公司認證進行登入，以存取應用程式。

###雲端部署

[雲端 RemoteApp 部署](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-cloud-deployment/)提供在雲端裝載應用程式的獨立方法。相對於連接到您的區域網路，雲端部署只存在於 Azure 雲端。

在 RemoteApp 預覽版中，我們為您提供預先安裝的 Office 2013 應用程式並準備要與您的使用者共用。如果您選擇利用可用的軟體，即可快速佈建您的服務。

使用雲端部署 Office 2013 應用程式的另一項優點就是應用程式和作業系統 (您的服務建置所在) 會透過定期更新永遠保持最新狀態，而 Microsoft Anti-Malware 端點保護可提供連續的防禦。您的使用者可使用其 Microsoft 帳戶或公司認證來存取應用程式。身為系統管理員的您只需擔心如何找出誰應該存取哪些應用程式。

您也可以建立雲端部署，來與使用者共用一個自訂應用程式或一組應用程式。若要這樣做，您必須[建立自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/) (這就是我們將應用程式發佈到 RemoteApp 的方式)，而當您建立部署時只需選擇該映像 (而不是 Office 2013 映像)。 

###混合式部署
[混合式 RemoteApp 部署](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-hybrid-deployment/)可讓您提供一組自訂應用程式給使用者以及您區域網路中資料和資源的存取權。不同於搭配雲端部署使用的自訂映像，您針對混合式部署建立的映像會在已加入網域的環境中執行應用程式，並授與您的區域網路及資料的完整存取權。

藉由整合 Active Directory 與 Azure Active Directory (使用 DirSync)，您的使用者可以使用其公司認證來存取應用程式和資料。當您在 Active Directory 中使用工作帳戶時，您可以將您的公司政策帶入雲端，以控制您透過 RemoteApp 提供的應用程式。

只要您使用 RD 工作階段主機角色服務在 Windows Server 2012 R2 上建置您的範本映像，您可以為使用者發佈的應用程式會有一些限制。如果應用程式在該範本映像環境中運作正常，使用者即可透過 RemoteApp 加以存取。 

###更新您的部署
混合式與雲端部署之間的其中一個主要差異就是處理軟體更新的方式。有了採用預先安裝的 Office 2013 映像的雲端部署，您就不必擔心任何更新。此服務會自行維護並持續推出應用程式和作業系統的更新。

對於混合式部署，以及採用自訂範本映像的雲端部署，您需負責維護映像和應用程式。對於已加入網域的映像，您可以使用 Windows Update、群組原則或 System Center 等工具控制更新。

更新自訂範本映像之後，您可將新的映像上傳至 Azure 雲端，然後更新部署以使用新映像。(您可以從 [RemoteApp 快速入門] 頁面或 [儀表板] 執行此作業。)

##支援的 RemoteApp 用戶端
在 Azure RemoteApp 的預覽版本中，我們發行了適用於 Windows 和 Windows RT 的新 Microsoft RemoteApp 用戶端應用程式，以及適用於 iOS 和 Android 之 Microsoft 遠端桌面應用程式的更新。您的使用者可以在其行動或運算裝置上使用這些應用程式，以存取新的 RemoteApp 程式。

##後續步驟
快！立即試用！這些文章可幫助您開始使用 RemoteApp：

- [如何為 RemoteApp 建立自訂範本映像](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-custom-image/)
- [如何建立 RemoteApp 的雲端部署](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-cloud-deployment/)
- [如何建立 RemoteApp 的混合式部署](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-create-hybrid-deployment/)
