<properties title="Add a user in RemoteApp" pageTitle="在 RemoteApp 中新增使用者" description="了解如何在 RemoteApp 中新增使用者" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#如何在 RemoteApp 中新增使用者
 
您必須先授與使用者存取權，他們才能在 RemoteApp 中看到和使用應用程式。這項工作很簡單：在 [使用者存取]**** 索引標籤上，輸入使用者的帳戶資訊以授與此服務的存取權。

您需要什麼帳戶資訊？這取決於您建立的收藏類型 (雲端或混合式)，還有您是否正在該收藏中使用 Office 365 ProPlus。

##雲端和混合式使用者帳戶資訊的比較
雲端收藏支援 Microsoft 帳戶與目錄同步處理的 Azure Active Directory 工作帳戶 (其也是 Office 365 帳戶)。 

混合式收藏僅支援已透過 Windows Server Active Directory 部署同步 (使用類似 DirSync 的工具) 的 Azure Active Directory 帳戶；更具體而言，就是已同步「密碼同步化」選項，或已同步設定 Active Directory Federation Services (AD FS) 同盟。如需 Azure AD 需求的詳細資訊，請參閱[設定 Azure RemoteApp 的 Active Directory](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-ad/)。

**注意：** Azure Active Directory 使用者必須是與您的訂閱相關聯的租用戶。(您可以在入口網站的 [設定]**** 索引標籤中檢視並修改您的訂閱。如需詳細資訊，請參閱[變更 RemoteApp 使用的 Azure Active Directory 租用戶](http://msdn.microsoft.com/zh-tw/3d6c4fd1-c981-4c57-9402-59fe31b11883)。)

##Office 365 ProPlus 使用者帳戶資訊
如果您的收藏中使用 Office 365 ProPlus 範本映像，或如果您建立了使用 Office 365 的自訂映像，則您只能新增在您的訂用帳戶的預設網域中擁有 Office 365 訂用帳戶的 Azure Active Directory 使用者。如需詳細資訊，請參閱[透過 Azure RemoteApp 使用 Office3 365](http://azure.microsoft.com/zh-tw/documentation/articles/remoteapp-o365/)。

<!--HONumber=35.2-->
