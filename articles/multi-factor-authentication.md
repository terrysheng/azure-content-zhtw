<properties linkid="manage-services-identity-multi-factor-authentication" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="What is Azure Multi-Factor Authentication?" authors="billmath" solutions="" manager="terrylan" editor="lisatoft" />

<tags ms.service="multi-factor-authentication" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="billmath"></tags>

# 什麼是 Azure Multi-Factor Authentication？

多因素或雙因素驗證是需要使用多種驗證方法，並在使用者登入和交易中新增重要第二安全性階層的驗證方法。其運作方式需要下列其中任何二或多個驗證方法：

-   您知道的某些資訊 (通常是密碼)
-   您擁有的某些東西 (不容易輕易複製的信任裝置，例如電話)
-   您身上的某些特徵 (生物識別技術)

多因素驗證的安全性仰賴其分層方法。使用多重驗證因素會為攻擊者帶來相當程度的挑戰。即使攻擊者試圖打探使用者的密碼，在不持有信任裝置的情況下便沒有任何意義。反之，如果使用者不小心遺失裝置，拾獲該裝置的人仍然無法使用此裝置，除非他或她也知道使用者的密碼。
Azure Multi-Factor Authentication 是一種多因素驗證服務，需要使用者同時使用行動裝置應用程式、通話或簡訊來驗證登入。它可與 Azure Active Directory 搭配使用，來保護內部部署資源和 Azure Multi-Factor Authentication Server 的安全，它還可以使用 SDK 來與自訂應用程式和目錄搭配使用。

![Azure Multi-Factor Authentication][]

### 保護雲端 Azure Active Directory 的安全

啟用適用於 Azure AD 身分識別的 Multi-Factor Authentication，系統將於使用者下次登入時出現設定其他驗證的提示。使用 Multi-Factor Authentication 來保護 Azure、Microsoft Online Services (如 Office 365 和 Dynamics CRM Online)，以及無需額外設定即可整合 Azure AD 的協力廠商雲端服務的安全。您可以快速啟用大量全域使用者和應用程式的多因素驗證。[詳細資訊][]

### 保護內部部署資源和 Active Directory 的安全

使用 Azure Multi-Factor Authentication Server 來啟用內部部署資源 (例如 IIS 和 Active Directory) 的 Multi-Factor Authentication。Azure Multi-Factor Authentication Server 可讓系統管理員整合與 IIS 驗證共同保護 Microsoft IIS Web 應用程式、RADIUS 驗證、LDAP 驗證和 Windows 驗證的安全。[詳細資訊][1]

### 保護自訂應用程式的安全

</p>
SDK 允許與雲端服務的直接整合。在應用程式的登入或交易程序中內建主動式驗證通話和簡訊驗證，並充分利用應用程式的現有使用者資料庫。[詳細資訊][2]

### Multi-Factor Authentication for Office 365

Azure Multi-Factor Authentication 技術支援的 Multi-Factor Authentication for Office 365，專用於 Office 365 應用程式並可透過 Office 365 入口網站進行管理。系統管理員現在可以使用多因素驗證來保護其 Office 365 資源的安全。[詳細資訊][3]

### 適用於 Azure 系統管理員的 Multi-Factor Authentication

適用於 Office 365 的 Multi-Factor Authentication 功能的相同子集將免費提供給所有 Azure 系統管理員。Azure 訂閱的每個管理帳戶現可透過啟用此多因素驗證核心功能，來取得額外的防護。因此想要存取 Azure 入口網站以建立 VM、網站、管理儲存體、行動服務或任何其他 Azure 服務的系統管理員，可在其系統管理員帳戶中新增多因素驗證。[詳細資訊][4]

### Multi-Factor Authentication 功能比較

下列內容將說明可用的多因素驗證版本，以及所提供的功能簡短摘要。您可以使用此內容來判斷適合您的多因素驗證版本。[詳細資訊][4]

![Azure Multi-Factor Authentication 功能比較][]

**其他資源**

-   [以組織方式註冊 Azure][]
-   [Azure 身分識別][]
-   [Azure Multi-Factor Authentication Library][]

  [Azure Multi-Factor Authentication]: ./media/multi-factor-authentication/WAMFA1.png
  [詳細資訊]: http://msdn.microsoft.com/zh-tw/library/dn249466.aspx
  [1]: http://msdn.microsoft.com/zh-tw/library/dn249467.aspx
  [2]: http://msdn.microsoft.com/zh-tw/library/dn249464.aspx
  [3]: http://msdn.microsoft.com/zh-tw/library/dn383636.aspx
  [4]: http://msdn.microsoft.com/zh-tw/library/dn249471.aspx
  [Azure Multi-Factor Authentication 功能比較]: ./media/multi-factor-authentication/mfacomparison1.png
  [以組織方式註冊 Azure]: /zh-tw/manage/services/identity/organizational-account/
  [Azure 身分識別]: /zh-tw/manage/windows/fundamentals/identity/
  [Azure Multi-Factor Authentication Library]: http://technet.microsoft.com/zh-tw/library/dn249471.aspx
