<properties
	pageTitle="Azure Active Directory 網域服務預覽：疑難排解指南 | Microsoft Azure"
	description="Azure AD 網域服務的疑難排解指南"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Azure AD 網域服務 *(預覽)* - 疑難排解指南
這篇文章提供設定或管理 Azure Active Directory (AD) 網域服務時，可能會遇到的問題之疑難排解提示。


### 您無法為 Azure AD 目錄啟用 Azure AD 網域服務
如果您嘗試為您的目錄啟用 Azure AD 網域服務時，遇到失敗或切換回 [已停用] 的情況，請執行下列疑難排解步驟：

- 請確定現有網域的名稱並未與該虛擬網路上可用的網域名稱相同。例如，假設您有名為 'contoso.com' 的網域已可用於選取的虛擬網路。接著，您可嘗試在該虛擬網路上啟用具有相同網域名稱 (即 'contoso.com') 的 Azure AD 網域服務受管理網域。您將會在嘗試啟用 Azure AD 網域服務時發生錯誤。這是因為名稱與該虛擬網路上的網域名稱衝突。在此情況下，您必須使用不同的名稱來設定 Azure AD 網域服務受管理網域。或者，您可以解除佈建現有的網域，然後繼續啟用 Azure AD 網域服務。

- 查看 Azure AD 目錄中是否有名稱為 [Azure AD 網域服務同步處理] 的應用程式。如果此應用程式存在，您必須將它刪除，然後再重新啟用 Azure AD 網域服務。執行下列步驟，以檢查應用程式是否存在並將它刪除 (如果應用程式存在的話)：

  1. 登入 **Azure 管理入口網站** ([https://manage.windowsazure.com](https://manage.windowsazure.com))。
  2. 在左窗格中，選取 [Active Directory] 節點。
  3. 選取您要啟用 Azure AD 網域服務的 Azure AD 租用戶 (目錄)。
  4. 瀏覽至 [應用程式] 索引標籤。
  5. 選取下拉式清單中的 [我公司所擁有的應用程式] 選項。
  6. 檢查名為 [Azure AD 網域服務同步處理] 的應用程式。如果應用程式存在，請繼續將它刪除。
  7. 刪除此應用程式後，請嘗試再次啟用 Azure AD 網域服務。


### 使用者無法登入 Azure AD 網域服務管理的網域
如果您遇到 Azure AD 租用戶中的一或多個使用者無法登入新建立的受管理網域的情況，請執行下列疑難排解步驟：

- 確定您已根據《入門指南》中所述的步驟來[啟用密碼同步處理](active-directory-ds-getting-started-password-sync.md)。

- 確定受影響的使用者帳戶不是 Azure AD 租用戶中的外部帳戶。外部帳戶的範例包括 Microsoft 帳戶 (例如 'joe@live.com') 或來自外部 Azure AD 目錄的使用者帳戶。因為 Azure AD 網域服務沒有這類使用者帳戶的認證，這些使用者會無法登入受管理的網域。

- 確認 Azure AD 租用戶中受影響的使用者帳戶 UPN 前置詞 (也就是 UPN 的第一個部分)，長度少於 20 個字元。例如，'joereallylongnameuser@contoso.com' UPN 的前置詞 ('joereallylongnameuser') 超過 20 個字元，因而在 Azure AD 網域服務的受管理網域中無法使用此帳戶。

- **同步處理的帳戶：**如果受影響的使用者帳戶會從內部部署目錄同步處理，請確認下列各項：
    - 您已部署或更新為 [Azure AD Connect 的最新版本](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect)。
    - 您已設定 Azure AD Connect 以[執行完整同步處理](active-directory-ds-getting-started-password-sync.md)。
    - 根據您的目錄大小，可能需要一些時間，使用者帳戶和認證雜湊才可在 Azure AD 網域服務中提供使用。確定您已等候足夠長的時間之後，再重試驗證 (視目錄大小的不同，可能需要幾個小時到一天的時間，大型目錄則可能需要兩天)。

- **僅限雲端帳戶**：如果受影響的使用者帳戶是僅限雲端的使用者帳戶，請確定在您啟用 Azure AD 網域服務之後，使用者已變更其密碼。這個步驟會導致產生 Azure AD 網域服務所需的認證雜湊。


### 連絡我們
如果對您的受管理網域有問題，請檢查以了解本疑難排解指南中所述的步驟是否可解決問題。如果您仍然有問題，請隨意與我們連絡：

- **電子郵件：**您可以透過 [Azure AD 網域服務意見反應](mailto:aaddsfb@microsoft.com)寄送電子郵件給我們務必包含您的 Azure AD 目錄的租用戶識別碼和您為 AAD 網域服務設定的網域名稱，以便我們調查問題。
- **[Azure Active Directory 使用者心聲頻道](https://feedback.azure.com/forums/169401-azure-active-directory/)：**請務必在問題前加上 **'AADDS'** 字詞，才可送達我們。

<!---HONumber=AcomDC_0128_2016-->