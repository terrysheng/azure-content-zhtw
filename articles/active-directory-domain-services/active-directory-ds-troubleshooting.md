<properties
	pageTitle="Azure Active Directory 網域服務預覽：疑難排解指南 | Microsoft Azure"
	description="Azure AD 網域服務的疑難排解指南"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="maheshu"/>

# Azure AD 網域服務 *(預覽)* - 疑難排解指南
這篇文章提供設定或管理 Azure AD 網域服務時可能遇到的問題的疑難排解提示。


### 使用者無法登入 Azure AD 網域服務管理的網域
如果您遇到 Azure AD 租用戶中的一或多個使用者無法登入新建立的受管理網域的情況，請執行下列疑難排解步驟：

- 確定您已根據《入門指南》中所述的步驟來[啟用密碼同步處理](active-directory-ds-getting-started-password-sync.md)。

- 確定受影響的使用者帳戶不是 Azure AD 租用戶中的外部帳戶。外部帳戶的範例包括 Microsoft 帳戶 (例如 'joe@live.com') 或來自外部 Azure AD 目錄的使用者帳戶。因為 Azure AD 網域服務沒有這類使用者帳戶的認證，這些使用者會無法登入受管理的網域。

- **同步的處理帳戶：**如果受影響的使用者帳戶是從內部部署目錄同步處理，請確定會遵循下列步驟：
    - 您已部署或更新為 Azure AD Connect 的 GA 版本。較舊版本將不會同步處理 NTLM/Kerberos 驗證所需的認證雜湊。
    - 您已建立啟用對 Azure AD 同步處理舊版認證所需的登錄機碼。
    - 在執行 Azure AD Connect 的伺服器上建立以上提到的登錄機碼之後，您已如文件中所述，強制 Azure AD 執行完整同步處理。
    - 根據您的目錄大小，可能需要一些時間，使用者帳戶和認證雜湊才可在 Azure AD 網域服務中提供使用。確定您等候夠久時間再重試驗證 (取決於您的目錄的大小，可能幾個小時，大型目錄則需一或兩天)。

- **僅限雲端帳戶**：如果受影響的使用者帳戶是僅限雲端的使用者帳戶，請確定在您啟用 Azure AD 網域服務之後，使用者已變更其密碼。這個步驟會導致產生 Azure AD 網域服務所需的認證雜湊。


### 連絡我們
如果對您的受管理網域有問題，請檢查以了解本疑難排解指南中所述的步驟是否可解決問題。如果您仍然有問題，請隨意與我們連絡：

- [Azure Active Directory 使用者心聲頻道](http://feedback.azure.com/forums/169401-azure-active-directory)。請確保在您的問題前附加 **'AADDS'** 字詞以便與我們連絡。
- 您也可以透過 [Azure AD 網域服務意見反應](mailto:aaddsfb@microsoft.com)寄送電子郵件給我們

<!---HONumber=Oct15_HO4-->